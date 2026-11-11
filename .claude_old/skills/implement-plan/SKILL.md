---
name: implement-plan
description: Implementa un plan de plans/<num_issue>-<breve-descripcion>.md en una rama propia creada desde development, aplicando TDD estricto tarea a tarea, marcando cada tarea como hecha en el plan y haciendo un commit por tarea. Úsalo cuando pidan implementar, ejecutar o desarrollar un plan existente.
argument-hint: <ruta o nombre del plan, p. ej. plans/42-paginar-vendehumos.md>
---

# implement-plan

Implementa el plan que indique el usuario, tarea a tarea, con TDD estricto.

Plan a implementar: $ARGUMENTS

## Pasos

### 1. Localiza y lee el plan

- Si `$ARGUMENTS` está vacío o no resuelve a un archivo existente, busca en `plans/` por nombre o número de issue; si hay ambigüedad o no existe, pregunta al usuario. No inventes el plan.
- Lee el plan completo (Objetivo, Alcance, Archivos afectados, Tareas, Criterios de aceptación, Dudas) y `CLAUDE.md`.
- Si hay **dudas abiertas** que bloqueen la implementación, pregúntaselas al usuario antes de seguir.
- Si todas las tareas ya están marcadas (`- [x]`), díselo al usuario y detente. Si algunas lo están, continúa por la primera sin marcar.

### 2. Prepara la rama

El nombre de la rama es el **nombre del plan** (el nombre del archivo sin `.md`, p. ej. `42-paginar-vendehumos`).

1. Comprueba que hay repositorio git (`git rev-parse --is-inside-work-tree`). Si no lo hay, avisa al usuario y detente (no ejecutes `git init` sin permiso).
2. Comprueba que el árbol de trabajo está limpio (`git status --short`). Si hay cambios sin commitear ajenos al plan, avisa y pregunta antes de continuar.
3. Comprueba si existe `development` (`git rev-parse --verify --quiet development`).
   - **Si no existe**: créala a partir de `main` (`git switch -c development main`). Si tampoco existe `main`, avisa al usuario y detente.
   - **Si existe**: `git switch development`.
4. Crea la rama del plan desde `development`: `git switch -c <nombre-del-plan>`.
   - Si la rama ya existe, pregunta si continuar en ella (`git switch`) en lugar de recrearla. No borres ni fuerces ramas.

### 3. Implementa cada tarea con TDD estricto

Procesa las tareas **en el orden del plan, una por una**. Para cada tarea:

1. **Rojo**: escribe primero el test (vitest) que describe el comportamiento de la tarea. Ejecútalo y **comprueba que falla** por el motivo esperado (no por un error de sintaxis o de import). No escribas código de producción antes de ver el test fallar.
2. **Verde**: escribe el **mínimo** código de producción necesario para que el test pase. Ejecuta el test y comprueba que pasa.
3. **Refactor**: limpia el código manteniendo los tests en verde. Ejecuta la suite completa para comprobar que no hay regresiones.
4. Ejecuta prettier y eslint sobre los archivos tocados, si están configurados.
5. **Solo cuando el test pase con el código implementado**, marca la tarea como hecha en el plan: cambia `- [ ]` por `- [x]` en esa tarea concreta (edita solo esa línea).
6. **Commit**: invoca la skill/slash command `commit` (`/commit`) con contexto sobre la tarea. El commit debe incluir el código, los tests y el plan actualizado. No hagas `git commit` manual ni `git push`.

Si una tarea no es testeable con un test automático (p. ej. solo documentación o configuración), indícalo explícitamente al usuario, explica cómo la verificas y pide confirmación antes de saltarte el ciclo rojo-verde.

### 4. Cierre

- Cuando todas las tareas estén hechas, ejecuta la suite completa y repasa los **Criterios de aceptación** del plan, indicando cuáles se cumplen.
- Responde con: rama creada, lista de tareas completadas con su commit, estado de los criterios de aceptación y cualquier incidencia. No hagas push ni abras PR salvo que el usuario lo pida.

## Reglas

- **Nunca** marques una tarea como hecha sin haber visto antes su test en verde.
- **Un commit por tarea**, nunca varias tareas en un mismo commit.
- Si un test no consigue pasar tras intentos razonables, detente, deja la tarea sin marcar y explica el bloqueo al usuario; no debilites el test para forzar el verde.
- Si una tarea del plan es incorrecta o está incompleta, no improvises cambios de alcance: consúltalo con el usuario.
- Respeta las convenciones del proyecto: capas `controllers`, `services`, `repositories`, `models` y `utils`; funciones en snake_case; prettier (sin `;`, comillas simples) y eslint; tests con vitest.
- No toques archivos fuera del alcance del plan.
