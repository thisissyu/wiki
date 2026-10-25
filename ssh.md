# ssh

> Ref: `man sshd_config`

## Installation

### Linux installation

```bash
sudo apt install openssh-server
sudo systemctl start ssh
```

### Mac installation

```bash
brew install openssh
sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist
```

Restart ssh service after modifying the ssh configuration file:

```bash
sudo launchctl unload /System/Library/LaunchDaemons/ssh.plist
sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist
```

## ssh-copy-id

1. In VPS's `/etc/ssh/sshd_config`, set this, then restart ssh service.

   ```text
   PubkeyAuthentication yes
   ```

   ```bash
   sudo systemctl restart ssh
   ```

2. In local machine, set ssh-copy-id:

   ```bash
   ssh-copy-id -i ~/.ssh/<YOUR_PUBLIC_KEY>.pub -p <VPS_PORT> <VPS_USER>@<VPS_IP>
   ```

## Add ssh host alias, and ssh-copy-id on windows

```powershell
# $HOME\.pwshrc.localhost.ps1

# NOTE: The function name must starts with a letter.
function s53 {
    ssh username@localhost -p 22
}

# You can use it in powershell by `s53`
```

```powershell
ssh-copy-id -i $HOME\.ssh\id_rsa.pub -p 22 username@ip.address
```

## Change ssh port

```bash
sudo vim /etc/ssh/sshd_config

# Port 22
Port 2222

sudo systemctl restart sshd
```

## Remove an entry from `~/.ssh/known_hosts`

```bash
ssh-keygen -R [10.0.224.7]:36000
# Or:
ssh-keygen -R 10.0.224.7 -p 36000
```

## Temp set ssh tunnel

```bash
# NOTE: In your laptop, run:
ssh -N -L 127.0.0.1:2095:127.0.0.1:2095 -L 127.0.0.1:2096:127.0.0.1:2096 <VPS_USER>@<VPS_IP>
# NOTE: In your laptop browser, open: http://127.0.0.1:2095/app/
```
