# 16 - Inventario Exhaustivo de Recursos y UIDs

> **Estado:** `[WEWEB-VERIFICADO]` / `[CONFIG-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Páginas de la Aplicación (10 Páginas)

| Nombre de Página | Path | UID Página | Evidencia |
|---|---|---|---|
| Home / Formulario | `/` | `50ee979b-2ff9-4235-8ea5-6ce664539886` | `[PROD-VERIFICADO]` |
| Solicitud Recibida | `/solicitud-recibida` | `7ee2bf10-5390-4e3a-b5e1-cf2489c9faee` | `[PROD-VERIFICADO]` |
| Seguimiento Público | `/seguimiento` | `99434d02-40ae-432d-8b01-ffaa107b5a8e` | `[PROD-VERIFICADO]` |
| Responder Información | `/solicitud-informacion` | `6c8fe77a-ec4f-40e1-ad26-7876a44ca70a` | `[PROD-VERIFICADO]` |
| Login | `/login` | `10cf599d-1dc8-4444-bca5-b3844fdf5ae9` | `[PROD-VERIFICADO]` |
| Solicitar Acceso | `/solicitar-acceso` | `a563fbb9-25f0-466d-a19e-f008892419db` | `[PROD-VERIFICADO]` |
| Bandeja de Gestión | `/gestion` | `a39854ef-f0ad-448f-aa1c-0e9e4a3b7080` | `[WEWEB-VERIFICADO]` |
| Detalle de Pedido | `/pedido/:id` | `48ba972e-d09f-4318-971c-3220fe4ae4ef` | `[WEWEB-VERIFICADO]` |
| Administración Usuarios | `/usuarios` | `7982e5ff-7e47-49d7-8c43-8ce8325ef01b` | `[WEWEB-VERIFICADO]` |
| Error 404 | `/404` | `40404040-4040-4040-4040-404040404040` | `[WEWEB-VERIFICADO]` |

---

## 2. Backend Workflows (17 Workflows)

| Nombre de Backend Workflow | UID Backend Workflow | Clasificación |
|---|---|---|
| `api_crear_pedido` | `9b40db24-c189-493e-afec-853b05423fcb` | ACTIVO ACTUAL |
| `api_obtener_detalle_pedido` | `a3bd5a7e-ca28-4e89-8d14-1cb8ff8fef1c` | ACTIVO ACTUAL |
| `api_listar_responsables` | `565645b9-79e5-4981-bdd4-763cf076601b` | ACTIVO ACTUAL |
| `api_actualizar_servicio` | `fa8c6c59-efd5-45cf-a734-758fae4d7705` | ACTIVO ACTUAL |
| `api_asignar_responsable_servicio` | `76b9f273-0aa7-4b71-9252-09292b234473` | ACTIVO ACTUAL |
| `api_actualizar_entrega_final` | `1a00a1fa-1f4a-436f-8012-ba781d4a0ea2` | ACTIVO ACTUAL |
| `api_solicitar_informacion` | `44ad1bdd-c7e8-45a7-9555-ed11931b30c7` | ACTIVO ACTUAL |
| `api_validar_token_solicitud_info` | `e2a48a91-4e78-4395-8854-941829e1db2a` | ACTIVO ACTUAL |
| `api_responder_solicitud_informacion` | `3f3aead2-7e66-41a4-980d-e17bbdc736b4` | ACTIVO ACTUAL |
| `api_buscar_seguimiento_publico` | `7c10d3e2-8921-4fca-91b4-2b9a71a48c3b` | ACTIVO ACTUAL |
| `api_registrar_solicitud_acceso` | `10ee0061-c651-456a-8573-ade4c43e37b0` | ACTIVO ACTUAL |
| `api_admin_listar_usuarios` | `8c4f9812-78d1-46ab-8547-19d28a7e0291` | ACTIVO ACTUAL |
| `api_admin_aprobar_usuario` | `29cf4710-38e9-4e71-8b01-ae48b71d9904` | ACTIVO ACTUAL |
| `api_admin_editar_nombre_usuario` | `377fb94b-1c66-4c93-84dc-8e02f4a77a91` | ACTIVO ACTUAL |
| `api_subir_archivo_solicitud` | `5b82da19-91a0-45bf-a773-459f2390bb8a` | ACTIVO ACTUAL |
| `bw_enviar_comunicacion_pedido` | `8e32cb71-f921-470a-a712-4091a679efb5` | ACTIVO ACTUAL |
| `bw_sincronizar_notion_pedido` | `0d19ca78-5e41-479e-b912-7789ef239841` | ACTIVO EN CONFIG |

---

## 3. Variables de Entorno del Proyecto

| Nombre Variable | Tipo | Propósito |
|---|---|---|
| `N8N_PEDIDOS_JWT_SECRET_V2` | Secret | Variable para firma HS256 de webhooks hacia n8n |
| `N8N_WEBHOOK_URL_PEDIDOS` | String | Endpoint URL de recepción de pedidos en n8n |
| `RESEND_API_KEY` | Secret | Variable para despacho de emails transaccionales |
| `NOTION_API_KEY` | Secret | Variable para integración con base de Notion |
| `NOTION_DATABASE_ID` | String | ID de base de datos de Notion |
| `APP_PUBLIC_URL` | String | Base URL pública del frontend |

---

## 4. Evidencia de Verificación
- `[WEWEB-VERIFICADO]`: UIDs y variables recolectados de los stores oficiales de WeWeb.
