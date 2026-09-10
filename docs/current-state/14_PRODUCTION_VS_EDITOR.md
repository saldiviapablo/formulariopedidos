# 14 - Matriz de Diferencias: Producción vs WeWeb Editor

> **Estado:** `[WEWEB-VERIFICADO]` / `[PROD-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Resumen Ejecutivo de Sincronización

A la fecha de la auditoría (2026-09-10), existen discrepancias clave entre lo que está publicado y activo en **Producción** (`https://secretariamedios-production.weweb.io/`) y lo que está configurado en el borrador de **WeWeb Editor**:

---

## 2. Matriz Comparativa Detallada

| Componente / Recurso | Estado en Producción Live | Estado en WeWeb Editor Draft | Causa Técnica | Impacto Operativo |
|---|---|---|---|---|
| **Formulario `/solicitar-acceso`** | Muestra campos legados: `Nombre y apellido *`, `Email *`, `Password *`. | Muestra campos del nuevo modelo: `Nombre *`, `Apellido *`, `Usuario *`, `Email *`, `Password *`. | Falta de publicación del frontend draft. | Los usuarios nuevos en Prod registran nombre combinado en lugar de campos divididos. |
| **Selector de Responsables (`/gestion`)** | Muestra `nombre_usuario` numérico (`21`, `22`, `23`). | Configurado para mostrar `nombre_usuario` como `label` y `user_id` como `value`. | **SINCRONIZADO EN BACKEND**: La query ya fue actualizada en backend. | El frontend en Prod ya consume el endpoint corregido. |
| **Esquema de `usuarios_acceso`** | Columnas `nombre`, `apellido`, `nombre_usuario` creadas y pobladas. | Columnas `nombre`, `apellido`, `nombre_usuario` creadas y pobladas. | **SINCRONIZADO**: El schema DDL fue migrado y publicado. | Base de datos completamente consistente. |
| **Rol de `test@gmail.com`** | `admin` | `admin` | **SINCRONIZADO**: Corregido en Auth. | Administrador general único inicial activo. |
| **Rol de `drivegobtdf@gmail.com`** | Sin rol admin (o no activo). | `equipo_interno` | **SINCRONIZADO**: Desacoplado de privilegios admin. | Cumple regla de negocio estricta. |

---

## 3. Evidencia de Verificación
- `[PROD-VERIFICADO]`: Captura y prueba directa contra el endpoint y frontend público de producción.
- `[WEWEB-VERIFICADO]`: Inspección del árbol DOM en Pinia stores del Editor.
