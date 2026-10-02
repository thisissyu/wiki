# codex

## Configuration

> Ref: <https://developers.openai.com/codex/config-reference>

### Add custom provider

```toml
# `~/.codex/config.toml`

model_provider = "custom"
model = "gpt-5.5"

[model_providers.custom]
name = "custom"
base_url = "http://127.0.0.1:8317/v1"
wire_api = "responses"
requires_openai_auth = true
supports_websockets = true
```

```json
// `~/.codex/auth.json`

{
  "OPENAI_API_KEY": "sk-your-api-key"
}
```

### Set reasoning effort

```toml
# `~/.codex/config.toml`

model_reasoning_effort = "xhigh"
plan_mode_reasoning_effort = "xhigh"
```

### Enable all permissions

```toml
# `~/.codex/config.toml`

approval_policy = "never"
sandbox_mode = "danger-full-access"
```

### Enable 1m context

```toml
# `~/.codex/config.toml`

model_context_window = 1050000
model_auto_compact_token_limit = 945000
```

### Use latest model or custom model

> Ref: <https://linux.do/t/topic/2042554>

```toml
# `~/.codex/config.toml`

model_catalog_json = "./models_cache.json"
```

Run `codex login` and `/model` to generate `~/.codex/models_cache.json` if you haven't done it before.

Then, modify `~/.codex/models_cache.json` to copy an old model, paste, and modify it to newest model.

e.g, copy `gpt-5.4`, and modify the model name to `gpt-5.5` or `claude-opus-4-7`.

### Add git commit co-author

```toml
# `~/.codex/config.toml`

suppress_unstable_features_warning = true

[features]
codex_git_commit = true
```

### Disable fast mode

```toml
# `~/.codex/config.toml`

[features]
fast_mode = false

[notice]
fast_default_opt_out = true
```

### Set environment variables for chatgpt app

```toml
# `~/.codex/config.toml`

[shell_environment_policy.set]
NO_PROXY = "localhost,127.0.0.1,::1"
```
