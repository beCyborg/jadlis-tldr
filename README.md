# YouTube TLDR — Claude Code Plugin

Плагин для Claude Code, который извлекает субтитры YouTube-видео и формирует практические выводы.

## Что делает

- Скачивает субтитры (автогенерированные или ручные)
- Анализирует содержание и извлекает практическую пользу
- Выводит структурированную выжимку: Суть, Ключевые идеи, Что делать, Ограничения
- Сохраняет результат в `.md` файл

## Зависимости

- **Python 3.8+** — обязательно
- **youtube-transcript-api** — устанавливается автоматически при первом запуске
- **yt-dlp** — опционально, используется как fallback

## Установка

### Разработка (локально)

```bash
claude --plugin-dir "/path/to/YouTube TLDR"
```

### Production (через маркетплейс)

1. Добавь маркетплейс в настройки Claude Code
2. Установи плагин:
```bash
claude plugin install yt-tldr@youtube-tldr-plugin
```

### Приватный репозиторий

Для автообновлений приватного маркетплейса нужен токен:
```bash
export GITHUB_TOKEN="ghp_..."
# или
export GH_TOKEN="ghp_..."
```

## Использование

```
/yt-tldr https://www.youtube.com/watch?v=VIDEO_ID
```

С указанием языка вывода:
```
/yt-tldr https://youtu.be/VIDEO_ID en
```

Поддерживаемые URL:
- `youtube.com/watch?v=...`
- `youtu.be/...`
- `youtube.com/shorts/...`
- `youtube.com/live/...`

## Troubleshooting

### "YouTube заблокировал запрос"

YouTube может блокировать IP. Решения:
- Подождать и попробовать позже
- Использовать VPN
- Установить `yt-dlp` как fallback: `brew install yt-dlp`

### "Субтитры не найдены"

Не все видео имеют субтитры. Проверь, включены ли CC на самом видео.

### "Не установлен youtube-transcript-api"

Перезапусти Claude Code — плагин автоматически установит зависимость. Или вручную:
```bash
pip install youtube-transcript-api
```
