# Homelab Automation

A self-hosted Linux homelab built on a Debian VM and configured with Ansible. The goal is a fully reproducible server: from a fresh Debian install to hardened SSH, firewall, Docker, and self-hosted services with one command.

## Architecture

```
 Host (Ubuntu, VirtualBox + Ansible control node)
   |
   |  SSH (key-based, port 22)
   v
 server1 (Debian 12 VM, bridged network)
   - UFW firewall + fail2ban
   - Docker + Docker Compose
   - Services (planned): Nginx Proxy Manager, Nextcloud, Pi-hole, Uptime Kuma
```

| Component | Details |
|---|---|
| Hypervisor | VirtualBox on Ubuntu |
| Server OS | Debian 12 (Bookworm), no desktop |
| VM specs | 2 vCPU, 4 GB RAM, 25 GB disk |
| Networking | Bridged adapter (VM gets its own LAN IP) |
| Automation | Ansible (run from host) |
| Containers | Docker Engine + Compose plugin |

## Project status

- [x] Debian 12 VM installed (minimal: SSH server + standard utilities)
- [x] SSH key login from host
- [x] Docker + Compose installed manually
- [ ] SSH hardening (no root login, no password auth)
- [ ] UFW firewall + fail2ban
- [ ] DHCP reservation / static IP
- [ ] Ansible inventory and connectivity test
- [ ] Playbook: base setup, hardening, Docker
- [ ] Docker Compose stack: reverse proxy, Nextcloud, Pi-hole, Uptime Kuma
- [ ] Automated backups (restic + systemd timer)
- [ ] Monitoring (Prometheus + Grafana)
- [ ] CI: ansible-lint via GitHub Actions

## Repository layout

```
.
├── README.md
├── docs/
│   ├── 01-vm-setup.md          # VM and Debian install notes
│   └── 02-troubleshooting.md   # Problems hit and how they were fixed
└── ansible/
    ├── ansible.cfg
    ├── inventory/hosts.ini
    └── playbooks/site.yml      # Draft: base + hardening + Docker
```

## Quick start

1. Build the Debian VM following [docs/01-vm-setup.md](docs/01-vm-setup.md).
2. Install Ansible on the host: `sudo apt install -y ansible`
3. Edit `ansible/inventory/hosts.ini` with your server's IP and user.
4. Test connectivity:
   ```bash
   cd ansible
   ansible all -m ping
   ```
5. Apply the playbook:
   ```bash
   ansible-playbook playbooks/site.yml --ask-become-pass
   ```

> The playbook is a draft and still needs testing on a snapshot-restored VM before it is marked done.

## What I learned

- Difference between NAT and bridged networking in VirtualBox, and why Ansible needs a stable, reachable address.
- SSH key authentication and safe hardening (test a second session before closing the first).
- Group membership changes need a fresh login session to take effect.
- Docker published ports bypass UFW rules, so only publish what is needed.

## Resume summary

> Designed and automated a self-hosted Debian infrastructure with Ansible and Docker Compose, including key-only SSH, UFW/fail2ban hardening, and reproducible deployment of containerised services.
