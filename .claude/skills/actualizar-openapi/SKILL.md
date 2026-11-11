---
name: actualizar-openapi
description: Mantiene docs/api/openapi.yml sincronizado con la API y genera la documentación HTML con Redocly. Úsalo siempre que cambie algo de la API (rutas, parámetros, validaciones, modelos, respuestas, códigos HTTP o seguridad) y cuando pidan actualizar o regenerar la documentación de la API.
argument-hint: <opcional: descripción del cambio realizado en la API>
---

# actualizar-openapi

Actualiza `docs/api/openapi.yml` para que refleje la API real y regenera el HTML con Redocly.

Cambio indicado por el usuario (opcional): $ARGUMENTS

## Pasos

### 1. Detecta qué ha cambiado en la API

- Si `$ARGUMENTS` describe el cambio, úsalo como punto de partida y verifícalo en el código.
- Si no, revisa `git diff` y `git status` sobre: `src/index.js` (rutas), `src/controllers/`, `src/services/`, `src/models/` (`CATEGORIAS`, `ROLES`, mapeos), `src/utils/validar_*.js`, `src/utils/paginacion.js` y `src/middlewares/`.
- Si no hay ningún cambio que afecte a la API, díselo al usuario y detente.
- Si `docs/api/openapi.yml` no existe, avisa y propón ejecutar antes `plans/36-openapi-docs.md` (skill `implement-plan`); no lo crees desde cero sin permiso.

### 2. Actualiza `docs/api/openapi.yml`

- Lee el documento y el código afectado; **la fuente de verdad es el código**, no inventes endpoints, campos ni códigos HTTP.
- Refleja el cambio: rutas y métodos, parámetros, cuerpos de petición, esquemas de `components`, enums (`categoria`, `rol`), límites de validación, códigos de respuesta reales (`{ errores }` en 400, `{ error }` en el resto) y seguridad (`bearerAuth`).
- Mantén el estilo existente: OpenAPI 3.0.3, descripciones en español, esquemas reutilizables con `$ref` y la paginación según `docs/api/api-pagination.md`.
- Elimina del documento lo que haya dejado de existir en la API.
- No toques código fuente ni archivos fuera de `docs/api/`.

### 3. Valida y genera el HTML

Ejecuta el script (valida con `redocly lint` y genera `docs/api/openapi.html` con `redocly build-docs`):

```bash
bash scripts/generar_docs_html.sh
```

- Si el lint da errores, corrige el YAML y vuelve a ejecutar. Los avisos no bloqueantes se mencionan en la respuesta.
- Si falla por falta de red o de `npx`, explícalo al usuario; no instales nada global.

### 4. Cierre

Responde con: qué se ha cambiado en `openapi.yml`, resultado del lint y ruta del HTML generado. No hagas commit salvo que el usuario lo pida.

## Reglas

- Cada cambio de la API debe acabar reflejado en `openapi.yml` en la misma tarea.
- No dejes el HTML desactualizado respecto al YAML: regenéralo siempre tras editarlo.
- El script es `scripts/generar_docs_html.sh`; acepta opcionalmente la ruta de entrada y de salida como argumentos.
