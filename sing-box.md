# sing-box

## Manually Setting Up sing-box on a VPS: VLESS + REALITY + AT&T IPv6

Environment: Debian VPS, sing-box 1.14.3, XpertV6, and Clash Verge Rev on Windows.

Goal: Connect to a VPS using VLESS + REALITY and route outbound traffic through a specific AT&T IPv6 address.

### 1. Prepare the IPv6 Address

Make sure XpertV6 has assigned the leased IPv6 address to your VPS.

Verify:

```bash
curl -6 --interface YOUR_ATT_IPV6 https://api6.ipify.org
```

Expected output: `YOUR_ATT_IPV6`.

### 2. Install sing-box

Use the [official installation script](https://sing-box.sagernet.org/installation/):

```bash
curl -fsSL https://sing-box.app/install.sh | sh
sing-box version
```

Make sure TCP port 443 is available:

```bash
ss -lntp '( sport = :443 )'
```

### 3. Generate REALITY Credentials

```bash
sing-box generate uuid
sing-box generate reality-keypair
sing-box generate rand --hex 8
```

Save the generated values:

- `UUID`
- `PrivateKey`
- `PublicKey`
- `Short ID`

Keep the private key on your VPS. Never share it.

### 4. Configure the Server

Edit `/etc/sing-box/config.json`:

```json
{
  "log": {
    "level": "info"
  },
  "dns": {
    "servers": [{ "type": "local", "tag": "local" }],
    "strategy": "ipv6_only"
  },
  "inbounds": [
    {
      "type": "vless",
      "listen": "0.0.0.0",
      "listen_port": 443,
      "users": [
        {
          "uuid": "YOUR_UUID",
          "flow": "xtls-rprx-vision"
        }
      ],
      "tls": {
        "enabled": true,
        "server_name": "www.cloudflare.com",
        "reality": {
          "enabled": true,
          "handshake": {
            "server": "www.cloudflare.com",
            "server_port": 443
          },
          "private_key": "YOUR_PRIVATE_KEY",
          "short_id": ["YOUR_SHORT_ID"]
        }
      }
    }
  ],
  "outbounds": [
    {
      "type": "direct",
      "tag": "att-ipv6",
      "inet6_bind_address": "YOUR_ATT_IPV6",
      "domain_resolver": {
        "server": "local",
        "strategy": "ipv6_only"
      }
    }
  ],
  "route": {
    "rules": [
      { "action": "resolve", "strategy": "ipv6_only" },
      { "ip_version": 4, "action": "reject" }
    ],
    "final": "att-ipv6"
  }
}
```

Replace all `YOUR_...` placeholders with your actual values.

### 5. Start sing-box

```bash
chown sing-box:sing-box /etc/sing-box/config.json
chmod 600 /etc/sing-box/config.json

sing-box check -c /etc/sing-box/config.json

systemctl enable sing-box
systemctl restart sing-box
systemctl is-active sing-box
```

Make sure your VPS firewall allows inbound TCP traffic on port 443.

### 6. Configure Clash Verge Rev

Create a new local YAML configuration in Clash Verge Rev on Windows:

```yaml
mixed-port: 7897
allow-lan: false
mode: rule
ipv6: true

proxies:
  - name: ATT-REALITY
    type: vless
    server: YOUR_VPS_IPV4
    port: 443
    uuid: YOUR_UUID
    network: tcp
    tls: true
    servername: www.cloudflare.com
    client-fingerprint: chrome
    flow: xtls-rprx-vision
    reality-opts:
      public-key: YOUR_PUBLIC_KEY
      short-id: YOUR_SHORT_ID

proxy-groups:
  - name: VPS
    type: select
    proxies:
      - ATT-REALITY

rules:
  - MATCH,VPS
```

Activate this configuration and select `ATT-REALITY`.

System Proxy and TUN can both remain disabled. Applications can connect manually through the local proxy at `127.0.0.1:7897`.

### 7. Verify the Outbound IP

Run the following command in Windows PowerShell:

```powershell
curl.exe -x http://127.0.0.1:7897 https://api6.ipify.org
```

Expected output: your leased AT&T IPv6 address.

Alternatively, open the following URL in a browser configured to use the proxy:

<https://v6.ipinfo.io/json>

Verified results from this setup:

| Field   | Result               |
| ------- | -------------------- |
| IP      | Configured AT&T IPv6 |
| ASN     | AS7018               |
| ISP     | AT&T                 |
| Country | US                   |
| Region  | Illinois             |
| City    | Warrenville          |

Setup complete.

Final connection path:

`Windows  Clash Verge Rev  VLESS + REALITY  VPS sing-box  AT&T IPv6  Internet`

Note: This configuration enforces IPv6-only outbound connections, so IPv4-only websites may be inaccessible. REALITY and TCP port 443 do not guarantee immunity from network blocking.
