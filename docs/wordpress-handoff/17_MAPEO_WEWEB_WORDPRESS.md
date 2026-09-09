# 17. Mapeo Integral WeWeb a WordPress

Este documento establece la correspondencia exacta componente por componente entre el proyecto WeWeb (`5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`) y la implementación en WordPress.org.

---

## 1. Mapeo de Páginas y Rutas

| Pantalla WeWeb | Ruta WeWeb | UID WeWeb | Componente / Archivo WordPress |
| :--- | :--- | :--- | :--- |
| **Home Institucional** | `/` (`/home`) | `79abd033-367a-45d1-ae9b-e20cf92a6636` | `templates/public/home.php` |
| **Login** | `/login` | `753b981e-f102-4df7-bcf7-9be83f324404` | `wp-login.php` o template personalizado `templates/auth/login.php` |
| **Solicitar Acceso** | `/solicitar-acceso` | `61edfb04-208f-4856-884e-d6ae78830b59` | `templates/auth/solicitar-acceso.php` |
| **Acceso Pendiente** | `/acceso-pendiente` | `32b9a62c-33d9-4860-a15f-fa351e45aa69` | `templates/auth/acceso-pendiente.php` |
| **Nueva Solicitud** | `/nueva-solicitud` | `72bfb8d9-76d8-45b5-a049-39f525c49d7e` | `templates/public/nueva-solicitud.php` + `assets/js/wizard-solicitud.js` |
| **Seguimiento** | `/seguimiento` | `1d98f231-a693-4a43-85b8-87ae55b6d43e` | `templates/public/seguimiento.php` + `assets/js/seguimiento.js` |
| **Completar Solicitud** | `/completar-solicitud` | `a1fa0234-aa68-4758-b554-4c7ddea469c5` | `templates/public/completar-solicitud.php` |
| **Bandeja de Gestión** | `/gestion` | `027a6d44-7b5b-4160-98ae-6bad99e24e57` | `templates/internal/gestion-bandeja.php` + `assets/js/gestion-app.js` |
| **Detalle de Pedido** | `/gestion/pedido/:id/detalle` | `f742dcb9-9662-44e8-b9d4-c52505827d23` | `templates/internal/pedido-detalle.php` |
| **Usuarios Admin** | `/gestion/usuarios` | `16b96f5c-76ab-41ae-bfca-902594e5d5f1` | `templates/internal/admin-usuarios.php` |

---

## 2. Mapeo de Base de Datos

| Tabla WeWeb Tables | Tipo WeWeb | Tabla Destino WordPress |
| :--- | :--- | :--- |
| `secuencias` | Tabla PostgreSQL | `{$wpdb->prefix}pedidos_secuencias` |
| `areas` | Tabla PostgreSQL | `{$wpdb->prefix}pedidos_areas` |
| `tipos_servicio` | Tabla PostgreSQL | `{$wpdb->prefix}pedidos_tipos_servicio` |
| `usuarios_acceso` | Tabla PostgreSQL | `{$wpdb->prefix}pedidos_usuarios_acceso` |
| `pedidos` | Tabla PostgreSQL | `{$wpdb->prefix}pedidos` |
| `servicios_solicitados` | Tabla PostgreSQL | `{$wpdb->prefix}pedidos_servicios_solicitados` |
| `archivos` | Tabla PostgreSQL | `{$wpdb->prefix}pedidos_archivos` |
| `comunicaciones_pedido` | Tabla PostgreSQL | `{$wpdb->prefix}pedidos_comunicaciones` |
| `solicitudes_informacion` | Tabla PostgreSQL | `{$wpdb->prefix}pedidos_solicitudes_info` |
| `auth.users` | Supabase Auth Table | `{$wpdb->prefix}users` (WordPress Core Users) |

---

## 3. Mapeo de Backend Workflows a Controladores REST

| Backend Workflow WeWeb | UID WeWeb | Endpoint REST WordPress |
| :--- | :--- | :--- |
| `api_crear_pedido_completo` | `819b8329-94b8-4a7d-929f-ce7a477c9161` | `POST /wp-json/pedidos/v1/public/solicitudes` |
| `api_actualizar_servicio` | `ec935c4c-4c71-4d1a-9ea5-fecc8b908312` | `PATCH /wp-json/pedidos/v1/gestion/pedidos/{id}` |
| `api_solicitar_informacion` | `44ad1bdd-c7e8-45a7-9555-ed11931b30c7` | `POST /wp-json/pedidos/v1/gestion/pedidos/{id}/solicitar-info` |
| `bw_enviar_comunicacion_pedido`| `5ebf567a-1a5b-4e8d-916d-51c97073b865` | `POST /wp-json/pedidos/v1/gestion/pedidos/{id}/comunicar` |
| `api_registrar_solicitud_acceso`| `10ee0061-c651-456a-8573-ade4c43e37b0` | `POST /wp-json/pedidos/v1/auth/solicitar-acceso` |
| `api_admin_gestionar_acceso_usuario`| `234ef4a9-f913-40c6-9292-ad02be4313a3` | `POST /wp-json/pedidos/v1/admin/usuarios/{id}/gestionar` |
| `api_admin_editar_nombre_usuario`| `377fb94b-1c66-4c93-84dc-8e02f4a77a91` | `PATCH /wp-json/pedidos/v1/admin/usuarios/{id}/nombre-usuario` |
| `api_listar_responsables` | `565645b9-79e5-4981-bdd4-763cf076601b` | `GET /wp-json/pedidos/v1/gestion/responsables` |
| `api_consultar_estado_pedido` | `c7fcc2c1-95a8-488a-9128-c83bc82d5af3` | `POST /wp-json/pedidos/v1/public/seguimiento` |
| `api_responder_solicitud_informacion`| `9db5d921-2e6b-4e8c-a9df-6d0dfc249a56` | `POST /wp-json/pedidos/v1/public/completar-info` |
