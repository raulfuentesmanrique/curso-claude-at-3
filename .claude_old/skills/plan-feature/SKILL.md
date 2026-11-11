---
name: plan-feature
description: Planifica una nueva funcionalidad o el arreglo de un error y guarda el plan en plans/<num_issue>-<breve-descripcion>.md usando una plantilla común. Úsalo cuando pidan planificar, diseñar o preparar el trabajo de una feature o un bug antes de implementarlo.
argument-hint: <num_issue> <descripción de la funcionalidad o error>
---

# plan-feature

Genera un plan de trabajo con un formato uniforme para todo el equipo. **Solo planifica: no modifiques código fuente.**

Petición del usuario: $ARGUMENTS

## Pasos

1. **Obtén el número de issue.** Si no viene en los argumentos, pregúntaselo al usuario antes de seguir. Si el trabajo no tiene issue, pregunta qué número usar y no lo inventes.
2. **Entiende el contexto.** Lee `CLAUDE.md` y explora el código relacionado con la petición (rutas, controllers, services, repositories, models, utils). Si la petición toca endpoints GET con paginación, lee `docs/api/api-pagination.md`.
3. **Si es un error**, localiza la causa probable y descríbela en el objetivo (comportamiento actual vs. esperado).
4. **Rellena la plantilla** [assets/plantilla.md](plantilla.md) respetando todas sus secciones y su orden, sin añadir ni quitar secciones:
   - **Objetivo**: qué se consigue y por qué.
   - **Alcance**: qué incluye y qué no.
   - **Archivos afectados**: rutas reales verificadas en el repo (o marcadas como "nuevo" si se van a crear), con la acción y el motivo.
   - **Tareas**: lista de checkboxes numerada, en orden de ejecución. Cada tarea debe ser concreta, verificable y de **5-10 minutos**; si una es mayor, divídela.
   - **Criterios de aceptación**: condiciones observables y comprobables que deben cumplirse para dar el trabajo por hecho (p. ej. códigos HTTP, formato de respuesta, tests que pasan).
   - **Dudas y decisiones asumidas** (siempre al final): separa las dudas abiertas de las decisiones tomadas por tu cuenta, indicando el motivo de cada decisión. Si no hay, escribe "Ninguna".
5. **Respeta las convenciones del proyecto** al planificar: capas `controllers`, `services`, `repositories`, `models` y `utils`; funciones en snake_case; tests con vitest; prettier (sin `;`, comillas simples) y eslint.
6. **Guarda el plan** en `plans/<num_issue>-<breve-descripcion>.md` (crea la carpeta `plans/` si no existe):
   - `<breve-descripcion>` en kebab-case, minúsculas, sin tildes ni caracteres especiales, 2-5 palabras (p. ej. `42-paginar-vendehumos.md`).
   - Si el archivo ya existe, pregunta antes de sobrescribirlo.
7. **Responde** con la ruta del archivo creado, un resumen de 2-3 líneas y las dudas abiertas, si las hay.

## Restricciones

- No implementes el plan ni edites archivos fuera de `plans/`.
- No inventes archivos, funciones o endpoints: verifica que existen antes de citarlos.
- Mantén el plan breve y accionable; evita relleno.
