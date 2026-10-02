# [study-linux] Linux user and permission

## Create user and specify home path

```bash
sudo chmod 755 /data/workspace
sudo useradd -m -d /data/workspace/songliyu -s /bin/bash songliyu
```

- `-m`: Automatically create home dir
- `-d`: Specify home path
- `-s`: Specify default shell

```bash
sudo passwd songliyu
```

```bash
# Check permission
ls -ld /data/workspace/songliyu
# If permission not correct, fix it.
sudo chown -R songliyu:songliyu /data/workspace/songliyu
```

## Add sudo permission without password required for normal user

```text
visudo

# root and users in group wheel can run anything on any machine as any user
%admin		ALL = (ALL) ALL
root		ALL = (ALL) ALL
songliyu    ALL = (ALL:ALL) NOPASSWD:ALL
```

1. `(ALL:ALL)` means the user can run commands as any user and any group.
1. `NOPASSWD:ALL` means no password required for all commands.
1. If use `%sudo ALL=(ALL:ALL) NOPASSWD:ALL`, it means all users in the `sudo` group can run commands without password. This is not recommended for security reason.
1. IMPORTANT: Put the `songliyu ...` line at the end of the file to avoid conflicts with other rules.
