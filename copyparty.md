# copyparty

> Ref: https://github.com/9001/copyparty

## Installation

```yaml
services:
  copyparty:
    image: copyparty/ac:latest
    container_name: copyparty
    restart: unless-stopped

    user: "${UID:-1000}:${GID:-1000}"

    ports:
      - "33923:3923"

    volumes:
      - "${HOME}:/w:rw"

    command:
      - "-v"
      - "/w::rw"
      - "--theme=1"
```

```bash
docker compose up -d
```
