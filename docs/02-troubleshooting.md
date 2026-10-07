# Troubleshooting Log

| Problem | Cause | Fix |
|---|---|---|
| Could not SSH to VM; IP was `10.0.2.15` | VirtualBox NAT mode | Switched adapter to Bridged (or use NAT port forwarding 2222 to 22) |
| Ran `ssh-keygen` / `ssh-copy-id` inside the VM | Wrong shell: prompt showed `server1` | Run key commands on the host; check the prompt or `hostname` first |
| `ssh-copy-id` printed usage text | Command run without `user@host` | `ssh-copy-id <user>@<server-ip>` |
| Docker: "permission denied" on the API socket | New group not applied to current session | Log out and back in (or `newgrp docker`); `id` shows the DB, `groups` shows the session |
| Another VM could not SSH in | Key not authorized / password auth disabled | Add that VM's public key to `~/.ssh/authorized_keys` |

## Habit

Before running a command, check the prompt: `server1` means inside the VM, anything else means the host.
