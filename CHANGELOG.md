# Changelog — jadlis-tldr

Формат: [Keep a Changelog](https://keepachangelog.com/ru/1.1.0/), версии — [SemVer](https://semver.org/lang/ru/).

## [2.0.0] — 2026-09-10 — переименование плагина в `jadlis-tldr` / plugin renamed to `jadlis-tldr`

### Для человека
- Плагин переименован `tldr` → `jadlis-tldr`: ставится теперь как `claude plugin install jadlis-tldr@jadlis`, полная форма команды — `/jadlis-tldr:tldr`.
- Короткая команда `/tldr` не изменилась — её задаёт фронтматтер `name` скилла, а не имя плагина.
- Совместимости со старым именем нет: установленный `tldr@jadlis` нужно снести и поставить заново.

### For agents
- Changed: `.claude-plugin/plugin.json` — `name` `tldr` → `jadlis-tldr`, `version` 1.0.2 → 2.0.0.
- Changed: `.claude-plugin/marketplace.json` — `plugins[0].name` → `jadlis-tldr`; имя маркетплейса `youtube-tldr-plugin` оставлено без изменений.
- Changed: `README.md`, `README.en.md` — установка `jadlis-tldr@jadlis`, маркетплейс-хаб `https://github.com/beCyborg/jadlis-hub`, полная форма команды `/jadlis-tldr:tldr`, упоминания плагина в тексте установщика и в `/plugin` → настройки.
- Changed: `CLAUDE.md` — имя плагина, тег `jadlis-tldr--v{X.Y.Z}`, рабочий клон `~/jadlis-tldr`, пути инструментов `~/jadlis-hub/tools/`, снято правило о неизменяемости имени.
- Changed: `.github/workflows/plugin-validate.yml` — вызов `beCyborg/jadlis-hub/.github/workflows/plugin-ci.yml@main`.
- Breaking: имя плагина и полная форма команды изменились; шимов и алиасов нет.
- Migration: `claude plugin uninstall tldr@jadlis --keep-data && claude plugin install jadlis-tldr@jadlis`; `userConfig.TLDR_SAVE_DIR` не менялся, скилл, хук и скрипт субтитров не трогали.

## [1.0.2] — 2026-09-07 — переименование репозитория / repository rename

### Для человека
- Репо переименовано в `jadlis-tldr`, маркетплейс `jadlis` (`tldr@jadlis`); старый адрес редиректит, старая установка продолжает работать.

### For agents
- Changed: `.claude-plugin/plugin.json` — `version` 1.0.1 → 1.0.2, `homepage`/`repository` → `https://github.com/beCyborg/jadlis-tldr`.
- Changed: `.claude-plugin/marketplace.json` — `homepage` записи → новый адрес; имя маркетплейса `youtube-tldr-plugin` оставлено без изменений.
- Changed: `README.md`, `README.en.md` — установка через хаб `https://github.com/beCyborg/jadlis-start.git`; блок альтернативной установки из своего маркетплейса заменён строкой о том, что старые установки продолжают работать.
- Changed: `CLAUDE.md` — заголовок и адрес хаба.
- Changed: `.github/workflows/plugin-validate.yml` — вызов переиспользуемого workflow `beCyborg/jadlis-start/.github/workflows/plugin-ci.yml@main` (`mode: plugin` — репо root-as-plugin).
- Migration: не требуется, скилл, хук и скрипт субтитров не менялись.

## [1.0.1] — 2026-09-06 — двуязычный README и гейты передачи / bilingual README and handover gates

### Для человека
- README переписан по пяти секциям (Зачем / Как выглядит / Как поставить / Как пользоваться / Границы и стоимость), рядом появился английский `README.en.md`.
- Основной путь установки теперь через хаб `jadlis`; свой маркетплейс `youtube-tldr-plugin` остался как альтернатива.
- В CI добавлена проверка на утечку секретов, в репозитории — конвенции для агентов (`CLAUDE.md`).

### For agents
- Added: `README.en.md`, `CHANGELOG.md`, `CLAUDE.md`, `docs/img/10-overview.v2-06.webp`.
- Changed: `README.md` — 5 секций + «Обновление» и «Лицензия»; установка через `https://github.com/beCyborg/jadlis-plugins.git` → `claude plugin install tldr@jadlis --config TLDR_SAVE_DIR=~/tldr`; вызов скилла записан как `/tldr:tldr`.
- Changed: `.github/workflows/plugin-validate.yml` — job `gitleaks` (`gitleaks/gitleaks-action@v2`, `fetch-depth: 0`) рядом с job `validate`.
- Changed: `.claude-plugin/plugin.json` — `version` 1.0.0 → 1.0.1.
- Migration: не требуется, скилл, хук и скрипт субтитров не менялись.

## [1.0.0] — 2026-09-01 — первый релиз по домашнему стандарту / first release on the house standard

### Для человека
- Плагин делает выжимку из YouTube-видео по субтитрам и из локальных текстовых файлов: суть, ключевые идеи, что делать, ограничения.
- Папка для выжимок настраивается (`TLDR_SAVE_DIR`, по умолчанию `~/tldr`), зависимость ставится сама при первом старте сессии.
- Репозиторий приведён к домашнему стандарту: явный semver, `$schema` в манифестах, LICENSE, CI-валидация плагина.

### For agents
- Added: `skills/tldr/SKILL.md`, `skills/tldr/references/analysis-prompt.md`, `skills/tldr/scripts/get_transcript.py`; `hooks/hooks.json` (SessionStart → venv + `requirements.txt`).
- Added: маркетплейс `youtube-tldr-plugin` в `.claude-plugin/marketplace.json` (root-as-plugin, `"source": "./"`); `.github/workflows/plugin-validate.yml`; `LICENSE` (MIT).
- Added: `userConfig.TLDR_SAVE_DIR` в `.claude-plugin/plugin.json`; поддержка текстовых файлов (`.md`, `.txt`, `.html`, `.pdf`, `.org`, `.rtf`, `.csv`, `.json`, `.xml`).
- Changed: скилл переименован `yt-tldr` → `tldr`, фронтматтер сведён к тегам; захардкоженный путь сохранения заменён настройкой.
- Changed: README — раздел ограничений (нужны субтитры, из PDF берётся текстовый слой).
