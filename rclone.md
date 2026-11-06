# rclone

> Ref: <https://github.com/rclone/rclone>

1. Installation

   ```bash
   sudo apt install rclone
   ```

1. Set allow other in global config

   ```bash
   sudo vim /etc/fuse.conf
   # Change from `#user_allow_other` to `user_allow_other`
   ```

1. Add a remote

   ```bash
   rclone config
   ```

1. Test connection

   ```bash
   # `rclone lsd <config_name>:`
   # For example,
   rclone lsd 203:
   ```

1. Mount

   ```bash
   mkdir -p ~/Public/203
   rclone mount 203: ~/Public/203 --vfs-cache-mode full --allow-other --daemon
   ```

1. Check mount status

   ```bash
   findmnt -M ~/Public/203
   ```

1. Unmount

   ```bash
   fusermount3 -u ~/Public/203
   ```
