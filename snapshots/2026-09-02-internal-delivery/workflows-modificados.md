# Workflows modificados

## wf_prepare_pedido_detalle

Inicializa por cada `servicio_id` un borrador aislado con estado, responsable, observaciones, enlace y nota de entrega.

## wf_cambiar_producto_final_url

Actualiza sólo el borrador del servicio repetido que originó el cambio.

## wf_cambiar_producto_final_nota

Actualiza sólo el borrador del servicio repetido que originó el cambio.

## wf_actualizar_servicio_gestion

- Envía los cinco campos internos permitidos del servicio actual.
- Valida localmente HTTPS y el requisito de entrega antes de `Finalizado`.
- Conserva el borrador y muestra el error cuando el backend rechaza.
- Refresca el detalle y confirma éxito sólo después de una respuesta persistida.
