# Backend y seguridad

## Workflow interno de comunicación

`bw_enviar_comunicacion_pedido` recibe referencias de pedido y servicio, tipo, contenido y, opcionalmente, el operador interno. Valida las referencias y el tipo permitido, obtiene el destinatario desde el pedido, registra un intento y utiliza la conexión nativa de correo configurada en Editor.

En éxito registra el resultado y la marca temporal. En fallo registra un error genérico y devuelve un resultado de entrega fallida; no propaga credenciales ni detalles del proveedor.

## Endpoints

- `api_crear_pedido_completo`: sigue creando un pedido por servicio o pieza de forma atómica e idempotente. Tras cada creación intenta la notificación de pedido ingresado. Un fallo de comunicación no revierte el pedido.
- `api_actualizar_servicio`: permanece restringido a los roles internos. Notifica únicamente transiciones relevantes: En revisión, En proceso, Finalizado y Cancelado. No reenvía una notificación si no hay cambio de estado.
- `api_solicitar_informacion`: permanece restringido a los roles internos. Envía una solicitud independiente sin cambiar automáticamente el estado del servicio.

## View y autorización

`vw_comunicaciones_pedido_interno` sirve el historial al detalle interno. Requiere usuario autenticado con rol `equipo_interno` o `admin`, mediante condición OR. Las vistas y endpoints públicos no reciben ni exponen el historial interno.

Las comunicaciones utilizan el destinatario almacenado en el pedido y no aceptan un destinatario controlado por el cliente. Esta implementación no modifica la seguridad de Storage, Auth ni las reglas de acceso de los recursos existentes.
