---
description: Crea un commit siguiendo la especificación de Conventional Commits
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git add:*), Bash(git commit:*)
argument-hint: [contexto opcional sobre el cambio]
---

## Contexto

- Estado actual: !`git status --short`
- Cambios en staging: !`git diff --cached`
- Cambios sin stagear: !`git diff`
- Commits recientes (para seguir el estilo): !`git log --oneline -10`

## Tarea

Crea un commit que siga la especificación [Conventional Commits 1.0.0](https://www.conventionalcommits.org/es/v1.0.0/).

Contexto adicional del usuario: $ARGUMENTS

### Formato

```
<tipo>[ámbito opcional][!]: <descripción>

[cuerpo opcional]

[pie(s) opcional(es)]
```

### Tipos permitidos

- `feat`: nueva funcionalidad (SemVer MINOR)
- `fix`: corrección de un error (SemVer PATCH)
- `docs`: cambios solo en documentación
- `style`: formato, espacios, punto y coma… sin cambio de lógica
- `refactor`: cambio de código que no corrige un error ni añade funcionalidad
- `perf`: mejora de rendimiento
- `test`: añadir o corregir tests
- `build`: sistema de build o dependencias
- `ci`: configuración de integración continua
- `chore`: tareas de mantenimiento que no tocan src ni tests
- `revert`: revierte un commit anterior

### Reglas

1. La descripción va en minúsculas, en imperativo y presente, sin punto final, y con un máximo de ~72 caracteres en la primera línea.
2. Usa un ámbito entre paréntesis cuando aclare la zona afectada, p. ej. `feat(auth): añade login con token`.
3. Los cambios incompatibles se marcan con `!` tras el tipo/ámbito y/o con un pie `BREAKING CHANGE: <explicación>` (SemVer MAJOR).
4. El cuerpo, si hace falta, explica el *qué* y el *por qué* (no el *cómo*), separado por una línea en blanco.
5. Los pies usan formato `Token: valor`, p. ej. `Refs: #123` o `Closes: #45`.
6. Escribe el mensaje en el mismo idioma que usan los commits recientes del repositorio.

### Pasos

1. Revisa el estado y los diffs anteriores. Si no hay cambios, díselo al usuario y detente.
2. Si no hay nada en staging, añade los archivos relevantes con `git add` (nombres concretos, nunca archivos sensibles como `.env` ni credenciales). Si los cambios mezclan propósitos distintos, propón dividirlos en varios commits.
3. Elige el tipo y ámbito que mejor describan el cambio.
4. Crea el commit con `git commit -m` (usa varios `-m` o un heredoc si hay cuerpo o pies).
5. Muestra el mensaje resultante y el resultado de `git status`.

### Restricciones

- No hagas `git push`.
- No uses `--no-verify` ni `--amend` salvo que el usuario lo pida explícitamente.
- Si un hook de pre-commit falla, corrige el problema y crea un commit nuevo.
