# 20. Matriz de Trazabilidad de Requisitos

Este documento traza cada necesidad operativa institucional con los artefactos de WeWeb y los componentes del código en WordPress.

---

## 1. Matriz de Trazabilidad

| ID Requisito | Requisito Funcional | Implementación WeWeb | Componente WordPress | Verificación QA |
| :--- | :--- | :--- | :--- | :--- |
| **REQ-01** | Asistente de Solicitud en 3 Pasos | Página `/nueva-solicitud` (`72bfb8d9...`) | `templates/public/nueva-solicitud.php` + `wizard-solicitud.js` | QA-01, QA-03 |
| **REQ-02** | Regla de Oro: 1 Servicio = 1 PED | `api_crear_pedido_completo` (`819b8329...`) | `PublicPedidosController::create_solicitud` | QA-02, QA-19 |
| **REQ-03** | Numeración Consecutiva `PED-YYYY-NNNNNN` | Tabla `secuencias` + lógica SQL | `SequenceService.php` + `wp_pedidos_secuencias` | QA-02, QA-19 |
| **REQ-04** | Seguimiento Público por Token / Email | Página `/seguimiento` (`1d98f231...`) | `templates/public/seguimiento.php` + `PublicPedidosController` | QA-04, QA-05 |
| **REQ-05** | Solicitud y Respuesta de Info con Token | `/completar-solicitud` + `api_solicitar_informacion` | `SolicitudesInfoController.php` + `completar-solicitud.php` | QA-07, QA-08 |
| **REQ-06** | Identidad `nombre_usuario` (2 a 30 chars) | `usuarios_acceso` + workflows de acceso | `AuthService.php` + `wp_pedidos_usuarios_acceso` | QA-09, QA-10 |
| **REQ-07** | Selector Responsable por `nombre_usuario` | `api_listar_responsables` (`565645b9...`) | `InternalGestionController::get_responsables` | QA-15 |
| **REQ-08** | Bandeja de Gestión con Filtros | Página `/gestion` (`027a6d44...`) | `templates/internal/gestion-bandeja.php` + `gestion-app.js` | QA-15, QA-20 |
| **REQ-09** | Comunicaciones vía n8n / Gmail | `bw_enviar_comunicacion_pedido` (`5ebf567a...`) | `N8nWebhookService.php` + `wp_pedidos_comunicaciones` | QA-16 |
| **REQ-10** | Almacenamiento Privado de Adjuntos | WeWeb Storage Privado | `StorageService.php` + directorio protegido | QA-17, QA-18 |
