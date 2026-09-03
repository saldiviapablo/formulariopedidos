# Tabla `comunicaciones_pedido`

Tabla de Project Data para la trazabilidad interna de notificaciones transaccionales.

| Columna | Tipo | Requerida | Uso |
| --- | --- | --- | --- |
| `pedido` | Link to Another Record | Sí | Pedido relacionado. |
| `servicio` | Link to Another Record | Sí | Servicio relacionado. |
| `tipo` | Text | Sí | Clase de comunicación. |
| `estado` | Text | No | Estado relacionado, cuando corresponde. |
| `destinatario` | Text | Sí | Destinatario resuelto por backend. |
| `asunto` | Text | Sí | Asunto de la comunicación. |
| `mensaje` | Text | Sí | Cuerpo registrado de la comunicación. |
| `resultado` | Text | Sí | Resultado de negocio del intento. |
| `provider_message_id` | Text | No | Identificador opcional del proveedor. |
| `error` | Text | No | Error saneado, si ocurrió. |
| `enviado_at` | Date/Time | No | Momento de envío exitoso. |
| `enviado_por` | User | No | Operador interno, cuando aplica. |

Los tipos admitidos son `pedido_ingresado`, `cambio_estado`, `informacion_faltante`, `finalizado` y `cancelado`. El workflow inicializa el resultado como pendiente y luego registra envío exitoso o fallo sin exponer datos sensibles en la interfaz.
