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

После вывода анализа на экран:
1. Сохрани результат в файл `yt-tldr-<video_id>.md` в текущей директории
2. Включи в файл: метаданные + полный анализ
3. Покажи путь: "Сохранено: `./yt-tldr-<video_id>.md`"
