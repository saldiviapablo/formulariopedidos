# 02. Inventario y Especificación Exhaustiva de Pantallas

Este documento cataloga las 10 pantallas que componen la aplicación WeWeb, detallando su ruta, UID, nivel de acceso, variables reactivas, componentes UI, estados visuales y eventos disparados.

---

## 1. Matriz General de Pantallas

| Pantalla | Ruta URL | UID WeWeb | Nivel de Acceso | Tipo de Layout |
| :--- | :--- | :--- | :--- | :--- |
| **Home Institucional** | `/` (`/home`) | `79abd033-367a-45d1-ae9b-e20cf92a6636` | Público | Landing / Portal Institucional |
| **Login / Iniciar Sesión** | `/login` | `753b981e-f102-4df7-bcf7-9be83f324404` | Público (Redirige si auth) | Formulario centrado |
| **Solicitud de Acceso** | `/solicitar-acceso` | `61edfb04-208f-4856-884e-d6ae78830b59` | Público | Formulario de Registro Interno |
| **Acceso Pendiente** | `/acceso-pendiente` | `32b9a62c-33d9-4860-a15f-fa351e45aa69` | Autenticado (`pendiente`) | Pantalla de Espera Informativa |
| **Nueva Solicitud** | `/nueva-solicitud` | `72bfb8d9-76d8-45b5-a049-39f525c49d7e` | Público | Wizard Multi-Step (3 Pasos) |
| **Seguimiento de Pedido** | `/seguimiento` | `1d98f231-a693-4a43-85b8-87ae55b6d43e` | Público (Con Token / Email) | Dashboard de Consulta Pública |
| **Completar Solicitud** | `/completar-solicitud` | `a1fa0234-aa68-4758-b554-4c7ddea469c5` | Público (Con Token Info) | Formulario de Aclaración |
| **Bandeja de Gestión** | `/gestion` | `027a6d44-7b5b-4160-98ae-6bad99e24e57` | `equipo_interno` / `admin` | Dashboard Tabular con Filtros |
| **Detalle de Pedido Interno** | `/gestion/pedido/:pedidoid/detalle` | `f742dcb9-9662-44e8-b9d4-c52505827d23` | `equipo_interno` / `admin` | Detalle con Pestañas / Sidebar |
| **Administración de Usuarios**| `/gestion/usuarios` | `16b96f5c-76ab-41ae-bfca-902594e5d5f1` | `admin` únicamente | Tabla Administrativa con Modales |

---

## 2. Detalle de Cada Pantalla

### 2.1. Home Institucional (`/` ó `/home`)
- **UID**: `79abd033-367a-45d1-ae9b-e20cf92a6636`
- **Propósito**: Punto de entrada al sistema para el público y funcionarios provinciales.
- **Estructura Visual**:
  - **Header**: Logo de Gobierno / Secretaría de Medios, enlace a "Seguimiento", enlace a "Ingreso Equipo".
  - **Hero Section**: Título destacado ("Portal de Solicitudes de Comunicación y Medios"), subtítulo explicativo y dos botones Call to Action principales:
    1. *"Nueva Solicitud"* -> Redirige a `/nueva-solicitud`.
    2. *"Consultar Estado de Pedido"* -> Redirige a `/seguimiento`.
  - **Sección de Servicios**: Tarjetas informativas de los tipos de servicios prestados (Prensa, Diseño, Coberturas, Redes, Streaming).
  - **Footer**: Enlaces institucionales, vías de contacto y copyright.

---

### 2.2. Login / Iniciar Sesión (`/login`)
- **UID**: `753b981e-f102-4df7-bcf7-9be83f324404`
- **Propósito**: Autenticación de miembros del equipo y administradores.
- **Componentes**:
  - Card central con logotipo.
  - Input Email institucional.
  - Input Contraseña (con botón toggle para mostrar/ocultar contraseña).
  - Botón "Iniciar Sesión" (dispara workflow WeWeb Auth Login).
  - Enlace inferior: "¿No tenés cuenta? Solicitá acceso aquí" -> Navega a `/solicitar-acceso`.
- **Lógica post-login**:
  - Si `estado_acceso == 'aprobado'` -> Redirige a `/gestion`.
  - Si `estado_acceso == 'pendiente'` -> Redirige a `/acceso-pendiente`.
  - Si `estado_acceso == 'revocado'` -> Muestra alerta de acceso inactivo y desautentica.

---

### 2.3. Solicitar Acceso (`/solicitar-acceso`)
- **UID**: `61edfb04-208f-4856-884e-d6ae78830b59`
- **Propósito**: Registro de nuevos postulantes del equipo interno.
- **Campos Físicos del Formulario**:
  1. `Nombre` (Input text, requerido, min 2 caracteres).
  2. `Apellido` (Input text, requerido, min 2 caracteres).
  3. `Usuario` (`nombre_usuario`) (Input text, requerido, 2 a 30 caracteres, sin espacios, normalizado a lowercase).
  4. `Correo electrónico` (Input email institucional, requerido, validado).
  5. `Contraseña` (Input password, requerido, min 8 caracteres).
  6. `Confirmar contraseña` (Input password, validación de coincidencia exacta).
- **Acción**:
  - Botón "Registrarme / Solicitar Acceso".
  - Dispara `api_registrar_solicitud_acceso` (`10ee0061-c651-456a-8573-ade4c43e37b0`).
  - Al completar exitosamente, crea usuario en `auth.users` y en `usuarios_acceso` con `estado_acceso = 'pendiente'`, y redirige inmediatamente a `/acceso-pendiente`.

---

### 2.4. Acceso Pendiente (`/acceso-pendiente`)
- **UID**: `32b9a62c-33d9-4860-a15f-fa351e45aa69`
- **Propósito**: Estado de espera para usuarios registrados aún no aprobados.
- **Estructura**:
  - Ilustración/Icono de reloj o espera.
  - Mensaje: "Tu solicitud de acceso está en revisión por el Administrador General".
  - Muestra el nombre y usuario registrado.
  - Botón "Cerrar Sesión" (destruye token JWT y redirige a `/login`).
  - Botón "Verificar estado" (reconsulta `vw_mi_acceso`; si ya fue aprobado, redirige a `/gestion`).

---

### 2.5. Nueva Solicitud (`/nueva-solicitud`)
- **UID**: `72bfb8d9-76d8-45b5-a049-39f525c49d7e`
- **Propósito**: Asistente en 3 pasos para ingreso de solicitudes por cualquier organismo solicitante.
- **Paso 1: Datos del Solicitante**:
  - Nombre, Apellido, Email, Teléfono, Cargo.
  - Select Área / Ministerio (alimentado por `vw_areas_activas`).
- **Paso 2: Selección y Requerimientos de Servicios**:
  - Tarjetas interactivas de servicios activos (`vw_tipos_servicio_activos`).
  - Subformularios dinámicos por servicio seleccionado.
- **Paso 3: Adjuntos y Confirmación**:
  - Uploader de archivos (WeWeb Storage).
  - Resumen completo.
  - Botón "Enviar Solicitud" -> Llama a `api_crear_pedido_completo` (`819b8329-94b8-4a7d-929f-ce7a477c9161`).
- **Estado de Éxito**:
  - Lista de números `PED-YYYY-NNNNNN` creados con enlaces y tokens.

---

### 2.6. Seguimiento de Pedido (`/seguimiento`)
- **UID**: `1d98f231-a693-4a43-85b8-87ae55b6d43e`
- **Propósito**: Consulta transparente del estado de tramitación de un pedido.
- **Formas de Acceso**:
  - Directo por URL params: `?ped=PED-2026-000001&token=...`
  - Búsqueda manual: Input PED + Input Email solicitante.
- **Contenido Visual al Cargar**:
  - Cabecera del pedido (Número, Fecha, Área, Servicio).
  - Stepper / Timeline de Estados: `Pendiente` -> `En Proceso` -> `Finalizado`.
  - Alerta de acción requerida si el estado es `requiere_informacion` (con botón hacia `/completar-solicitud`).
  - Lista de entregables finales o comentarios públicos.

---

### 2.7. Completar Solicitud (`/completar-solicitud`)
- **UID**: `a1fa0234-aa68-4758-b554-4c7ddea469c5`
- **Propósito**: Permite al solicitante responder a una solicitud de aclaración formulada por el equipo interno.
- **Carga de Datos**:
  - Lee `token` desde query params (`?token=...`).
  - Invoca backend workflow para obtener el detalle de la solicitud de información vinculada.
- **Formulario**:
  - Muestra la nota/pregunta escrita por el gestor.
  - Textarea para la respuesta del solicitante.
  - Uploader de archivos adicionales.
  - Botón "Enviar Aclaración" -> Llama a `api_responder_solicitud_informacion`.

---

### 2.8. Bandeja de Gestión (`/gestion`)
- **UID**: `027a6d44-7b5b-4160-98ae-6bad99e24e57`
- **Propósito**: Consola operativa central para el equipo de medios y administradores.
- **Componentes**:
  - **Barra Superior**: Contadores de métricas (Total, Pendientes, En Proceso, Requieren Info, Finalizados).
  - **Filtros Rápidos**:
    - Input de búsqueda de texto libre (busca en número PED, solicitante, área y observaciones).
    - Select de Estado (`Todos`, `Pendiente`, `En Proceso`, `Requiere Info`, `Finalizado`, `Cancelado`).
    - Select de Área solicitante.
    - Select de Responsable asignado (alimentado por `api_listar_responsables` con `label = nombre_usuario`, `value = user_id`).
  - **Tabla de Pedidos (`vw_servicios_gestion`)**:
    - Columnas: Número PED, Fecha Solicitud, Solicitante, Área, Tipo de Servicio, Prioridad, Responsable Asignado, Estado, Acciones.
    - Selector directo en celda de Responsable para asignación inmediata.
    - Selector directo en celda de Estado.
    - Botón "Ver Detalle" -> Navega a `/gestion/pedido/:pedidoid/detalle`.
  - **Paginador**: Control de 10, 25, 50 filas por página.

---

### 2.9. Detalle de Pedido Interno (`/gestion/pedido/:pedidoid/detalle`)
- **UID**: `f742dcb9-9662-44e8-b9d4-c52505827d23`
- **Propósito**: Vista en profundidad de un pedido específico.
- **Pestañas / Bloques de Contenido**:
  1. **Información General**: Datos completos del solicitante, área, fechas, prioridad y descripción.
  2. **Especificación del Servicio**: Campos dinámicos del tipo de servicio (jsonb formateado).
  3. **Archivos Adjuntos**: Visor y descargador de archivos subidos por el solicitante o equipo.
  4. **Solicitar Información**: Historial de solicitudes de info enviadas y formulario para crear una nueva (dispara `api_solicitar_informacion` y envía email al solicitante).
  5. **Comunicaciones por Email**: Bitácora de correos emitidos hacia el solicitante y formulario de redacción de mensaje libre vía n8n (`bw_enviar_comunicacion_pedido`).
  6. **Trazabilidad y Bitácora**: Log de cambios de estado, fechas y usuarios responsables.

---

### 2.10. Administración de Usuarios (`/gestion/usuarios`)
- **UID**: `16b96f5c-76ab-41ae-bfca-902594e5d5f1`
- **Propósito**: Gestión exclusiva del Administrador General para el control de accesos e identidades.
- **Componentes**:
  - Tabla de usuarios (`vw_usuarios_acceso_admin`): Email, Nombre y Apellido, Usuario (`nombre_usuario`), Rol Auth, Estado de Acceso, Fecha Solicitud, Acciones.
  - Botón "Aprobar" (asigna rol `equipo_interno` o `admin`).
  - Botón "Revocar / Desactivar".
  - Botón "Editar Usuario" (Modal para actualizar `nombre_usuario` con validación estricta 2-30 chars).
