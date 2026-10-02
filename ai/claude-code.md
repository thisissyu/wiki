# claude-code

## Installation

```bash
brew install --cask claude-code
brew tap farion1231/ccswitch
brew install --cask cc-switch
brew install ccusage
```

## Settings

> Ref:
>
> - <https://docs.bigmodel.cn/cn/coding-plan/tool/claude#%E6%96%B9%E5%BC%8F%E4%B8%89%EF%BC%9A%E6%89%8B%E5%8A%A8%E9%85%8D%E7%BD%AE>
> - <https://code.claude.com/docs/en/settings#permission-settings>
> - <https://code.claude.com/docs/en/iam#permission-modes>

> NOTE:
>
> - Use cc-switch to automatically switch between different models.
> - The manually configuration listed below is used as reference.

`~/.claude/settings.json`

```json
{
  "enabledPlugins": {
    "ralph-loop@claude-plugins-official": true,
    "superpowers@claude-plugins-official": true
  },
  "env": {
    "API_TIMEOUT_MS": "3000000",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1",
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  },
  "permissions": {
    "defaultMode": "bypassPermissions"
  }
}
```

`~/.claude.json`

Add this:

```json
{
  "hasCompletedOnboarding": true
}
```

## Add plugins

```bash
/plugin install ralph-loop@claude-plugins-official
```

## Add skills

> Ref: [./skill.md](./skill.md)

### Add skills from marketplace

```bash
# Ref: https://github.com/anthropics/skills
/plugin marketplace add anthropics/skills
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills

# Ref: https://github.com/obra/superpowers
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace

# Ref: https://github.com/OthmanAdi/planning-with-files
/plugin marketplace add OthmanAdi/planning-with-files
/plugin install planning-with-files@planning-with-files

# NOTE: Add telegram support
# Ref: https://github.com/anthropics/claude-plugins-official
/plugin marketplace add anthropics/claude-plugins-official
/plugin install telegram@claude-plugins-official
```
