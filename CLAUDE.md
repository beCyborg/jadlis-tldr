# jadlis-tldr — конвенции репо

Root-as-plugin: манифест плагина и манифест маркетплейса лежат в `.claude-plugin/`, источник записи — `"./"`. Плагин `jadlis-tldr`, маркетплейс `youtube-tldr-plugin`. Основной путь установки у получателя — хаб `jadlis` (`https://github.com/beCyborg/jadlis-hub`), который тянет этот репо записью `url` с пином `ref` + `sha`. Правила ниже читает агент, который правит и коммитит.

## Коммиты

- Тема — Conventional Commits на английском: `type(scope): subject`, ≤72 символа. `scope` = `tldr`, `docs`, `hooks` или `repo`.
- Тело двухслойное: `Что изменилось:` — 1–3 предложения по-русски для человека; `Details (for agents):` — буллеты `Added / Changed / Removed / Migration / Refs` с путями.
- Без строк атрибуции (`Co-Authored-By` и подобных).

## Релизы

- Версия живёт только в `.claude-plugin/plugin.json`; бамп — в том же коммите, что и изменение (версия = ключ кеша обновлений).
- Тег `jadlis-tldr--v{X.Y.Z}`: `claude plugin tag --push .` (root-as-plugin — точка). GitHub Release поверх тега: `python3 ~/jadlis-hub/tools/release-notes.py . [--title]`.
- `CHANGELOG.md` в корне: `## [X.Y.Z] — YYYY-MM-DD — <кратко по-русски> / <short EN>`, затем `### Для человека` (≤3 буллета) и `### For agents` (`Added / Changed / Removed / Migration / Breaking`, с путями).
- После релиза обновить пин в хабе: `python3 ~/jadlis-hub/tools/bump-pin.py jadlis-tldr jadlis-tldr--v{X.Y.Z}`.
- Только patch-forward: никаких force-push и переписывания тегов.

## README и доки

- Пара `README.md` (RU) + `README.en.md` (EN); первая строка — переключатель языка. Одинаковое число и порядок H2. Проверка: `python3 ~/jadlis-hub/tools/readme-parity.py .`
- Пять секций документа инструмента: **Зачем / Как выглядит / Как поставить / Как пользоваться / Границы и стоимость**; дополнительные H2 («Обновление», «Лицензия») — только одинаково в обеих языковых версиях.
- Иллюстрации — `docs/img/*.webp`, копии из `~/jadlis-hub/docs/img/`; каждая просмотрена глазами, отметка ☑ ставится в `~/jadlis-hub/docs/img/INDEX.md`. Схемы — Mermaid.
- Механику сверять с кодом: поддерживаемые URL и расширения — в `skills/tldr/SKILL.md`, установка зависимости — в `hooks/hooks.json`, папка сохранения — `userConfig.TLDR_SAVE_DIR`. Рядом с числом писать тег (`(тег jadlis-tldr--v2.0.0)`).
- Примеры заметок — только синтетические: реальные выжимки пользователя в репо не попадают. Оценок времени не давать.

## Приватность

- В файлах репо нет ключей, почт, телефонов, путей владельца (`/Users/<имя>` → `~`) и имени владельца; путь к vault не хардкодить — он приходит из `TLDR_SAVE_DIR`. Перед пушем: `gitleaks git .` и `python3 ~/jadlis-hub/tools/privacy-grep.py .` (оба гоняет CI).
- Плагин не требует API-ключей: субтитры берутся публичным путём (`youtube-transcript-api`, при блокировке — `yt-dlp`).

## Разработка

- Правки только в рабочем клоне `~/jadlis-tldr`, никогда в `~/.claude/plugins/marketplaces/` (фоновый рефреш стирает правки).
- Перед коммитом: `claude plugin validate .`, `claude plugin validate .claude-plugin/marketplace.json`, `readme-parity.py`, `privacy-grep.py`.
- Зависимости — только через `requirements.txt`: хук `SessionStart` разворачивает venv в `${CLAUDE_PLUGIN_DATA}` и сравнивает файл с кэшированной копией. Ставить пакеты в системный python нельзя.
- Имя плагина — `jadlis-tldr` (правило каталога: репо и плагин всегда `jadlis-<имя>`); имя маркетплейса `youtube-tldr-plugin` оставлено как есть. Короткая команда `/tldr` задаётся фронтматтером `name` входного скилла и от имени плагина не зависит; папки скиллов не переименовываются.
- Язык доков — русский (RU-файл первичен) + английская пара; код, идентификаторы и имена файлов — английский.
