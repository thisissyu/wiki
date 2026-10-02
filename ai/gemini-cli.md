# gemini-cli

## Official version

```bash
brew install gemini-cli

# Set proxy to TUN mode.
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7890
gemini-cli -m gemini-2.5-pro
```

## Fork version

```bash
git clone git@github.com:ysl2/gemini-cli.git -b v0.1.15
cd gemini-cli
npm run clean && npm run build && npm install -g .
```

Usage:

```bash
# For google login:
NODE_NO_WARNINGS=1 http_proxy=http://127.0.0.1:7890 https_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7890 GOOGLE_CLOUD_PROJECT=test-project-key gemini -m gemini-2.5-pro

# For openai API models:
NODE_NO_WARNINGS=1 OPENAI_API_KEY=test-api-key OPENAI_BASE_URL=https://api.example.com OPENAI_MODEL=test-model gemini
```
