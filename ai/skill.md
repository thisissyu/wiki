# skill

## Skill source

- <https://skills.sh>
- <https://skillhub.tencent.com/>
- <https://clawhub.ai/>

## Add skills

### Add skills from `http://skills.sh`

```bash
# npx skills add vercel-labs/skills -s find-skills -g -y
# npx skills add vercel-labs/agent-browser -s agent-browser -g -y
npx skills add obra/superpowers -g -y
npx skills add othmanadi/planning-with-files -s planning-with-files -g -y
npx skills add anthropics/skills -s pdf -g -y
npx skills add cloudai-x/threejs-skills -g -y
```

### Add skills from source

```bash
mkdir -p ~/.claude/skills
cd ~/.claude
git clone git@github.com:anthropics/skills.git skills-anthropics
cd skills
ln -s ../skills-anthropics/skills/* ./
```

## superpowers

### Install & Uninstall (local)

#### codex

```bash
# Installtion
git clone git@github.com:obra/superpowers.git
cd superpowers
codex plugin marketplace add "$PWD"
codex plugin add superpowers@superpowers-dev
codex plugin list

# Update
codex plugin remove superpowers@superpowers-dev
codex plugin add superpowers@superpowers-dev

# Uninstall
codex plugin remove superpowers@superpowers-dev
codex plugin marketplace remove superpowers-dev
```

#### opencode

```bash
# Install
git clone git@github.com:obra/superpowers.git
cd superpowers
opencode plugin "$PWD" --global

# Uninstall
# Manually remove these in ~/.config/opencode/opencode.jsonc
# {
# "plugin": [
#     "/Users/songliyu/Documents/doctor/researches/topic-modeling/superpowers"
# ]
# }
# # Ref:
# # - https://opencode.ai/docs/plugins
# # - https://opencode.ai/docs/troubleshooting
```

#### claude-code

```bash
# Install
git clone git@github.com:obra/superpowers.git
cd superpowers
# Temp local test, no persistent install:
# claude --plugin-dir "$PWD"
# claude -p "Tell me about your superpowers" --plugin-dir "$PWD"
claude
/plugin marketplace add /Users/songliyu/Documents/doctor/researches/topic-modeling/superpowers
/plugin install superpowers@superpowers-dev

# Update
/reload-plugins

# Uninstall
claude plugin list
claude plugin uninstall superpowers@superpowers-dev
claude plugin uninstall superpowers@superpowers-dev --scope user
claude plugin uninstall superpowers@superpowers-dev --scope project
claude plugin uninstall superpowers@superpowers-dev --scope local
claude plugin marketplace list
claude plugin marketplace remove superpowers-dev

# Ref:
# - https://code.claude.com/docs/en/plugins
# - https://code.claude.com/docs/en/discover-plugins
```
