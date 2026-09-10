# 13 - Sistema de Diseño, UI/UX y Estilos

> **Estado:** `[WEWEB-VERIFICADO]` / `[PROD-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Identidad Visual y Paleta de Colores

La aplicación implementa la identidad visual institucional del Gobierno de Tierra del Fuego:

| Token / Nombre | Valor HEX / RGB | Uso Principal |
|---|---|---|
| **Azul Institucional (Primary)** | `#003366` / `#0B4F8A` | Botones principales, barras de navegación, títulos destacados. |
| **Azul Secundario / Hover** | `#002244` | Hover de botones, estados activos de sidebar. |
| **Gris Fondo (Background)** | `#F8F9FA` | Fondo general de páginas y aplicaciones. |
| **Gris Tarjeta (Card/Surface)** | `#FFFFFF` | Contenedores de formularios, tarjetas de pedidos y modales. |
| **Borde / Separador** | `#E5E7EB` | Bordes de inputs, tablas y divisiones de tabs. |
| **Texto Principal** | `#1F2937` | Tipografía principal, etiquetas y encabezados. |
| **Texto Secundario / Muted** | `#6B7280` | Textos de ayuda, timestamps y metadatos. |
| **Estado Éxito (Success)** | `#10B981` | Badges de `Finalizado`, alertas de confirmación. |
| **Estado Advertencia (Warning)**| `#F59E0B` | Badges de `Esperando información`, `En revisión`. |
| **Estado Error / Peligro (Danger)**| `#EF4444` | Badges de `Cancelado`, alertas de validación de campos. |
| **Estado Información (Info)** | `#3B82F6` | Badges de `Nuevo`, `Asignado`, `En proceso`. |

---

## 2. Tipografía y Componentes UI

- **Familia Tipográfica:** Inter / System UI Font Stack (`font-sans`).
- **Escala Modular:**
  - Encabezados H1: `text-2xl font-bold tracking-tight` (24px - 32px).
  - Encabezados H2/H3: `text-lg font-semibold` (18px - 20px).
  - Cuerpo / Inputs: `text-sm font-normal` (14px).
  - Badges / Muted: `text-xs font-medium` (12px).
- **Componentes Clave:**
  - **Dropzone / Uploader:** Borde punteado con feedback visual en drag-over.
  - **Badges de Estado:** Pills redondeadas con color de fondo pastel y texto sólido.
  - **Tabs de Navegación:** Pestañas horizontales con indicador de borde inferior azul.
  - **Modales:** Overlays oscurecidos (`backdrop-blur-sm bg-black/40`) con foco accesible.

---

## 3. Responsive Design
- **Desktop (>= 1024px):** Layout en 2 o 3 columnas para Kanban y formulario con panel lateral de resumen.
- **Tablet (768px - 1023px):** Kanban con scroll horizontal, formulario en columna única.
- **Mobile (< 768px):** Formulario optimizado en ancho 100%, navegación colapsada en menú hamburguesa, botones en ancho completo.

---

## 4. Evidencia de Verificación
- `[WEWEB-VERIFICADO]`: Clases CSS y tokens de Design System extraídos de `parse_design.py`.
