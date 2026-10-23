# Spec 001 — MVP de habits-cli

Estado: borrador para revisión.

## 1. Contexto y objetivo

Quien estudia de forma autodidacta suele abandonar sus hábitos por falta de una señal visible de constancia. Esta primera funcionalidad ofrece lo mínimo para sostener esa señal: registrar hábitos de estudio, anotar cada día que se cumplieron y ver cuántos días seguidos lleva cada uno (la **racha**).

**Objetivo:** que una persona pueda crear un hábito, marcarlo como hecho hoy y consultar en cualquier momento la racha de cada uno de sus hábitos, con datos que sobreviven entre usos.

**Qué significa "éxito":** tras una semana de uso real, la racha mostrada coincide siempre con la que la persona calcularía a mano.

## 2. Usuarios

- **Estudiante individual (usuario principal):** usa la herramienta a diario desde la terminal, en su propio equipo, sin cuenta ni conexión. Maneja entre 1 y unas pocas decenas de hábitos.
- **Desarrollador junior que mantiene el proyecto (usuario secundario):** necesita que el comportamiento esté descrito sin ambigüedades para poder verificarlo con pruebas.

## 3. Historias de usuario

- **HU-1.** Como estudiante, quiero crear un hábito con un nombre, para empezar a seguirlo.
- **HU-2.** Como estudiante, quiero marcar un hábito como hecho hoy, para dejar constancia de que lo cumplí.
- **HU-3.** Como estudiante, quiero ver todos mis hábitos con su racha actual, para saber cuáles llevo bien y cuáles estoy a punto de perder.
- **HU-4.** Como estudiante, quiero que equivocarme (nombre repetido, hábito inexistente, marcar dos veces) me dé un mensaje claro y no estropee mis datos.

## 4. Requisitos funcionales

Notación EARS: *El sistema deberá…* (ubicuo); *Cuando…, el sistema deberá…* (evento); *Mientras…, el sistema deberá…* (estado); *Si…, entonces el sistema deberá…* (comportamiento no deseado).

### RF-1 Crear un hábito (HU-1)
- **RF-1.1** Cuando la persona solicite crear un hábito con un nombre válido y no existente, el sistema deberá registrarlo sin marcas de cumplimiento y confirmarlo con un mensaje.
- **RF-1.2** Si el nombre solicitado ya corresponde a un hábito existente (según RF-2), entonces el sistema deberá rechazar la creación, informar que el hábito ya existe y dejar los datos sin cambios.
- **RF-1.3** Si el nombre está vacío o contiene solo espacios, entonces el sistema deberá rechazar la creación e informar que el nombre es obligatorio.

### RF-2 Identidad de los nombres (HU-1, HU-2, HU-4)
- **RF-2.1** El sistema deberá considerar iguales dos nombres que difieran solo en mayúsculas/minúsculas o en espacios al inicio y al final (p. ej. "Leer", "leer" y " leer " son el mismo hábito).
- **RF-2.2** El sistema deberá considerar distintos dos nombres que difieran en tildes o en espacios internos (p. ej. "leer" y "léer").
- **RF-2.3** El sistema deberá mostrar cada hábito con el nombre tal como se escribió al crearlo, recortando espacios extremos.

### RF-3 Marcar un hábito como hecho hoy (HU-2, HU-4)
- **RF-3.1** Cuando la persona marque como hecho un hábito existente que no tiene marca de hoy, el sistema deberá registrar la fecha de hoy para ese hábito y confirmarlo con un mensaje.
- **RF-3.2** Cuando la persona marque como hecho un hábito que ya tiene marca de hoy, el sistema deberá informar que ya estaba marcado hoy, terminar con éxito y no duplicar el registro ni alterar la racha.
- **RF-3.3** Si el hábito indicado no existe, entonces el sistema deberá informar que no existe, terminar con error y dejar los datos sin cambios.
- **RF-3.4** El sistema deberá permitir marcar únicamente la fecha de hoy.

### RF-4 Listar hábitos con su racha (HU-3)
- **RF-4.1** Cuando la persona solicite el listado, el sistema deberá mostrar, por cada hábito, su nombre y su racha actual en días.
- **RF-4.2** Cuando la persona solicite el listado y no existan hábitos, el sistema deberá informarlo con un mensaje que indique cómo crear el primero y terminar con éxito.
- **RF-4.3** Mientras un hábito tenga marca de hoy, el sistema deberá indicar en el listado que ya está hecho hoy.
- **RF-4.4** Mientras un hábito no tenga marca de hoy, el sistema deberá indicar en el listado que está pendiente hoy.
- **RF-4.5** El sistema deberá mostrar los hábitos en un orden estable y predecible entre ejecuciones. [NECESITA ACLARACIÓN: ¿orden de creación o alfabético?]

### RF-5 Cálculo de la racha (HU-3)
- **RF-5.1** El sistema deberá definir la racha actual como la cantidad de días consecutivos con marca que terminan hoy, o terminan ayer si hoy todavía no está marcado.
- **RF-5.2** Mientras hoy no esté marcado y ayer sí lo esté, el sistema deberá mantener la racha viva (contando hasta ayer) durante todo el día de hoy.
- **RF-5.3** Mientras hoy y ayer no estén marcados, el sistema deberá mostrar una racha de 0.
- **RF-5.4** Mientras un hábito no tenga ninguna marca, el sistema deberá mostrar una racha de 0.
- **RF-5.5** Cuando existan huecos entre marcas, el sistema deberá contar solo la secuencia consecutiva más reciente que cumpla RF-5.1.
- **RF-5.6** El sistema deberá contar cada día calendario como máximo una vez, aunque se haya marcado varias veces.

### RF-6 Persistencia (HU-1, HU-2, HU-3)
- **RF-6.1** El sistema deberá conservar los hábitos y sus marcas entre ejecuciones sucesivas.
- **RF-6.2** Si una operación falla, entonces el sistema deberá conservar los datos tal como estaban antes de la operación.
- **RF-6.3** Si los datos guardados no se pueden leer, entonces el sistema deberá informar del problema con un mensaje claro, terminar con error y no sobrescribir los datos existentes.

### RF-7 Mensajes y resultado de las operaciones (HU-4)
- **RF-7.1** El sistema deberá mostrar todos los mensajes a la persona en español.
- **RF-7.2** Cuando una operación termine correctamente, el sistema deberá finalizar con un resultado de éxito.
- **RF-7.3** Si una operación se rechaza por entrada inválida o inexistente (RF-1.2, RF-1.3, RF-3.3), entonces el sistema deberá finalizar con un resultado de error distinto del de éxito.
- **RF-7.4** El sistema no deberá mostrar a la persona trazas técnicas ni mensajes internos en ningún caso de error previsto.

## 5. Requisitos no funcionales

- **RNF-1 Idioma:** mensajes en español; identificadores y comentarios en inglés (constitución, principio 6).
- **RNF-2 Privacidad y conectividad:** todo funciona sin conexión; los datos permanecen en el equipo de la persona.
- **RNF-3 Rendimiento:** con 100 hábitos y 5 años de marcas diarias, cada operación responde en menos de 1 segundo en un equipo de uso doméstico.
- **RNF-4 Integridad:** una interrupción durante una operación no deja los datos a medias ni corruptos.
- **RNF-5 Verificabilidad:** las reglas de racha deben poder comprobarse con cualquier fecha de "hoy", sin esperar días reales.
- **RNF-6 Mantenibilidad:** el comportamiento debe poder entenderse y modificarse por un desarrollador junior (constitución, preámbulo).
- **RNF-7 Estabilidad de datos:** los datos creados con esta versión deben seguir siendo legibles por versiones futuras salvo que una spec posterior indique lo contrario.

## 6. Casos límite

| # | Situación | Comportamiento esperado |
|---|-----------|-------------------------|
| CL-1 | Crear "Leer" cuando existe "leer" | Rechazo por duplicado (RF-1.2, RF-2.1) |
| CL-2 | Crear con nombre vacío o solo espacios | Rechazo por nombre obligatorio (RF-1.3) |
| CL-3 | Marcar un hábito inexistente | Error, sin cambios (RF-3.3) |
| CL-4 | Marcar dos veces el mismo día | Aviso, sin error, racha intacta (RF-3.2) |
| CL-5 | Marcado ayer, no hoy | Racha viva hasta ayer (RF-5.2) |
| CL-6 | Marcado hace dos días o más, no ayer ni hoy | Racha 0 (RF-5.3) |
| CL-7 | Hábito recién creado sin marcas | Racha 0, pendiente hoy (RF-5.4, RF-4.4) |
| CL-8 | Marcas de lunes, martes y jueves; hoy es jueves | Racha 1: el hueco del miércoles rompe la secuencia (RF-5.5) |
| CL-9 | Marcar a las 23:59 y de nuevo a las 00:01 | Se cuentan como dos días distintos; el día lo define la fecha local del equipo |
| CL-10 | Listado sin hábitos | Mensaje guía, éxito (RF-4.2) |
| CL-11 | Datos guardados ilegibles | Error claro, sin sobrescribir (RF-6.3) |
| CL-12 | Nombres con tildes o con "ñ" | Se aceptan; "leer" ≠ "léer" (RF-2.2) |
| CL-13 | El reloj del equipo retrocede y existen marcas con fecha posterior a "hoy" | [NECESITA ACLARACIÓN: ¿se ignoran esas marcas futuras para la racha o se rechaza el uso?] |

## 7. Fuera de alcance

Queda fuera de esta spec y se tratará, si procede, en specs futuras:

- Borrar o renombrar hábitos.
- Marcar fechas pasadas o futuras, y desmarcar.
- Mejor racha histórica, estadísticas y gráficos.
- Frecuencias distintas de la diaria, días de descanso y metas semanales.
- Recordatorios y notificaciones.
- Varios usuarios, sincronización entre equipos y copias de seguridad.
- Exportar o importar datos.
- Categorías, etiquetas, notas o descripciones de los hábitos.

## 8. Criterios de finalización

La funcionalidad se considera terminada cuando:

1. Cada criterio RF-1 a RF-7 tiene al menos una verificación automatizada que pasa.
2. Cada caso límite CL-1 a CL-12 está cubierto por una verificación automatizada.
3. `pytest -q` pasa completo.
4. Un recorrido manual funciona: crear dos hábitos, marcar uno, listar (racha 1 y estado correcto en ambos), cerrar y repetir el listado en una ejecución nueva con el mismo resultado.
5. Todos los mensajes y errores previstos están en español y sin trazas técnicas.
6. Las dudas abiertas de la sección 9 están resueltas o aplazadas de forma explícita por la persona responsable.
7. La implementación respeta `docs/constitution.md`.

## 9. Dudas abiertas

- **[NECESITA ACLARACIÓN] D-1 Nombres de los comandos:** esta spec describe las operaciones (crear, marcar, listar) pero no fija cómo se invocan ni su vocabulario exacto en español.
- **[NECESITA ACLARACIÓN] D-2 Orden del listado** (RF-4.5): ¿orden de creación, alfabético o por racha?
- **[NECESITA ACLARACIÓN] D-3 Marcas futuras por retroceso del reloj** (CL-13).
- **[NECESITA ACLARACIÓN] D-4 Longitud máxima del nombre** y si se admiten caracteres especiales o saltos de línea.
- **[NECESITA ACLARACIÓN] D-5 Formato exacto del listado:** cómo se presenta la racha ("3 días", "1 día", "0 días") y el estado de hoy.
- **[NECESITA ACLARACIÓN] D-6 Cambio de día con la herramienta en uso:** si el día cambia entre dos operaciones, ¿"hoy" se evalúa en cada operación? Se asume que sí.
