# 10 - Autenticación, Usuarios, Roles y Permisos (RBAC)

> **Estado:** `[WEWEB-VERIFICADO]` / `[CONFIG-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Proveedor de Autenticación y Arquitectura de Sesión

El sistema utiliza el plugin nativo **WeWeb Auth Plugin (`plugin-auth`)** conectado a la base de datos PostgreSQL interna (`auth.users`).
- **Mecanismo de Sesión:** Token JWT emitido al autenticarse en `/login`, almacenado en cookie segura (`SameSite=Lax`). `[CONFIG-VERIFICADO]`
- **Expiración de Sesión:** 7 días con renovación automática.

---

## 2. Distinción entre ROL y ESTADO_ACCESO

Es fundamental separar dos conceptos independientes en el modelo de seguridad: `[CONFIG-VERIFICADO]`

1. **ROL (WeWeb Auth Plugin):** Determina qué páginas y recursos puede ver el usuario si su sesión es válida.
   - `admin`: Acceso total (incluye `/usuarios`).
   - `equipo_interno`: Acceso a bandeja `/gestion` y detalle `/pedido/:id`.
   - `anonimo`: Solo páginas públicas.
2. **ESTADO_ACCESO (`usuarios_acceso.estado_acceso`):** Determina si la cuenta está habilitada operativamente en la base de datos.
   - `pendiente`: Cuenta registrada recientemente; espera aprobación de un administrador.
   - `aprobado`: Cuenta validada; sus datos se exponen en selectores de responsables.
   - `revocado`: Cuenta deshabilitada.

---

## 3. Matriz de Roles y Permisos (RBAC)

| Recurso / Funcionalidad | Anónimo | `equipo_interno` | `admin` | Evidencia |
|---|---|---|---|---|
| Cargar Nueva Solicitud (`/`) | Sí | Sí | Sí | `[PROD-VERIFICADO]` |
| Ver Confirmación (`/solicitud-recibida`) | Sí (con token) | Sí | Sí | `[PROD-VERIFICADO]` |
| Consultar Seguimiento (`/seguimiento`) | Sí (con PED/token) | Sí | Sí | `[PROD-VERIFICADO]` |
| Responder Solicitud Info (`/solicitud-informacion`) | Sí (con token válido) | Sí | Sí | `[PROD-VERIFICADO]` |
| Registrar Solicitud de Acceso (`/solicitar-acceso`) | Sí | No (redirige) | No (redirige) | `[PROD-VERIFICADO]` |
| Iniciar Sesión (`/login`) | Sí | No (redirige) | No (redirige) | `[PROD-VERIFICADO]` |
| Bandeja de Gestión (`/gestion`) | **No (401)** | **Sí** | **Sí** | `[WEWEB-VERIFICADO]` |
| Ver Detalle de Pedido (`/pedido/:id`) | **No (401)** | **Sí** | **Sí** | `[WEWEB-VERIFICADO]` |
| Cambiar Estado de Servicio | **No (401)** | **Sí** | **Sí** | `[CONFIG-VERIFICADO]` |
| Asignar Responsable de Servicio | **No (401)** | **Sí** | **Sí** | `[CONFIG-VERIFICADO]` |
| Solicitar Información Faltante | **No (401)** | **Sí** | **Sí** | `[CONFIG-VERIFICADO]` |
| Cargar Entrega Final y Finalizar | **No (401)** | **Sí** | **Sí** | `[CONFIG-VERIFICADO]` |
| Panel de Administración de Usuarios (`/usuarios`) | **No (401)** | **No (403)** | **Sí** | `[WEWEB-VERIFICADO]` |
| Aprobar / Rechazar Usuarios Nuevos | **No** | **No** | **Sí** | `[CONFIG-VERIFICADO]` |
| Modificar `nombre_usuario` de Operadores | **No** | **No** | **Sí** | `[CONFIG-VERIFICADO]` |

---

## 4. El Nuevo Modelo de Identidad (`nombre_usuario`)

Para desacoplar los correos electrónicos privados y nombres personales de los operadores en las interfaces públicas e internas:
- **Restricciones:** 2 a 30 caracteres, minúsculas (`a-z`), números (`0-9`), puntos (`.`), guiones (`_`, `-`). `[CONFIG-VERIFICADO]`
- **Normalización:** `LOWER(TRIM(input))` obligatoria antes de persistir.
- **Unicidad:** Restricción `UNIQUE` en base de datos.
- **Uso en Interfaz:** Es el único identificador visualizado en los selectores de responsables de `/gestion` y `/pedido/:id`.

---

## 5. Cuentas y Roles Tipificados (Sanitizado)

| Tipo de Cuenta | Rol WeWeb Auth | `estado_acceso` | Propósito |
|---|---|---|---|
| `admin@example.com` | `admin` | `aprobado` | Administrador general inicial del sistema. |
| `operador1@example.com` | `equipo_interno` | `aprobado` | Operador / Diseñador del equipo interno. |
| `operador2@example.com` | `equipo_interno` | `aprobado` | Redactor / Operador de prensa del equipo interno. |

---

## 6. Evidencia de Verificación
- `[CONFIG-VERIFICADO]`: Esquema de `usuarios_acceso`, triggers y plugins de WeWeb Auth verificados.
