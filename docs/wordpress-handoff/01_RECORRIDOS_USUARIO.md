# 01. Recorridos de Usuario (User Journeys)

Este documento describe la totalidad de los flujos de interacción de los distintos perfiles de usuario dentro del sistema **PEDIDOS — Secretaría de Medios**, detallando precondiciones, pasos, decisiones del sistema, llamadas a la API, efectos colaterales y manejo de excepciones.

---

## 1. Perfiles de Usuario y Modelo de Actores

| Perfil | Nivel de Privilegio | Modo de Acceso | Finalidad Principal |
| :--- | :--- | :--- | :--- |
| **Solicitante Público** | Anónimo / Ciudadano / Organismo | Acceso público sin login previo | Crear solicitudes de servicios de medios, consultar seguimiento de pedidos y responder pedidos de aclaración/archivos. |
| **Postulante a Equipo** | Registrado en espera | Cuenta Auth (`estado_acceso = 'pendiente'`) | Registro de cuenta corporativa solicitando ingreso al equipo interno de gestión. |
| **Equipo Interno (Gestor)** | Autenticado | Rol `equipo_interno`, `estado_acceso = 'aprobado'` | Visualizar bandeja unificada de pedidos, filtrar, reasignar responsables, actualizar estados, solicitar más datos y comunicarse por email. |
| **Administrador General** | Autenticado y Superusuario | Rol `admin`, `estado_acceso = 'aprobado'` | Control total operativo + administración de accesos, roles, revocaciones y normalización de identidades (`nombre_usuario`). |

---

## 2. Recorrido 1: Solicitud de Servicios (Solicitante Público)

### Diagrama de Secuencia
```mermaid
sequenceDiagram
    autonumber
    actor S as Solicitante (Público)
    participant F as Frontend (/nueva-solicitud)
    participant B as Backend API (api_crear_pedido_completo)
    participant DB as PostgreSQL (pedidos, servicios, secuencias)
    participant N as Webhook n8n (Email)

    S->>F: Accede a /nueva-solicitud (Paso 1: Datos Contacto)
    S->>F: Ingresa Nombre, Apellido, Email, Teléfono, Cargo, Área
    S->>F: Avanza a Paso 2 (Selección de Servicios)
    S->>F: Selecciona 1..N servicios y completa campos específicos
    S->>F: Avanza a Paso 3 (Revisión y Adjuntos)
    S->>F: Sube archivos de referencia (opcional)
    S->>F: Clic en "Confirmar y Enviar Solicitud"
    F->>B: POST /api_crear_pedido_completo
    activate B
    B->>DB: Bloqueo y obtención de secuencia anual (PED-YYYY-NNNNNN)
    loop Por cada servicio seleccionado
        B->>DB: INSERT INTO pedidos (1 pedido por servicio/pieza)
        B->>DB: INSERT INTO servicios_solicitados
        B->>DB: INSERT INTO archivos (relación con pedido)
    end
    B->>N: Trigger Webhook n8n (Confirmación de recepción por email)
    B-->>F: Retorna lista de PEDs creados con tokens de seguimiento
    deactivate B
    F-->>S: Pantalla de Éxito con los números PED y enlaces directos
```

### Descripción Detallada del Recorrido:
1. **Entrada y Navegación**:
   - URL: `/nueva-solicitud`.
   - El formulario se presenta como un wizard progresivo de 3 pasos con persistencia reactiva en variables locales.
2. **Paso 1 — Identificación y Datos de Contacto**:
   - Campos requeridos: `Nombre`, `Apellido`, `Email institucional`, `Teléfono de contacto`, `Cargo/Función`, `Área u Organismo solicitante` (select dinámico alimentado por `vw_areas_activas`).
   - Validaciones: Email válido mediante regex, teléfono mínimo 6 dígitos, área seleccionada obligatoria.
   - Acción: Botón "Siguiente" valida el formulario; si es válido, avanza a Paso 2.
3. **Paso 2 — Selección y Parametrización de Servicios**:
   - Se muestra un catálogo de servicios activos (`vw_tipos_servicio_activos`) categorizados (ej. Cobertura Fotográfica, Gacetilla de Prensa, Diseño Gráfico, Audiovisual, Campañas).
   - El usuario selecciona uno o varios servicios mediante checkbox / toggle cards.
   - Cada servicio seleccionado expande su propio bloque de campos requeridos (ej. Fecha y hora del evento, locación, dimensiones solicitadas, formato de entrega, público objetivo, etc.).
4. **Paso 3 — Archivos Adjuntos y Revisión Consolidada**:
   - El solicitante puede adjuntar 1 a N archivos de apoyo (documentos, manuales de marca, borradores, fotos de referencia).
   - Los archivos se suben al almacenamiento seguro (WeWeb Storage privado).
   - Se presenta un resumen general de datos de contacto y detalle de cada servicio configurado.
   - El usuario marca la confirmación de veracidad y hace clic en **"Confirmar y Enviar Solicitud"**.
5. **Lógica de Creación Atómica (Regla de Oro: 1 Servicio = 1 PED)**:
   - El backend workflow `api_crear_pedido_completo` recibe el payload con el array de servicios.
   - Para **cada servicio** se genera un número único consecutivo basado en la secuencia anual `PED-YYYY-NNNNNN` (ej. `PED-2026-000101`, `PED-2026-000102`).
   - Se crea un registro en `pedidos` (con `estado_general = 'pendiente'`), un registro en `servicios_solicitados`, y se asocian los registros en `archivos`.
   - Se genera un token criptográfico `token_acceso` (UUID v4 / hash 64 chars) por cada pedido para acceso público seguro.
   - Se dispara un webhook a n8n para enviar un correo unificado de confirmación al solicitante con la lista de códigos PED y sus enlaces directos.
6. **Pantalla de Finalización y Confirmación**:
   - Se limpian las variables del wizard.
   - Se despliega una vista de éxito con las tarjetas de cada PED generado, botón de copiar enlace al portapapeles y enlace para descargar comprobante o volver a la home.

---

## 3. Recorrido 2: Consulta Pública de Estado y Seguimiento

```mermaid
sequenceDiagram
    autonumber
    actor S as Solicitante
    participant F as Frontend (/seguimiento)
    participant B as Backend API (api_consultar_estado_pedido)
    participant DB as PostgreSQL

    S->>F: Accede a /seguimiento
    alt Enlace directo desde Email con Token
        S->>F: Abre /seguimiento?ped=PED-2026-000101&token=abc123xyz
        F->>B: POST api_consultar_estado_pedido {numero_pedido, token}
    else Búsqueda Manual
        S->>F: Ingresa Número PED y Email solicitante
        F->>B: POST api_consultar_estado_pedido {numero_pedido, email}
    end
    B->>DB: Consulta validada en pedidos + servicios + solicitudes_informacion
    B-->>F: Retorna datos del pedido (estado, historial, hitos públicos)
    F-->>S: Renderiza línea de tiempo y estado actual del pedido
```

### Comportamiento Operativo:
- **Validación de Identidad**: Para proteger la privacidad institucional, la consulta requiere coincidencia exacta de (`numero_pedido` + `token_acceso`) o (`numero_pedido` + `solicitante_email`).
- **Visualización**:
  - Badge de estado general (`Pendiente`, `En Proceso`, `Requiere Información`, `Finalizado`, `Cancelado`).
  - Hitos temporales (Fecha de ingreso, fecha de inicio de producción, fecha de entrega estimada).
  - Alerta especial si el pedido está en `requiere_informacion`: se resalta un banner con botón de acción directa hacia `/completar-solicitud?token=...`.

---

## 4. Recorrido 3: Aclaración y Entrega de Información (/completar-solicitud)

```mermaid
sequenceDiagram
    autonumber
    actor S as Solicitante
    participant F as Frontend (/completar-solicitud)
    participant B as Backend API
    participant DB as PostgreSQL
    participant N as Webhook n8n

    S->>F: Ingresa con token único (/completar-solicitud?token=tok_info_123)
    F->>B: Valida token y obtiene detalles de la solicitud de información
    B-->>F: Retorna motivo del pedido de info y requerimientos
    F-->>S: Muestra mensaje del equipo gestor y formulario de respuesta
    S->>F: Escribe texto aclaratorio y/o adjunta nuevos archivos
    S->>F: Clic en "Enviar Respuesta"
    F->>B: POST api_responder_solicitud_informacion {token, respuesta_texto, archivos}
    B->>DB: UPDATE solicitudes_informacion (estado = 'respondida', respondida_at = NOW())
    B->>DB: UPDATE pedidos (estado_general = 'en_proceso' / 'en_revision')
    B->>DB: INSERT INTO archivos (nuevos adjuntos vinculados)
    B->>N: Notifica por email al responsable asignado
    B-->>F: Confirmación exitosa
    F-->>S: Mensaje de confirmación y redirección a /seguimiento
```

---

## 5. Recorrido 4: Registro y Aprobación de Usuario Interno

```mermaid
sequenceDiagram
    autonumber
    actor U as Nuevo Miembro
    actor A as Administrador General
    participant F as Frontend (/solicitar-acceso)
    participant B as Backend Auth & API
    participant DB as PostgreSQL (usuarios_acceso)
    participant M as Admin Users (/gestion/usuarios)

    U->>F: Completa Nombre, Apellido, Usuario (2-30 car), Email, Password
    F->>B: POST api_registrar_solicitud_acceso
    B->>DB: Valida unicidad de email y nombre_usuario (trim + lowercase)
    B->>DB: Crea Auth User + registro en usuarios_acceso (estado_acceso = 'pendiente')
    B-->>F: Registro exitoso
    F-->>U: Redirección automática a /acceso-pendiente
    
    Note over A,M: El Administrador ingresa periódicamente a la gestión
    A->>M: Abre /gestion/usuarios
    M->>DB: Carga vw_usuarios_acceso_admin
    M-->>A: Lista usuarios pendientes, aprobados y revocados
    A->>M: Selecciona usuario pendiente y hace clic en "Aprobar"
    A->>M: Selecciona rol ("equipo_interno" o "admin")
    M->>B: POST api_admin_gestionar_acceso_usuario {user_id, accion: 'aprobar', rol}
    B->>DB: UPDATE usuarios_acceso (estado_acceso = 'aprobado', aprobado_at = NOW(), aprobado_por = admin_id)
    B->>DB: UPDATE auth.users (role = nuevo_rol)
    B-->>M: Éxito
    
    Note over U,F: El usuario ahora puede iniciar sesión
    U->>F: Inicia sesión en /login
    F->>B: Autenticación exitosa
    F-->>U: Redirección a /gestion
```

---

## 6. Recorrido 5: Gestión Integral de Pedidos (Equipo Interno)

```mermaid
sequenceDiagram
    autonumber
    actor G as Gestor (Equipo Interno)
    participant Bnd as Bandeja (/gestion)
    participant Det as Detalle (/gestion/pedido/:id/detalle)
    participant API as Backend APIs
    participant DB as PostgreSQL
    participant N as n8n Webhook

    G->>Bnd: Accede a /gestion (Bandeja unificada)
    Bnd->>API: Fetch vw_servicios_gestion con filtros activos
    API-->>Bnd: Lista de pedidos tabulada
    G->>Bnd: Aplica filtro por Estado, Área, Responsable o Búsqueda de texto
    G->>Bnd: Modificación rápida: Asigna responsable desde selector dropdown
    Bnd->>API: POST api_actualizar_servicio {pedido_id, responsable_id}
    
    G->>Bnd: Clic en "Ver Detalle" de un pedido
    Bnd->>Det: Navega a /gestion/pedido/PED-2026-000101/detalle
    Det->>API: Fetch vw_pedido_detalle_interno, archivos, comunicaciones, solicitudes
    API-->>Det: Datos completos del pedido
    
    alt Solicitar más información al solicitante
        G->>Det: Abre tab "Solicitar Información"
        G->>Det: Redacta motivo y requisitos faltantes
        Det->>API: POST api_solicitar_informacion {pedido_id, motivo}
        API->>DB: INSERT INTO solicitudes_informacion (genera token único)
        API->>DB: UPDATE pedidos SET estado_general = 'requiere_informacion'
        API->>N: Envía email al solicitante con enlace tokenizado
        API-->>Det: Solicitud registrada y visible en el historial
    else Enviar Comunicación directa por Email
        G->>Det: Tab "Comunicaciones" -> Redacta asunto y mensaje
        Det->>API: POST bw_enviar_comunicacion_pedido {pedido_id, asunto, mensaje}
        API->>N: POST n8n webhook (envío por Gmail)
        API->>DB: INSERT INTO comunicaciones_pedido
        API-->>Det: Mensaje agregado a la bitácora
    else Finalizar Pedido
        G->>Det: Cambia estado a "Finalizado" con notas de cierre
        Det->>API: POST api_actualizar_servicio {pedido_id, estado: 'finalizado'}
        API->>DB: Actualiza estado y fecha_cierre
        API->>N: Notifica al solicitante que su pedido está listo
    end
```

---

## 7. Recorrido 6: Gestión de Usuarios y Seguridad (Administrador)

1. **Navegación**: El administrador hace clic en "Usuarios" en la barra superior o ingresa a `/gestion/usuarios`.
2. **Listado y Filtros**:
   - Pestañas de estado: `Pendientes de Aprobación`, `Activos / Aprobados`, `Revocados / Inactivos`.
3. **Acciones Disponibles**:
   - **Aprobar Solicitud**: Asigna rol operativo (`equipo_interno`) o administrativo (`admin`), activa la cuenta en `usuarios_acceso`.
   - **Rechazar / Revocar**: Cambia `estado_acceso` a `revocado` e invalida sesiones activas.
   - **Editar Identidad (`nombre_usuario`)**:
     - Modal de edición rápida del alias de sistema.
     - Valida en vivo: min 2, max 30 caracteres, sin espacios intermedios raros, normalizado a minúsculas (`trim + lowercase`).
     - Ejecuta `api_admin_editar_nombre_usuario` verificando unicidad contra la base de datos.
