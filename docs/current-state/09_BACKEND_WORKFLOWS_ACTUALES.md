# 09 - Backend Workflows y Endpoints de API

> **Estado:** `[WEWEB-VERIFICADO]` / `[CONFIG-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Clasificación Exhaustiva de los 17 Backend Workflows

| # | Nombre de Backend Workflow | UID Backend Workflow | Clasificación | Parámetros Principales | Operación / Side Effects |
|---|---|---|---|---|---|
| 1 | `api_crear_pedido` | `9b40db24-c189-493e-afec-853b05423fcb` | **ACTIVO ACTUAL** | `nombre_apellido`, `telefono`, `correo`, `area_solicitante`, `tipos_pedido`, `servicios`, `archivos` | Reserva secuencia atómica, inserta `pedidos`, inserta `servicios_solicitados`, vincula `archivos`, dispara `bw_enviar_comunicacion_pedido`. |
| 2 | `api_obtener_detalle_pedido` | `a3bd5a7e-ca28-4e89-8d14-1cb8ff8fef1c` | **ACTIVO ACTUAL** | `pedido_id` (UUID) | Query relacional completa de pedido, servicios, solicitudes de info, archivos y comunicaciones. |
| 3 | `api_listar_responsables` | `565645b9-79e5-4981-bdd4-763cf076601b` | **ACTIVO ACTUAL** | Ninguno | Query `auth.users` + `usuarios_acceso` (solo aprobados). Retorna `value` (UUID), `label` (`nombre_usuario`). |
| 4 | `api_actualizar_servicio` | `fa8c6c59-efd5-45cf-a734-758fae4d7705` | **ACTIVO ACTUAL** | `servicio_id`, `estado`, `observaciones_internas`, `motivo_cancelacion` | Update en `servicios_solicitados`. Si cambia estado, dispara notificación por email. |
| 5 | `api_asignar_responsable_servicio` | `76b9f273-0aa7-4b71-9252-09292b234473` | **ACTIVO ACTUAL** | `servicio_id`, `responsable_id` | Actualiza `responsable` en `servicios_solicitados` y setea `estado = 'Asignado'` si estaba `Nuevo`. |
| 6 | `api_actualizar_entrega_final` | `1a00a1fa-1f4a-436f-8012-ba781d4a0ea2` | **ACTIVO ACTUAL** | `servicio_id`, `producto_final_url`, `producto_final_nota` | Update en `servicios_solicitados`: `estado = 'Finalizado'`, link HTTPS y nota. Dispara email de finalización. |
| 7 | `api_solicitar_informacion` | `44ad1bdd-c7e8-45a7-9555-ed11931b30c7` | **ACTIVO ACTUAL** | `pedido_id`, `servicio_id`, `mensaje_solicitado` | Inserta en `solicitudes_informacion` con token SHA256 (15 días). Dispara email `informacion_faltante`. **No cambia estado de servicio.** |
| 8 | `api_validar_token_solicitud_info` | `e2a48a91-4e78-4395-8854-941829e1db2a` | **ACTIVO ACTUAL** | `token` | Calcula SHA256, valida `estado == 'pendiente'` y `now() <= expires_at`. |
| 9 | `api_responder_solicitud_informacion` | `3f3aead2-7e66-41a4-980d-e17bbdc736b4` | **ACTIVO ACTUAL** | `token`, `respuesta_texto`, `archivos` | Update en `solicitudes_informacion`: `estado = 'respondida'`, guarda texto y asocia archivos. |
| 10 | `api_buscar_seguimiento_publico` | `7c10d3e2-8921-4fca-91b4-2b9a71a48c3b` | **ACTIVO ACTUAL** | `criterio` (PED o Token) | Retorna estado general, fecha, tipos de servicios y estados públicos (sin datos confidenciales). |
| 11 | `api_registrar_solicitud_acceso` | `10ee0061-c651-456a-8573-ade4c43e37b0` | **ACTIVO ACTUAL** | `nombre`, `apellido`, `nombre_usuario`, `email`, `password` | Crea usuario en `auth.users`, normaliza `nombre_usuario = lower(trim())`, inserta en `usuarios_acceso` con `estado_acceso = 'pendiente'`. |
| 12 | `api_admin_listar_usuarios` | `8c4f9812-78d1-46ab-8547-19d28a7e0291` | **ACTIVO ACTUAL** | Ninguno | Query `usuarios_acceso` + `auth.users` con roles y estado de aprobación. |
| 13 | `api_admin_aprobar_usuario` | `29cf4710-38e9-4e71-8b01-ae48b71d9904` | **ACTIVO ACTUAL** | `usuario_id`, `rol` (`equipo_interno` / `admin`) | Update `usuarios_acceso`: `estado_acceso = 'aprobado'`, setea `aprobado_at`, `aprobado_por`. Asigna rol en WeWeb Auth. |
| 14 | `api_admin_editar_nombre_usuario` | `377fb94b-1c66-4c93-84dc-8e02f4a77a91` | **ACTIVO ACTUAL** | `usuario_id`, `nombre_usuario` | Normaliza a minúsculas, valida regex `^[a-z0-9._-]{2,30}$`, valida unicidad y actualiza `usuarios_acceso`. |
| 15 | `api_subir_archivo_solicitud` | `5b82da19-91a0-45bf-a773-459f2390bb8a` | **ACTIVO ACTUAL** | Archivo binario + metadatos | Almacena archivo en WeWeb Private Storage y retorna path y token de acceso. |
| 16 | `bw_enviar_comunicacion_pedido` | `8e32cb71-f921-470a-a712-4091a679efb5` | **ACTIVO ACTUAL** | `pedido_id`, `tipo`, `destinatario`, `asunto`, `payload` | Ejecuta n8n trigger (con JWT efímero) si `tipo == 'pedido_ingresado'`, o envía email vía Resend para otros tipos. Registra en `comunicaciones_pedido`. |
| 17 | `bw_sincronizar_notion_pedido` | `0d19ca78-5e41-479e-b912-7789ef239841` | **ACTIVO EN CONFIG** | `pedido_id` | Sincroniza pedido y servicios con la base de datos de Notion de la Secretaría (`[NO-VERIFICADO]` ejecución en producción). |

---

## 2. Evidencia de Verificación
- `[WEWEB-VERIFICADO]`: Todos los 17 workflows fueron inspeccionados con sus triggers, variables y acciones en los volcados oficiales de WeWeb.
