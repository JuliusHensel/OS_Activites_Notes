# Linux

## Process Management

### Core Processes
- **init** (`/sbin/init`) - PID 1, started by kernel (PID 0)
- **kthreadd** - Kernel thread daemon, PID 2
  - All kernel processes are forked from `kthreadd`
  - Kernel processes identified by names in square brackets `[ ]`
- **User processes** - Forked from `/sbin/init` or a direct ancestor

### Process IDs and Sessions
- **RUID** - Real User ID; who actually started the process
- **EUID** - Effective User ID; whose permissions the process is currently using
- **SID** - Session ID (assigned despite UID)
  - *Note: Attackers obfuscate actions by hopping between accounts. SID remains the same and allows you to expose the entire attack surface* --POC
- **AUID** - (To be defined)
- **UID** - (To be defined)

### Process States
- **Zombie** - Processes that stopped executing but haven't been reaped
  - Cannot be killed, take PIDs, use zero resources
- **Orphan** - Sub-processes whose parent was reaped
  - Adopted by `/bin/init` with PPID of 1
- **Daemon** - Intentionally orphaned processes that persist until terminated
  - PPID of 1 (e.g., ssh)

## Important Files

| File | Purpose | Notes |
|------|---------|-------|
| `/sbin/init` | Main init system | |
| `/lib/systemd/systemd` | Modern systemd init | |
| `/etc/crontab` | System cron jobs | Look for scripts running as root |
| `/var/spool/cron/crontabs/` | User cron jobs | Look for scripts running as root |
| `/var/log/auth.log` | Authentication logs | Check for brute force attempts and failed logins --POC |
| `/var/log/secure` | Security logs | Check for brute force attempts and failed logins --POC |
| `/etc/login.defs` | Login defaults | |
| `/etc/passwd` | User database | Format: `username:password:UID:GID:comment:home_directory:shell` |
| `/etc/group` | Group membership | Shows which users belong to which group |
| `/etc/shadow` | Password file | |
| `/etc/sudoers` `/etc/sudoers.d/` | Sudo permissions | Shows permissions for user accounts. *If system accounts have NOPASSWD privileges, may indicate compromise* --POC/Priv Esc |

## User Accounts

### Human Accounts
- Typically run `/bin/bash` or `/bin/sh`

### System Accounts
- Typically run `/bin/nologin` or `/bin/false`
- *Note: System users running a normal shell may indicate compromise* --POC

---

# Windows

## Registry Locations for Persistence

### Local Machine (HKLM) - All Users
- `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
- `HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce`
- `HKLM\SYSTEM\CurrentControlSet\services`
- `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders`

### User Hive (HKU) - Specific Users
- `HKU\<SID>\Software\Microsoft\Windows\CurrentVersion\Run`
- `HKU\<SID>\Software\Microsoft\Windows\CurrentVersion\RunOnce`