# 🦁 LionsSpace — **Convierte tus horas libres en tiempo productivo**
![Estado](https://img.shields.io/badge/Estado-En%20desarrollo-orange)
![Enfoque](https://img.shields.io/badge/Enfoque-Experiencia%20Universitaria-blue)

> **LionsSpace** ayuda a la comunidad universitaria a encontrar, reservar y usar cubículos y espacios de estudio de forma justa, segura y en tiempo real.

---

## 2. El Problema en el Campus

En el día a día universitario, muchos estudiantes tienen **huecos entre clases** que podrían aprovechar para estudiar, avanzar proyectos o reunirse en equipo. Hoy se enfrentan a una realidad frustrante:

- Se pierde tiempo buscando lugares disponibles.
- Existen apartados informales de espacios (por ejemplo, con mochilas) que afectan la equidad.
- La administración no cuenta con información clara para mejorar la ocupación y el uso de los espacios.

> Resultado: menos productividad estudiantil y recursos físicos subutilizados.

---

## 3. La Solución (LionsSpace 🦁)

LionsSpace transforma esta experiencia con tres pilares clave:

- **📍 Reserva en tiempo real:** visualiza disponibilidad y aparta espacios de estudio cuando realmente los necesitas.
- **🆔 Verificación de identidad con “Soy León”:** acceso confiable para miembros de la comunidad institucional.
- **✅ Liberación automática por check-in:** si no confirmas tu llegada, el espacio se libera para otra persona y se evitan “espacios fantasma”.

---

## 4. Impacto y Beneficios de Negocio

### Para los Estudiantes
- Mejor aprovechamiento de horas libres.
- Menos estrés y menos tiempo perdido buscando lugar.
- Mayor acceso justo a espacios de estudio y colaboración.

### Para la Universidad / Administración
- Optimización de cubículos y áreas comunes.
- Mayor orden, trazabilidad y seguridad en el uso de espacios.
- Información útil para tomar mejores decisiones de operación y crecimiento.

---

## 5. Problemas y Riesgos Potenciales

1. **Riesgo de acceso y autorización (Soy León)**
   - La universidad puede negar el acceso a API/base de datos o permitir solo campos muy limitados.
   - La aprobación puede demorarse por revisiones legales y de seguridad.

2. **Privacidad y cumplimiento legal**
   - Los datos estudiantiles y académicos son sensibles.
   - Puede requerirse permiso explícito, anonimización y manejo estricto de datos personales.

3. **Ausencia de una interfaz oficial de integración**
   - Si Soy León no tiene API pública, la integración puede depender de exportaciones manuales o soluciones inestables.
   - Cambios en Soy León pueden romper la integración.

4. **Calidad y consistencia de datos**
   - Los datos externos pueden estar incompletos, desactualizados, duplicados o en formatos distintos.
   - El mapeo de identificadores entre sistemas (ID de estudiante, ID de materia, etc.) puede fallar.

5. **Dependencia de actores externos**
   - El avance depende de respuestas del área de TI/administración de la universidad, no solo del ritmo de desarrollo.
   - Esto puede bloquear hitos de forma inesperada.

6. **Limitaciones de entorno para desarrollo/pruebas**
   - Puede no existir un entorno sandbox o de pruebas.
   - Probar con datos reales de producción puede estar restringido o prohibido.

7. **Requisitos de seguridad fuera del alcance inicial**
   - Si la integración es aprobada, puede exigir cifrado, auditoría, control por roles y revisiones de seguridad.
   - Esto incrementa la complejidad y el tiempo de implementación.

8. **Mantenimiento y soporte a largo plazo inciertos**
   - Si LionsSpace depende fuertemente de Soy León y cambian las políticas, funcionalidades clave podrían dejar de operar.
   - Futuros mantenedores pueden no contar con los mismos permisos de acceso.

9. **Riesgo de cronograma para entrega académica**
   - La negociación con la universidad puede tomar más tiempo que el semestre o calendario del proyecto.
   - Puede ser necesario implementar funcionalidades de respaldo no dependientes de Soy León.

10. **Riesgo de comunicación y documentación**
    - Los requerimientos de la universidad pueden ser ambiguos o cambiar con el tiempo.
    - La falta de documentación técnica formal de Soy León puede generar retrabajo.

---

## 6. Navegación del Repositorio

Para conocer la documentación formal de la materia y la visión completa del producto, consulta:

- **`docs/vision-del-producto.md`**
