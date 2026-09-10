# 18 - Informe de Control de Calidad (QA), Saneamiento y Validación

> **Estado:** `[DOC-ACTUAL]`  
> **Fecha de Revisión:** 2026-09-10  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Branch de Trabajo:** `audit/current-weweb-2026-09-10`

---

## 1. Resumen de Errores Encontrados en la Primera Versión

Durante la primera pasada de auditoría se identificaron las siguientes deficiencias documentales:
1. **Presencia de PII Real:** Se incluyeron correos electrónicos reales de usuarios y administradores en el informe principal y en los anexos `02`, `03`, `10` y `14`.
2. **Enlaces Locales Absolutos:** Se generaron 93 enlaces en formato local absoluto en el informe principal, incompatibles con la navegación en GitHub.
3. **Afirmación Genérica de "100% Cobertura":** No se discriminó la cobertura de inventario frente a los diferentes niveles de verificación técnica (`[PROD-VERIFICADO]`, `[WEWEB-VERIFICADO]`, `[CONFIG-VERIFICADO]`).
4. **Falta de Precisión en Términos de Negocio:** No se distinguía formalmente el *Área del Solicitante* (Ministerio) frente al *Área del Pedido* (Medios) y los *Tipos de Servicio / Piezas*.

---

## 2. Correcciones y Saneamiento Aplicado

### 2.1 Eliminación Total de PII
- Se reemplazaron todos los correos electrónicos reales por patrones genéricos de ejemplo (`admin@example.com`, `operador1@example.com`, `solicitante@example.com`).
- Se sanitizaron nombres y teléfonos de contacto en ejemplos de payloads y schemas JSON.

### 2.2 Control de Secretos
- Se auditó que ninguna clave privada, token JWT firmado, contraseña o API key figure con su valor real en el código markdown. Solo se documentan nombres de variables de entorno y algoritmos utilizados (`HS256`).

### 2.3 Corrección de Enlaces a Rutas Relativas
- Se eliminaron todos los prefijos locales absolutos.
- Todos los links internos fueron convertidos a rutas relativas válidas para GitHub:
  - Desde raíz: `docs/current-state/01_PAGINAS_Y_NAVEGACION.md`
  - Entre anexos: `01_PAGINAS_Y_NAVEGACION.md`

### 2.4 Matriz de Cobertura Desglosada
- Se reemplazaron las menciones de "100% verificado" por la tabla detallada en [17_COVERAGE_EVIDENCE.md](17_COVERAGE_EVIDENCE.md), separando inventario, documentación, pruebas en producción, pruebas en editor y verificación por configuración.

---

## 3. Verificación de Puntos Críticos de Negocio

1. **Solicitar Información NO Cambia Estados:** Confirmado en backend workflow `44ad1bdd-c7e8-45a7-9555-ed11931b30c7`.
2. **Regla del PED:** El número de PED (`pedido_visible`) reside en `pedidos` (1 pedido general puede contener N servicios asociados).
3. **Gestión Kanban:** Confirmado que opera sin HTML5 Drag & Drop; la transición de estados es mediante selectores/modales.
4. **Integración n8n:** Webhook con JWT HS256 clasificado como `[CONFIG-VERIFICADO]` en WeWeb y `[NO-VERIFICADO]` en su ejecución interna en n8n.
5. **Notion Sync:** Workflow `bw_sincronizar_notion_pedido` clasificado como `ACTIVO EN CONFIGURACIÓN` / `[NO-VERIFICADO]` en producción en vivo.

---

## 4. Estado de Verificación Final
- **PII Real Restante:** CERO.
- **Secretos Reales Restantes:** CERO.
- **Rutas Locales Restantes:** CERO.
- **Nivel de Confianza Documental:** **ALTO**.
- **Lista para servir como fuente de futura migración:** **SÍ**.
