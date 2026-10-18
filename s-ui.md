# s-ui

## Deploy s-ui in VPS

> Ref: <https://github.com/alireza0/s-ui>

1. Set allow policy in vps frontend firewall

   Create an allow policy group, then check and ensure this group contains two policies below, and not open port 2095.

   The keypoint is, we should allow port 22 and port 443, while keep port 2095 closed.

   | Key           | Value     |
   | ------------- | --------- |
   | Direction     | Inbound   |
   | Protocol      | TCP       |
   | Port Range    | 22        |
   | Authorized IP | 0.0.0.0/0 |
   | Note          | SSH       |

   | Key           | Value     |
   | ------------- | --------- |
   | Direction     | Inbound   |
   | Protocol      | TCP       |
   | Port Range    | 443       |
   | Authorized IP | 0.0.0.0/0 |
   | Note          | Reality   |

1. Install s-ui

   ```bash
   bash <(curl -Ls https://raw.githubusercontent.com/alireza0/s-ui/master/install.sh)
   # NOTE: Input `n` to keep default settings.

   s-ui
   # NOTE: Select `6` to set username and password.
   ```

1. In your current physical laptop, run:

   ```bash
   ssh -N -L 127.0.0.1:2095:127.0.0.1:2095 -L 127.0.0.1:2096:127.0.0.1:2096 root@<VPS_IP>
   # NOTE: In your laptop browser, open: http://127.0.0.1:2095/app/
   ```

1. Panel settings:

   - TLS Settings → Add

     | Setting              | Value                     |
     | -------------------- | ------------------------- |
     | Name                 | `Reality`                 |
     | Type                 | `Reality`                 |
     | SNI                  | `www.microsoft.com`       |
     | Handshake Server     | `www.microsoft.com`       |
     | Server Port          | `443`                     |
     | Private / Public Key | Click Generate            |
     | Short IDs            | Click Refresh to generate |
     | Fingerprint          | `Chrome`                  |
     | ECH                  | Disabled                  |

     Click Save.

   - Inbounds → Add

     Server Side

     | Setting          | Value        |
     | ---------------- | ------------ |
     | Type             | `VLESS`      |
     | Tag              | `US-Reality` |
     | Address          | `::`         |
     | Port             | `443`        |
     | Enable Transport | Disabled     |
     | Clients          | `None`       |
     | TLS Template     | `Reality`    |
     | Enable Multiplex | Disabled     |

     Client Side

     | Setting             | Value            |
     | ------------------- | ---------------- |
     | Network             | `TCP/UDP`        |
     | UDP Packet Encoding | `none`           |
     | Dial Options        | Leave as default |
     | Multi Domain        | Click `+`        |
     | Address             | `<VPS_IP>`       |
     | Port                | `443`            |

     Click Save.

   - Clients → Add

     Basics

     | Setting        | Value               |
     | -------------- | ------------------- |
     | Enable         | Enabled             |
     | Name           | `myuser`            |
     | Inbounds       | Select `US-Reality` |
     | Other Settings | Leave as default    |

     Config

     | Setting        | Value                   |
     | -------------- | ----------------------- |
     | VLESS UUID     | Automatically generated |
     | VLESS Flow     | `xtls-rprx-vision`      |
     | Other Settings | Leave as default        |

     Click Save.

1. Get Subscription Link

   Go to Clients → Find myuser → Copy Subscription Link.

   | Setting           | Value                           |
   | ----------------- | ------------------------------- |
   | Subscription Port | `2096` (default)                |
   | Subscription Path | `/sub/` (default)               |
   | Subscription URL  | `http://127.0.0.1:2096/sub/...` |
   | Node Address      | VPS Public IP                   |
   | Node Port         | `443`                           |

   Import the subscription URL into your proxy client and update the subscription.
