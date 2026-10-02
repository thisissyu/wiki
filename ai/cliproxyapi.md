# cliproxyapi

> Ref: <https://github.com/router-for-me/CLIProxyAPI>

> Notice:
> - If you want cliproxyapi website to be public available, you must change the `remote-management.secret-key` and the default `API_KEY`.
> - Don't forget to save website settings changes !!!!
> - Otherwise, your website will be dangerous.

## Installation

### Install by brew

```bash
brew install cliproxyapi
```

Then, modify configuration file, check the "Configuration" section below.

Set auto start after login computer:

```bash
# Start service and register start when login
brew services start cliproxyapi

# Check service status
brew services list

# Stop service
brew services stop cliproxyapi

# Restart service
brew services restart cliproxyapi
```

### Install by docker

```bash
cd ~/Documents
git clone git@github.com:router-for-me/CLIProxyAPI.git
cd CLIProxyAPI
vim docker-compose.yml
```

```diff
diff --git a/docker-compose.yml b/docker-compose.yml
index ad2190c2..85ebac25 100644
--- a/docker-compose.yml
+++ b/docker-compose.yml
@@ -1,6 +1,7 @@
 services:
   cli-proxy-api:
     image: ${CLI_PROXY_IMAGE:-eceasy/cli-proxy-api:latest}
+    network_mode: host
     pull_policy: always
     build:
       context: .
@@ -14,13 +15,6 @@ services:
     #   - .env
     environment:
       DEPLOY: ${DEPLOY:-}
-    ports:
-      - "8317:8317"
-      - "8085:8085"
-      - "1455:1455"
-      - "54545:54545"
-      - "51121:51121"
-      - "11451:11451"
     volumes:
       - ${CLI_PROXY_CONFIG_PATH:-./config.yaml}:/CLIProxyAPI/config.yaml
       - ${CLI_PROXY_AUTH_PATH:-./auths}:/root/.cli-proxy-api
```

Then, modify configuration file, check the "Configuration" section below.

After configuration, start service:

```bash
CLI_PROXY_CONFIG_PATH="$HOME/.cli-proxy-api/config.yaml" CLI_PROXY_AUTH_PATH="$HOME/.cli-proxy-api/auths" CLI_PROXY_LOG_PATH="$HOME/.cli-proxy-api/logs" docker compose up -d
```

## Configuration

Download config file and set frontend page's password.

```bash
# For brew, install cliproxyapi first, then, edit the default config file located below.
# Check the config file path by `cliproxyapi -h`
vim $(brew --prefix)/etc/cliproxyapi.conf

# For docker, download config file first, then install cliproxyapi.
mkdir -p ~/.cli-proxy-api/logs
wget -O ~/.cli-proxy-api/config.yaml https://ghfast.top/https://raw.githubusercontent.com/router-for-me/CLIProxyAPI/refs/heads/main/config.example.yaml
vim ~/.cli-proxy-api/config.yaml
```

```yaml
remote-management:
  # Whether to allow remote (non-localhost) management access.
  # When false, only localhost can access management endpoints (a key is still required).
  allow-remote: true
  # management key. if a plaintext value is provided here, it will be hashed on startup.
  # all management requests (even from localhost) require this key.
  # leave empty to disable the management api entirely (404 for all /v0/management routes).
  secret-key: "Enter your password here."

usage-statistics-enabled: true

# NOTE: Proxy url is only needed for docker install. No need for brew install.
# NOTE: Proxy url is only needed for docker install. No need for brew install.
# NOTE: Proxy url is only needed for docker install. No need for brew install.
# NOTE: For macOS brew install, system-wide TUN already handles outbound traffic.
# Proxy URL. Supports socks5/http/https protocols. Example: socks5://user:pass@192.168.1.1:1080/
# Per-entry proxy-url also supports "direct" or "none" to bypass both the global proxy-url and environment proxies explicitly.
proxy-url: "http://yourname:yourpassword@127.0.0.1:7897"

# Ref: https://github.com/router-for-me/CLIProxyAPI/discussions/2728
disable-cooling: true
```

## Usage

- Frontend: <http://127.0.0.1:8317/management.html>
- API subscription settings:
  - For claude:

    ```json
    // ~/.claude/settings.json

    {
      "env": {
        "ANTHROPIC_API_KEY": "your-api-key-1",
        "ANTHROPIC_BASE_URL": "http://127.0.0.1:8317",
        "ANTHROPIC_DEFAULT_HAIKU_MODEL": "claude-opus-4-6",
        "ANTHROPIC_DEFAULT_OPUS_MODEL": "claude-opus-4-6",
        "ANTHROPIC_DEFAULT_SONNET_MODEL": "claude-opus-4-6",
        "ANTHROPIC_MODEL": "claude-opus-4-6",
        "ANTHROPIC_REASONING_MODEL": "claude-opus-4-6",
        "CLAUDE_CODE_EFFORT_LEVEL": "max"
      },
      "permissions": {
        "defaultMode": "bypassPermissions"
      },
      "skipDangerousModePermissionPrompt": true
    }
    ```

    Add model projection: `(codex) gpt-5.4 -> claude-opus-4-6`

    <p><img src=".assets/cliproxyapi/img/2026-04-14-11-29-57.png" alt="" width=100% style="display: block; margin: auto;"></p>

  - For codex:

    ```toml
    # ~/.codex/config.toml

    model_provider = "newapi"
    model = "gpt-5.4"
    model_reasoning_effort = "xhigh"
    approval_policy = "never"
    sandbox_mode = "danger-full-access"

    [model_providers.newapi]
    name = "NewAPI"
    base_url = "http://127.0.0.1:8317/v1"
    wire_api = "responses"
    requires_openai_auth = true
    ```
