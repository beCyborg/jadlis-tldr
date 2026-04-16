---
name: yt-tldr
description: >
  Извлекает субтитры YouTube-видео и формирует практические выводы.
  Триггеры: "youtube tldr", "yt tldr", "разбери видео", "о чём видео", "выжимка из видео",
  "проанализируй видео", "что в видео", "посмотри видео", "summarize video".
  DO NOT TRIGGER when: user wants to download/save a video, or needs verbatim transcript.
argument-hint: <youtube-url> [lang]
allowed-tools:
  - Bash(python3 *)
  - Bash(which *)
  - Bash(mkdir *)
  - Read
  - Write
---

# /yt-tldr — анализ YouTube-видео по субтитрам

Ты извлекаешь субтитры YouTube-видео и формируешь практические выводы.

**Аргументы:** `$ARGUMENTS`

---

## Фаза 1 — Валидация

Извлеки из `$ARGUMENTS`:
- **URL** — первый аргумент, содержащий YouTube-ссылку
- **Язык вывода** — второй аргумент (опционально): `ru` (по умолчанию) или `en`

Поддерживаемые форматы URL:
- `https://www.youtube.com/watch?v=VIDEO_ID`
- `https://youtu.be/VIDEO_ID`
- `https://youtube.com/shorts/VIDEO_ID`
- `https://youtube.com/live/VIDEO_ID`
- `https://www.youtube.com/watch?v=VIDEO_ID&list=...` (извлечь только video_id)

Извлеки `video_id` из URL (11 символов после `v=`, после `youtu.be/`, после `shorts/`, после `live/`).

Если URL не найден или невали��ный — спроси пользователя через AskUserQuestion.

## Фаза 2 — Извлечение субтитров

Определи путь к Python:
```bash
VENV_PYTHON="${CLAUDE_PLUGIN_DATA}/venv/bin/python3"
if [ -x "$VENV_PYTHON" ]; then
  PYTHON="$VENV_PYTHON"
else
  PYTHON="python3"
fi
```

Запусти скрипт:
```bash
$PYTHON ${CLAUDE_SKILL_DIR}/scripts/get_transcript.py <video_id>
```

Скрипт возвращает JSON в stdout. Проверь поле `status`:

**Если `status: "ok"`:**
- Запомни `text`, `language`, `is_generated`, `duration_minutes`, `word_count`
- Переходи к Фазе 3

**Если `status: "error"`:**
- `no_captions` → "Для этого видео нет субтитров. Попробуй видео с включёнными CC."
- `blocked` → Покажи сообщение из `message`. Предложи попробовать позже или использовать VPN.
- `network` → "Ошибка сети. Проверь подключение к интернету."
- `invalid_id` → "Видео недоступно. Проверь ссылку."
- `missing_deps` → "Не установлен youtube-transcript-api. Перезапусти Claude Code или выполни: pip install youtube-transcript-api"
- ОСТАНОВИСЬ после вывода ошибки.

## Фаза 3 — Анализ

Прочитай шаблон анализа:
```
${CLAUDE_SKILL_DIR}/references/analysis-prompt.md
```

Примени шаблон к тексту субти��ров. Учитывай:
- Если `word_count < 200` — используй сокращённый формат (только Суть + Что делать)
- Если `duration_minutes > 120` — предупреди: "Это длинное видео (>2 часов). Анализ мо��ет пропустить нюансы."
- Язык вывода: используй язык из аргументов (по умолчанию русский)

Выведи метаданные перед анализом:
```
**Длительность:** ~X мин | **Язык субтитров:** Y | **Тип:** автогенерация/ручные
```

## Фаза 4 — Сохранение

### 4.1 Каталог назначения

Все сохранения → `/Users/cyborg/Jadlis/Знания/YouTube-TLDR/`. Если папка не существует — создай её:

```bash
mkdir -p "/Users/cyborg/Jadlis/Знания/YouTube-TLDR"
```

### 4.2 Имя файла — slug из темы видео

Сформируй slug на русском из **главной темы видео** (ориентируйся на раздел "Суть" из анализа; если оригинальное название видео уже информативное и русское — используй его как основу).

Правила формирования slug:
- Нижний регистр, кириллица разрешена
- Пробелы → дефис `-`
- Убрать пунктуацию, кавычки, скобки, эмодзи, слэши
- Английские термины оставлять латиницей как есть (напр. `react`, `llm`, `prompt-caching`)
- Максимум 80 символов — обрезать по границе слова
- Расширение `.md`

Примеры хороших slug:
- Видео про prompt caching → `prompt-caching-и-cache-aware-load-balancing.md`
- Лекция Karpathy про LLM → `как-устроены-llm-объяснение-karpathy.md`
- Разбор книги → `atomic-habits-выжимка.md`
- Интервью о продуктивности → `продуктивность-без-выгорания-cal-newport.md`

**Избегай** общих slug без темы: `обзор.md`, `лекция.md`, `видео.md`, `интервью.md` — они не отражают содержание.

При коллизии (файл с таким именем уже есть в целевой папке) — добавь суффикс `-2`, `-3` и т.д.

### 4.3 Канонический frontmatter

Файл должен начинаться ровно этим блоком. Порядок полей и имена полей — фиксированы:

```yaml
---
source: https://www.youtube.com/watch?v=<video_id>
video_id: <video_id>
title: <оригинальное название видео; если неизвестно — краткая формулировка темы>
duration_minutes: <число из get_transcript.py>
language: <код языка субтитров: en, ru, es, …>
captions_type: <auto | manual>
word_count: <число из get_transcript.py>
tags: [yt-tldr]
---
```

Правила заполнения:
- `captions_type: auto` если `is_generated == true` из JSON скрипта, иначе `manual`
- `title` — оригинальное название, если его удалось вывести из субтитров/контекста; иначе — краткая формулировка темы на русском
- `tags` — всегда массив с единственным элементом `yt-tldr` (можешь добавить тематические теги после, но `yt-tldr` должен быть первым)

### 4.4 Тело файла

После frontmatter:

```markdown
# <title или тема видео>

**Длительность:** ~X мин | **Язык субтитров:** Y | **Тип:** автогенерация/ручные
**Источник:** https://www.youtube.com/watch?v=<video_id>

<полный анализ из Фазы 3>
```

### 4.5 Финальное сообщение пользователю

Выведи абсолютный путь к сохранённому файлу:

```
Сохранено: /Users/cyborg/Jadlis/Знания/YouTube-TLDR/<имя-файла>.md
```
