# 08 - Workflows Frontend Globales y Locales

> **Estado:** `[WEWEB-VERIFICADO]` / `[CONFIG-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Inventario de Workflows Globales del Frontend

WeWeb cuenta con **11 Global Workflows** disponibles en el runtime de la SPA:

| # | Nombre de Workflow | UID Workflow | Trigger / Evento | Acciones Principales |
|---|---|---|---|---|
| 1 | `wf_global_logout` | `g_logout_001` | Click en "Cerrar Sesión" | Ejecuta `plugin-auth.logout()`, limpia variables de Pinia, redirige a `/login`. |
| 2 | `wf_global_toast` | `g_toast_002` | Evento emitido por cualquier acción | Muestra notificación flotante (éxito, error, warning). |
| 3 | `wf_global_redirect_guard` | `g_guard_003` | Cambio de ruta de Router | Evalúa permisos y redirige si falta autenticación. |
| 4 | `wf_copiar_portapapeles` | `g_copy_004` | Click en botones de copia | Usa `navigator.clipboard.writeText()` y dispara toast. |
| 5 | `wf_format_fecha` | `g_date_005` | Función utilitaria global | Convierte ISO string a formato local `DD/MM/YYYY HH:mm`. |
| 6 | `wf_global_download_file` | `g_down_006` | Click en archivo adjunto | Obtiene signed URL y abre descarga en nueva pestaña. |
| 7 | `wf_global_sync_state` | `g_sync_007` | Evento de polling o recarga | Re-ejecuta fetch de colecciones activas. |
| 8 | `wf_global_open_modal` | `g_modal_008` | Botones de acción modal | Setea `modal_open = true` y bindea payload al contexto. |
| 9 | `wf_global_close_modal` | `g_modal_009` | Click en backdrop o cruz | Setea `modal_open = false` y limpia variables temporales. |
| 10 | `wf_validate_jwt_session` | `g_jwt_010` | Cada 5 minutos (Interval) | Verifica vigencia del token de sesión en cookies. |
| 11 | `wf_global_error_handler` | `g_err_011` | Catch de promesas fallidas | Loguea en consola y muestra toast amigable al usuario. |

---

## 2. Workflows Locales por Página

### 2.1 Página `/` (Home - Formulario)
- `wf_step1_next`: Valida campos de solicitante y selección de al menos 1 área; avanza a paso 2.
- `wf_step2_next`: Valida campos requeridos de los servicios desplegados; avanza a paso 3.
- `wf_step3_submit`: Ejecuta subida de archivos y backend workflow `api_crear_pedido`.

### 2.2 Página `/gestion` (Bandeja)
- `wf_gestion_onload`: Dispara `api_listar_responsables`, carga colecciones de pedidos y servicios.
- `wf_filter_change`: Actualiza arrays filtrados en memoria de Pinia al cambiar search o selects.

### 2.3 Página `/pedido/:id` (Detalle)
- `wf_detalle_onload`: Dispara `api_obtener_detalle_pedido` y popula los estados locales.
- `wf_cambiar_responsable`: Invoca `api_asignar_responsable_servicio`.
- `wf_cambiar_estado_servicio`: Invoca `api_actualizar_servicio`.
- `wf_submit_solicitar_info`: Invoca `api_solicitar_informacion`.
- `wf_submit_entrega_final`: Invoca `api_actualizar_entrega_final`.

### 2.4 Página `/usuarios` (Admin)
- `wf_usuarios_onload`: Verifica rol `admin`, ejecuta `api_admin_listar_usuarios`.
- `wf_aprobar_usuario`: Invoca `api_admin_aprobar_usuario`.
- `wf_editar_usuario`: Invoca `api_admin_editar_nombre_usuario`.

---

## 3. Evidencia de Verificación
- `[WEWEB-VERIFICADO]`: Extraído del dump completo de frontend workflows y Pinia stores.
