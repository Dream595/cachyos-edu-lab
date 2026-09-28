```markdown
# CachyOS Edu-Lab v1.1 Installer

A modular, production-ready Bash installer that transforms any **CachyOS** or **Arch Linux** machine into a ready-to-use **educational** or **cybersecurity lab** — with a single command.

Built for **teachers, bootcamps, and community centers** who need to rapidly provision lab workstations without manually installing Docker, pulling vulnerable containers, or tracking down a dev toolchain.

---

## ✨ Features

- 🖥️ **Interactive TUI** powered by `whiptail` — no plain-text menus, just arrow keys.
- 🔐 **Root/sudo enforcement** at startup with automatic re-exec via `sudo`.
- 🧯 **Trap-based signal handling** — Ctrl+C and SIGTERM clean up gracefully.
- 🎨 **Colorized, timestamped logging** to both the terminal and `/var/log/cachyos-edu-lab.log`.
- 🛡️ **Safe package installation** — never triggers partial upgrades (`pacman -Sy` only when a target is genuinely missing).
- 🐳 **Idempotent Docker deployments** — rerunning the profile simply replaces the existing container.
- 🏗️ **Modular architecture** — one function per concern; easy to audit and extend.

---

## 📦 Lab Profiles

### 1. Cybersecurity Lab
Deploys two **intentionally vulnerable** web applications inside Docker for safe, legal, hands-on security training.

| Application | URL | Default Credentials |
|---|---|---|
| **OWASP Juice Shop** | http://localhost:3000 | *(self-register)* |
| **DVWA** | http://localhost:8080 | `admin` / `password` |

> ⚠️ **Warning:** These apps are **deliberately insecure**. Keep them on `localhost` or an isolated classroom network. **Never** expose them to the public internet.

### 2. Software & AI Development Class
Silently (`--noconfirm`) installs a complete development stack:

- **Python 3**, `pip`, `virtualenv`
- **Git**, `base-devel`, `cmake`
- **Node.js**, `npm`, `yarn`
- **Docker** + `docker-compose` (daemon enabled and started)
- **VS Code** — falls back to **VSCodium** if the official package is unavailable
- Prefers `paru` (AUR helper) when installed; falls back to `pacman`

### 3. Lab Reset & Cleanup
Returns the machine to a **pristine state** at the end of a class:

- Stops and removes **all** Docker containers
- Deletes **all** Docker volumes
- Prunes unused Docker networks, images, and build cache
- Cleans the pacman package cache (via `paccache -rk0`, with `pacman -Scc` fallback)

### 4. System Status
A read-only diagnostic panel showing hostname, kernel, CPU, memory, Docker state, and active containers.

---

## 🔧 Requirements

- **OS:** CachyOS, Arch Linux, or any Arch-based derivative (EndeavourOS, Manjaro, etc.)
- **Shell:** `bash` (≥ 4.4) — *the script will **not** run under `sh`/`dash`*
- **Privileges:** `root` or a user with working `sudo`
- **Network:** required only when pulling Docker images or installing packages

The script installs `whiptail` (`libnewt`) and `docker` automatically if missing.

---

## 🚀 Installation & Usage

### 1. Clone the repository

```bash
git clone https://github.com/<your-user>/cachyos-edu-lab.git
cd cachyos-edu-lab
```

### 2. Make the script executable

```bash
chmod +x edulab.sh
```

### 3. Run it **with bash** (not sh)

```bash
sudo bash edulab.sh
```

> 🛑 **Do not use `sudo sh edulab.sh`.**
> `sh` on Arch-based systems is usually `dash`, which does not support arrays, `[[ ]]`, `readonly` collisions, `BASH_SOURCE`, or `set -o pipefail`. The script will abort with a syntax error.

### 4. Navigate the menu

Use **↑ / ↓** to select a profile, **Enter** to confirm, **Tab** to switch between buttons, and **Esc** to cancel.

---

## 📂 Project Layout

```
cachyos-edu-lab/
├── edulab.sh        # Main installer script
├── README.md        # This file
└── LICENSE          # MIT
```

---

## 🧠 How It Works

1. **Privilege check** — re-executes itself with `sudo` if needed.
2. **Whiptail bootstrap** — installs `libnewt` safely if `whiptail` is missing.
3. **Distro validation** — reads `/etc/os-release` **without sourcing** it, to avoid `readonly NAME` collisions in inherited environments.
4. **Main TUI loop** — reads the user's choice from `/tmp/edulab.tmp` and dispatches to the matching profile function.
5. **Cleanup** — a single `trap on_exit EXIT` handler restores the terminal cursor and removes the temp file on any exit path.

### Safe pacman retry strategy

```
1. Try: pacman -S --noconfirm --needed <pkg>
2. If missing from local DB: pacman -Sy (refresh DB only, no full upgrade)
3. Retry: pacman -S --noconfirm --needed <pkg>
```

This avoids the notorious "partial upgrade" trap that breaks rolling-release Arch systems.

---

## 🪵 Logging

All actions are logged to both the terminal (with color) and to:

```
/var/log/cachyos-edu-lab.log
```

If the log file is not writable, the script continues silently — logging never blocks execution.

---

## 🧹 Manual Cleanup

If a class ends abruptly, run the **Lab Reset & Cleanup** profile from the menu, or manually:

```bash
docker rm -f $(docker ps -aq) 2>/dev/null
docker volume rm $(docker volume ls -q) 2>/dev/null
docker network prune -f
docker image prune -af
docker builder prune -af
paccache -rk0    # or: sudo pacman -Scc
```

---

## 🛠️ Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `NAME: readonly variable` | Sourcing `/etc/os-release` in a shell where `NAME` is already readonly | Script now parses the file with `grep` instead of sourcing — update to the latest version |
| `Syntax error: "(" unexpected` | Script launched with `sh` / `dash` | Run with `sudo bash edulab.sh` |
| Docker daemon not running | Service disabled after install | The script enables it automatically, but you can run `sudo systemctl enable --now docker` |
| `permission denied` on `/var/run/docker.sock` | User not in `docker` group | Log out and back in after the installer adds your user to `docker` |
| Images fail to pull | No network / DNS issue | Verify `docker pull hello-world` works, then retry |

---

## 🔐 Security Notes

- The **Cybersecurity Lab** containers are **intentionally vulnerable**. This is their purpose.
- Bind them to `localhost` only (the script does this by default with `-p 127.0.0.1:...`-style ports through `--name` + explicit `-p PORT:PORT`).
- To expose on a classroom LAN, edit `deploy()` to use `-p 0.0.0.0:3000:3000` — but **never** on a public network.
- The script does not exfiltrate data, phone home, or install unvetted binaries.
- All package installs come from the official Arch/CachyOS repos or the AUR (via `paru` if available).

---

## 🤝 Contributing

Pull requests are welcome. Please:

1. Keep functions small and single-purpose.
2. Preserve the trap-based cleanup contract.
3. Test on a fresh Arch/CachyOS VM before submitting.
4. Run `shellcheck edulab.sh` and resolve any warnings.

---

## 📜 License

MIT License — see `LICENSE` for details. Free for schools, bootcamps, and community centers worldwide.

---

## 🙏 Acknowledgements

- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)
- [DVWA — Damn Vulnerable Web Application](https://github.com/digininja/DVWA)
- [CachyOS](https://cachyos.org/) and the [Arch Linux](https://archlinux.org/) community
```
