# opencode

## Installation

```bash
# Install opencode
brew install anomalyco/tap/opencode

# Install oh-my-opencode
brew tap oven-sh/bun
brew install bun
bunx oh-my-opencode install
```

## Add custom provider

> Ref: <https://opencode.ai/docs/providers/#custom-provider>

```bash
opencode auth login
```

`~/.config/opencode/opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "my-custom": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "my-custom",
      "options": {
        "baseURL": "https://api.siliconflow.cn/v1"
      },
      "models": {
        "deepseek-ai/DeepSeek-R1": {
          "name": "deepseek-ai/DeepSeek-R1"
        }
      }
    }
  },
  "plugin": ["oh-my-opencode"],
  "permission": {
    "*": "allow"
  }
}
```

`~/.local/share/opencode/auth.json`

```json
{
  "my-custom": {
    "type": "api",
    "key": "sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
  }
}
```

`~/.config/opencode/oh-my-opencode.json`

```json
{
  "$schema": "https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/master/assets/oh-my-opencode.schema.json",
  "agents": {
    "Sisyphus": {
      "model": "my-custom/deepseek-ai/DeepSeek-R1"
    },
    "Planner-Sisyphus": {
      "model": "my-custom/deepseek-ai/DeepSeek-R1"
    },
    "librarian": {
      "model": "my-custom/deepseek-ai/DeepSeek-R1"
    },
    "explore": {
      "model": "my-custom/deepseek-ai/DeepSeek-R1"
    },
    "oracle": {
      "model": "my-custom/deepseek-ai/DeepSeek-R1"
    },
    "frontend-ui-ux-engineer": {
      "model": "my-custom/deepseek-ai/DeepSeek-R1"
    },
    "document-writer": {
      "model": "my-custom/deepseek-ai/DeepSeek-R1"
    },
    "multimodal-looker": {
      "model": "my-custom/deepseek-ai/DeepSeek-R1"
    }
  }
}
```

## Add skills

> Ref: <https://opencode.ai/docs/skills/#place-files>
