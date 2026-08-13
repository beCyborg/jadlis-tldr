# tldr — Claude Code Plugin

Плагин для Claude Code: делает практическую выжимку из YouTube-видео (по субтитрам) и из локальных текстовых файлов.

## Что делает

- **YouTube** — скачивает субтитры (автогенерированные или ручные) по ссылке на видео, Shorts или трансляцию.
- **Локальные файлы** — читает `.md`, `.txt`, `.html`, `.pdf`, `.org`, `.rtf`, `.csv`, `.json`, `.xml`.
- Анализирует содержание и извлекает применимую пользу, а не пересказ.
- Выводит структуру: **Суть → Ключевые идеи → Что делать → Ограничения**.
- Сохраняет результат отдельной `.md`-заметкой с осмысленным именем и тегами.

## Prerequisites

- **Python 3.8+** — обязательно.
- **youtube-transcript-api** — ставится автоматически в изолированный venv плагина при первом старте сессии.
- **yt-dlp** — опционально, fallback при блокировках YouTube (`brew install yt-dlp`).

## Установка

```
/plugin marketplace add https://github.com/beCyborg/youtube-tldr-plugin.git
/plugin install tldr@youtube-tldr-plugin
```

Полный HTTPS-URL — защита от клона по SSH: shorthand `owner/repo` уходит на SSH, а SSH-ключа на GitHub у нового пользователя обычно нет. `youtube-tldr-plugin` — имя маркетплейса, который добавляет первая команда; во второй подставляется именно оно.

### Куда сохраняются выжимки

По умолчанию — в `~/tldr`. Чтобы задать свою папку (например, внутри Obsidian-vault):

```bash
claude plugin install tldr@youtube-tldr-plugin --config TLDR_SAVE_DIR=<путь>
```

Настройка `TLDR_SAVE_DIR` доступна и через `/plugin` → tldr → настройки. Без неё плагин работает из коробки, просто пишет в `~/tldr`.

## Использование

```
/tldr https://www.youtube.com/watch?v=VIDEO_ID
```

С языком вывода (`ru` по умолчанию, `en` — второй аргумент):

```
/tldr https://youtu.be/VIDEO_ID en
```

Локальный файл:

```
/tldr ~/Downloads/статья.pdf
```

Поддерживаемые URL: `youtube.com/watch?v=…`, `youtu.be/…`, `youtube.com/shorts/…`, `youtube.com/live/…`.

## Обновления

`version` в манифестах не задан — версией служит commit SHA. Каждый пуш доезжает по команде:

```
/plugin update tldr@youtube-tldr-plugin
```

Авто-обновление сторонних маркетплейсов по умолчанию выключено — включить можно в `/plugin` → Marketplaces → youtube-tldr-plugin.

## Troubleshooting

### «Для этого видео нет субтитров»

Не у всех видео есть CC. Проверьте, включены ли субтитры на самом видео.

### «YouTube заблокировал запрос»

YouTube иногда блокирует IP. Варианты: подождать и повторить, включить VPN, установить `yt-dlp` как fallback (`brew install yt-dlp`).

### «Не установлен youtube-transcript-api»

Перезапустите Claude Code — SessionStart-хук развернёт venv и поставит зависимость сам. Вручную:

```bash
pip install youtube-transcript-api
```

## Лицензия

MIT — см. [LICENSE](LICENSE).
