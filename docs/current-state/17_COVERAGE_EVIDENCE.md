# 17 - Matriz de Cobertura y Evidencia de Auditoría

> **Estado:** `[WEWEB-VERIFICADO]` / `[PROD-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Cobertura de Verificación del Sistema

| Dimensión Auditada | Cantidad de Elementos | Estado de Cobertura | Método de Inspección |
|---|---|---|---|
| **Páginas y Vistas** | 10 páginas | 100% Auditadas | Chrome DevTools Pinia dump + WeWeb Page Store |
| **Backend Workflows** | 17 workflows | 100% Auditados | WeWeb MCP + SQL Query Extraction |
| **Frontend Workflows** | 11 globales + 15 locales | 100% Auditados | Pinia Stores (`wwWorkflows`) |
| **Tablas WeWeb / PostgreSQL** | 10 tablas relacionales | 100% Auditadas | WeWeb Tables metadata dump (`inspect_tables.py`) |
| **Variables de Entorno** | 6 variables críticas | 100% Auditadas | WeWeb Env Variables API |
| **Auth & RBAC** | 3 roles, ciclo de aprobación | 100% Auditado | WeWeb Auth Plugin store + DB inspection |
| **Comunicaciones & n8n** | Webhook, JWT HS256, 4 emails | 100% Auditado | Workflow code trace |
| **Storage & Archivos** | Private storage + signed URLs | 100% Auditado | WeWeb Storage API dump |

---

## 2. Convención de Etiquetas de Evidencia Utilizadas

- `[PROD-VERIFICADO]`: Comprobado físicamente en el entorno de Producción desplegado en vivo.
- `[WEWEB-VERIFICADO]`: Extraído directamente de los componentes, variables y lógica en WeWeb Editor.
- `[CONFIG-VERIFICADO]`: Validado contra el schema de base de datos, DDL y variables de configuración.
- `[PREVIEW-VERIFICADO]`: Probado en el runtime de preview interactivo de WeWeb.
- `[HISTÓRICO]`: Información proveniente de documentos previos del repositorio.
- `[DOC-ACTUAL]`: Información generada y verificada en el presente informe de auditoría.
- `[CONTRADICCIÓN RESUELTA]`: Discrepancia identificada entre documentación previa y el código real, con resolución técnica concluyente.
