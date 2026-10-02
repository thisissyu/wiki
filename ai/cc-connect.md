# cc-connect

> - https://github.com/chenhg5/cc-connect/blob/main/INSTALL.md
> - https://github.com/chenhg5/cc-connect/blob/main/docs/usage.md

## Installation

```bash
npm install -g cc-connect@beta
# NOTE: For macOS only:
xattr -d com.apple.quarantine cc-connect
# Add into auto start service:
cc-connect daemon start
```

## Configuration

```toml
# cc-connect configuration
# Docs: https://github.com/chenhg5/cc-connect

[log]
level = "info"  # debug, info, warn, error

[[projects]]
name = "my-project"

[projects.agent]
type = "codex"  # "claudecode", "codex", "cursor", "gemini", "qoder", "opencode", or "iflow"

[projects.agent.options]
work_dir = "/Users/your-username"
mode = "yolo"  # "suggest" (default), "auto-edit", "full-auto", "yolo"
provider = "openai"   # active provider
# model = "gpt-5.4"  # optional: specify model

[[projects.agent.providers]]
name = "openai"
api_key = "your-api-key-1"
base_url = "http://127.0.0.1:8317/v1"
model = "gpt-5.4"

# --- Choose at least one platform below ---

# Feishu / Lark (WebSocket, no public IP needed)
# [[projects.platforms]]
# type = "feishu"
#
# [projects.platforms.options]
# app_id = "your-feishu-app-id"
# app_secret = "your-feishu-app-secret"

# For more platforms (DingTalk, Telegram, Slack, Discord, LINE, WeChat Work)
# see: https://github.com/chenhg5/cc-connect/blob/main/config.example.toml

[[projects.platforms]]
type = "telegram"

[projects.platforms.options]
token = "1234567890:ABCdefGHIjklMNOpqrsTUVwxyz"
```
