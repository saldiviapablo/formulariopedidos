# Decisiones

## 2026-09-01 — Especificación funcional redesign 310826 cerrada

Todavía no implementada en WeWeb.

- El formulario público solicita nombre y apellido, teléfono, correo electrónico y área o dependencia, sin autenticación.
- Las áreas públicas quedan limitadas a Diseño gráfico, Cobertura de eventos, Gacetilla y Publicaciones en redes sociales, con selección múltiple.
- Fotografía y Audiovisual se reemplazan únicamente en la experiencia pública por Cobertura de eventos; Prensa / Difusión se reemplaza por Gacetilla. No se borran ni transforman datos históricos.
- Diseño gráfico ofrece exactamente Flyers, Invitación, Certificado y Otros. Cada pieza genera un PED independiente.
- Publicaciones en redes sociales deja de utilizar el selector Foto / Placa / Reel y solicita fecha, copy y referencias opcionales.
- El flujo público se organiza en tres pasos: datos y áreas, formularios seleccionados, y revisión agrupada.
- La regla operativa queda definida como `1 servicio o pieza = 1 PED`, aun cuando la carga pública agrupe varios pedidos.
- Los correos futuros mediante Resend se limitan a Nuevo / Ingresado, En revisión, En proceso, Finalizado y Cancelado. Asignado y los demás estados internos no generan correo.
- `Solicitar información faltante` será una acción independiente, repetible y trazable; no cambiará automáticamente el estado.
- El producto final no se almacenará en WeWeb Storage. El servicio o ticket tendrá `producto_final_url` y `producto_final_nota` para una entrega mediante URL HTTPS externa.
- Se propone para una fase futura `comunicaciones_pedido`, sin crearla todavía.
- El seguimiento público futuro requerirá PED y correo electrónico; nunca permitirá consultar solamente con el PED.
- Los adjuntos originales del solicitante permanecen previstos en WeWeb Storage Private.
- Se preserva la arquitectura actual donde sea compatible, sin migraciones destructivas, renumeraciones ni cambios históricos.

1. WeWeb es el núcleo del sistema.
2. El solicitante utiliza un formulario público sin login.
3. La gestión interna utiliza WeWeb Auth y roles.
4. Los adjuntos enviados por el solicitante se almacenan en WeWeb Storage Private; el producto final se entrega mediante una URL HTTPS externa.
5. La regla vigente es `1 PED = 1 servicio solicitado`.
6. El usuario puede seleccionar múltiples áreas y múltiples servicios o piezas.
7. Los pedidos históricos no se migran automáticamente.
8. El redesign 310826 está documentado, pero todavía no está implementado en WeWeb.
9. La próxima referencia funcional es `Form modificaciones 310826.pdf`.
10. Evolución prevista:
   - Cobertura reemplazará Fotografía + Audiovisual.
   - Gacetilla reemplazará Prensa/Difusión.
   - Diseño se reducirá a Flyers, Invitación, Certificado y Otros.
   - Redes sociales se simplificará.
   - Se incorporará una página pública de seguimiento.
   - Se incorporarán notificaciones por email mediante Resend.

## 2026-09-01 — Inicio redesign 310826

- El snapshot previo permanece preservado en `main`, commit `50d6de10d23b83419bd60764820506ee5db90e58` y tag `pre-redesign-2026-09-01`.
- El nuevo trabajo se desarrolla en la rama `redesign-310826`.
- La arquitectura propuesta está documentada en `docs/redesign-310826.md`.
- La nueva arquitectura todavía no fue implementada en WeWeb.
- Se mantienen como invariantes la multiselección y la regla `1 servicio o pieza = 1 PED`.
- Resend, la entrega externa del producto final, la tabla `comunicaciones_pedido` y `/seguimiento` permanecen sin implementación.
