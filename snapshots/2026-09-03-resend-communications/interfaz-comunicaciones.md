# Interfaz interna de comunicaciones

El detalle interno de cada servicio incorpora un bloque de comunicaciones debajo de los archivos asociados.

## Solicitar información faltante

- Presenta un campo de texto con una instrucción clara para el operador.
- Conserva el borrador y muestra un error cuando el envío no es posible.
- Ejecuta el endpoint interno correspondiente y refresca el historial al finalizar.
- No modifica el estado del servicio por sí mismo.

## Historial

El historial presenta de forma legible el tipo, estado relacionado cuando existe, asunto, resultado y detalles operativos disponibles. No muestra UUID, secretos ni datos internos innecesarios.

Al guardar cambios de estado, la interfaz informa por separado si el cambio fue guardado y si la notificación se entregó. Así se mantiene visible que un fallo de email no revierte el cambio de negocio.
