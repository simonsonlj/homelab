May 12, 2026
>**Status**: Xubuntu is fully installed. SSH operating. Plans for metasploitable on VM.

> **Initial State**: Laptop running Windows 7.
**Goal**: Replace OS with Xubuntu 26.04 for low-memory optimization.
**File Backup**: Verified and saved important data.
**Prepare ISO**: Downloaded `iso.torrent`. Extracted ISO via qBittorrent. Flashed USB drive using Rufus.

>**In BIOS**:
**Display Issue**: Screen is black; external monitor doesn't show BIOS.
**Workaround**: Used a flashlight against the screen and was able to see LCD. This confirmed that the issue was a broken backlight.
**BIOS Config**: Fast Boot and Secure Boot unavailable. Confirmed Legacy system.

>**In Xubuntu from Live USB**:
**Problem**: Installer window appeared and crashed immediately.
**Tried**: `sudo ubiquity` and `sudo ubuntu-installer`. Both failed.
**Solution**: Found the following command which forces software rendering: `env BAMF_DESKTOP_FILE_HINT=/var/lib/snapd/desktop/applications/ubuntu-desktop-bootstrap_ubuntu-desktop-bootstrap.desktop LIBGL_ALWAYS_SOFTWARE=1 /snap/bin/ubuntu-desktop-bootstrap`
**Success: Xubuntu installed**

>**Security Choice**: Restricting Xubuntu machine from home Wifi.
**Sideloading**: Dowloaded openssh package onto USB from my main machine then installed via Xubuntu terminal.
**Success: SSH connection from main machine** 