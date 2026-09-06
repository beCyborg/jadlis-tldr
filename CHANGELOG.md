# Changelog — tldr

Формат: [Keep a Changelog](https://keepachangelog.com/ru/1.1.0/), версии — [SemVer](https://semver.org/lang/ru/).

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
