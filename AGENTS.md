# Agent Instructions for edu-rust-gigachat-app-frontend

## Project Overview

Учебное веб-приложение с UI/UX на Rust, использующее API проекта
`edu-rust-gigachat-app-backend`. Это отдельный проект, предназначенный для студентов.

## Key Constraints

- Приложение работает только как клиент к существующему API.
- Базовый URL API должен быть конфигурируемым.
- Секреты не хранятся в репозитории.

## Framework Choice

Основной фреймворк: Yew (зрелый фронтенд для Web UI на Rust).
Альтернативы упоминаются в документации, но не используются без решения
преподавателя.

## Development Notes

- Интерфейс должен быть доступным и понятным для студентов.
- Дизайн должен быть прагматичным, без излишних визуальных эффектов.
- Все изменения должны учитывать учебную цель и ясность кода.

## Documentation First

Перед внесением изменений смотреть:
- docs/framework_selection.md
- docs/ui_ux_requirements.md
- docs/api_contract.md

## Testing Expectations

При появлении исходников предусмотреть:
- базовые UI-тесты,
- проверку корректности запросов к API,
- обработку сетевых ошибок.

## Agent Notes (non-student)

- Вводный файл для Moodle должен иметь заголовок, который явно содержит номер лабораторной работы и её тему.

## Beads + Git Workflow

Проект использует **beads** как встроенный issue‑трекер.

Рекомендуемый режим (по документации beads):
- авто‑синхронизация + git‑хуки + merge‑driver для `.beads/issues.jsonl`.

Если это свежий клон:
```bash
bd init
bd hooks install
git config merge.beads.driver true
```

Используйте `bd sync` только как fallback, если auto‑sync/хуки не сработали.

Полезные команды:
- `bd ready` — найти доступную работу
- `bd create "Заголовок" --type task --priority 2 --description "что и зачем"` — создать задачу
- `bd update <id> --status in_progress` — взять задачу
- `bd close <id>` — закрыть задачу

### Обязательный порядок работы (симуляция живого разработчика)

**Строго обязательно (без исключений):**

1. **До начала любой работы:** выполнить `bd create` с описанием (`--description`).
2. **Перед изменениями:** перевести задачу в `in_progress`.
3. **Только после этого:** выполнять правки, тесты и исправления.
4. **Подготовить индекс:** добавить изменения в индекс (`git add -A`).
5. **Коммит проекта:** первая строка коммита должна начинаться с ID issue
   в квадратных скобках и содержать тип и приоритет в современном формате:
   `[<issue-id>] <type>(P#): <issue title>`.
   Префикс ID задач для этого репозитория: `ergaf-` (только нижний регистр).
   Тело коммита должно совпадать с `--description`. После описания
   добавьте список изменённых файлов (см. шаблон ниже).
6. **Закрытие задачи:** `bd close <id>` выполняется после коммита проекта.
7. **Синхронизация beads:** `bd sync` выполняется после `bd close`.

**Важно:** `bd sync` коммитит **только** `.beads/issues.jsonl`
(данные beads). Изменения проекта он **не коммитит**.
Коммит проекта выполняется вручную. `bd sync` не выполняет
`git add`, поэтому новые файлы нужно добавить в индекс заранее.
`bd sync` **по умолчанию выполняет `git pull`**, поэтому отдельный
`git pull` **до или после** `bd sync` не нужен и ломает порядок.
Повторный push допускается только если `bd sync` не выполнялся
или завершился с ошибкой.

**Шаблон коммита (issue → commit):**
```bash
ID="<issue id>"
TYPE="<issue type>"
PRIO="<issue priority>"
TITLE="<issue title>"
DESC="<issue description>"
HEADER="[$ID] $TYPE($PRIO): $TITLE"
FILES=$(git diff --cached --name-only | sed 's/^/- /')
printf "%s\n\n%s\n\nИзменения:\n%s\n" "$HEADER" "$DESC" "$FILES" | git commit -F -
```

Вариант со статусами файлов (A/M/D/R/C + пояснение):
```bash
ID="<issue id>"
TYPE="<issue type>"
PRIO="<issue priority>"
TITLE="<issue title>"
DESC="<issue description>"
HEADER="[$ID] $TYPE($PRIO): $TITLE"
FILES=$(git diff --cached --name-status | awk '
  {
    code=$1; from=$2; to=$3;
    status="изменён";
    if (code ~ /^A/) status="создан";
    else if (code ~ /^D/) status="удалён";
    else if (code ~ /^R/) { status="переименован"; printf "- %s %s -> %s (%s)\n", code, from, to, status; next }
    else if (code ~ /^C/) { status="скопирован"; printf "- %s %s -> %s (%s)\n", code, from, to, status; next }
    printf "- %s %s (%s)\n", code, from, status;
  }')
printf "%s\n\n%s\n\nИзменения:\n%s\n" "$HEADER" "$DESC" "$FILES" | git commit -F -
```

Если `.beads/issues.jsonl` попал в индекс, его можно исключить из проектного
коммита и оставить для `bd sync`:
```bash
git restore --staged .beads/issues.jsonl
```

**Примечание про хуки:** если `bd sync` вызывается внутри git‑хуков,
он может запускать повторную синхронизацию. Это ожидаемое поведение,
и ручной `git push` после него не требуется.

**Формальное описание процесса (CNCF):**
- `agents-issue-workflow.cncf.yaml` — Serverless Workflow (CNCF) для агентских правил.

**Запрещено:**
- начинать работу без `bd create`;
- закрывать задачу до `bd sync`;
- выполнять работу, если задача не в `in_progress`.

<!-- BEGIN BEADS INTEGRATION v:1 profile:minimal hash:ca08a54f -->
## Beads Issue Tracker

This project uses **bd (beads)** for issue tracking. Run `bd prime` to see full workflow context and commands.

### Quick Reference

```bash
bd ready              # Find available work
bd show <id>          # View issue details
bd update <id> --claim  # Claim work
bd close <id>         # Complete work
```

### Rules

- Use `bd` for ALL task tracking — do NOT use TodoWrite, TaskCreate, or markdown TODO lists
- Run `bd prime` for detailed command reference and session close protocol
- Use `bd remember` for persistent knowledge — do NOT use MEMORY.md files

## Session Completion

**When ending a work session**, you MUST complete ALL steps below. Work is NOT complete until `git push` succeeds.

**MANDATORY WORKFLOW:**

1. **File issues for remaining work** - Create issues for anything that needs follow-up
2. **Run quality gates** (if code changed) - Tests, linters, builds
3. **Update issue status** - Close finished work, update in-progress items
4. **PUSH TO REMOTE** - This is MANDATORY:
   ```bash
   git pull --rebase
   bd dolt push
   git push
   git status  # MUST show "up to date with origin"
   ```
5. **Clean up** - Clear stashes, prune remote branches
6. **Verify** - All changes committed AND pushed
7. **Hand off** - Provide context for next session

**CRITICAL RULES:**
- Work is NOT complete until `git push` succeeds
- NEVER stop before pushing - that leaves work stranded locally
- NEVER say "ready to push when you are" - YOU must push
- If push fails, resolve and retry until it succeeds
<!-- END BEADS INTEGRATION -->
