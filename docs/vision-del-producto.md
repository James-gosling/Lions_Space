Visión del producto
Autor: Rodrigo Valdespino Vertiz

Fecha de la última versión: 13 de agosto de 2026

Repositorio: [github.com/tu-usuario/LionsSpace](https://github.com/tu-usuario/LionsSpace)

1. Descripción del sistema
Nombre del sistema: LionsSpace

Descripción:

Es una aplicación web centralizada diseñada para que la comunidad universitaria busque, reserve y confirme el uso de cubículos de estudio, laboratorios y salas de trabajo en tiempo real. Permite visualizar la disponibilidad exacta de los espacios según la agenda de los alumnos, gestionando las reservaciones de forma transparente y asegurando que las instalaciones se aprovechen al máximo mediante una verificación de identidad y presencia integrada al ecosistema digital del campus.

2. Problema y usuarios
El problema:

Actualmente, los estudiantes pierden tiempo valioso buscando espacios disponibles durante sus horas libres o bloques sin clase, ya que los cubículos o salas suelen apartarse "de palabra" o se quedan vacíos por reservas no aprovechadas. Además, la administración no cuenta con visibilidad en tiempo real ni datos reales para controlar el acceso y uso efectivo de las instalaciones.

Cómo se resuelve hoy sin el sistema:

Los estudiantes caminan por los edificios buscando mesas o cubículos libres durante sus descansos, o dejan objetos personales sobre los muebles para "apartar" el lugar por horas sin estar presentes. En otros casos, deben formarse en recepción para anotarse manualmente en una bitácora de papel.

Usuarios del sistema:

Tipo de usuarioQué necesita del sistemaQué le preocupaEstudianteReservar un espacio de forma rápida entre sus clases, validar su acceso con su cuenta institucional y asegurar que el lugar estará disponible durante su tiempo libre.Perder sus horas libres buscando lugar o que alguien no autorizado esté ocupando el cubículo que reservó.Administrador de Campus / Encargado de EdificioMaximizar la ocupación real de los espacios, evitar "reservas fantasma" y garantizar que solo la comunidad universitaria verificada utilice las instalaciones.Que los espacios permanezcan desocupados mientras hay demanda y no tener un registro claro de quién está haciendo uso de las salas.
Un conflicto entre usuarios:

El Estudiante busca flexibilidad para extender su tiempo de estudio o cancelar a último minuto si le surge un contratiempo en sus horas de descanso, sin ser sancionado. El Administrador necesita aplicar reglas estrictas de liberación de espacios no utilizados y restringir el tiempo máximo de apartado por alumno para garantizar un flujo equitativo entre toda la comunidad.

3. Alcance
Dentro del alcance
Catálogo interactivo de espacios filtrable por capacidad, equipamiento (pantallas, pizarrón, contactos) y ubicación dentro del campus.

Módulo de reservas en tiempo real adaptado a las horas libres y huecos del horario escolar del estudiante.

Sistema de Check-in presencial mediante escaneo con la cámara del celular o validación cruzada con la app institucional "Soy León" y cuenta de correo universitario.

Panel de administración para gestión de salas, consulta de métricas de uso y control de asistencia/sanciones.

Explícitamente fuera del alcance
Automatización física de apertura de puertas mediante torniquetes o cerraduras electrónicas inteligentes (IoT).

Cobro o procesamiento de pagos por uso de espacios.

Módulo para préstamo y control de equipo físico (pantallas portátiles, plumones, cables HDMI).

Por qué queda fuera:

La integración con cerraduras físicas IoT y el cobro quedan fuera por la complejidad de hardware y el tiempo disponible en el semestre. El objetivo de LionsSpace es resolver la orquestación, disponibilidad y verificación de presencia, no la infraestructura física de acceso.

4. Tipo de sistema y restricciones
Tipo de sistema:

Web y SaaS (Sistema Web de Información en Tiempo Real).

Por qué es de ese tipo:

Requiere acceso concurrente e inmediato desde navegadores móviles sin forzar a instalar software adicional, sincronizando en tiempo real el estado de cada espacio para toda la comunidad.

Atributos de calidad que impone:

AtributoPor qué importa en mi casoQué pasa si no se cumpleConsistencia / ConcurrenciaVarios estudiantes pueden intentar apartar la misma sala al terminar su clase simultáneamente.Se generan reservas dobles (overbooking), provocando discusiones y malestar entre los alumnos.Autenticación y SeguridadDebe validar la identidad mediante la cuenta universitaria/app "Soy León".Usuarios ajenos a la universidad o cuentas falsas podrían acaparar espacios e impactar la seguridad del campus.Disponibilidad en Horas Libre/DescansoEl sistema debe responder sin demoras en los picos de cambio de bloque escolar y horas de receso.Si el sistema se cae entre clases, los alumnos no pueden hacer check-in y el espacio se libera incorrectamente.
Reglas de negocio que ya identifiqué:

Autenticación Obligatoria: Todo usuario debe iniciar sesión con su correo institucional y estar activo en el padrón universitario (o validado vía app "Soy León") para crear o unirse a una reserva.

Liberación por Inasistencia (Check-in Dual): La reserva otorga un tiempo de tolerancia de 10 minutos al iniciar el bloque libre. El usuario debe confirmar presencia escaneando el QR de la sala con su cámara o confirmando su llegada mediante el enlace integrado con Soy León; de lo contrario, la sala se libera automáticamente para otros estudiantes.

Límite de Ocupación por Ventana Libre: Un estudiante no puede mantener más de una reserva activa simultánea durante el mismo bloque de horario libre en un solo día.
