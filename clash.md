# clash

> Ref:
> - https://github.com/MetaCubeX/mihomo
> - https://github.com/MetaCubeX/meta-rules-dat

## Usage

```bash
# Use wget:
# wget --user-agent="Clash/1.18.0" -O main.yaml "Your subscription link here"
wget --user-agent="clash.meta" -O main.yaml "Your subscription link here"
# Or, use curl:
# curl -A "Clash/1.18.0" -o main.yaml "Your subscription link here"
curl -A "clash.meta" -o main.yaml "Your subscription link here"

~/.vocal/clash -d ~/.config/clash -f ~/.config/clash/main.yaml
```
