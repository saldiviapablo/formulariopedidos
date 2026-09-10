# 15 - Diferencias con Documentación Histórica y Refutaciones

> **Estado:** `[HISTÓRICO]` vs `[WEWEB-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Errores y Desactualizaciones en Documentos Anteriores

La auditoría técnica exhaustiva detectó inconsistencias en la documentación previa del repositorio:

| # | Afirmación en Documentación Histórica | Realidad Técnica Verificada | Evidencia |
|---|---|---|---|
| 1 | *"Solicitar Información cambia el estado del pedido o servicio a Esperando información automáticamente."* | **FALSO / REFUTADO:** `api_solicitar_informacion` solo inserta en `solicitudes_informacion` y envía email. No modifica el estado de `pedidos` ni de `servicios_solicitados`. | `[CONFIG-VERIFICADO]` SQL de `44ad1bdd-c7e8-45a7-9555-ed11931b30c7`. |
| 2 | *"El formulario tiene un paso inicial de selección de categoría general antes de los datos del solicitante."* | **FALSO:** El Paso 1 contiene tanto los datos personales del solicitante como los checkboxes de Áreas (`selected_areas`). El Paso 2 contiene los servicios específicos. | `[WEWEB-VERIFICADO]` DOM de página `50ee979b-2ff9-4235-8ea5-6ce664539886`. |
| 3 | *"El selector de responsables muestra el Nombre y Apellido del operador."* | **FALSO:** El selector fue migrado para mostrar estrictamente `nombre_usuario` como `label` y Auth UUID como `value`. | `[CONFIG-VERIFICADO]` SQL de `565645b9-79e5-4981-bdd4-763cf076601b`. |
| 4 | *"Las notificaciones se envían exclusivamente por n8n para todos los estados."* | **FALSO:** Solo `pedido_ingresado` utiliza el webhook nativo de n8n con JWT; los demás estados usan el plugin WeWeb Resend/SMTP directamente. | `[CONFIG-VERIFICADO]` Workflow `8e32cb71-f921-470a-a712-4091a679efb5`. |
| 5 | *"Existen tablas separadas para cada tipo de servicio."* | **FALSO:** Todos los servicios solicitados residen en una única tabla polimórfica relacional `servicios_solicitados` con columna `informacion_especifica` (JSONB). | `[CONFIG-VERIFICADO]` Schema de WeWeb Tables. |

---

## 2. Evidencia de Verificación
- `[CONTRADICCIÓN RESUELTA]`: Las discrepancias fueron validadas contra el código real y volcados de base de datos.
