# ssh-sshfs

## Installation

```bash
sudo apt install -y sshfs
```

## Usage

```bash
sshfs <REMOTE_USER>@<REMOTE_IP>:<REMOTE_PATH> <LOCAL_PATH> -o reconnect,ServerAliveInterval=15,ServerAliveCountMax=3,idmap=user,allow_other
```
