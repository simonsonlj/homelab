## June 18, 2026 ##  

**Status**:  
>**Running**: UFW, Fail2Ban, Docker, Portainer, Matrix Synapse + PostgreSQL, Tailscale  
**Planned**: Reverse proxy, TLS/HTTPS, DNS setup  
**Consider**: Pi-hole, Cockpit, Home Assistant, Netdata, Immich, Paperless-ngx, Vaultwarden, Authelia, Homepage, Wazuh, CrowdSec, Lynis, and more

**Confirmed/discovered (not new work, just verified)**:  
>Acer has two network interfaces: enp3s0 (ethernet, 10.10.10.3, isolated LAN) and wlp4s0 (WiFi, 192.168.12.147, home network/internet)  
T-Mobile Nokia 5G21 gateway confirmed as router  
CGNAT confirmed: no public IPv4, curl ifconfig.me returned IPv6 only — conventional port forwarding ruled out for IPv4 internet exposure  

**Decisions made**:  
>Keep both interfaces — isolated LAN (10.10.10.x) preserved for future Kali/Metasploitable work  
Domain chosen, not registered: \*\*\*\*.xyz  
Planned subdomain structure: chat.\*\*\*\*.xyz, portfolio.\*\*\*\*.xyz, etc.  
VPS tunnel (WireGuard + reverse proxy on a cheap VPS) identified as the correct long-term architecture for genuine public internet exposure  
Tailscale chosen as intermediate step — gets chat server accessible outside LAN quickly, without VPS complexity, while accepting the limitation that other users need Tailscale installed  

**Actually done**:  
>Tailscale installed on Acer and MSI, both on the same tailnet  

## June 14, 2026 ##

**Status**:  
>**Running**: UFW, Fail2Ban, Docker, Portainer, Matrix Synapse + PostgreSQL.  
**Planned**: Reverse proxy, TLS/HTTPS, DNS setup, router port forwarding, and network topology decision (server has two interfaces — isolated LAN `10.10.10.x` via ethernet, home network/internet `192.168.x.x` via WiFi) all required before exposing Synapse externally — deliberately sequenced, not yet started.  
**Consider**: Pi-hole, Cockpit, Home Assistant, Netdata, Immich, Paperless-ngx, Vaultwarden, Tailscale, Authelia, Homepage, Wazuh, CrowdSec, Lynis, and more

**Verify UFW and Fail2Ban** (installed previously, confirming actual active state)  
>**UFW**: `sudo ufw status verbose`  
**Result**: Active. Default deny incoming, allow outgoing, deny routed. Only 22/tcp (SSH) allowed in, IPv4 and IPv6. Logging on (low).

>**Fail2Ban**: `sudo systemctl status fail2ban` confirmed active/running. `sudo fail2ban-client status` confirmed `sshd` jail active.  
**Jail detail**: `sudo fail2ban-client status sshd` — 0 currently/total failed, 0 banned (expected, low exposure so far).  
**Configured thresholds** (tighter than default):  
`maxretry`: 3 (default 5)  
`findtime`: 600s / 10 min  
`bantime`: 3600s / 1 hour (default 600s / 10 min)

**Deploy Portainer**  
>**Reasoning**: Quality-of-life container management UI before adding more Docker services.  
**Commands**:  
`docker volume create portainer_data`  
`docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest`  
**Access**: `https://10.10.10.3:9443` (self-signed cert, browser warning expected).  
**Setup**: Initial setup screen timed out once — fixed via `docker restart portainer`, completed setup on retry. Admin account created.  
**Note**: Uses named volume (`portainer_data`), not bind mount — Docker manages location under `/var/lib/docker/volumes/`. Inconsistent with bind-mount convention adopted for subsequent services, but left as-is since already deployed and working.

**Docker user group**: Added `juniper` to `docker` group (`sudo usermod -aG docker $USER`) to avoid needing `sudo` for every Docker command. Understood and accepted tradeoff: docker group membership is effectively root-equivalent on this host; acceptable since `juniper` already has full sudo access as sole admin.

**Cleanup noted**: Leftover `hello-world` test container flagged for removal.

**Docker project convention established**: `~/docker/<service-name>/` per service, with `docker-compose.yml` and bind-mounted data folders (e.g. `synapse-data/`, `postgres-data/`). Chosen over named volumes for visibility/backup simplicity, going forward.

**Domain chosen**: `machinetheory.xyz`. Planned subdomain structure (e.g. `chat.machinetheory.xyz`, `git.machinetheory.xyz`) for future services and portfolio site.

**Deploy Matrix Synapse + PostgreSQL**  
>**Why Postgres over SQLite**: SQLite locks the full DB on writes (poor concurrency), weaker performance at any real scale, and migrating SQLite → Postgres later is an avoidable hassle. Decided to configure Postgres from the start.  
**Directory setup**:  
`mkdir -p ~/docker/synapse/synapse-data`  
`mkdir -p ~/docker/synapse/postgres-data`  
`cd ~/docker/synapse`  
**Compose file** (`~/docker/synapse/docker-compose.yml`): defines `synapse` (matrixdotorg/synapse:latest) and `db` (postgres:15) services. Synapse depends on `db`, bind-mounts `./synapse-data:/data`, exposes `8008:8008`. Postgres bind-mounts `./postgres-data`, uses `POSTGRES_INITDB_ARGS=--encoding=UTF-8 --lc-collate=C --lc-ctype=C` — **mandatory** for Synapse compatibility.

>**Generate initial homeserver config** (one-time, permanent server name):  
`docker run -it --rm -v ./synapse-data:/data -e SYNAPSE_SERVER_NAME=chat.machinetheory.xyz -e SYNAPSE_REPORT_STATS=no matrixdotorg/synapse:latest generate`  
**Result**: Generated `homeserver.yaml`, signing key, log config. Server name `chat.machinetheory.xyz` now permanently baked into signing key and config — cannot be changed without rebuilding the homeserver.

>**Point Synapse at Postgres**: Default generated config uses SQLite. Edited `homeserver.yaml` database block to use `psycopg2` driver, pointing at `host: db` (resolves via Docker Compose's internal DNS by service name), with matching Postgres credentials.  
**Permissions note**: `homeserver.yaml` ownership set to Synapse's internal container UID (991:991) on generation — required `sudo nano` to edit.

>**Launch**:  
`docker compose up -d`  
**Verification**: `docker compose ps` confirmed both containers `Up`, Synapse reporting `(healthy)` after initial Postgres schema migrations completed. `docker compose logs synapse` showed normal background schema/index updates — no errors.

**Create admin account**:  
`docker exec -it synapse register_new_matrix_user http://localhost:8008 -c /data/homeserver.yaml`  
Created `@juniper:chat.machinetheory.xyz` as admin via CLI tool (not public registration).

**Verify functional client login**: Connected via Element desktop client. Logged in using full user ID with manually-specified homeserver URL (`http://10.10.10.3:8008`), since `chat.machinetheory.xyz` has no DNS record yet — login succeeded. Identity server warning noted as expected/benign (unrelated optional feature, not blocking).

**Security checks performed**:  
>**Public registration**: `grep -i "enable_registration" homeserver.yaml` returned no output → defaults to `false`. Confirmed registration is admin-only, not open to arbitrary signups.  
**Internet exposure**: Confirmed via `sudo ufw status verbose` that port 8008 is not in the allow list — only 22/tcp (SSH) is allowed in. Synapse is LAN-only at the firewall level; no router port-forwarding or DNS configured, so not reachable from the internet.

## May 27, 2026 ##

**Status**:  
Running: UFW, Fail2Ban, Docker.  
Consider: Portainer, Cockpit, Pi-hole, Nginx Proxy Mng, Home Asssistant, Netdata, Immich, Paperless-ngx, Vaultwarden, Tailscale, Authelia, Homepage, Wazuh, CrowdSec, Lynis, and more  

**Install Docker** (used https://linuxize.com/post/how-to-install-docker-on-debian-13/)  
>**Remove conflicting packages**: (not really necessary here because of clean install but good to know)  
`for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done`  
**Setup up repository**:  
**System update and required packages install**:  
`sudo apt update`  
`sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release`  
**Add GPG key to verify authenticity**:  
`sudo install -m 0755 -d /etc/apt/keyrings`  
`sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc`  
`sudo chmod a+r /etc/apt/keyrings/docker.asc`  
**Add the Docker APT Repository**:  
`sudo tee /etc/apt/sources.list.d/docker.sources <<EOF`  
`Types: deb`  
`URIs: https://download.docker.com/linux/debian`  
`Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")`  
`Components: stable`  
`Architectures: $(dpkg --print-architecture)`  
`Signed-By: /etc/apt/keyrings/docker.asc`  
`EOF`  
**Install Docker engine**:  
`sudo apt update`  
`sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`  

**Verify installation**:  
>`sudo systemctl start docker`  
**Check service status**: `sudo systemctl status docker`  
**Confirm Docker client and daemon respond**: `sudo docker version`  
**Run test image**: `sudo docker run hello-world`  

## May 19, 2026 ##  

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
