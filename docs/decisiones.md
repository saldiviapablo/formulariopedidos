# Decisiones

1. WeWeb es el núcleo del sistema.
2. El solicitante utiliza un formulario público sin login.
3. La gestión interna utiliza WeWeb Auth y roles.
4. Los archivos se almacenan en WeWeb Storage Private.
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
- Resend, el producto final y `/seguimiento` permanecen como decisiones futuras sin implementación.
