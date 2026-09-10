# 14 - Matriz de Diferencias: Producción vs WeWeb Editor

> **Estado:** `[WEWEB-VERIFICADO]` / `[PROD-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Resumen Ejecutivo de Sincronización

A la fecha de la auditoría (2026-09-10), existen diferencias verificables entre la versión publicada en **Producción** (`https://secretariamedios-production.weweb.io/`) y el borrador de trabajo en **WeWeb Editor**:

---

## 2. Matriz Comparativa Detallada

| Recurso / Componente | Producción (Live) | Editor Actual (Draft) | Diferencia | Impacto | Evidencia |
|---|---|---|---|---|---|
| **Formulario `/solicitar-acceso`** | Muestra campos legados: `Nombre y apellido *`, `Correo electrónico *`, `Contraseña *`, `Confirmar contraseña *`. | Muestra campos del nuevo modelo: `Nombre *`, `Apellido *`, `Usuario *`, `Correo electrónico *`, `Contraseña *`, `Confirmar contraseña *`. | Frontend draft no publicado a Production. | Los registros en Prod siguen enviando nombre combinado en vez de campos divididos. | `[PROD-VERIFICADO]` vs `[WEWEB-VERIFICADO]` |
| **Selector de Responsables (`/gestion`)** | Muestra valores de `nombre_usuario` (ej: `21`, `22`, `23`). | Configurado con binding `label = nombre_usuario` y `value = user_id`. | **Sincronizado en Backend:** El backend workflow desplegado ya provee la query corregida. | El frontend en producción consume el endpoint sin exponer PII. | `[PROD-VERIFICADO]` vs `[CONFIG-VERIFICADO]` |
| **Schema de `usuarios_acceso`** | Columnas `nombre`, `apellido`, `nombre_usuario` existentes y pobladas. | Columnas `nombre`, `apellido`, `nombre_usuario` existentes y pobladas. | **Sincronizado:** Migración DDL publicada y aplicada. | Estructura de base de datos consistente en ambos entornos. | `[CONFIG-VERIFICADO]` |
| **Rol Administrador Inicial** | Cuenta designada con rol `admin`. | Cuenta designada con rol `admin`. | **Sincronizado:** Asignación en WeWeb Auth verificada. | Un único Administrador general inicial operativo. | `[CONFIG-VERIFICADO]` |
| **Operadores Internos** | Cuentas operativas con rol `equipo_interno`. | Cuentas operativas con rol `equipo_interno`. | **Sincronizado:** Sin privilegios de administrador indebidos. | Cumplimiento de la regla de privilegios mínimos. | `[CONFIG-VERIFICADO]` |

---

## 3. Evidencia de Verificación
- `[PROD-VERIFICADO]`: Pruebas de navegación e interacción contra el endpoint público en producción.
- `[WEWEB-VERIFICADO]`: Inspección del DOM y de los stores de Pinia en WeWeb Editor.
