# 10 - Autenticación, Usuarios, Roles y Permisos (RBAC)

> **Estado:** `[WEWEB-VERIFICADO]` / `[CONFIG-VERIFICADO]`  
> **Proyecto WeWeb ID:** `5791a02a-c0b8-49dc-a5b6-afe5fbb53b60`  
> **Fecha de Auditoría:** 2026-09-10

---

## 1. Proveedor de Autenticación y Arquitectura de Sesión

El sistema utiliza el plugin nativo **WeWeb Auth Plugin (`plugin-auth`)** conectado a la base de datos PostgreSQL interna (`auth.users`).
- **Mecanismo de Sesión:** Token JWT emitido al autenticarse en `/login`, almacenado en cookie segura (`SameSite=Lax`).
- **Expiración de Sesión:** 7 días con renovación automática.

---

## 2. Matriz de Roles y Permisos (RBAC)

El sistema define formalmente dos roles internos:
1. `admin` (Administrador General del Sistema).
2. `equipo_interno` (Operador / Diseñador / Redactor de la Secretaría).
3. *(Anónimo / Solicitante: usuarios públicos sin autenticación).*

| Recurso / Funcionalidad | Anónimo | `equipo_interno` | `admin` |
|---|---|---|---|
| Cargar Nueva Solicitud (`/`) | Sí | Sí | Sí |
| Ver Confirmación (`/solicitud-recibida`) | Sí (con token) | Sí | Sí |
| Consultar Seguimiento (`/seguimiento`) | Sí (con PED/token) | Sí | Sí |
| Responder Solicitud Info (`/solicitud-informacion`) | Sí (con token válido) | Sí | Sí |
| Registrar Solicitud de Acceso (`/solicitar-acceso`) | Sí | No (redirige) | No (redirige) |
| Iniciar Sesión (`/login`) | Sí | No (redirige) | No (redirige) |
| Bandeja de Gestión (`/gestion`) | **No (401)** | **Sí** | **Sí** |
| Ver Detalle de Pedido (`/pedido/:id`) | **No (401)** | **Sí** | **Sí** |
| Cambiar Estado de Servicio | **No (401)** | **Sí** | **Sí** |
| Asignar Responsable de Servicio | **No (401)** | **Sí** | **Sí** |
| Solicitar Información Faltante | **No (401)** | **Sí** | **Sí** |
| Cargar Entrega Final y Finalizar | **No (401)** | **Sí** | **Sí** |
| Panel de Administración de Usuarios (`/usuarios`) | **No (401)** | **No (403)** | **Sí** |
| Aprobar / Rechazar Usuarios Nuevos | **No** | **No** | **Sí** |
| Modificar `nombre_usuario` de Operadores | **No** | **No** | **Sí** |
| Reasignar Rol (`admin` / `equipo_interno`) | **No** | **No** | **Sí** |

---

## 3. El Nuevo Modelo de Identidad (`nombre_usuario`)

Para desacoplar los emails privados y nombres personales de los operadores, se implementó el campo `nombre_usuario` en la tabla `usuarios_acceso`:
- **Restricciones:**
  - Longitud: 2 a 30 caracteres.
  - Caracteres permitidos: Letras minúsculas (`a-z`), números (`0-9`), puntos (`.`), guiones bajos (`_`), y guiones medios (`-`).
  - Normalización: Automática mediante `LOWER(TRIM(input))` antes de INSERT o UPDATE.
  - Constraint: `UNIQUE` en base de datos.
- **Uso en Interfaz:**
  - Es el único identificador visible en el selector de Responsables de `/gestion` y `/pedido/:id`.

---

## 4. Estado de Usuarios en Producción

| Email de Cuenta | Rol WeWeb Auth | `nombre_usuario` | `estado_acceso` | Estado de Auditoría |
|---|---|---|---|---|
| `test@gmail.com` | `admin` | `23` | `aprobado` | `[PROD-VERIFICADO]` Administrador Inicial |
| `pablosaldiviainfo@gmail.com` | `equipo_interno` | `22` | `aprobado` | `[PROD-VERIFICADO]` Operador Interno |
| `pablo2003_87@hotmail.com` | `equipo_interno` | `21` | `aprobado` | `[PROD-VERIFICADO]` Operador Interno |

---

## 5. Evidencia de Verificación
- `[PROD-VERIFICADO]`: Usuarios, roles y accesos confirmados en la base de datos de producción y Auth Plugin.
