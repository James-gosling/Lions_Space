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
**Aplicación Web Transaccional estructurada en Arquitectura de 3 Capas**.  
El sistema desacopla sus responsabilidades para mantener modularidad, mantenibilidad y seguridad:
1. **Capa de Presentación (Frontend):** Interfaz web ligera adaptada a navegadores móviles y de escritorio, orientada a capturar las solicitudes del usuario y mostrar el estado de disponibilidad.
2. **Capa de Negocio / Lógica de Aplicación (API Backend):** Servicios y endpoints de API que centralizan la ejecución de las reglas de negocio (políticas de cancelación, márgenes de tolerancia, control de accesos y prevención de colisiones de reserva). Esta capa orquesta las comunicaciones y llamadas hacia servicios externos como la API de identidad institucional.
3. **Capa de Datos (Persistencia):** Base de datos relacional encargada del almacenamiento y la consistencia transaccional (ACID) del estado de los espacios compartidos.

### 4.2 Atributos de calidad impuestos

| Atributo de calidad | Por qué importa en este sistema | Impacto si no se cumple |
|---|---|---|
| **Consistencia / Integridad Transaccional (ACID)** | Múltiples alumnos intentan reservar el mismo espacio simultáneamente durante los cambios de clase. | Se producen dobles reservas (*overbooking*), derivando en conflictos entre estudiantes y pérdida de confiabilidad en el sistema. |
| **Autenticación y Seguridad (RBAC)** | Valida identidad universitaria y restringe privilegios según el tipo de usuario. | Usuarios ajenos al campus podrían reservar espacios o acceder a reportes e historiales confidenciales. |
| **Rendimiento y Disponibilidad en Picos** | El sistema debe atender altas cargas de solicitudes en los cambios de hora lectiva con baja latencia. | Si la plataforma se degrada, los estudiantes no pueden ejecutar su *check-in* a tiempo y el sistema cancela reservas válidas por error. |
| **Resiliencia y Desacoplamiento** | Si la API externa de "Soy León" experimenta caídas o latencias altas, el sistema debe seguir operando. | Una falla del proveedor institucional de identidad bloquearía por completo las reservaciones de todo el campus. |

### 4.3 Reglas de negocio identificadas
* **RN-01 (Elegibilidad de Usuario):** La creación de apartados está restringida exclusivamente a alumnos con matrícula activa y sesión validada en la plataforma.
* **RN-02 (Cupo Máximo de Reservas Simultáneas):** Cada estudiante puede mantener únicamente una reservación activa en el sistema. Para agendar un nuevo bloque temporal, la reserva previa debe haberse completado, cancelado o liberado.
* **RN-03 (Confirmación de Llegada y Liberación por Inasistencia):** El usuario cuenta con un margen estricto de 10 minutos a partir del inicio del horario agendado para registrar su asistencia en el sistema. Transcurrido este periodo sin confirmación (*check-in*), la reserva se anula automáticamente y el cubículo queda disponible de inmediato para el resto de la comunidad.
* **RN-04 (Trazabilidad y Segregación de Auditoría):** Cualquier cambio de estado en un espacio (creación, confirmación, cancelación o liberación por inasistencia) genera un registro inmutable. El acceso de lectura a estos registros queda delimitado por la zona o edificio bajo la responsabilidad de cada rol administrativo.
* **RN-05 (Autenticación en Modo de Contingencia):** Si el servicio de identidad central ("Soy León") presenta indisponibilidad o degradación, la plataforma conmuta a un mecanismo de validación alterno basado en credenciales de correo electrónico institucional, garantizando la continuidad operativa del sistema de reservas.

---

## 5. Ciclo de vida elegido

### 5.1 Modelo seleccionado
**Metodología Ágil Incremental (Sprints de 2 semanas)**.

### 5.2 Justificación basada en el alcance y restricciones

1. **Gestión de requisitos cambiantes en el tiempo:**  
   En un contexto escolar y de validación con usuarios reales en campus, los requisitos no pueden darse por sentados ni congelarse desde el inicio. A medida que alumnos y administradores interactúan con las primeras versiones, surgen retroalimentaciones y cambios en tolerancias de tiempo, criterios de reserva o vistas de catálogo. El enfoque ágil incremental permite asimilar estas modificaciones vuelta tras vuelta sin que representen una crisis para el cronograma.

2. **Ejecución completa de actividades por iteración:**  
   En lugar de dividir el proyecto en fases secuenciales que postergan la integración al final del semestre, cada ciclo de 1 a 2 semanas ejecuta de forma completa las cuatro actividades esenciales: **Especificar, Diseñar, Construir y Validar**. Cada iteración produce una porción de software terminado y evaluable (Incremento 1: Catálogo y consulta; Incremento 2: Reserva transaccional; Incremento 3: Módulo de *check-in* y anulación automática).

3. **Software funcionando sobre documentación rígida:**  
   Al ser una herramienta orientada a pocas pantallas clave y flujos específicos, la prioridad es contar con código que funcione y pruebe la lógica de negocio directamente en el navegador, minimizando la carga burocrática de modelos teóricos pesados.

4. **Respuesta ante el cambio y mitigación de dependencias:**  
   Frente a la dependencia de servicios externos (API "Soy León"), un ciclo ágil permite construir de inmediato un mecanismo simulado (*mock*) en las primeras iteraciones para no frenar la construcción de la lógica transaccional, integrando el servicio real cuando los accesos institucionales queden autorizados.

5. **Por qué se descartan otros modelos:**
   * **Modelo en Cascada:** Asume que todo se puede saber de antemano; cualquier cambio tardío o bloqueo técnico en la integración externa impediría llegar a tiempo con un sistema entregable.
   * **Modelos Orientados a Riesgos (Espiral / Proceso Unificado) y Prototipado Desechable:** Fueron diseñados para proyectos de infraestructura crítica de gran envergadura o interfaces experimentales complejas; en este contexto escolar añaden una carga documental innecesaria y artefactos descartables que restan tiempo a la entrega de valor incremental.
