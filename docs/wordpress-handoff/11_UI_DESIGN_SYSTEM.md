# 11. Design System, Paleta de Color y Tokens UI

Este documento compila el sistema de diseño visual institucional utilizado en WeWeb para ser trasladado fielmente a WordPress mediante CSS / Tailwind CSS.

---

## 1. Paleta de Colores Institucional

| Token | Hex | Muestra | Uso Principal |
| :--- | :--- | :--- | :--- |
| `--color-primary-900` | `#0f172a` | Slate 900 | Headers, títulos principales, botones primarios |
| `--color-primary-600` | `#0284c7` | Sky 600 | Acentos, enlaces, pasos activos en wizard |
| `--color-primary-50` | `#f0f9ff` | Sky 50 | Fondos de tarjetas seleccionadas, banners info |
| `--color-bg-body` | `#f8fafc` | Slate 50 | Fondo general de la aplicación |
| `--color-bg-card` | `#ffffff` | White | Fondo de contenedores, formularios y modales |
| `--color-border` | `#e2e8f0` | Slate 200 | Bordes de inputs, tablas y separadores |
| `--color-text-main` | `#1e293b` | Slate 800 | Texto de lectura, etiquetas de formulario |
| `--color-text-muted` | `#64748b` | Slate 500 | Subtítulos, timestamps, placeholders |

---

## 2. Colores Semánticos de Estado (Badges y Alertas)

| Estado | Fondo (`bg`) | Texto (`text`) | Borde (`border`) |
| :--- | :--- | :--- | :--- |
| **`pendiente`** | `#fef9c3` (Yellow 100) | `#854d0e` (Yellow 800) | `#fde047` |
| **`en_proceso`** | `#e0f2fe` (Sky 100) | `#075985` (Sky 800) | `#7dd3fc` |
| **`requiere_informacion`**| `#ffedd5` (Orange 100) | `#9a3412` (Orange 800) | `#fdba74` |
| **`finalizado`** | `#dcfce7` (Green 100) | `#166534` (Green 800) | `#86efac` |
| **`cancelado`** | `#fee2e2` (Red 100) | `#991b1b` (Red 800) | `#fca5a5` |

---

## 3. Tipografía y Escalas

- **Font Family Principal**: `'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif`.
- **Pesos Utilizados**:
  - `400` (Regular): Párrafos, textos de inputs.
  - `500` (Medium): Labels de formularios, items de navegación.
  - `600` (SemiBold): Encabezados secundarios (H2, H3), botones, badges.
  - `700` (Bold): Encabezados principales (H1), números PED destacados.
- **Escala de Espaciado**:
  - `xs`: `4px` | `sm`: `8px` | `md`: `16px` | `lg`: `24px` | `xl`: `32px` | `2xl`: `48px`.
