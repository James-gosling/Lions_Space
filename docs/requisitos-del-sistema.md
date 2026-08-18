# Especificación Formal de Requisitos del Sistema

## Encabezado y metadatos

| Campo | Valor |
|---|---|
| Proyecto | LionsSpace 🦁 |
| Autor | Rodrigo Valdespino Vertiz |
| Versión | v0.2.0 - Sprint Semanas 5-7 |
| Repositorio | https://github.com/James-gosling/Lions_Space |
| Estado | En Revisión por Dupla |

> **Norma de referencia:** Este documento se redacta bajo principios de especificación de requisitos de ISO/IEC/IEEE 29148, priorizando completitud, consistencia, verificabilidad y trazabilidad.

---

## 1. Técnicas de elicitación y validación de supuestos

### 1.1 Fuentes de información utilizadas

1. **Entrevistas simuladas estructuradas con cliente y dupla de revisión**
   - Objetivo: capturar objetivos de negocio, reglas operativas y criterios de éxito medibles.
   - Evidencia esperada: acuerdos sobre reglas de reserva, check-in, roles y auditoría.

2. **Observación directa de operación en campus (levantamiento contextual)**
   - Objetivo: identificar patrones de demanda, horarios pico, puntos de fricción y prácticas actuales de ocupación de espacios.
   - Evidencia esperada: validación de necesidad de control de concurrencia, liberación por inasistencia y registro auditable.

3. **Análisis de restricciones institucionales de integración**
   - Objetivo: asegurar alineación con autenticación federada y reutilización de la base de datos universitaria.
   - Evidencia esperada: prohibición explícita de duplicación de identidades y datos académicos en LionsSpace.

### 1.2 Supuesto crítico del proyecto

**Supuesto crítico:**
La base de datos universitaria y la app institucional **Soy León** proveen servicios de autenticación y validación de estatus académico con disponibilidad suficiente en horas operativas para soportar el flujo de reservas y check-in sin almacenamiento duplicado de identidades en LionsSpace.

**Impacto técnico si el supuesto es falso:**
- Imposibilidad de validar identidad/estatus en tiempo de operación.
- Interrupción de flujos RF01, RF02 y RF04 por dependencia externa.
- Necesidad de rediseño arquitectónico (mecanismos de contingencia, colas de sincronización o replicación parcial), con incremento de complejidad y riesgo de inconsistencia de datos.

**Impacto financiero si el supuesto es falso:**
- Costos adicionales de infraestructura y desarrollo por integración alternativa.
- Incremento de horas de implementación, pruebas de integración y soporte.
- Posible costo de licenciamiento/servicios externos para identidad y alta disponibilidad.

> **Criterio de validación del supuesto:** Ejecutar pruebas de integración en horarios pico simulados y confirmar cumplimiento de métricas RNF-02 y RNF-03 sin cache persistente de identidades.

---

## 2. Tabla de requisitos funcionales (RF)

| ID | Nombre del Requisito | Actor Principal | Descripción Formal | Criterio de Aceptación / Verificación | Prioridad |
|---|---|---|---|---|---|
| RF01 | Autenticación federada institucional | Estudiante | El sistema **DEBE** autenticar al estudiante mediante servicios institucionales (base de datos universitaria y/o app Soy León) sin crear cuentas locales duplicadas. | Se ejecuta prueba de inicio de sesión con credenciales válidas e inválidas; el acceso solo procede con validación institucional y no se crea registro de credencial local en LionsSpace. | Must Have |
| RF02 | Consulta de disponibilidad en tiempo real | Estudiante | El sistema **DEBE** mostrar disponibilidad de espacios por edificio, franja horaria, capacidad y equipamiento, considerando reservas confirmadas y en tolerancia de check-in. | En prueba con datos de carga, la vista refleja cambios de estado (disponible/reservado/liberado) dentro del tiempo definido en RNF-01 después de una operación de reserva o liberación. | Must Have |
| RF03 | Creación de reserva con control de solapamiento | Estudiante | El sistema **DEBE** crear reservas evitando doble asignación del mismo espacio y horario, y **DEBE** impedir más de una reserva activa por estudiante en el mismo bloque horario. | En prueba concurrente sobre mismo espacio/horario, solo una transacción resulta confirmada; intentos adicionales retornan rechazo controlado con motivo de conflicto. | Must Have |
| RF04 | Check-in dual con tolerancia de 10 minutos | Estudiante | El sistema **DEBE** permitir confirmar presencia mediante escaneo QR/cámara en sala o validación equivalente vía Soy León dentro de una ventana máxima de 10 minutos desde el inicio de la reserva. | En pruebas funcionales, check-in exitoso dentro de los 10 minutos cambia estado a "en uso"; intentos después de 10 minutos son rechazados y activan flujo de liberación. | Must Have |
| RF05 | Liberación automática por inasistencia | Administrador de Edificio | El sistema **DEBE** liberar automáticamente la reserva cuando no exista check-in válido dentro de la ventana de tolerancia y **DEBE** registrar el evento como inasistencia. | Prueba temporal controlada confirma que, al minuto 10 sin check-in, la reserva cambia a "liberada" y queda bitácora con marca temporal, usuario y motivo. | Must Have |
| RF06 | Gestión de roles y permisos RBAC | SuperAdmin TI | El sistema **DEBE** aplicar control de acceso por roles (Estudiante, Administrador de Edificio, SuperAdmin TI) restringiendo operaciones según privilegios definidos. | Matriz de pruebas de autorización verifica que cada rol solo accede a funciones permitidas; cualquier operación no autorizada devuelve código de denegación auditado. | Must Have |
| RF07 | Bitácora inmutable de auditoría | Administrador de Edificio | El sistema **DEBE** registrar de forma inmutable los eventos críticos (autenticación, reserva, check-in, liberación, cambios de rol y consultas administrativas). | Intentos de modificación/eliminación de eventos auditables desde interfaz o API son rechazados; se valida integridad y secuencia temporal de registros. | Should Have |
| RF08 | Consulta de historial segmentado | Administrador de Edificio | El sistema **DEBE** permitir al administrador consultar historial de su edificio y al estudiante consultar únicamente su historial personal. | Pruebas de autorización de lectura verifican segmentación por rol y alcance; no se exponen registros fuera del ámbito permitido. | Should Have |

---

## 3. Tabla de requisitos no funcionales y restricciones (RNF)

| ID | Categoría | Requisito No Funcional Cuantitativo | Métrica / Umbral | Verificación |
|---|---|---|---|---|
| RNF-01 | Concurrencia / Rendimiento | El sistema **DEBE** soportar operaciones de reserva y check-in en horas pico sin degradación funcional. | Mínimo **60 transacciones/segundo** sostenidas durante 15 minutos; percentil 95 de tiempo de respuesta **≤ 2.0 s** para operaciones de reserva/check-in. | Prueba de carga reproducible con datos de pico académico; reporte de throughput y latencia p95/p99. |
| RNF-02 | Seguridad y Privacidad | El sistema **DEBE** proteger autenticación y datos de sesión conforme a RBAC y cifrado de transporte/almacenamiento. | Tokens JWT con expiración máxima de **15 minutos** y refresh token de **8 horas**; comunicación obligatoria **TLS 1.2+**; cifrado en reposo **AES-256** para datos sensibles de auditoría. | Revisión de configuración de seguridad, pruebas de expiración de token y escaneo de endpoints para validación TLS. |
| RNF-03 | Disponibilidad y Confiabilidad | El sistema **DEBE** mantener continuidad operativa durante periodos académicos activos. | Disponibilidad mensual en horario operativo (06:00-22:00) **≥ 99.5%**; tasa de operaciones fallidas de reserva/check-in **< 1.0%** mensual. | Monitoreo de uptime y bitácora de errores; cálculo mensual con evidencia trazable. |
| RNF-04 | Usabilidad / Accesibilidad móvil | El sistema **DEBE** garantizar uso efectivo en dispositivos móviles por la población estudiantil. | Cumplimiento **WCAG 2.1 nivel AA** en pantallas críticas; tiempo de finalización de tarea "reservar + check-in" **≤ 120 s** para usuarios recurrentes en pruebas de usabilidad (n≥20). | Auditoría de accesibilidad y prueba moderada de tareas con medición cronometrada. |

> **Restricción transversal:** Se prohíbe duplicar la base de datos de identidad universitaria dentro de LionsSpace; toda verificación de identidad y estatus debe consumirse por integración institucional autorizada.

---

## 4. Matriz de trazabilidad y conflictos de interés

| RF | Objetivo del Estudiante | Objetivo del Administrador | Conflicto de Interés Identificado | Resolución Especificada |
|---|---|---|---|---|
| RF01 | Acceso inmediato con cuenta institucional | Controlar acceso exclusivo a usuarios válidos | Fricción entre rapidez de acceso y verificación estricta | Autenticación federada obligatoria con validación de estatus activo |
| RF02 | Visualizar espacios disponibles en tiempo real | Evitar ocupación no controlada o datos desactualizados | El estudiante requiere disponibilidad instantánea; el administrador requiere consistencia operativa | Actualización de estado en tiempo real con control de concurrencia (RNF-01) |
| RF03 | Reservar el espacio deseado sin rechazo arbitrario | Impedir overbooking y monopolio de espacios | Preferencia individual vs equidad y control institucional | Reglas de unicidad por espacio/horario y límite de reserva simultánea |
| RF04 | Tolerancia razonable para confirmar llegada | Confirmar uso real del recurso asignado | Flexibilidad de llegada vs evidencia verificable de presencia | Check-in dual obligatorio con ventana de 10 minutos |
| RF05 | Mantener reserva aun con retraso | Reasignar espacios no utilizados | Interés de retención vs optimización de ocupación | Liberación automática al minuto 10 sin check-in válido |
| RF06 | Mantener privacidad de su información | Acceso administrativo suficiente para operación | Privacidad individual vs supervisión operativa | RBAC estricto por rol y función |
| RF07 | Transparencia sobre su historial | Evidencia legal/operativa no alterable | Posible disputa sobre sanciones o asistencias | Bitácora inmutable con sellado temporal de eventos |
| RF08 | Consultar solo su propio historial | Consultar solo edificios asignados | Riesgo de acceso cruzado no autorizado | Segmentación de historial por alcance y rol |

---

## 5. Caso de uso crítico (formato estructurado)

### CU-01: Reservar Espacio y Completar Check-in Dual

| Campo | Especificación |
|---|---|
| ID | CU-01 |
| Nombre | Reservar Espacio y Completar Check-in Dual |
| Actor principal | Estudiante |
| Actores secundarios | Servicio de autenticación institucional, App Soy León, Sistema de auditoría |
| Propósito | Asegurar la asignación válida de un espacio y confirmar su uso real mediante check-in dual dentro de tolerancia definida. |
| Disparador | El estudiante requiere un espacio de estudio para una franja horaria disponible. |
| Precondiciones | (1) Usuario autenticado por federación institucional. (2) Estatus académico activo validado. (3) Espacio objetivo disponible. |
| Postcondición de éxito | Reserva activa en estado "en uso" con check-in confirmado y evento registrado en bitácora inmutable. |
| Postcondición de fallo | Reserva liberada automáticamente por inasistencia y evento auditado con motivo de timeout. |

#### Flujo principal

1. El estudiante accede al módulo de búsqueda de espacios.
2. El sistema presenta espacios disponibles por edificio, horario, capacidad y equipamiento.
3. El estudiante selecciona un espacio y una franja horaria.
4. El sistema valida reglas de negocio (sin solapamiento y sin doble reserva activa en el mismo bloque).
5. El sistema crea la reserva con estado "pendiente de check-in" y registra evento de auditoría.
6. Al iniciar la franja reservada, el estudiante ejecuta check-in por una de dos vías válidas:
   - 6.1 Escaneo de QR/cámara en la sala.
   - 6.2 Validación de presencia mediante integración con Soy León.
7. El sistema verifica que el check-in ocurra dentro de los 10 minutos de tolerancia.
8. El sistema actualiza estado a "en uso" y registra evento de confirmación en bitácora.

#### Flujos alternos

**A1: Espacio ya no disponible por concurrencia**
- A1.1 En el paso 4, el sistema detecta conflicto de reserva simultánea.
- A1.2 El sistema rechaza la transacción y muestra disponibilidad actualizada.
- A1.3 El caso de uso finaliza sin reserva creada.

**A2: Check-in fuera de ventana de tolerancia (timeout 10 min)**
- A2.1 En el paso 7, el sistema detecta check-in posterior al minuto 10.
- A2.2 El sistema rechaza el check-in por expiración de tolerancia.
- A2.3 El sistema ejecuta liberación automática de la reserva.
- A2.4 El sistema registra inasistencia en bitácora inmutable.
- A2.5 El caso de uso finaliza con postcondición de fallo.

**A3: Inasistencia total (sin intento de check-in)**
- A3.1 Transcurridos 10 minutos desde inicio de reserva, no existe evidencia de check-in.
- A3.2 El sistema libera automáticamente el espacio.
- A3.3 El sistema registra evento de inasistencia y disponibilidad recuperada.

#### Reglas de negocio aplicadas en CU-01

- RB-01: Identidad institucional válida y estatus académico activo.
- RB-02: Un único titular por reserva de espacio-horario.
- RB-03: Ventana máxima de check-in de 10 minutos desde inicio de reserva.
- RB-04: Toda transición de estado genera evento de auditoría inmutable.

#### Criterios de aceptación del caso de uso

1. En escenario exitoso, la reserva pasa por estados `pendiente de check-in` → `en uso` y quedan dos eventos auditables mínimos (creación + confirmación).
2. En escenarios A2 y A3, la reserva pasa a `liberada` al exceder tolerancia y queda evento de inasistencia con marca temporal verificable.
3. Ningún actor sin permisos RBAC válidos puede crear reserva, confirmar check-in ni consultar bitácoras fuera de su ámbito.
