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
