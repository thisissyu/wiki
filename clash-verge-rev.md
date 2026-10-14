# clash-verge-rev

## Configuration

### Enable TUN service for Linux

```bash
sudo clash-verge-service-install
sudo systemctl daemon-reload
sudo systemctl enable --now clash-verge-service
sudo systemctl status clash-verge-service
```

### Set chain proxy

Create a new profile (proxies are copied from your other profiles)

```yaml
# Profile Template for Clash Verge
mixed-port: 7890
allow-lan: true
bind-address: '*'
mode: rule

proxies:
  - { name: 'US', ..., dialer-proxy: 'HK' }
  - { name: 'HK', ... }

proxy-groups:
  # NOTE: The first proxy here will be the default one. so you should put outbound proxy to first to prevent connection leak.
  - { name: MyGroup, type: select, proxies: ['US', 'HK'] }

rules:
  # Example: Set target IP direct (replace to real IPv4).
  # - 'IP-CIDR,1.2.3.4/32,DIRECT,no-resolve'

  # Localhost
  - 'IP-CIDR,127.0.0.0/8,DIRECT,no-resolve'

  # Local WIFI
  - 'IP-CIDR,192.168.31.0/24,DIRECT,no-resolve'

  # School inner net
  - 'IP-CIDR,10.102.32.202/32,DIRECT,no-resolve'
  - 'IP-CIDR,10.102.32.203/32,DIRECT,no-resolve'

  # School public net
  # - 'IP-CIDR,a.b.c.d/32,DIRECT,no-resolve'

  # Other public net
  # - 'IP-CIDR,a.b.c.d/32,DIRECT,no-resolve'

  # Default internetional. Set others to go through this proxy.
  - 'MATCH,MyGroup'
```
