# Visión del producto

**Autor:** Rodrigo Valdespino Vertiz  
**Fecha de la última versión:** 13 de agosto de 2026  
**Repositorio:** https://github.com/James-gosling/Lions_Space

---

## 1. Descripción del sistema

**Nombre del sistema:** LionsSpace

**Descripción:**  
Es una aplicación web centralizada diseñada para que la comunidad universitaria busque, reserve y confirme el uso de cubículos y salas de estudio en tiempo real. Permite visualizar la disponibilidad exacta de los espacios según la agenda de los alumnos, coordinando las reservaciones de forma transparente e impidiendo solapamientos mediante una verificación de identidad y presencia integrada al ecosistema digital del campus (app "Soy León" y credenciales institucionales).

---

## 2. Problema y usuarios

**El problema:**  
Actualmente, los estudiantes pierden tiempo valioso buscando espacios disponibles durante sus horas libres o bloques sin clase, ya que los cubículos o salas suelen apartarse "de palabra" o se quedan vacíos por reservas no aprovechadas. Además, la administración no cuenta con visibilidad en tiempo real, historiales de ocupación ni datos consolidados para auditar el acceso y uso efectivo de las instalaciones.

**Cómo se resuelve hoy sin el sistema:**  
Los estudiantes caminan por los edificios buscando mesas o cubículos libres durante sus descansos, o dejan objetos personales sobre los muebles para "apartar" el lugar por horas sin estar presentes. En otros casos, deben formarse en recepción para anotarse manualmente en una bitácora de papel.

**Usuarios del sistema:**

| Tipo de usuario | Qué necesita del sistema | Qué le preocupa |
|---|---|---|
| **Estudiante** | Reservar un espacio de forma rápida entre sus clases, validar su acceso con su cuenta universitaria y asegurar que el lugar estará disponible durante su tiempo libre. | Perder sus horas libres buscando lugar o que alguien no autorizado esté ocupando el cubículo que reservó. |
| **Administrador de Espacios / Edificio** | Maximizar la ocupación real de los espacios, evitar "reservas fantasma", contar con un historial y panel de control para auditoría, y garantizar que solo la comunidad universitaria verificada utilice las instalaciones. | Que los espacios permanezcan desocupados mientras hay demanda y no tener un registro o historial claro de quién está haciendo uso de las salas. |
| **SuperAdmin (TI Campus)** | Integrar el sistema de forma eficiente con la base de datos y servicios existentes de la universidad (SSO / App "Soy León") sin crear bases de datos duplicadas ni comprometer la seguridad. | Riesgos de seguridad en autenticación, sobrecarga de sistemas existentes y falta de control sobre la asignación de roles. |

**Un conflicto entre usuarios:**  
El **Estudiante** busca flexibilidad para extender su tiempo de estudio o cancelar a último minuto si le surge un contratiempo en sus horas de descanso, sin ser sancionado. El **Administrador** necesita aplicar reglas estrictas de liberación de espacios no utilizados mediante historiales de asistencia y restringir el tiempo máximo de apartado por alumno para garantizar un flujo equitativo entre toda la comunidad.

---

## 3. Alcance

### 3.1 Dentro del alcance (Inclusiones con verbos verificables)
El sistema LionsSpace realiza exclusivamente las siguientes funciones:
- **Autentica** la identidad y estatus activo de los alumnos y administradores consultando las credenciales institucionales ("Soy León" / correo universitario).
- **Despliega** el catálogo interactivo de cubículos mostrando su capacidad máxima, ubicación por edificio y atributos fijos (pizarrón, pantalla fija, número de tomas eléctricas).
- **Registra** reservaciones de bloques de horario vinculando la matrícula del solicitante con la sala, bloqueando transaccionalmente el espacio para evitar colisiones.
- **Habilita y procesa** el *check-in* presencial del usuario mediante lectura de código QR de la sala o confirmación directa en la interfaz web durante la ventana de tolerancia.
- **Cancela y libera** automáticamente el cubículo al inventario público si expira la tolerancia de 10 minutos sin registrar el *check-in*.
- **Aplica control de acceso por roles (RBAC)** restringiendo las vistas y acciones para Estudiante, Administrador de Edificio y SuperAdmin.
- **Genera** reportes de métricas de ocupación, horas pico e historial de uso inmutable para el personal de administración.

### 3.2 Explícitamente fuera del alcance (Exclusiones)
- **No automatiza el control de acceso físico:** No acciona cerraduras electrónicas inteligentes, torniquetes ni detecta la presencia física con sensores IoT en puertas.
- **No procesa pagos ni transacciones monetarias:** No cuenta con pasarela de cobro ni aplica cargos económicos o tarifas de arrendamiento.
- **No gestiona el inventario ni préstamo de equipo físico móvil:** No rastrea plumones, pantallas portátiles, controles remotos ni cables adaptadores.
- **No escribe ni sobreescribe datos en sistemas académicos centrales:** No altera registros de materias, calificaciones ni expedientes dentro de "Soy León" / SIU.

### 3.3 Justificación de exclusiones
La integración con cerraduras físicas IoT, sensores de presencia y pasarelas de pago quedan excluidas debido a la complejidad de hardware, costos de adquisición y restricciones de tiempo del semestre. El propósito de LionsSpace es resolver la orquestación lógica, la concurrencia y la verificación de presencia por software, sin asumir dependencias de infraestructura física que pongan en riesgo la entrega académica.

### 3.4 Funcionalidad futura (Fuera del semestre)
- **Módulo de navegación en interiores (Indoor Wayfinding):** Guía visual interactiva tipo mapa dentro del campus para orientar al alumno paso a paso hacia el cubículo asignado.

---

## 4. Tipo de sistema y restricciones

**Tipo de sistema:**  
Sistema de Información Web Transaccional e Interactivo en Tiempo Real (Arquitectura Cliente-Servidor).

**Por qué es de ese tipo:**  
Coordina el acceso concurrente a recursos físicos limitados mediante transacciones web inmediatas desde dispositivos móviles, garantizando que el estado de ocupación de las salas se mantenga sincronizado y consistente para todos los usuarios.

**Atributos de calidad que impone:**

| Atributo | Por qué importa en mi caso | Qué pasa si no se cumple |
|---|---|---|
| **Consistencia / Concurrencia (Integridad Transaccional)** | Varios estudiantes pueden intentar apartar la misma sala al terminar su clase de manera simultánea. | Se generan reservas dobles (*overbooking*), provocando conflictos entre alumnos y pérdida de confiabilidad en la plataforma. |
| **Autenticación y Seguridad (RBAC)** | Debe validar la identidad mediante la cuenta universitaria/app "Soy León" y aplicar permisos según el rol. | Usuarios ajenos a la universidad o cuentas no autorizadas podrían acaparar espacios o acceder a historiales confidenciales. |
| **Disponibilidad y Rendimiento en Horas Pico** | El sistema debe responder con baja latencia en los cambios de bloque escolar y horas de receso. | Si el sistema se cae o se degrada entre clases, los alumnos no pueden hacer *check-in* a tiempo y el espacio se libera incorrectamente. |
| **Resiliencia / Desacoplamiento** | Si la API externa de "Soy León" presenta fallos o latencia, el sistema no debe quedar inoperativo. | Una caída del servicio institucional bloquearía por completo el acceso a las salas de todo el campus. |

**Reglas de negocio identificadas:**

1. **RN-01 (Autenticación y Estatus Activo):** Todo usuario debe autenticarse con su cuenta institucional/app **"Soy León"**. El sistema valida que su estatus sea "Activo" para permitir crear reservas.
2. **RN-02 (Liberación Automática por Inasistencia):** La reserva otorga un tiempo de tolerancia de 10 minutos a partir de la hora de inicio. El usuario debe confirmar presencia realizando *check-in*; si no lo hace, al minuto 11 la sala se libera automáticamente al catálogo público.
3. **RN-03 (Límite de Ocupación por Usuario):** Un estudiante solo puede mantener 1 reserva activa o en curso a la vez, evitando el acaparamiento de múltiples cubículos.
4. **RN-04 (Inmutabilidad y Jerarquía del Historial):** Cada evento de reserva y *check-in* genera un registro inmutable en la bitácora. Los Administradores solo consultan los historiales de los edificios asignados, mientras que el alumno solo visualiza su historial personal.
5. **RN-05 (Mecanismo de Contingencia de Identidad):** Ante una indisponibilidad de la API externa de "Soy León", el sistema habilitará autenticación de respaldo validando el dominio del correo institucional.
