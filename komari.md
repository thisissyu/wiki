# komari

## Installation

```bash
vim docker-compose.yml
```

```yaml
version: "3.8"
services:
  komari:
    image: ghcr.io/komari-monitor/komari:latest
    container_name: komari
    ports:
      - "25774:25774"
    volumes:
      - ./data:/app/data
    restart: unless-stopped
```

```bash
docker compose up -d
```

## Usage

1. In inner net komari panel machine, add autossh tunnel to public net VPS:

   ```bash
   autossh -M 0 -o ExitOnForwardFailure=yes -o ServerAliveInterval=15 -o ServerAliveCountMax=3 -NfR 127.0.0.1:25774:127.0.0.1:25774 -p 36000 root@<VPS_IP>
   ```

1. Install agent in VPS with command provided by komari web.

## Configuration

1. Theme: <https://github.com/sanrokamlan-prog/komari-theme-Glassmorphism>

1. Latency monitor:

   > Ref: <https://www.zstaticcdn.com/>

   | Task          | Type | Target                        |
   | ------------- | ---- | ----------------------------- |
   | 山东电信 IPv4 | TCP  | sd-ct-v4.ip.zstaticcdn.com:80 |
   | 山东联通 IPv4 | TCP  | sd-cu-v4.ip.zstaticcdn.com:80 |
   | 山东移动 IPv4 | TCP  | sd-cm-v4.ip.zstaticcdn.com:80 |
