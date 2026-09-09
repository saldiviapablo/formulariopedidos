# 18. Matriz de Pruebas de Aceptación (QA Test Matrix)

Este documento detalla los 25 casos de prueba de control de calidad que garantizan la paridad y solidez funcional de la plataforma en WordPress.

---

## 1. Casos de Prueba Críticos

| ID | Módulo | Caso de Prueba | Entrada / Acción | Resultado Esperado |
| :--- | :--- | :--- | :--- | :--- |
| **QA-01** | Wizard | Creación con 1 servicio | Completa contacto y selecciona Fotografía | Crea 1 registro PED (`PED-2026-000001`), genera token, dispara webhook n8n y muestra pantalla de éxito. |
| **QA-02** | Wizard | Creación con N servicios (Regla 1:1) | Selecciona Fotografía + Prensa + Diseño | Crea 3 números consecutivos (`PED-...01`, `PED-...02`, `PED-...03`), 3 tokens únicos y envía 1 email consolidado. |
| **QA-03** | Wizard | Validación de campos obligatorios | Intenta avanzar sin email o área | Botón "Siguiente" bloqueado y mensajes de error en rojo bajo cada campo. |
| **QA-04** | Seguimiento | Consulta con Token válido | Abre `/seguimiento?ped=PED-2026-000001&token=...` | Muestra estado, timeline de hitos y datos del pedido sin requerir login. |
| **QA-05** | Seguimiento | Consulta manual con Email | Ingresa PED + Email registrado | Valida coincidencia exacta y muestra el estado del pedido. |
| **QA-06** | Seguimiento | Consulta con credenciales inválidas | Ingresa PED existente con email incorrecto | Muestra error: "No se encontró ningún pedido coincidente". |
| **QA-07** | Solicitud Info | Aclaración requerida | Gestor solicita info desde detalle | Pedido pasa a `requiere_informacion`, token info generado y correo enviado al solicitante. |
| **QA-08** | Completar Info | Respuesta del solicitante | Solicitante responde en `/completar-solicitud` | Solicitud pasa a `respondida`, pedido vuelve a `en_proceso`, gestor notificado. |
| **QA-09** | Auth | Validación `nombre_usuario` 2-30 | Intenta registrar usuario de 1 carácter o 35 car | Formulario rechaza y exige longitud entre 2 y 30 caracteres. |
| **QA-10** | Auth | Normalización a lowercase | Registra usuario "Pablo.Saldivia" | Se guarda en base de datos como "pablo.saldivia". |
| **QA-11** | Auth | Unicidad de `nombre_usuario` | Intenta registrar usuario ya existente | Retorna error HTTP 409: "El nombre de usuario ya está en uso". |
| **QA-12** | Auth | Estado inicial `pendiente` | Completa registro público | Cuenta creada con `estado_acceso = 'pendiente'`, redirige a `/acceso-pendiente`. |
| **QA-13** | Admin Users | Aprobación de usuario | Admin aprueba en `/gestion/usuarios` | `estado_acceso` pasa a `aprobado`, rol asignado, usuario puede iniciar sesión en `/gestion`. |
| **QA-14** | Admin Users | Revocación de usuario | Admin revoca usuario activo | `estado_acceso` pasa a `revocado`, sesiones invalidadas inmediatamente. |
| **QA-15** | Bandeja | Asignación rápida de Responsable | Cambia selector en celda de `/gestion` | Asignación guardada, selector muestra `nombre_usuario` (nunca email). |
| **QA-16** | Detalle | Envío de correo institucional | Envía mensaje desde tab Comunicaciones | Mensaje registrado en `comunicaciones_pedido` y despachado por n8n / Gmail. |
| **QA-17** | Archivos | Subida de archivo seguro | Adjunta PDF de 10 MB | Archivo guardado en carpeta protegida fuera de acceso público web. |
| **QA-18** | Archivos | Bloqueo de archivos maliciosos | Intenta subir archivo `.php` o `.exe` | Rechazo inmediato con error de formato no permitido. |
| **QA-19** | Concurrencia | Concurrencia en secuencia PED | 10 envíos simultáneos en el mismo milisegundo | 10 números estrictamente consecutivos sin colisiones ni saltos. |
| **QA-20** | Responsive | Visualización en Mobile (< 640px) | Abre `/gestion` en pantalla de 375px | Tabla convertida en lista de tarjetas táctiles perfectamente legibles. |
