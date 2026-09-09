# 06. Backend Workflows y Lógica de Negocio en Servidor

Este documento especifica exhaustivamente todos los Backend Workflows (Endpoints / Microservicios) implementados en WeWeb Tables / PostgreSQL, documentando sus parámetros de entrada, consultas SQL, bloqueos atómicos, llamadas externas a n8n y estructuras de respuesta.

---

## 1. Inventario de Backend Workflows

| Nombre del Workflow | UID WeWeb | Método / Trigger | Nivel de Seguridad | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `api_crear_pedido_completo` | `819b8329-94b8-4a7d-929f-ce7a477c9161` | POST Público | Validación de schema | Crea pedidos consecutivos (1 por servicio), asigna secuencia atómica y dispara webhook n8n. |
| `api_actualizar_servicio` | `ec935c4c-4c71-4d1a-9ea5-fecc8b908312` | POST Autenticado | `equipo_interno` / `admin` | Actualiza responsable asignado, estado general, prioridad o notas de un pedido. |
| `api_solicitar_informacion` | `44ad1bdd-c7e8-45a7-9555-ed11931b30c7` | POST Autenticado | `equipo_interno` / `admin` | Genera token criptográfico, cambia estado a `requiere_informacion` y notifica al solicitante. |
| `bw_enviar_comunicacion_pedido` | `5ebf567a-1a5b-4e8d-916d-51c97073b865` | POST Autenticado | `equipo_interno` / `admin` | Registra mensaje en `comunicaciones_pedido` y despacha el correo vía n8n / Gmail. |
| `api_registrar_solicitud_acceso`| `10ee0061-c651-456a-8573-ade4c43e37b0` | POST Público | Rate limiting | Crea cuenta Auth, normaliza `nombre_usuario` (2-30 chars, lowercase) y registra perfil pendiente. |
| `api_admin_gestionar_acceso_usuario` | `234ef4a9-f913-40c6-9292-ad02be4313a3` | POST Autenticado | `admin` únicamente | Aprueba, rechaza o revoca usuarios y asigna roles (`equipo_interno`, `admin`). |
| `api_admin_editar_nombre_usuario` | `377fb94b-1c66-4c93-84dc-8e02f4a77a91` | POST Autenticado | `admin` únicamente | Modifica el alias de sistema garantizando unicidad y normalización 2-30. |
| `api_listar_responsables` | `565645b9-79e5-4981-bdd4-763cf076601b` | GET Autenticado | `equipo_interno` / `admin` | Retorna lista de miembros activos (`label = nombre_usuario`, `value = user_id`). |
| `api_consultar_estado_pedido` | `c7fcc2c1-95a8-488a-9128-c83bc82d5af3` | POST Público | Token / Email check | Devuelve información pública y trazabilidad de un pedido específico. |
| `api_responder_solicitud_informacion` | `9db5d921-2e6b-4e8c-a9df-6d0dfc249a56` | POST Público | Validación de Token | Registra respuesta del solicitante, adjunta archivos y reanuda el trámite del pedido. |

---

## 2. Especificación Detallada de Workflows Clave

### 2.1. `api_crear_pedido_completo`
- **UID**: `819b8329-94b8-4a7d-929f-ce7a477c9161`
- **Algoritmo de Numeración Atómica**:
  ```sql
  -- Bloqueo y obtención de secuencia anual
  UPDATE secuencias 
  SET valor = valor + 1, updated_at = NOW() 
  WHERE nombre = 'pedidos_' || TO_CHAR(NOW(), 'YYYY')
  RETURNING valor, prefijo, anio;
  -- Formateo: PED-YYYY-LPAD(valor::text, 6, '0') -> 'PED-2026-000101'
  ```
- **Regla Fundamental (1 Servicio = 1 PED)**:
  - Si el solicitante seleccionó 3 servicios, el bucle se ejecuta 3 veces consecutivas dentro de una transacción.
  - Genera 3 filas en `pedidos` con 3 números PED consecutivos.
  - Inserta los detalles en `servicios_solicitados`.
  - Asocia los registros en `archivos`.
- **Llamada Saliente Webhook n8n**:
  - URL: `<N8N_WEBHOOK_URL>` (ej. `https://n8n.pablosaldiviafotos.ar/webhook/pedidos-email`).
  - Evento: `NUEVA_SOLICITUD_CONFIRMACION`.
  - Payload:
    ```json
    {
      "evento": "NUEVA_SOLICITUD",
      "solicitante": {
        "nombre": "Juan",
        "apellido": "Pérez",
        "email": "solicitante@gobierno.example"
      },
      "pedidos_creados": [
        {
          "numero_pedido": "PED-2026-000101",
          "servicio": "Fotografía",
          "token_acceso": "tok_sec_9876543210"
        }
      ]
    }
    ```

---

### 2.2. `api_listar_responsables`
- **UID**: `565645b9-79e5-4981-bdd4-763cf076601b`
- **Consulta SQL**:
  ```sql
  SELECT 
      u.id AS value,
      COALESCE(NULLIF(BTRIM(ua.nombre_usuario), ''), 'sin_usuario') AS label,
      COALESCE(NULLIF(BTRIM(ua.nombre_usuario), ''), 'sin_usuario') AS nombre_usuario,
      u.id AS user_id,
      u.email
  FROM auth.users u
  JOIN usuarios_acceso ua ON ua.id = u.id
  WHERE ua.estado_acceso = 'aprobado'
  ORDER BY label ASC;
  ```
- **Garantía**: Solo devuelve usuarios activos y aprobados. El `label` visible es **únicamente `nombre_usuario`**, nunca el email ni el nombre personal.
