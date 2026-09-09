# 07. Autenticación, Roles y Matriz de Permisos (RBAC)

Este documento detalla la arquitectura de seguridad, la gestión de sesiones, el ciclo de vida de identidades y la matriz de control de acceso basada en roles (RBAC).

---

## 1. Arquitectura de Identidad

El sistema implementa una arquitectura desacoplada en dos capas:
1. **Capa de Autenticación (`auth.users`)**: Administra credenciales (email, hash bcrypt de contraseña), tokens JWT y roles a nivel proveedor (`admin`, `equipo_interno`).
2. **Capa de Perfil Operativo (`usuarios_acceso`)**: Vinculada 1 a 1 mediante `id = auth.users.id`. Almacena nombre, apellido, el identificador corporativo normalizado `nombre_usuario`, el estado de aprobación (`pendiente`, `aprobado`, `revocado`) y la trazabilidad de auditoría (`solicitado_at`, `aprobado_at`, `aprobado_por`).

```
+--------------------------+          +-----------------------------------+
|      auth.users          |  1 <---> 1 |         usuarios_acceso           |
+--------------------------+          +-----------------------------------+
| id: UUID                 |          | id: UUID (PK / FK)                |
| email: VARCHAR           |          | nombre: VARCHAR                   |
| encrypted_password       |          | apellido: VARCHAR                 |
| role: equipo_interno/admin|         | nombre_usuario: VARCHAR (2-30 UK) |
+--------------------------+          | estado_acceso: pendiente/aprobado |
                                      +-----------------------------------+
```

---

## 2. Matriz de Control de Acceso por Pantalla y Acción

| Recurso / Acción | Público Anónimo | Postulante Pendiente | Equipo Interno (`equipo_interno`) | Administrador General (`admin`) |
| :--- | :---: | :---: | :---: | :---: |
| **Ver Home (`/home`)** | SÍ | SÍ | SÍ | SÍ |
| **Crear Solicitud (`/nueva-solicitud`)** | SÍ | SÍ | SÍ | SÍ |
| **Consultar Seguimiento (`/seguimiento`)** | SÍ (Con Token/Email) | SÍ | SÍ | SÍ |
| **Responder Aclaración (`/completar-solicitud`)** | SÍ (Con Token Info) | SÍ | SÍ | SÍ |
| **Solicitar Acceso (`/solicitar-acceso`)** | SÍ | NO (Redirige) | NO (Redirige) | NO (Redirige) |
| **Ver Espera (`/acceso-pendiente`)** | NO | SÍ | NO (Redirige) | NO (Redirige) |
| **Bandeja de Pedidos (`/gestion`)** | NO (401/403) | NO (Redirige) | SÍ | SÍ |
| **Ver Detalle de Pedido (`/gestion/pedido/:id`)** | NO (401/403) | NO | SÍ | SÍ |
| **Asignar Responsable a Pedido** | NO | NO | SÍ | SÍ |
| **Cambiar Estado de Pedido** | NO | NO | SÍ | SÍ |
| **Solicitar Información al Solicitante** | NO | NO | SÍ | SÍ |
| **Enviar Email al Solicitante** | NO | NO | SÍ | SÍ |
| **Administrar Usuarios (`/gestion/usuarios`)** | NO (401/403) | NO (403) | NO (403) | SÍ |
| **Aprobar / Revocar Usuarios** | NO | NO | NO | SÍ |
| **Editar `nombre_usuario` de Terceros** | NO | NO | NO | SÍ |

---

## 3. Reglas de Validación de Identidad (`nombre_usuario`)

- Longitud permitida: **2 a 30 caracteres**.
- Caracteres válidos: letras minúsculas (`a-z`), números (`0-9`), puntos (`.`), guiones (`-`) y guiones bajos (`_`).
- Normalización obligatoria antes de persistir: `trim().toLowerCase()`.
- Unicidad absoluta en `usuarios_acceso.nombre_usuario`.
