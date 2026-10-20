# mihomo

> Ref:
>
> - <https://github.com/MetaCubeX/mihomo>
> - <https://github.com/MetaCubeX/meta-rules-dat>

## Usage

```bash
# Use wget:
# wget --user-agent="Clash/1.18.0" -O config.yaml "Your subscription link here"
wget --user-agent="clash.meta" -O config.yaml "Your subscription link here"
# Or, use curl:
# curl -A "Clash/1.18.0" -o config.yaml "Your subscription link here"
curl -A "clash.meta" -o config.yaml "Your subscription link here"

~/.vocal/mihomo -d ~/.config/mihomo -f ~/.config/mihomo/config.yaml
```

```bash
curl -s http://127.0.0.1:39090/proxies | jq '.proxies | to_entries[] | select(.value.type == "Selector") | {group: .key, current: .value.now, nodes: .value.all}'
curl -X PUT -H 'Content-Type: application/json' -d '{"name":"美国2"}' 'http://127.0.0.1:39090/proxies/BoostNet'
```

## Configuration

### Set authentication

1. In clash's config file:

   ```yaml
   authentication:
     - "your_username:your_password"
   secret: "your_password"
   ```

2. In your `~/.bashrc`:

   ```bash
   export http_proxy='http://your_username:your_password@127.0.0.1:7890'
   export https_proxy="$http_proxy"
   export all_proxy="$http_proxy"

   export HTTP_PROXY="$http_proxy"
   export HTTPS_PROXY="$https_proxy"
   export ALL_PROXY="$all_proxy"
   ```
