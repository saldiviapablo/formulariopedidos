# Rediseño funcional PEDIDOS — 31/08/2026

## Estado del documento

Especificación funcional cerrada el 2026-09-01 e implementada en el entorno Editor de WeWeb el 2026-09-02.

La rama de trabajo es `redesign-310826`. El core público fue implementado sin modificar Production ni migrar datos históricos.

## Estado de implementación — 2026-09-02

Implementado en Editor:

- Rediseño público core de tres pasos.
- Cuatro áreas públicas: Diseño gráfico, Cobertura de eventos, Gacetilla y Publicaciones en redes sociales.
- Formularios definitivos, multiselección y creación de un PED independiente por servicio o pieza.
- Adjuntos automáticos aislados por servicio en WeWeb Storage Private.
- Revisión agrupada, resultado por ticket y progreso de tres pasos.
- Compatibilidad histórica conservada, incluido el PED multi-servicio previo.

Pendiente de una fase posterior:

- Producto final, `producto_final_url` y `producto_final_nota`.
- Resend, emails automáticos y solicitar información faltante.
- `comunicaciones_pedido`.
- `/seguimiento`.
- Publicación en Production.

## Principios generales

- WeWeb continúa siendo la plataforma full-stack principal.
- El formulario público permanece accesible sin autenticación.
- Una solicitud puede incluir varias áreas y varias piezas o servicios.
- Cada pieza o servicio seleccionado genera un PED independiente.
- No se eliminan, renombran ni migran registros históricos como parte de este rediseño.
- No se transforman pedidos históricos de Fotografía o Audiovisual en Cobertura de eventos.
- Los adjuntos enviados por el solicitante continúan previstos en WeWeb Storage Private.
- El producto final se entrega mediante una URL HTTPS externa y no se almacena en WeWeb Storage.
- Resend será el proveedor futuro de correo, pero su configuración no forma parte de esta especificación documental.

## Flujo público

El flujo público se simplifica a tres pasos:

1. Datos del solicitante y selección de áreas.
2. Formularios correspondientes únicamente a las áreas y piezas seleccionadas.
3. Revisión final agrupada antes del envío.

Después de una creación exitosa, el resultado debe mostrar un bloque o tarjeta independiente por cada PED generado.

### Datos obligatorios del solicitante

- Nombre y apellido.
- Teléfono.
- Correo electrónico.
- Área o dependencia.

Estos datos son generales para la solicitud y no deben duplicarse dentro de cada servicio.

## Áreas públicas

El formulario público muestra exactamente estas cuatro áreas:

1. Diseño gráfico.
2. Cobertura de eventos.
3. Gacetilla.
4. Publicaciones en redes sociales.

Se conserva la selección múltiple de áreas.

### Compatibilidad con el catálogo histórico

- Fotografía y Audiovisual dejan de mostrarse como áreas públicas independientes y son reemplazadas funcionalmente por Cobertura de eventos.
- Prensa / Difusión deja de mostrarse como área pública y es reemplazada funcionalmente por Gacetilla.
- Los registros históricos no se eliminan ni se reconvierten.
- La implementación futura deberá resolver esta presentación sin romper relaciones ni trazabilidad existentes.

## Diseño gráfico

El área permite seleccionar una o varias piezas. Las únicas piezas públicas son:

- Flyers.
- Invitación.
- Certificado.
- Otros.

Cada pieza seleccionada genera un PED independiente y conserva estado, responsable, observaciones, archivos, trazabilidad y producto final propios.

### Flyers

Campos:

- Fecha de la actividad o de la pieza: obligatoria.
- Título: obligatorio.
- Especificaciones o detalle: obligatorio.
- Logos o imágenes de referencia: adjunto opcional.
- Enlace de referencia: opcional.

Validación de fecha:

- Zona horaria: Ushuaia.
- La fecha debe ser posterior al día actual.

### Invitación

Campos:

- Fecha de la actividad: obligatoria.
- Especificaciones o detalle: obligatorio.
- Archivo de referencia: opcional.

Validación de fecha:

- Zona horaria: Ushuaia.
- La fecha debe ser igual o posterior al día actual.

Los campos públicos anteriores que no figuran en esta lista se eliminan del formulario rediseñado. Invitación digital e invitación impresa pueden conservarse históricamente en el catálogo, pero la experiencia pública presenta una única pieza llamada Invitación.

### Certificado

Campos:

- Especificaciones o detalle: obligatorio.
- Archivo de referencia: opcional.

Los campos públicos específicos anteriores que no figuran en esta lista se eliminan del formulario rediseñado.

### Otros

Campos:

- Especificaciones o detalle: obligatorio.
- Archivo de referencia: opcional.

Es una solicitud libre y simple. No posee selector de subtipo.

## Cobertura de eventos

Cobertura de eventos reemplaza en la experiencia pública a las áreas separadas de Fotografía y Audiovisual.

Campos:

- Descripción de la cobertura: obligatoria.
- ¿Participan autoridades?: selección Sí/No obligatoria.
- Autoridades participantes: obligatorio solamente cuando la respuesta anterior es Sí.
- Fecha: obligatoria.
- Hora de inicio: obligatoria.
- Hora de finalización: opcional.
- Lugar: obligatorio.
- Ciudad: obligatoria.

Validaciones:

- Zona horaria: Ushuaia.
- La fecha debe ser igual o posterior al día actual.
- Las horas utilizan formato `HH:mm`.
- Si se informa una hora de finalización, debe ser posterior a la hora de inicio.

## Gacetilla

Gacetilla reemplaza en la experiencia pública a Prensa / Difusión.

Campos:

- Archivo base o material para la gacetilla: opcional.
- Aclaración: opcional.
- Nombre del contacto: obligatorio.
- WhatsApp del contacto: obligatorio.
- Información adicional: opcional.

El archivo es explícitamente opcional; su ausencia no bloquea el envío.

## Publicaciones en redes sociales

Se elimina el selector de formato Foto / Placa / Reel. El pedido describe el contenido a publicar sin elegir ese subtipo.

La interfaz debe comunicar estas pautas:

- Incluir el texto completo que se desea publicar.
- Responder con claridad qué sucede, cómo, cuándo y dónde.
- Utilizar textos simples y breves.
- Se recomienda una extensión aproximada de 300 a 500 caracteres.
- El contenido recibido después de las 18:00 puede programarse para el siguiente día hábil o según disponibilidad del equipo.

Campos:

- Fecha de publicación: obligatoria.
- Copy o texto completo: obligatorio.
- Enlace de referencia: opcional.
- Archivo: opcional.

Validación de fecha:

- Zona horaria: Ushuaia.
- La fecha debe ser igual o posterior al día actual.

## Regla de tickets

La unidad operativa es el servicio o la pieza:

`1 servicio o pieza = 1 PED`

Aunque el usuario complete una única experiencia de solicitud con selección múltiple, el backend debe generar un PED independiente por cada servicio o pieza. Cada PED tiene de manera independiente:

- estado;
- responsable;
- observaciones internas;
- archivos y referencias;
- trazabilidad;
- producto final.

La revisión final y el resultado deben agrupar visualmente los PED originados en una misma carga sin fusionar su gestión.

## Estados y comunicaciones por correo

Resend será el proveedor futuro. No se configura en esta etapa.

Los únicos estados que generan correo al solicitante son:

- Nuevo / Ingresado.
- En revisión.
- En proceso.
- Finalizado.
- Cancelado.

Los demás estados son internos y no generan correo. En particular, Asignado es un estado o evento interno.

### Solicitar información faltante

El panel interno incorporará una acción independiente llamada `Solicitar información faltante`.

- No cambia automáticamente el estado del PED.
- El operador escribe el mensaje que se enviará.
- El correo se enviará mediante Resend.
- El estado actual se conserva, salvo que el operador lo cambie manualmente por otra acción.
- La acción puede repetirse cuantas veces sea necesario.
- Cada envío debe quedar registrado para trazabilidad.

## Producto final

El producto final no se carga en WeWeb Storage.

El equipo interno informa una URL HTTPS externa, por ejemplo de Drive, Dropbox u otro proveedor, y puede agregar una nota opcional. Se prevén estos campos en el servicio o ticket:

- `producto_final_url`.
- `producto_final_nota`.

Ambos son de uso interno y no forman parte de los datos originales del solicitante.

Al pasar un PED a Finalizado, el correo debe incluir el enlace externo y, cuando exista, la nota. La disponibilidad y seguridad del archivo dependen de los permisos configurados en el proveedor externo.

## Registro futuro de comunicaciones

Se propone crear en una fase futura la tabla `comunicaciones_pedido`. Esta especificación no autoriza su creación.

Campos previstos:

- pedido;
- servicio;
- tipo de comunicación;
- estado relacionado, opcional;
- destinatario;
- asunto;
- resultado;
- identificador del mensaje del proveedor, opcional;
- error, opcional;
- fecha y hora.

Tipos de comunicación previstos:

- `pedido_ingresado`;
- `cambio_estado`;
- `informacion_faltante`;
- `finalizado`;
- `cancelado`.

## Seguimiento público futuro

Se prevé una página pública `/seguimiento` con consulta mediante:

- número PED;
- correo electrónico del solicitante.

El PED por sí solo no es suficiente para consultar información.

La respuesta puede mostrar exclusivamente:

- número PED;
- pieza o servicio;
- estado;
- fecha de creación;
- fecha de última actualización.

No debe exponer:

- UUID internos;
- responsable interno;
- observaciones internas;
- rutas de Storage;
- otros pedidos del mismo solicitante.

La exposición del enlace al producto final en esta pantalla queda como una decisión de implementación posterior.

## Alcance técnico preservado

- Se reutiliza la arquitectura actual de WeWeb siempre que sea compatible con esta especificación.
- Los adjuntos originales del solicitante continúan utilizando Storage Private y acceso controlado.
- El producto final utiliza exclusivamente una URL HTTPS externa.
- Se conserva la selección múltiple y la creación independiente de PED por servicio o pieza.
- No se borran ni renumeran pedidos.
- No se alteran datos históricos ni catálogos históricos de forma destructiva.
- No se realiza una migración automática de Fotografía o Audiovisual hacia Cobertura de eventos.
- No se implementan en esta etapa Resend, la tabla de comunicaciones, `/seguimiento` ni cambios en WeWeb.

## Cierre

Este documento cierra la definición funcional del rediseño `310826`. La implementación deberá ejecutarse en fases posteriores, con autorización específica y validación separada para Editor y Production.
