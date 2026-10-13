# RustDesk Remote Access to My Home Server (Direct IP)

Remote desktop access to my Windows home server (`sykeshomeserver`) over the LAN using RustDesk's direct IP feature, with no public relay server involved.

## Problem

Connecting by RustDesk ID through the public server failed with:

> Connection error: The connection is not allowed. This public server currently requires users to log in before connecting.

The public server is for demo/testing only and now requires a login because of scam and botnet abuse. It is not meant for production or sensitive use.

## Approaches Tried

| Approach | Result |
|---|---|
| Public RustDesk server | Blocked, login required |
| Self-hosted `hbbs`/`hbbr` in Docker | Could not get it working reliably |
| **Direct IP access** | **Worked** |

## Solution: Direct IP Access

**On the server (`sykeshomeserver`):**

1. Open RustDesk, then Settings, then Security, and click **Unlock security settings**.
2. Enable **Enable direct IP access**.
3. Allow inbound TCP 21118 in Windows Firewall:
   ```powershell
   New-NetFirewallRule -DisplayName "RustDesk Direct" -Direction Inbound -Protocol TCP -LocalPort 21118 -Action Allow -Profile Any
   ```
4. Set a permanent password under Settings, then Security.

**On the client:**

1. Enter the server's **LAN IP address** (not its RustDesk ID) in the "Control Remote Desktop" box.
2. Click **Connect** and enter the permanent password.

## Troubleshooting Checklist

- Confirm the server is listening: `netstat -an | findstr 21118`
- Confirm the client can reach it: `Test-NetConnection <SERVER_LAN_IP> -Port 21118`
- Use the IP address, not the ID. IDs route through the public server.
- Check router settings for AP/client isolation or guest network restrictions.
- Make sure both machines are on the same subnet.

## Display Tweaks

- Switch to fullscreen from the session toolbar.
- Display menu: set **View Style** to *Scale adaptive* or *Original*.
- Set **Image Quality** to *Best* (plenty of bandwidth on a LAN).
- Lower the server's display resolution if text looks too small.

## Next Steps

- Reserve a static IP / DHCP reservation for the server in the router.
- Use Tailscale for remote access outside the home network instead of port forwarding.
- Revisit a native (non-Docker) self-hosted `hbbs`/`hbbr` setup.

## Lessons Learned

- Error messages often point directly to the fix: here, the public server needed a login and the project is designed to be self-hosted.
- Isolating each layer (listener, firewall, client) with `netstat` and `Test-NetConnection` is faster than changing settings at random.
- Direct IP access is the simplest option when both machines are on the same network.
