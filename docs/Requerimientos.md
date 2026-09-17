Alineados directamente con la frontera del sistema y los atributos de calidad delimitados en la Visión del Producto de **LionsSpace**, aquí tienes los requerimientos funcionales y no funcionales estructurados con rigor técnico y criterios verificables:

---

### Requerimientos Funcionales (RF)

* **RF-01 (Autenticación Institucional):** El sistema debe autenticar a los usuarios mediante la integración con las credenciales de la cuenta "Soy León" y validar que el estatus académico del solicitante sea "Activo".


* **RF-02 (Mecanismo de Contingencia de Identidad):** En caso de falla o indisponibilidad en la API de "Soy León", el sistema debe permitir el acceso mediante verificación de código de un solo uso (OTP) enviado al correo electrónico institucional del usuario.


* **RF-03 (Consulta y Filtrado de Catálogo):** El sistema debe desplegar la lista de cubículos disponibles para una fecha y franja horaria seleccionadas, permitiendo filtrar por edificio, capacidad máxima y recursos fijos (como pizarrón o pantallas).


* **RF-04 (Registro y Bloqueo de Reserva):** El sistema debe registrar la reserva de un cubículo asociando la matrícula del estudiante a un bloque horario específico, aplicando un bloqueo transaccional inmediato que impida asignaciones duplicadas sobre el mismo espacio.


* **RF-05 (Restricción de Concurrencia por Usuario):** El sistema debe rechazar cualquier intento de reserva si el estudiante ya cuenta con una reserva activa o pendiente de check-in en el sistema.


* **RF-06 (Procesamiento de Check-in Presencial):** El sistema debe habilitar el botón o lectura de código QR de check-in al minuto cero de la reserva y validar la confirmación de llegada del titular antes de que concluyan los 10 minutos de tolerancia.


* **RF-07 (Liberación Automática por Inasistencia):** El sistema debe cancelar la reserva y actualizar el estado del cubículo a disponible de manera automática si el usuario no registra su check-in al cumplirse el minuto 11 desde el inicio del bloque.


* **RF-08 (Cancelación Voluntaria):** El sistema debe permitir al estudiante cancelar su reservación activa antes del inicio del bloque horario, liberando el espacio inmediatamente para otros usuarios.


* **RF-09 (Control de Acceso Basado en Roles - RBAC):** El sistema debe segregar permisos e interfaces según el rol asignado (Estudiante: consultar y reservar; Administrador: consultar y monitorear edificios asignados; SuperAdmin: configuración global de espacios).


* **RF-10 (Registro Inmutable de Bitácora):** El sistema debe almacenar un registro de auditoría inmutable por cada evento de creación, confirmación, cancelación voluntaria o liberación automática de una reserva.


* **RF-11 (Tablero de Métricas Administrativas):** El sistema debe generar reportes agregados que muestren tasas de ocupación, horas de mayor demanda e inasistencias por cubículo y por edificio para el perfil Administrador.



---

### Requerimientos No Funcionales (RNF)

* **RNF-01 (Consistencia e Integridad Transaccional):** El motor de base de datos debe operar bajo aislamiento transaccional estricto (ACID) para prevenir condiciones de carrera (*race conditions*) y sobreventas (*overbooking*) ante solicitudes concurrentes en el mismo milisegundo.


* **RNF-02 (Rendimiento y Tiempo de Respuesta):** El sistema debe procesar las consultas de disponibilidad de cubículos y las confirmaciones de check-in con un tiempo de respuesta de servidor inferior a 800 ms durante periodos de carga regular, y menor a 1.5 s en picos de cambio de hora escolar.


* **RNF-03 (Disponibilidad y Tolerancia a Fallos):** El núcleo de reservas debe mantener una disponibilidad operativa de al menos 99.5% durante el horario lectivo del campus (07:00 a 22:00 horas).


* **RNF-04 (Desacoplamiento y Resiliencia):** El servicio web debe implementar patrones de resiliencia (*circuit breaker* y *timeouts* de 3 segundos) hacia la API externa de "Soy León" para evitar que la lentitud del proveedor externo congele la interfaz de usuario.


* **RNF-05 (Seguridad de la Información y Mínimo Privilegio):** Todas las comunicaciones entre cliente y servidor deben cifrarse mediante HTTPS/TLS 1.3. El sistema no debe almacenar contraseñas institucionales en texto plano ni acceder a expedientes escolares ajenos al estatus activo y matrícula del alumno.


* **RNF-06 (Usabilidad y Diseño Mobile-First):** La interfaz web debe ser completamente responsiva y estar optimizada para pantallas móviles de smartphones, asegurando que el flujo completo de reserva o confirmación de check-in se concrete en un máximo de 3 interacciones (*clicks* o toques).


* **RNF-07 (Trazabilidad y No Repudio):** Los registros de auditoría de la bitácora deben estar indexados por marca de tiempo UTC y matrícula de usuario, impidiendo su edición o borrado por cualquier nivel de usuario en la aplicación.



---

Puedes adjuntar estos bloques directamente en una nueva sección de especificación de requisitos en tu repositorio (por ejemplo, dentro de `docs/especificacion-requisitos.md` o anexo al documento de visión), bro.
