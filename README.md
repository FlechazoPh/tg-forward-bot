# TG Forward Bot

A Telegram bot that forwards messages to a group with source attribution. Send or forward any message to the bot, and it will re-post it to your target group with the original channel/group info and a clickable link back to the source message.

Perfect for archiving content from channels that might get banned — your forwarded copies survive in your own group.

## Features

- **Forward any message** to the bot → auto re-posts to your group
- **Source attribution**: includes original channel/group name + hyperlink to the original message (works for public and private chats)
- **Media support**: video, photo, GIF/animation, document, and text
- **Quality preserved**: video dimensions/duration probed via ffprobe and passed explicitly (no stretching)
- **Protected content**: for channels that disallow forwarding, just send the media file directly to the bot
- **Access control**: only whitelisted usernames can use the bot
- **Auto cleanup**: downloaded files are deleted after successful forwarding
- **Anti-flood**: rate limiting between sends, respects Telegram's 429 retry_after

## Requirements

- Python 3.8+
- `ffprobe` (from ffmpeg)
- `curl`
- A Telegram bot token (from [@BotFather](https://t.me/BotFather))
- Telegram API credentials (api_id/api_hash from [my.telegram.org](https://my.telegram.org)) — only needed if you self-host the Bot API server
- (Optional) Self-hosted [telegram-bot-api](https://github.com/tdlib/telegram-bot-api) for files over 50MB (supports up to 2000MB in local mode)

## Quick Start

1. Clone this repo:
   ```bash
   git clone https://github.com/YOUR_USERNAME/tg-forward-bot.git
   cd tg-forward-bot
   ```

2. Configure credentials (all files are git-ignored, only `.example` templates are committed):
   ```bash
   cp .bot_token.example .bot_token
   cp .chat_id.example .chat_id
   cp .allowed_users.example .allowed_users
   # then edit each file with your real values
   chmod 600 .bot_token .chat_id .allowed_users
   ```

   - `.bot_token`: your bot token from @BotFather
   - `.chat_id`: target group/channel ID (e.g. `-1001234567890`)
   - `.allowed_users`: one Telegram username per line (without @), only these users can use the bot

3. If you self-host telegram-bot-api in local mode, point the scripts at it:
   ```python
   API = "http://127.0.0.1:8081"  # edit at the top of tgforward / tgsend
   ```

4. Run the forwarder daemon:
   ```bash
   ./tgforward
   ```
   Or install as a systemd service (see `tgforward.service.example`).

5. Send or forward any message to your bot in Telegram → it appears in your target group with source info.

## Scripts

| Script | Purpose |
|--------|---------|
| `tgforward` | Long-polling daemon. Listens for messages sent to the bot, downloads media, re-posts to the target group with source attribution. |
| `tgsend` | One-shot sender. `tgsend <video> <source_url> <caption_text> <author_name> <author_handle>` — sends a video with an HTML caption (text hyperlinked to source + author + hashtags). Probes real dimensions via ffprobe. |

## Caption Format

Forwarded messages get a caption like:

```
<original caption or text>

来源：<a href="https://t.me/...">Channel Name（频道）</a>
#转发存档
```

## Security Notes

- Never commit `.bot_token`, `.api_creds`, `.chat_id`, or `.allowed_users` — they are in `.gitignore`.
- All credential files should be `chmod 600`.
- The bot only responds to whitelisted usernames.

## License

MIT
