# sub2api

## Installation

```bash
cd sub2api/deploy

cp .env.example .env

POSTGRES_PASSWORD=$(openssl rand -hex 32)
JWT_SECRET=$(openssl rand -hex 32)
TOTP_ENCRYPTION_KEY=$(openssl rand -hex 32)

# NOTE:
# - The `-i ''` option is for macOS.
# - If you're using Linux, you can use `-i` without the empty string.

sed -i '' "s/^POSTGRES_PASSWORD=.*/POSTGRES_PASSWORD=$POSTGRES_PASSWORD/" .env
sed -i '' "s/^JWT_SECRET=.*/JWT_SECRET=$JWT_SECRET/" .env
sed -i '' "s/^TOTP_ENCRYPTION_KEY=.*/TOTP_ENCRYPTION_KEY=$TOTP_ENCRYPTION_KEY/" .env

sed -i '' "s/^ADMIN_EMAIL=.*/ADMIN_EMAIL=your_email@example.com/" .env
sed -i '' "s/^ADMIN_PASSWORD=.*/ADMIN_PASSWORD=your_password/" .env

sed -i '' "s/^BIND_HOST=.*/BIND_HOST=127.0.0.1/" .env
sed -i '' "s/^SERVER_PORT=.*/SERVER_PORT=8080/" .env

mkdir -p data postgres_data redis_data

docker compose -f docker-compose.local.yml up -d --pull always
```

## Configuration

### Set up token refresh

```bash
cd sub2api/deploy
docker compose -f docker-compose.local.yml down
vim .env
```

Add or modify these envs below. The default configuration for token refresh is as follows:

The default value is recommended, beacuse most of the oauth tokens only expires in 1 hour.

```bash
# -----------------------------------------------------------------------------
# Token Refresh Configuration
# -----------------------------------------------------------------------------
TOKEN_REFRESH_ENABLED=true  # Enable background token auto-refresh service
TOKEN_REFRESH_CHECK_INTERVAL_MINUTES=5  # Check every 5 minutes
TOKEN_REFRESH_REFRESH_BEFORE_EXPIRY_HOURS=0.5  # Refresh only if the token is expiring within 0.5 hours (30 minutes)
TOKEN_REFRESH_MAX_RETRIES=3  # Maximum number of retries if refresh fails
TOKEN_REFRESH_RETRY_BACKOFF_SECONDS=2  # Base backoff time in seconds for retries
```

After modifying the `.env` file, restart the services:

```bash
docker compose -f docker-compose.local.yml up -d --pull always
```

### Batch token refresh

> Ref: [.assets/sub2api/refresh_tokens.py](.assets/sub2api/refresh_tokens.py)

```bash
vim /usr/local/bin/sub2api-refresh-tokens.sh
```

```bash
#!/usr/bin/env bash
set -euo pipefail

BASE_URL="http://127.0.0.1:8080/api/v1"
ADMIN_KEY="your-admin-key"

resp="$(curl -fsS \
    -H "x-api-key: ${ADMIN_KEY}" \
    "${BASE_URL}/admin/accounts?page=1&page_size=1000&type=oauth")"

code="$(jq -r '.code // empty' <<<"$resp")"
if [ "$code" != "0" ]; then
    echo "list accounts failed:"
    jq . <<<"$resp"
    exit 1
fi

ids="$(jq -r '[.data.items[].id] | @json' <<<"$resp")"

if [ "$ids" = "[]" ]; then
    echo "no matching accounts"
    exit 0
fi

curl -fsS -X POST \
    -H "x-api-key: ${ADMIN_KEY}" \
    -H "Content-Type: application/json" \
    -d "{\"account_ids\":${ids}}" \
    "${BASE_URL}/admin/accounts/batch-refresh" | jq .
```

```bash
chmod +x /usr/local/bin/sub2api-refresh-tokens.sh
```

Add this into crontab. e.g, run on every day at 3am:

```bash
0 3 * * * /usr/local/bin/sub2api-refresh-tokens.sh >> /var/log/sub2api-refresh-tokens.log 2>&1
```
