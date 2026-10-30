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

## Temporary Port Forwarding

```bash
# NOTE: In your laptop, run:
ssh -p <VPS_SSH_PORT> <VPS_USER>@<VPS_IP> -N -L 127.0.0.1:2095:127.0.0.1:2095 -L 127.0.0.1:2096:127.0.0.1:2096
# NOTE: In your laptop browser, open: http://127.0.0.1:2095/app/
```

## Temporary Port Forwarding Through Two SSH Jump Hosts

Your local Mac A, on a private network, connects to development machine D through jump host B (SSH port 36003) and then bastion host C (SSH port 36000).

D already uses [autossh](./ssh-autossh.md) to expose its SSH port `36002` at `127.0.0.1:36001` on C through a reverse SSH tunnel.

```text
The arrows below show which machine initiates each underlying TCP connection.

+---------------------+     +---------------------+     +---------------------------+
| A: Local Mac        | --> | B: Jump host 1      | --> | C: Bastion / jump host 2  |
| Private network     |     | SSH: 36003          |     | SSH: 36000                |
| Local entry: 38137  |     +---------------------+     | Reverse SSH entry:        |
+---------------------+                                 | 127.0.0.1:36001           |
                                                        +---------------------------+
                                                                      ^
                                                                      |
                                                          Existing autossh connection
                                                          initiated by D to C:36000
                                                                      |
                                                        +---------------------------+
                                                        | D: Development machine    |
                                                        | SSH: 36002                |
                                                        | Service: 8317             |
                                                        +---------------------------+

Existing reverse mapping:
C:127.0.0.1:36001 --> D:127.0.0.1:36002

Service access through the SSH tunnel:
A:127.0.0.1:38137 --> D:127.0.0.1:8317
```

To access the service on D's port `8317` through A's port `38137`, run the following command on A:

```bash
ssh -o ExitOnForwardFailure=yes \
  -J <USER_B>@<VPS_B>:36003,<USER_C>@<VPS_C>:36000 \
  -p 36001 <USER_D>@127.0.0.1 \
  -N \
  -L 127.0.0.1:38137:127.0.0.1:8317
```

Replace the usernames and host addresses. Once connected, access `127.0.0.1:38137` on A.

Parameter explanations:

- `-J`: Specifies the jump hosts in order: B, then C.
- `-p 36001 <USER_D>@127.0.0.1`: Connects from the final jump host C to its own port `36001`, then follows the existing reverse tunnel to log in to D.
- `-L`: Creates a local listener on A's port `38137` and forwards incoming connections to D's port `8317`.

Keep the command running. When finished, press `Ctrl+C` to close the temporary tunnel.

### `-L` vs `-R`

- `-L`: Creates the forwarding entry on Local.
- `-R`: Creates the forwarding entry on Remote.

When SSH runs on A and logs in to D through B and C, the listener is the entry point you connect to; SSH forwards that connection to the target service.

| Forwarding option                   | Listener (forwarding entry point you connect to) | Target service      |
| ----------------------------------- | ------------------------------------------------ | ------------------- |
| `-L 127.0.0.1:38137:127.0.0.1:8317` | A: `127.0.0.1:38137`                             | D: `127.0.0.1:8317` |
| `-R 127.0.0.1:38137:127.0.0.1:8317` | D: `127.0.0.1:38137`                             | A: `127.0.0.1:8317` |

Use `-L` here: connect to `127.0.0.1:38137` on A to access the service on D's port `8317`.
