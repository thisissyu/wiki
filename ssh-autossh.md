# autossh

1. Prepare two machines:

   Inner A
   - local ssh address: 127.0.0.1
   - ssh port: 22
   - username: innuser

   Public B
   - ip: 47.xxx.xxx.227
   - ssh port: 36000
   - username: pubuser

1. In Public B, edit `/etc/ssh/sshd_config`, change `GatewayPorts` to `clientspecified`, and set the settings below, then restart ssh service.

   ```text
   GatewayPorts clientspecified
   ClientAliveInterval 15
   ClientAliveCountMax 3
   ```

   ```bash
   sudo systemctl reload ssh
   ```

1. In Inner A, genetate ssh key，and copy it to Public B.

   ```bash
   ssh-keygen
   ssh-copy-id -p 36000 pubuser@47.xxx.xxx.227
   ```

1. In Inner A, install autossh, and execute the following command to create a reverse ssh tunnel.

   NOTE: There are 3 listen address choices, plus 2 monitor methods, 6 commands for total, just choose one.

   The basic command template is:

   ```bash
   # NOTE: "$AUTOSSH_PATH" is optional, to specify which ssh you want to use.
   AUTOSSH_PATH=/path/to/ssh \
   autossh -M [monitor_port] -o ExitOnForwardFailure=yes -o ServerAliveInterval=15 -o ServerAliveCountMax=3 \
       -NfR [Public B listen address]:[Public B port]:[Inner A target address]:[Inner A port] -p 36000 pubuser@47.xxx.xxx.227
   ```

   Where there are three choices to select the public B listen address, the difference is:

   | Public B listen address | Visit from Public B (as jumphost) | Visit from Internet | NOTE                                                                      |
   | ----------------------- | --------------------------------- | ------------------- | ------------------------------------------------------------------------- |
   | 0.0.0.0                 | Y                                 | Y                   |                                                                           |
   | 47.xxx.xxx.227          | Y (only via public IP)            | Y                   | Requires that IP to be assigned to a local network interface on Public B. |
   | 127.0.0.1               | Y                                 | N                   |                                                                           |

   There are two monitor methods, `autossh -M 0` (recommend) and `autossh -M [monitor port]`.

   - (Recommended) Method 1: Use `autossh -M 0`. OpenSSH detects connection failures, and autossh automatically restarts SSH.

     ```bash
     # Choose one public listen address choice to fit you need.

     # Choice 1, set public listen address to 0.0.0.0
     autossh -M 0 -o ExitOnForwardFailure=yes -o ServerAliveInterval=15 -o ServerAliveCountMax=3 -NfR 0.0.0.0:36001:127.0.0.1:22 -p 36000 pubuser@47.xxx.xxx.227
     # Test login: There must be `innuser`, not `pubuser`.
     ssh -p 36001 innuser@47.xxx.xxx.227

     # Choice 2, set public listen address to 47.xxx.xxx.227
     autossh -M 0 -o ExitOnForwardFailure=yes -o ServerAliveInterval=15 -o ServerAliveCountMax=3 -NfR 47.xxx.xxx.227:36001:127.0.0.1:22 -p 36000 pubuser@47.xxx.xxx.227
     # Test login: There must be `innuser`, not `pubuser`.
     ssh -p 36001 innuser@47.xxx.xxx.227

     # Choice 3, set public listen address to 127.0.0.1
     autossh -M 0 -o ExitOnForwardFailure=yes -o ServerAliveInterval=15 -o ServerAliveCountMax=3 -NfR 127.0.0.1:36001:127.0.0.1:22 -p 36000 pubuser@47.xxx.xxx.227
     # Test login: Run on Public B. There must be `innuser`, not `pubuser`.
     ssh -p 36001 innuser@127.0.0.1
     ```

     Full port occupation table as below:

     | Port  | Inner A | Public B | Note                                 |
     | ----- | ------- | -------- | ------------------------------------ |
     | 36001 |         | Y        | For logging in Inner A from Public B |
     | 22    | Y       |          | Inner A's ssh port                   |
     | 36000 |         | Y        | Public B's ssh port                  |

   - Method 2, use autossh independent monitor tunnel `autossh -M [monitor port]` to let autossh to detect error and auto restore.

     ```bash
     # NOTE: Just replace the commands above from `autossh -M 0` to `autossh -M 46001`
     ```

     IMPORTANT: This will automatically occupy two monitor port in Inner A, but only one monitor port in Public B.

     Full port occupation table as below:

     | Port  | Inner A | Public B | Note                                       |
     | ----- | ------- | -------- | ------------------------------------------ |
     | 46001 | Y       | Y        | Monitor port 1 (manually specified by us)  |
     | 46002 | Y       |          | Monitor port 2 (auto genetated by autossh) |
     | 36001 |         | Y        | For logging in Inner A from Public B       |
     | 22    | Y       |          | Inner A's ssh port                         |
     | 36000 |         | Y        | Public B's ssh port                        |

1. Kill: In Inner A:

   ```bash
   pgrep -af autossh
   kill -TERM <autossh_PID>
   ```

1. Batch script `~/autos.sh`

   ```bash
   #!/bin/bash
   SSH_OPTS=(
     -o ExitOnForwardFailure=yes
     -o ServerAliveInterval=15
     -o ServerAliveCountMax=3
   )

   autossh -M 0 "${SSH_OPTS[@]}" -NfR 0.0.0.0:37000:127.0.0.1:7000 -p 36000 pubuser@47.xxx.xxx.227
   autossh -M 0 "${SSH_OPTS[@]}" -NfR 0.0.0.0:38000:127.0.0.1:8000 -p 36000 pubuser@47.xxx.xxx.227
   ```
