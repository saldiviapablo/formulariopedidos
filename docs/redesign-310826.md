# Redesign funcional 310826

## Estado y alcance

Esta especificación inicia el rediseño funcional basado en `Form modificaciones 310826.pdf`.

- Rama de trabajo: `redesign-310826`.
- Snapshot estable previo: commit `50d6de10d23b83419bd60764820506ee5db90e58`.
- Tag preservado: `pre-redesign-2026-09-01`.
- Estado en WeWeb: no implementado.

El objetivo es simplificar fuertemente la experiencia del solicitante sin perder las capacidades técnicas existentes: persistencia atómica, numeración correlativa, idempotencia, archivos privados, gestión independiente y compatibilidad histórica.

## Áreas públicas previstas

La solicitud pública mostrará cuatro áreas principales:

1. Diseño gráfico.
2. Cobertura de eventos.
3. Gacetilla.
4. Publicaciones en redes sociales.

El solicitante podrá seleccionar una, varias o todas. La multiselección de áreas continúa siendo una capacidad obligatoria.

## Diseño gráfico

Diseño gráfico se simplificará visualmente a cuatro piezas:

- Flyers.
- Invitación.
- Certificado.
- Otros.

Se conservará la selección múltiple de piezas. Si una solicitud incluye las cuatro opciones, generará cuatro unidades de trabajo y cuatro PED independientes.

Las piezas históricas de Diseño gráfico no se eliminarán de la base en esta etapa. Antes de implementar se definirá cuáles pasarán a estar inactivas para nuevas solicitudes, preservando siempre los registros históricos.

## Cobertura de eventos

Cobertura de eventos reemplazará en la experiencia pública a Fotografía y Audiovisual. Esas áreas no deberán seguir apareciendo al solicitante como opciones independientes.

El flujo contempla conceptualmente:

- Descripción del evento.
- Autoridades asistentes o quiénes participan.
- Lugar.
- Fecha.
- Hora.

Los campos, obligatoriedad, adjuntos y reglas de validación se cerrarán antes de implementar.

## Gacetilla

Gacetilla reemplazará a Prensa / Difusión como opción pública visible.

El flujo contempla conceptualmente:

- Archivo.
- Contacto para notas.
- Nombre y apellido.
- WhatsApp.

Los campos exactos, formatos y reglas se cerrarán antes de implementar.

## Publicaciones en redes sociales

Publicaciones en redes sociales permanecerá como área visible, con un formulario considerablemente más simple.

La referencia funcional contempla:

- Archivo y/o enlace del copy.
- Información necesaria para la publicación.
- Recordatorios operativos.
- Tiempos de publicación.

No se implementará hasta definir campos, obligatoriedad, formatos y validaciones exactas.

## Datos generales del solicitante

Se mantienen:

- Nombre y apellido.
- Teléfono.
- Correo electrónico.
- Área o dependencia solicitante.

El solicitante no deberá autenticarse. El formulario continuará siendo público.

## Regla definitiva de tickets

La regla funcional y técnica continúa siendo:

> Un servicio o pieza solicitada genera un ticket/PED independiente.

No se volverá a un PED por envío ni a un PED por área.

Cada PED mantendrá independientemente:

- Estado.
- Responsable.
- Observaciones.
- Archivos.
- Trazabilidad.

La numeración continuará con el formato `PED-AAAA-NNNNNN`. Un mismo envío podrá contener múltiples áreas y múltiples servicios o piezas dentro de cada área.

## Panel interno

El modelo conceptual permanece:

> Una fila = un PED = un trabajo.

Cada ticket podrá abrirse, asignarse, cambiar de estado, registrar observaciones y gestionar archivos de forma independiente.

El panel interno no se rediseñará hasta cerrar el nuevo flujo público.

## Notificaciones futuras con Resend

Resend es el proveedor previsto para notificaciones por email. No está configurado todavía y no se almacenan claves en este repositorio.

Se prevén notificaciones al correo del solicitante cuando el ticket:

- Ingresa.
- Pasa a En revisión.
- Pasa a En proceso.
- Pasa a Esperando información.
- Pasa a Correcciones.
- Pasa a Finalizado.
- Pasa a Cancelado.

`Asignado` podrá permanecer como evento interno salvo decisión posterior. La futura API key deberá guardarse como variable segura del Backend.

## Producto final

Cuando un ticket pase a `Finalizado`, el solicitante deberá poder acceder al producto terminado.

La preferencia técnica es:

- Archivo en Storage Private.
- Acceso seguro y temporal.
- Sin exponer `storage_path`.
- Sin adjuntar archivos pesados directamente al email.

La autorización, duración y mecanismo definitivo se cerrarán antes de implementar.

## Seguimiento público futuro

Se prevé una página pública `/seguimiento`, sin login.

La consulta exigirá conjuntamente:

- Número de ticket/PED.
- Correo electrónico utilizado en la solicitud.

El Backend deberá validar ambos valores antes de devolver información. No será suficiente conocer únicamente el PED secuencial.

Información pública prevista:

- PED.
- Servicio o pieza.
- Estado actual.
- Fecha de ingreso.
- Última actualización.

No se devolverán:

- Observaciones internas.
- Responsable interno, salvo decisión posterior.
- UUID.
- `storage_path`.
- Información de otros pedidos.

Para entregar el producto final se evaluará una protección adicional mediante token seguro.

## Decisiones pendientes antes de implementar

- Campos exactos y obligatoriedad de Cobertura de eventos.
- Campos exactos y obligatoriedad de Gacetilla.
- Simplificación exacta de Publicaciones en redes sociales.
- Tratamiento público de los servicios históricos de Diseño gráfico.
- Reglas de adjuntos y validaciones de cada flujo.
- Mecanismo seguro de entrega del producto final.
- Contrato y protección del endpoint de seguimiento.

Hasta cerrar estas decisiones no debe modificarse WeWeb para implementar el rediseño.
