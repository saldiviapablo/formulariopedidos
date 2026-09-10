# 08 - Workflows Frontend Globales y Locales

> **Estado:** `[WEWEB-VERIFICADO]` / `[CONFIG-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Workflows Globales del Frontend (11 Workflows)

El runtime de la SPA cuenta con **11 Global Workflows** registrados en el proyecto: `[WEWEB-VERIFICADO]`

| # | Nombre de Workflow | Trigger / Evento | Acciones Principales | Evidencia |
|---|---|---|---|---|
| 1 | `wf_global_logout` | Click en "Cerrar Sesión" | Ejecuta `plugin-auth.logout()`, limpia variables de Pinia y redirige a `/login`. | `[WEWEB-VERIFICADO]` |
| 2 | `wf_global_toast` | Evento emitido por cualquier acción | Muestra notificación flotante (éxito, error, warning). | `[WEWEB-VERIFICADO]` |
| 3 | `wf_global_redirect_guard` | Cambio de ruta en Router | Evalúa permisos y redirige si falta autenticación. | `[WEWEB-VERIFICADO]` |
| 4 | `wf_copiar_portapapeles` | Click en botones de copia | Invoca `navigator.clipboard.writeText()` y dispara toast de confirmación. | `[WEWEB-VERIFICADO]` |
| 5 | `wf_format_fecha` | Helper utilitario global | Convierte ISO strings a formato local `DD/MM/YYYY HH:mm`. | `[WEWEB-VERIFICADO]` |
| 6 | `wf_global_download_file` | Click en archivo adjunto | Obtiene signed URL y abre la descarga en nueva pestaña. | `[WEWEB-VERIFICADO]` |
| 7 | `wf_global_sync_state` | Evento de sincronización | Re-ejecuta fetch de colecciones activas. | `[WEWEB-VERIFICADO]` |
| 8 | `wf_global_open_modal` | Botones de apertura modal | Setea variable `modal_open = true` y bindea payload. | `[WEWEB-VERIFICADO]` |
| 9 | `wf_global_close_modal` | Click en backdrop o botón cerrar | Setea `modal_open = false` y limpia variables temporales. | `[WEWEB-VERIFICADO]` |
| 10 | `wf_validate_jwt_session` | Intervalo periódico | Verifica vigencia del token de sesión en cookies. | `[WEWEB-VERIFICADO]` |
| 11 | `wf_global_error_handler` | Catch de promesas fallidas | Registra en consola y muestra toast amigable al usuario. | `[WEWEB-VERIFICADO]` |

---

## 2. Workflows Locales y Handlers por Vista

### 2.1 Página `/` (Home - Formulario)
- `wf_step1_next`: Valida campos de solicitante y selección de al menos 1 área; avanza a paso 2. `[WEWEB-VERIFICADO]`
- `wf_step2_next`: Valida campos requeridos de los servicios desplegados; avanza a paso 3. `[WEWEB-VERIFICADO]`
- `wf_step3_submit`: Ejecuta subida de archivos e invoca backend workflow `api_crear_pedido`. `[WEWEB-VERIFICADO]`

### 2.2 Página `/gestion` (Bandeja)
- `wf_gestion_onload`: Dispara `api_listar_responsables` y carga colecciones de pedidos y servicios. `[WEWEB-VERIFICADO]`
- `wf_filter_change`: Actualiza arrays filtrados en Pinia al modificar search o selects. `[WEWEB-VERIFICADO]`

### 2.3 Página `/pedido/:id` (Detalle)
- `wf_detalle_onload`: Dispara `api_obtener_detalle_pedido` y popula los estados locales. `[WEWEB-VERIFICADO]`
- `wf_cambiar_responsable`: Invoca `api_asignar_responsable_servicio`. `[WEWEB-VERIFICADO]`
- `wf_cambiar_estado_servicio`: Invoca `api_actualizar_servicio`. `[WEWEB-VERIFICADO]`
- `wf_submit_solicitar_info`: Invoca `api_solicitar_informacion`. `[WEWEB-VERIFICADO]`
- `wf_submit_entrega_final`: Invoca `api_actualizar_entrega_final`. `[WEWEB-VERIFICADO]`

### 2.4 Página `/usuarios` (Admin)
- `wf_usuarios_onload`: Verifica rol `admin` y ejecuta `api_admin_listar_usuarios`. `[WEWEB-VERIFICADO]`
- `wf_aprobar_usuario`: Invoca `api_admin_aprobar_usuario`. `[WEWEB-VERIFICADO]`
- `wf_editar_usuario`: Invoca `api_admin_editar_nombre_usuario`. `[WEWEB-VERIFICADO]`

---

## 3. Evidencia de Verificación
- `[WEWEB-VERIFICADO]`: Extraído directamente de los volcados de Pinia stores (`wwWorkflows`) en WeWeb Editor.
