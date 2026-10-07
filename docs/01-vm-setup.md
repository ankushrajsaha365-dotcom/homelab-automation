# VM Setup Notes

## VirtualBox VM

| Setting | Value |
|---|---|
| Name | server1 |
| Type / Version | Linux / Debian (64-bit) |
| RAM | 4096 MB |
| CPUs | 2 |
| Disk | 25 GB VDI, dynamically allocated |
| Network | Bridged Adapter |
| Unattended install | Skipped (manual install for a lean server) |

## Debian 12 install choices

- Text-mode "Install"
- Hostname `server1`, root password left blank (root disabled, user gets sudo)
- Guided partitioning, entire disk, single partition
- Software selection: **only** SSH server + standard system utilities (desktop and GNOME unticked)
- GRUB installed to `/dev/sda`

## First boot

```bash
sudo apt update && sudo apt full-upgrade -y
sudo apt install -y curl vim git ufw fail2ban
ip a
```

## SSH key login (run on the host)

```bash
ssh-keygen -t ed25519 -C "homelab"
ssh-copy-id <user>@<server-ip>
ssh -o PasswordAuthentication=no <user>@<server-ip>   # must succeed
```

## SSH hardening (on server1)

`/etc/ssh/sshd_config.d/hardening.conf`:

```
PermitRootLogin no
PasswordAuthentication no
```

```bash
sudo systemctl restart ssh
```

Keep one session open and test a second login before closing the first.

## Firewall and fail2ban

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow OpenSSH
sudo ufw enable
sudo systemctl enable --now fail2ban
```

## Docker (official repository method)

```bash
sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian $(. /etc/os-release && echo $VERSION_CODENAME) stable" | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker $USER
```

Log out and back in, then verify with `docker run hello-world`.

## Snapshots

- `clean-base-install`: after Debian install and updates
- `docker-installed`: after Docker verified
