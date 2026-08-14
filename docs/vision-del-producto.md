# Visión del producto

**Autor:** Rodrigo Valdespino Vertiz  
**Fecha de la última versión:** 13 de agosto de 2026  
**Repositorio:** https://github.com/James-gosling/Lions_Space

---

## 1. Descripción del sistema

**Nombre del sistema:** LionsSpace

**Descripción:**  
Es una aplicación web centralizada diseñada para que la comunidad universitaria busque, reserve y confirme el uso de cubículos de estudio, laboratorios y salas de trabajo en tiempo real. Permite visualizar la disponibilidad exacta de los espacios según la agenda de los alumnos, gestionando las reservaciones de forma transparente y asegurando que las instalaciones se aprovechen al máximo mediante una verificación de identidad y presencia integrada al ecosistema digital del campus (app "Soy León" y credenciales institucionales).

---

## 2. Problema y usuarios

**El problema:**  
Actualmente, los estudiantes pierden tiempo valioso buscando espacios disponibles durante sus horas libres o bloques sin clase, ya que los cubículos o salas suelen apartarse "de palabra" o se quedan vacíos por reservas no aprovechadas. Además, la administración no cuenta con visibilidad en tiempo real, historiales de ocupación ni datos reales para controlar el acceso y uso efectivo de las instalaciones.

**Cómo se resuelve hoy sin el sistema:**  
Los estudiantes caminan por los edificios buscando mesas o cubículos libres durante sus descansos, o dejan objetos personales sobre los muebles para "apartar" el lugar por horas sin estar presentes. En otros casos, deben formarse en recepción para anotarse manualmente en una bitácora de papel.

**Usuarios del sistema:**

| Tipo de usuario | Qué necesita del sistema | Qué le preocupa |
|---|---|---|
| **Estudiante** | Reservar un espacio de forma rápida entre sus clases, validar su acceso con su cuenta universitaria y asegurar que el lugar estará disponible durante su tiempo libre. | Perder sus horas libres buscando lugar o que alguien no autorizado esté ocupando el cubículo que reservó. |
| **Administrador de Espacios / Edificio** | Maximizar la ocupación real de los espacios, evitar "reservas fantasma", contar con un historial y panel de control para auditoría, y garantizar que solo la comunidad universitaria verificada utilice las instalaciones. | Que los espacios permanezcan desocupados mientras hay demanda y no tener un registro o historial claro de quién está haciendo uso de las salas. |
| **SuperAdmin (TI Campus)** | Integrar el sistema de forma eficiente con la base de datos y sistemas existentes de la universidad (SSO / App "Soy León") sin crear bases de datos duplicadas. | Riesgos de seguridad en autenticación, sobrecarga de sistemas existentes y falta de control sobre la asignación de roles. |

**Un conflicto entre usuarios:**  
El **Estudiante** busca flexibilidad para extender su tiempo de estudio o cancelar a último minuto si le surge un contratiempo en sus horas de descanso, sin ser sancionado. El **Administrador** necesita aplicar reglas estrictas de liberación de espacios no utilizados mediante historiales de asistencia y restringir el tiempo máximo de apartado por alumno para garantizar un flujo equitativo entre toda la comunidad.

---

## 3. Alcance

### Dentro del alcance

- Catálogo interactivo de espacios filtrable por capacidad, equipamiento (pantallas, pizarrón, contactos) y ubicación dentro del campus.
- Módulo de reservas en tiempo real adaptado a las horas libres y huecos del horario escolar del estudiante.
- Sistema de *Check-in* presencial mediante escaneo con la cámara del celular o validación cruzada con la app institucional **"Soy León"** y cuenta de correo universitario.
- Esquema de control de acceso basado en roles (**RBAC**): Estudiante, Administrador de Espacios y SuperAdmin.
- Mini-perfil de usuario integrado con la base de datos universitaria existente para consulta de estatus académico y matrícula en tiempo real (sin duplicar datos).
- Panel de administración con módulo de **Historial, Auditoría Global y Métricas de Ocupación** para control administrativo.

### Explícitamente fuera del alcance

- Automatización física de apertura de puertas mediante torniquetes o cerraduras electrónicas inteligentes (IoT).
- Cobro o procesamiento de pagos por uso de espacios.
- Módulo para préstamo y control de equipo físico (pantallas portátiles, plumones, cables HDMI).

**Por qué queda fuera:**  
La integración con cerraduras físicas IoT y el cobro quedan fuera por la complejidad de hardware y el tiempo disponible en el semestre. El objetivo de LionsSpace es resolver la orquestación, disponibilidad, verificación de presencia e integración de datos, no la infraestructura física de acceso.

---

## 4. Tipo de sistema y restricciones

**Tipo de sistema:**  
Web y SaaS (Sistema Web de Información en Tiempo Real).

**Por qué es de ese tipo:**  
Requiere acceso concurrente e inmediato desde navegadores móviles sin forzar a instalar software adicional, sincronizando en tiempo real el estado de cada espacio para toda la comunidad.

**Atributos de calidad que impone:**

| Atributo | Por qué importa en mi caso | Qué pasa si no se cumple |
|---|---|---|
| **Consistencia / Concurrencia** | Varios estudiantes pueden intentar apartar la misma sala al terminar su clase simultáneamente. | Se generan reservas dobles (*overbooking*), provocando discusiones y malestar entre los alumnos. |
| **Autenticación y Seguridad (RBAC)** | Debe validar la identidad mediante la cuenta universitaria/app "Soy León" y aplicar permisos según el rol. | Usuarios ajenos a la universidad o cuentas falsas podrían acaparar espacios o acceder a historiales confidenciales. |
| **Disponibilidad en Horas Libre/Descanso** | El sistema debe responder sin demoras en los picos de cambio de bloque escolar y horas de receso. | Si el sistema se cae entre clases, los alumnos no pueden hacer *check-in* y el espacio se libera incorrectamente. |

**Reglas de negocio que ya identifiqué:**

1. **Autenticación y Estatus Activo:** Todo usuario debe autenticarse con su cuenta institucional/app **"Soy León"**. El sistema valida en tiempo real con la base de datos de la universidad que su estatus académico sea "Activo" para permitir crear reservas.
2. **Liberación por Inasistencia (*Check-in* Dual):** La reserva otorga un tiempo de tolerancia de 10 minutos al iniciar el bloque libre. El usuario debe confirmar presencia escaneando el QR de la sala con su cámara o confirmando su llegada mediante el enlace integrado con **Soy León**; de lo contrario, la sala se libera automáticamente y se registra la inasistencia en su historial.
3. **Inmutabilidad y Jerarquía del Historial:** Cada evento de reserva y check-in genera un registro inmutable en la bitácora del sistema. Los Administradores solo pueden consultar el historial de los edificios a su cargo, mientras que el usuario estudiante solo ve su historial personal en su mini-perfil.
4. **Límite de Ocupación por Ventana Libre:** Un estudiante no puede mantener más de una reserva activa simultánea durante el mismo bloque de horario libre en un solo día.
