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
bind-address: "*"
mode: rule

proxies:
  - { name: "US", ..., dialer-proxy: "HK" }
  - { name: "HK", ... }

proxy-groups:
  # NOTE: The first proxy here will be the default one. so you should put outbound proxy to first to prevent connection leak.
  - { name: MyGroup, type: select, proxies: ["US", "HK"] }

rules:
  # Example: Set target IP direct (replace to real IPv4).
  # - "IP-CIDR,1.2.3.4/32,DIRECT,no-resolve"

  # Localhost
  - "IP-CIDR,127.0.0.0/8,DIRECT,no-resolve"

  # Local WIFI
  - "IP-CIDR,192.168.31.0/24,DIRECT,no-resolve"

  # School inner net
  - "IP-CIDR,10.102.32.202/32,DIRECT,no-resolve"
  - "IP-CIDR,10.102.32.203/32,DIRECT,no-resolve"

  # School public net
  # - "IP-CIDR,a.b.c.d/32,DIRECT,no-resolve"

  # Other public net
  # - "IP-CIDR,a.b.c.d/32,DIRECT,no-resolve"

  # Default internetional. Set others to go through this proxy.
  - "MATCH,MyGroup"
```

### Kill Switch with nftables (Debian + Clash Verge Rev)

Objective:

- All regular outbound internet traffic must pass through Clash, which determines whether to use `PROXY` or `DIRECT` based on its routing rules.
- If Clash is not running, crashes, or its TUN interface becomes unavailable, applications must not be allowed to bypass Clash and connect directly to the internet.
- Replies to established inbound connections (e.g., SSH and RDP) are allowed to bypass Clash.

1. Environment

   - Debian Linux (systemd 262)
   - Clash Verge Rev
   - TUN interface: `Meta`
   - Physical network interfaces: `wlp3s0`, `enp2s0`
   - Network manager: NetworkManager

   The following configuration is specific to this environment. Commands automatically create or overwrite the corresponding configuration files. Perform these steps on a machine where local network recovery is possible.

1. Create nftables Rules

   ```bash
   sudo mkdir -p /etc/nftables.d

   sudo tee /etc/nftables.d/clash-guard.nft >/dev/null <<'EOF'
   table inet clash_guard {
       set mihomo_svc {
           type cgroupsv2
       }

       chain output_guard {
           type filter hook output priority filter; policy drop;

           oifname "lo" accept
           oifname "Meta" accept

           oifname { "wlp3s0", "enp2s0" } ct direction reply ct state established counter accept

           oifname { "wlp3s0", "enp2s0" } udp sport 68 udp dport 67 accept
           oifname { "wlp3s0", "enp2s0" } udp sport 546 udp dport 547 accept

           oifname { "wlp3s0", "enp2s0" } icmpv6 type {
               nd-router-solicit,
               nd-neighbor-solicit,
               nd-neighbor-advert
           } accept

           meta l4proto { tcp, udp } socket cgroupv2 level 2 @mihomo_svc accept

           counter drop
       }

       chain forward_guard {
           type filter hook forward priority filter; policy drop;

           oifname "Meta" accept
           iifname "Meta" ct state established,related accept

           counter drop
       }
   }
   EOF
   ```

   Validate the configuration syntax:

   ```bash
   sudo nft -c -f /etc/nftables.d/clash-guard.nft
   ```

1. Configure the Mihomo Service Allowlist

   Configure systemd to automatically maintain the Mihomo service's cgroup allowlist, allowing Clash to establish outbound connections according to its routing rules.

   ```bash
   sudo mkdir -p /etc/systemd/system/clash-verge-service.service.d

   sudo tee /etc/systemd/system/clash-verge-service.service.d/90-clash-guard.conf >/dev/null <<'EOF'
   [Service]
   NFTSet=cgroup:inet:clash_guard:mihomo_svc
   EOF
   ```

1. Create the Kill Switch Service

   ```bash
   sudo tee /etc/systemd/system/clash-guard.service >/dev/null <<'EOF'
   [Unit]
   Description=Clash Fail-Closed Firewall
   DefaultDependencies=no
   Wants=network-pre.target
   Before=network-pre.target clash-verge-service.service shutdown.target
   After=local-fs.target nftables.service
   Conflicts=shutdown.target

   [Service]
   Type=oneshot
   RemainAfterExit=yes
   ExecStart=/usr/sbin/nft -f /etc/nftables.d/clash-guard.nft

   [Install]
   WantedBy=sysinit.target
   EOF
   ```

1. Configure the NetworkManager Startup Dependency

   Ensure NetworkManager starts only after the Kill Switch has been successfully loaded.

   ```bash
   sudo mkdir -p /etc/systemd/system/NetworkManager.service.d

   sudo tee /etc/systemd/system/NetworkManager.service.d/90-require-clash-guard.conf >/dev/null <<'EOF'
   [Unit]
   Requires=clash-guard.service
   After=clash-guard.service
   EOF
   ```

1. Enable the Kill Switch

   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable clash-guard.service
   ```

1. Reboot the System

   ```bash
   sudo reboot
   ```

   The Kill Switch will be loaded automatically during system startup.

1. Expected Behavior

   | Scenario                    | Behavior                                                           |
   | --------------------------- | ------------------------------------------------------------------ |
   | Clash is running normally   | Traffic is routed according to Clash rules                         |
   | Clash selects DIRECT        | Clash is allowed to establish a direct connection                  |
   | Clash is not running        | Regular outbound internet traffic is blocked                       |
   | Clash crashes or exits      | Applications are prevented from falling back to direct connections |
   | TUN becomes unavailable     | Applications are prevented from bypassing TUN                      |
   | Inbound SSH/RDP connections | Established connection replies are allowed directly                |
   | System reboots              | Kill Switch loads automatically                                    |

   Core principle: Clash controls traffic routing, while nftables prevents regular outbound traffic from bypassing Clash.

   Note: This configuration protects conventional IP outbound and forwarded traffic. It does not guarantee absolute leak prevention for every protocol, isolated network namespace, or privileged operation.
