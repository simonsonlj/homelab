May 19, 2026

**Status**: Debian CLI running, SSH access, basic firewall, basic fail-login ban. Consider: Container management e.g. Docker, Cockpit, Pi-hole, Nginx Proxy Mng, Home Asssistant, Netdata, Immich, Paperless-ngx, Vaultwarden, Tailscale, Authelia, Homepage, Wazuh, CrowdSec, Lynis, and more

**Preliminaries**
>**Initial State**: Running Windows 10.
**Goal**: Convert laptop into a headless server.
**Windows Key**: Recorded for possible future use.
**Data Check**: No important data to backup on laptop.

**Preparing Debian**
>**Verification**: Compared SHA256 of .iso, verified PGP signature via CLI and Kleopatra.
**Result**: Integrity and validity of .iso confirmed.
**Boot drive**: Burned Debian ISO to USB using Rufus.
**Installation**: Clean install, walkthrough with AI guide to understand each step.
**Configuration**: No GUI, to maximize resources.

**Network Configuration**
>**Ethernet**: Set up static IP over ethernet for local access only. 
**Wi-Fi**: Modified /etc/network/interfaces to disable/enable Wi-Fi as desired.

**Hardening and Security**
>**Firewall (UFW)**: Default policy: Deny Incoming, Allow Outgoing.
**Exceptions**: Enabled SSH for remote management.

>**SSH Hardening**: Confirmed sudo user status for my user. Then modified `sshd_config`: set `PermitRootLogin` to `no`

>**Brute Force Protection**: Installed and configured Fail2Ban. Created `jail.local` from `jail.conf` to protect custom settings from package updates.
**Whitelisting**: Added main machine's local IP to avoid self-lockout.
**Consider**: Forever bans are an option, but they need to be linked to a database to be permanent.