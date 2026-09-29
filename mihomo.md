# mihomo

> Ref:
> - https://github.com/MetaCubeX/mihomo
> - https://github.com/MetaCubeX/meta-rules-dat

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
