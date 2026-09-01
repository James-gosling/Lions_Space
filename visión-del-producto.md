# Visión del producto: LionsSpace

**Autor:** Rodrigo Valdespino Vertiz  
**Fecha:** Septiembre 2026  
**Repositorio:** https://github.com/James-gosling/Lions_Space

---

## 1. Descripción del sistema

**Nombre del sistema:** LionsSpace

**Descripción:**  
LionsSpace es una aplicación web centralizada diseñada para que la comunidad universitaria busque, reserve y confirme el uso de cubículos y salas de estudio en tiempo real. Su función principal es coordinar la disponibilidad de los espacios físicos en función de los horarios y horas libres de los estudiantes, previniendo el apartado informal y los traslapes de reserva mediante un mecanismo de validación de identidad institucional ("Soy León") y confirmación de presencia (*check-in*).

---

## 2. Problema y usuarios

**El problema:**  
Los estudiantes pierden tiempo lectivo buscando cubículos desocupados durante sus horas libres debido a la falta de información centralizada y al uso de apartados informales con objetos personales. Simultáneamente, las salas permanecen desaprovechadas por "reservas fantasma" (apartados sin asistencia), mientras que la administración carece de métricas de ocupación e historiales confiables para auditar el uso de las instalaciones.

**Cómo se resuelve hoy sin el sistema:**  
Los alumnos recorren manualmente los pasillos y edificios esperando encontrar un lugar vacío, apartan mesas dejando mochilas o chamarras por horas sin ocuparlas, o deben acudir a una recepción para registrarse manualmente en bitácoras físicas de papel.

**Usuarios del sistema:**

| Tipo de usuario | Qué necesita del sistema | Qué le preocupa |
|---|---|---|
| **Estudiante** | Reservar un espacio de forma rápida entre sus clases, validar su acceso con su cuenta institucional y asegurar la disponibilidad del cubículo en su tiempo libre. | Perder sus horas libres buscando lugar o encontrar su espacio apartado ocupado por otra persona sin autorización. |
| **Administrador de Espacios / Edificio** | Maximizar el aprovechamiento de los cubículos, erradicar reservas fantasma y auditar el historial de ocupación mediante un panel centralizado. | Que las salas queden desocupadas habiendo demanda y no tener registros confiables de los usuarios que utilizan la infraestructura. |
| **SuperAdmin (TI Campus)** | Integrar el sistema de forma segura con los servicios existentes de la universidad ("Soy León" / SSO) evitando duplicidad de bases de datos. | Vulnerabilidades de seguridad en autenticación, degradación de servicios institucionales y asignación incorrecta de roles de acceso. |

**Conflicto entre usuarios:**  
El **Estudiante** busca flexibilidad para cancelar a último minuto si le surge un imprevisto sin recibir sanciones. El **Administrador** requiere aplicar liberaciones automáticas estrictas por inasistencia y restringir reservas simultáneas para asegurar una rotación equitativa de los espacios para toda la comunidad.

---

## 3. Alcance

### 3.1 Dentro del alcance (Inclusiones con verbos verificables)
1. **Autentica** la identidad y estatus activo de los usuarios mediante la integración con la cuenta institucional "Soy León" o un módulo de respaldo desacoplado.
2. **Despliega** el catálogo de cubículos disponibles por edificio, mostrando su capacidad máxima y equipamiento fijo (pizarrón, pantallas fijas, conexiones eléctricas) para la fecha y hora seleccionadas.
3. **Registra** reservaciones de franjas horarias específicas vinculando la matrícula del solicitante con la sala, aplicando bloqueos transaccionales para impedir solapamientos.
4. **Habilita y procesa** el *check-in* digital de presencia dentro de una ventana de tolerancia temporal que inicia al comenzar el bloque reservado.
5. **Cancela y libera** automáticamente el cubículo al inventario público cuando expira el tiempo límite de tolerancia sin confirmación de llegada.
6. **Aplica control de acceso por roles (RBAC)** restringiendo permisos e interfaces para Estudiantes, Administradores y SuperAdmin.
7. **Genera** reportes y métricas de ocupación e historial de uso para auditoría administrativa.

### 3.2 Explícitamente fuera del alcance (Exclusiones)
1. **No controla cerraduras electrónicas ni detecta presencia física con hardware IoT:** El sistema no opera chapas magnéticas, torniquetes ni cámaras de presencia física.
2. **No procesa cobros ni transacciones monetarias:** No incluye pasarelas de pago, cobro de rentas ni penalizaciones económicas.
3. **No gestiona el inventario ni préstamo de equipo físico móvil:** No rastrea plumones, cables adaptadores ni monitores portátiles.
4. **No escribe ni altera datos escolares en sistemas centrales:** No modifica calificaciones, inscripciones ni expedientes académicos en las bases de datos de "Soy León" o SIU.

### 3.3 Justificación de exclusiones
La exclusión de cerraduras electrónicas, sensores IoT y pasarelas de pago responde a la necesidad de eliminar dependencias externas de hardware, compras y tiempos de instalación en instalaciones físicas que comprometerían la entrega técnica durante el semestre. LionsSpace se enfoca en resolver la orquestación lógica, la concurrencia y la equidad de acceso mediante software transaccional.

### 3.4 Funcionalidad futura (Backlog fuera del semestre)
* **Módulo de navegación interior (Indoor Wayfinding):** Guía interactiva paso a paso mediante mapa 2D/3D del campus para dirigir al estudiante desde su ubicación hacia el cubículo asignado.

---

## 4. Tipo de sistema y restricciones

### 4.1 Tipo de sistema
**Sistema de Información Web Transaccional e Interactivo en Tiempo Real** bajo arquitectura cliente-servidor. Opera sincronizando estados sobre recursos físicos compartidos y finitos mediante transacciones concurrentes desde navegadores web móviles.

### 4.2 Atributos de calidad impuestos

| Atributo de calidad | Por qué importa en este sistema | Impacto si no se cumple |
|---|---|---|
| **Consistencia / Integridad Transaccional (ACID)** | Múltiples alumnos intentan reservar el mismo espacio simultáneamente durante los cambios de clase. | Se producen dobles reservas (*overbooking*), derivando en conflictos entre estudiantes y pérdida de confiabilidad en el sistema. |
| **Autenticación y Seguridad (RBAC)** | Valida identidad universitaria y restringe privilegios según el tipo de usuario. | Usuarios ajenos al campus podrían reservar espacios o acceder a reportes e historiales confidenciales. |
| **Rendimiento y Disponibilidad en Picos** | El sistema debe atender altas cargas de solicitudes en los cambios de hora lectiva con baja latencia. | Si la plataforma se degrada, los estudiantes no pueden ejecutar su *check-in* a tiempo y el sistema cancela reservas válidas por error. |
| **Resiliencia y Desacoplamiento** | Si la API externa de "Soy León" experimenta caídas o latencias altas, el sistema debe seguir operando. | Una falla del proveedor institucional de identidad bloquearía por completo las reservaciones de todo el campus. |

### 4.3 Reglas de negocio identificadas
* **RN-01 (Membresía Activa):** Únicamente los usuarios validados con estatus académico activo pueden crear reservaciones.
* **RN-02 (Ventana de Tolerancia para Check-in):** El check-in se habilita al minuto 0 de la reserva y expira al minuto 10. Si al minuto 11 no hay confirmación, el sistema ejecuta la cancelación automática y devuelve el espacio al catálogo público.
* **RN-03 (Límite de Concurrencia por Usuario):** Un estudiante solo puede tener una única reserva activa o en curso a la vez para impedir el acaparamiento.
* **RN-04 (Inmutabilidad de la Bitácora de Auditoría):** Todo evento de reserva, confirmación o cancelación genera un registro inmutable. Los administradores solo pueden auditar los edificios asignados a su rol.
* **RN-05 (Mecanismo de Contingencia de Identidad):** Ante la falta de respuesta de la API externa de "Soy León", el sistema activa un modo de contingencia que valida el acceso mediante el dominio de correo electrónico institucional.

---

## 5. Ciclo de vida elegido

### 5.1 Modelo seleccionado
**Modelo Iterativo e Incremental orientado a Riesgos (derivado del Proceso Unificado y Prototipado Rápido)**.

### 5.2 Justificación basada en el alcance y restricciones
1. **Gestión de riesgos de integración externa:** La dependencia de la API de "Soy León" y las autorizaciones institucionales representan un riesgo crítico de cronograma. Un modelo iterativo permite construir un primer incremento con un mecanismo de autenticación simulado (Mock / Fallback) para avanzar en la lógica transaccional de reservas sin quedar bloqueados por aprobaciones de terceros.
2. **Validación temprana de usabilidad móvil y concurrencia:** El flujo de reserva y *check-in* en cambios de hora exige probar la interacción en dispositivos móviles con usuarios reales desde fases tempranas, ajustando la interfaz antes de la entrega final.
3. **Por qué no Cascada:** Un modelo secuencial lineal asume requisitos completamente fijos y congelados desde el inicio. Si se aplicara Cascada y los permisos de la API institucional se demoran o modifican en semanas avanzadas, el costo del cambio obligaría a rehacer la fase de diseño e implementación completa al final del semestre.
