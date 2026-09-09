# 21. Inventario Técnico Completo del Proyecto WeWeb

Este documento recopila todos los identificadores únicos (UIDs), metadatos, tablas y servicios del proyecto WeWeb auditado.

---

## 1. Metadatos del Proyecto WeWeb

- **Project UID**: `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`
- **Nombre de Proyecto**: PEDIDOS — Secretaría de Medios
- **Entornos**: Editor / Production

---

## 2. Inventario de Páginas (10)

1. `79abd033-367a-45d1-ae9b-e20cf92a6636`: `/home` (Home Institucional)
2. `753b981e-f102-4df7-bcf7-9be83f324404`: `/login` (Iniciar Sesión)
3. `61edfb04-208f-4856-884e-d6ae78830b59`: `/solicitar-acceso` (Registro Público Interno)
4. `32b9a62c-33d9-4860-a15f-fa351e45aa69`: `/acceso-pendiente` (Pantalla de Espera)
5. `72bfb8d9-76d8-45b5-a049-39f525c49d7e`: `/nueva-solicitud` (Wizard de Solicitudes)
6. `1d98f231-a693-4a43-85b8-87ae55b6d43e`: `/seguimiento` (Consulta de Estado)
7. `a1fa0234-aa68-4758-b554-4c7ddea469c5`: `/completar-solicitud` (Respuesta de Información)
8. `027a6d44-7b5b-4160-98ae-6bad99e24e57`: `/gestion` (Bandeja Operativa Unificada)
9. `f742dcb9-9662-44e8-b9d4-c52505827d23`: `/gestion/pedido/:pedidoid/detalle` (Detalle de Pedido)
10. `16b96f5c-76ab-41ae-bfca-902594e5d5f1`: `/gestion/usuarios` (Administración de Usuarios)

---

## 3. Inventario de Backend Workflows (10)

1. `819b8329-94b8-4a7d-929f-ce7a477c9161`: `api_crear_pedido_completo`
2. `ec935c4c-4c71-4d1a-9ea5-fecc8b908312`: `api_actualizar_servicio`
3. `44ad1bdd-c7e8-45a7-9555-ed11931b30c7`: `api_solicitar_informacion`
4. `5ebf567a-1a5b-4e8d-916d-51c97073b865`: `bw_enviar_comunicacion_pedido`
5. `10ee0061-c651-456a-8573-ade4c43e37b0`: `api_registrar_solicitud_acceso`
6. `234ef4a9-f913-40c6-9292-ad02be4313a3`: `api_admin_gestionar_acceso_usuario`
7. `377fb94b-1c66-4c93-84dc-8e02f4a77a91`: `api_admin_editar_nombre_usuario`
8. `565645b9-79e5-4981-bdd4-763cf076601b`: `api_listar_responsables`
9. `c7fcc2c1-95a8-488a-9128-c83bc82d5af3`: `api_consultar_estado_pedido`
10. `9db5d921-2e6b-4e8c-a9df-6d0dfc249a56`: `api_responder_solicitud_informacion`

---

## 4. Inventario de Tablas y Vistas WeWeb

- **Tablas (10)**: `secuencias`, `areas`, `tipos_servicio`, `usuarios_acceso`, `pedidos`, `servicios_solicitados`, `archivos`, `comunicaciones_pedido`, `solicitudes_informacion`, `auth.users`.
- **Vistas (9)**: `vw_servicios_gestion`, `vw_pedido_detalle_interno`, `vw_usuarios_acceso_admin`, `vw_mi_acceso`, `vw_areas_activas`, `vw_tipos_servicio_activos`, `vw_archivos_pedido_interno`, `vw_comunicaciones_pedido_interno`, `vw_solicitudes_informacion_interno`.
