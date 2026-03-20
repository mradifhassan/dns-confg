---
title: How to Permanently Block Adult/Porn Sites on Ubuntu Laptop (2026 Guide)
description: Step-by-step guide to block NSFW content on Ubuntu using free OpenDNS FamilyShield. Works after fresh reinstall. GUI + CLI methods + verification + troubleshooting.
last_updated: March 20, 2026
---

# How to Block Adult & Porn Sites on Ubuntu (Tested on 22.04 & 24.04)

This guide helps you block porn/adult websites system-wide on Ubuntu for better focus, productivity, or parental control.  
**Method used**: Free **OpenDNS FamilyShield** (no account needed).  
**Tested**: Works on laptops after full Ubuntu reinstall.

## Prerequisites
- Ubuntu 22.04 or 24.04 (GNOME)
- Backup first (commands below)
- 2 minutes

## Method 1: Easy GUI (Beginners)

1. Click **Activities** → type “Settings” → open **Network**
2. Click the gear icon ⚙ next to your Wi-Fi (or Wired)
3. Go to **IPv4** tab
4. Change **Method** to **Automatic (DHCP) addresses only**
5. In **DNS** field type:  
   `208.67.222.123,208.67.220.123`
6. Click **Apply** → reconnect to Wi-Fi
7. Reboot

**Test**: Open https://welcome.opendns.com → should say “You are using OpenDNS!”

## Method 2: Reliable CLI Method (Recommended for Laptops – Never Breaks After Reinstall)

```bash
# 1. Backup
sudo cp /etc/NetworkManager/NetworkManager.conf ~/NM-backup.conf

# 2. Force FamilyShield globally
sudo mkdir -p /etc/systemd/resolved.conf.d
cat <<EOF | sudo tee /etc/systemd/resolved.conf.d/family-shield.conf
[Resolve]
DNS=208.67.222.123 208.67.220.123
FallbackDNS=208.67.222.123 208.67.220.123
Domains=~.
DNSStubListener=yes
DNSSEC=no
EOF

sudo mkdir -p /etc/NetworkManager/conf.d
cat <<EOF | sudo tee /etc/NetworkManager/conf.d/dns.conf
[main]
dns=systemd-resolved
EOF

# 3. Fix symlink
sudo rm -f /etc/resolv.conf
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# 4. Apply
sudo systemctl restart systemd-resolved NetworkManager
sudo nmcli connection down "YOUR_WIFI_NAME" && sudo nmcli connection up "YOUR_WIFI_NAME"
