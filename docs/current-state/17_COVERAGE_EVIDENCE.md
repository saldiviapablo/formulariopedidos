# 17 - Matriz de Cobertura y Evidencia de Auditoría

> **Estado:** `[WEWEB-VERIFICADO]` / `[PROD-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Matriz Desglosada de Cobertura y Verificación

Para evitar afirmaciones genéricas de "100%", a continuación se presenta la desagregación rigurosa por nivel de verificación:

| Dimensión Auditada | Total Inventariado | Total Documentado | Verificado en Production | Verificado vía WeWeb MCP / DOM | Verificado en Configuración / SQL | No Verificado / No Inspeccionable |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| **Páginas y Vistas** | 10 | 10 | 6 (Rutas públicas) | 10 (Árboles DOM y Stores) | 10 (Page Settings) | 0 |
| **Backend Workflows** | 17 | 17 | 3 (Endpoints públicos) | 17 (Pinia & API) | 17 (SQL & Triggers) | 1 (Ejecución Notion) |
| **Frontend Workflows** | 26 (11 glob + 15 loc) | 26 | 6 (Interacciones públicas) | 26 (Stores Pinia) | 26 (Lógica JS) | 0 |
| **Tablas PostgreSQL** | 10 | 10 | 1 (`usuarios_acceso` live) | 10 (WeWeb Tables) | 10 (DDL & Metadata) | 0 |
| **Variables de Entorno** | 6 | 6 | 1 (`APP_PUBLIC_URL`) | 6 (Env Store) | 6 (Secrets config) | 0 |
| **Auth & RBAC** | 3 roles + 3 estados | 6 elementos | 2 (Público + Guards) | 6 (Auth Plugin) | 6 (Tabla `usuarios_acceso`) | 0 |
| **Comunicaciones & n8n** | 1 webhook + 4 emails | 5 flujos | 0 (Sin envíos reales) | 5 (Workflows) | 5 (Triggers & JWT logic) | 1 (Lógica interna n8n) |
| **Storage & Archivos** | 1 bucket privado | 1 flujo | 1 (Carga pública Dropzone) | 1 (Storage API) | 1 (Tabla `archivos`) | 0 |

---

## 2. Glosario de Convención de Etiquetas de Evidencia

- `[PROD-VERIFICADO]`: Comprobado e interactuado directamente en el entorno de Producción desplegado en vivo.
- `[WEWEB-VERIFICADO]`: Extraído directamente de los componentes, variables y árboles DOM en WeWeb Editor.
- `[CONFIG-VERIFICADO]`: Validado contra el schema físico DDL, consultas SQL puras y variables de configuración.
- `[PREVIEW-VERIFICADO]`: Comprobado en el runtime de preview interactivo de WeWeb.
- `[HISTÓRICO]`: Información documentada en iteraciones anteriores del repositorio.
- `[DOC-ACTUAL]`: Información generada y verificada en el presente informe de auditoría.
- `[CONTRADICCIÓN RESUELTA]`: Discrepancia identificada entre documentación previa y el código real, con resolución técnica concluyente.
- `[NO-VERIFICADO]`: Flujo o componente presente en configuración cuya ejecución real no fue disparada para preservar la integridad del entorno.
- `[NO-INSPECCIONABLE]`: Servicio o infraestructura externa cuyo código interno no es accesible desde el contexto de WeWeb.
