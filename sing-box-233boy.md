# sing-box-233boy

## Usage

1. Check current proxies

   ```bash
   sb info
   ```

1. Add new proxy

   ```bash
   sb add hy
   sb info hy
   ```

1. Change proxy port to 443

   ```bash
   sb port reality 443
   ```

1. Make TCP and UDP both use port 443

   ```bash
   # For example, currently we have a reality proxy in port 443, and a hy2 proxy in higher port, we want to change hy2 proxy's port to 443.

   # Disable 233boy/sing-box's port conflict check, and change hy2's port to 443
   is_cant_test_port=1 sb port hy 443

   # Check whether TCP and UDP is listening in 443
   ss -lntp | grep ':443'
   ss -lunp | grep ':443'
   ```

1. Switch proxy to ipv6

   ```bash
   # 1. First check if your sing-box is listening ipv6's port, e.g, port 443
   ss -6 -lunp | grep ':443'
   # 2. If the command above shows sing-box is listening, then you don't need to modify config, just replace current ipv4 address to ipv6
   ip='<YOUR_IPV6_ADDRESS_HERE>' sb info
   ```
