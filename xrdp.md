# xrdp

> NOTE: If you use GNOME, xrdp is not necessary. Just use `gnome-remote-desktop` in GNOME's "Setting" -> "System" -> "Remote Desktop"

## Setting Up a Lightweight Remote Browser on a Debian 12 VPS

Deploy Openbox + XRDP + Firefox ESR on a Debian 12 VPS with 1GB RAM. Access it through Windows Remote Desktop and automatically launch Firefox with Claude.

Environment: Debian 12, 1GB RAM, 10GB SSD, Windows.

1. Connect via SSH.

   Run in Windows PowerShell:

   ```powershell
   ssh -p <SSH_PORT> <VPS_USER>@<VPS_IP>
   ```

   All following Linux commands are executed as <VPS_USER> on the VPS.

1. Install the Desktop and Browser.

   ```bash
   sudo apt update
   sudo apt install -y --no-install-recommends \
   xrdp xorgxrdp openbox firefox-esr \
   dbus-x11 fonts-dejavu-core
   ```

1. Configure Openbox and Firefox.

   Create a desktop user:

   ```bash
   sudo adduser desktop
   ```

   Configure the graphical session:

   ```bash
   sudo tee /home/desktop/.xsession > /dev/null <<'EOF'
   #!/bin/sh
   exec dbus-launch --exit-with-session openbox-session
   EOF

   sudo chmod +x /home/desktop/.xsession
   sudo chown desktop:desktop /home/desktop/.xsession
   ```

   Configure Firefox to automatically open Claude:

   ```bash
   sudo mkdir -p /home/desktop/.config/openbox

   sudo tee /home/desktop/.config/openbox/autostart > /dev/null <<'EOF'
   /usr/bin/firefox-esr --new-window https://claude.ai \
       > /home/desktop/firefox-autostart.log 2>&1 &
   EOF

   sudo chown -R desktop:desktop /home/desktop/.config
   ```

1. Configure XRDP.

   Bind XRDP to localhost on port `33389`:

   ```bash
   sudo sed -i \
   '0,/^port=3389$/s|^port=3389$|port=tcp://127.0.0.1:33389|' \
   /etc/xrdp/xrdp.ini

   sudo adduser xrdp ssl-cert

   sudo systemctl enable xrdp
   sudo systemctl restart xrdp
   ```

1. Establish an SSH Tunnel from Windows.

   Run in Windows PowerShell:

   ```powershell
   ssh -p <SSH_PORT> <VPS_USER>@<VPS_IP> -N -L 127.0.0.1:33389:127.0.0.1:33389
   ```

   Keep the PowerShell window open.

1. Connect via Remote Desktop.

   Press `Win + R` and enter:

   ```text
   mstsc
   ```

   Connect to:

   ```text
   127.0.0.1:33389
   ```

   Login credentials:

   - Session: `Xorg`
   - Username: `desktop`
   - Password: The password created for the desktop user

   Recommended resolution: `1024 x 768`.

   Result: Windows connects to the VPS through an SSH tunnel. Firefox launches automatically in the Openbox desktop, opens `https://claude.ai`, and accesses the internet through the VPS.

1. Reduce RDP Bandwidth.

   - Windows Remote Desktop (mstsc):

   - Resolution: 1024 x 768
   - Color depth: 16-bit
   - Disable desktop background, font smoothing, and animations.
   - Disable showing window contents while dragging.
   - Enable persistent bitmap caching.

   - Firefox:

   - Disable video autoplay.
   - Disable smooth scrolling.
   - Keep unnecessary tabs closed.
