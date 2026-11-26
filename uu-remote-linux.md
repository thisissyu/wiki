# uu-remote-linux

> Ref: <https://github.com/VallierDev/uu-remote-linux>

## Usage

1. Login UU Remote (only needed in first time)

   ```bash
   #!/bin/bash
   env \
       DISPLAY=:0 \
       XAUTHORITY=/run/user/1000/.mutter-Xwaylandauth.JDMVX3 \
       WAYLAND_DISPLAY=wayland-0 \
       XDG_SESSION_TYPE=wayland \
       XDG_RUNTIME_DIR=/run/user/1000 \
       DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus \
       uuremote-server login
   ```

1. Start UU Remote

   ```bash
   #!/bin/bash
   env \
       DISPLAY=:0 \
       XAUTHORITY=/run/user/1000/.mutter-Xwaylandauth.JDMVX3 \
       WAYLAND_DISPLAY=wayland-0 \
       XDG_SESSION_TYPE=wayland \
       XDG_RUNTIME_DIR=/run/user/1000 \
       DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus \
       XDG_CURRENT_DESKTOP=GNOME \
       UU_DESKTOP_BACKEND=portal \
       uuremote-server
   ```
