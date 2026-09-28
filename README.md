# CachyOS Edu-Lab Installer (v1.1.0)

[![License: MIT](https://shields.io)](https://opensource.org)
[![Platform: CachyOS / Arch Linux](https://shields.io)](https://cachyos.org)

An advanced, production-ready automation script that instantly transforms any **CachyOS** or **Arch Linux** machine into a fully configured workstation for educational classes or cybersecurity labs.

Built with an optimized, lightweight Text User Interface (TUI) via `whiptail`, this tool eliminates hours of manual setup. It is specifically tailored for schools, universities, bootcamps, and local community IT labs aiming to leverage CachyOS's superior kernel performance.

---

## 🚀 Key Features

* **🎛️ Interactive TUI Menu:** Clean, keyboard-navigated graphical terminal menus (safely auto-installs `libnewt` if missing without breaking local sync).
* **🛡️ Cybersecurity Lab Profile:** Automatically provisions legal, isolated vulnerable applications ([OWASP Juice Shop](https://owasp.org) on port `3000` and [DVWA](https://dvwa.co.uk) on port `8080`) wrapped inside secure Docker containers.
* **💻 Software & AI Dev Environment:** Mass-deploys industry-standard developer essentials natively (`python`, `pip`, `git`, `nodejs`, `npm`, and `code` / `vscodium`) leveraging CachyOS's compiler optimizations.
* **🧼 Lab Reset & Cleanup:** One-click pristine restoration. Safely purges active docker environments, deletes lingering volumes, and clears package manager caches to free up storage.
* **📊 Live Status Dashboard:** Integrated lightweight system monitor showing hardware vitals (CPU, RAM, Kernel version) and live container port routing.
* **⚡ Robust Core Architecture:** Engineered with strict bash execution flags (`set -euo pipefail`), automated elevation checks (`sudo` re-exec), and reliable `trap`-based interrupt cleanup handlers.

---

## 📦 Quick Start (One-Command Deployment)

Open your terminal on your target CachyOS / Arch workstation and execute the following one-liner to download and boot the interface instantly:

```bash
curl -sSL https://githubusercontent.com -o edulab.sh && chmod +x edulab.sh && ./edulab.sh
```

---

## 🛠️ Requirements & Dependencies

The installer is engineered to handle its own dependencies gracefully. However, ensure the host system fulfills:
* An active installation of **CachyOS**, **Arch Linux**, or verified close derivatives.
* Active internet access to fetch upstream software repositories and Docker images.
* Root or privileged `sudo` user privileges (the script enforces this programmatically).

---

## 📂 Project Architecture

```text
cachyos-edu-lab/
├── edulab.sh        # Minified, hyper-optimized production Bash script
└── README.md        # Comprehensive international documentation
```

---

## 📄 License & Terms

This project is open-source and licensed under the terms of the **MIT License**. 

*Disclaimer: The Cybersecurity Profile deploys INTENTIONALLY vulnerable software containers. These services are exposed exclusively to the local loopback interface (`localhost`). Do not bind these ports to public-facing network interfaces.*

---
**Maintained by [Dream595](https://github.com) & the Open-Source Community.**
