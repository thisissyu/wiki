# openclaw

## Installation

Refer to: <https://openclaw.ai/>

Run on windows:

```powershell
$env:OPENCLAW_DISABLE_BONJOUR="1"; openclaw gateway run --force --verbose
openclaw dashboard
```

## Add full access

`nvim ~/.openclaw/openclaw.json`

```json
{
  ...
  "tools": {
    "profile": "full"
  },
  ...
}
```

## Add skills

Refer to [./skill.md](./skill.md)

## Add browser support

- Online install

  <https://chromewebstore.google.com/detail/openclaw-browser-relay/nglingapjinhecnfejdcpihlpneeadjp?pli=1>

- Offline install:

  ```bash
  openclaw browser extension install
  openclaw browser extension path

  # The command above will show this path: `~/.openclaw/browser/chrome-extension`
  ```

Enable chrome's developer mode, and add the path above into chrome extensions page.

## Add web_search support

```bash
openclaw configure --section web
# Then, enter the brave search api key.
```

## QQ Bot

<https://q.qq.com/qqbot/openclaw/login.html>

1. For old version

   ```bash
   openclaw plugins install @sliverp/qqbot@latest
   openclaw channels add --channel qqbot --token "xxxxxxxxxxxxxxxxxxxxxxxxxxx"
   openclaw gateway restart
   ```

2. For new version

   > Ref: <https://github.com/tencent-connect/openclaw-qqbot>

   ```bash
   # Uninstall old plugins (skip if first install)
   openclaw plugins uninstall qqbot
   openclaw plugins uninstall openclaw-qqbot

   # Install latest
   openclaw plugins install @tencent-connect/openclaw-qqbot@latest

   # Configure channel (first install only)
   openclaw channels add --channel qqbot --token "AppID:AppSecret"

   # Start / restart
   openclaw gateway restart
   ```
