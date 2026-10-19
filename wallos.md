# wallos

> Ref: https://github.com/ellite/wallos

## Installation

```bash
cd
mkdir -p ~/Documents/wallos/db ~/Documents/wallos/logos
cd ~/Documents/wallos
vim docker-compose.yml
```

```yaml
services:
  wallos:
    image: bellamy/wallos:latest
    ports:
      - "8282:80"
    volumes:
      - ./db:/var/www/html/db
      - ./logos:/var/www/html/images/uploads/logos
```

```bash
docker compose up -d
```
