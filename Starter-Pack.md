# 🛡️ Kali Linux & OWASP Juice Shop — Install Guide

> **Audience:** First-time security learners who have never used a terminal.
>
> **Goal:** Install VMware → import Kali Linux → launch OWASP Juice Shop inside Kali → start hacking (legally).
>
> **What you will need:**
> - A computer running **Windows 10/11** or **macOS**
> - A stable internet connection
> - About **1–2 hours** for the full setup

---

## Table of Contents

1. [Machine Requirements](#part-1-machine-requirements)
2. [Download & Install VMware](#part-2-download--install-vmware)
3. [Download & Import the Kali Linux VM](#part-3-download--import-the-kali-linux-vm)
4. [Start Kali Linux & Open the Terminal](#part-4-start-kali-linux--open-the-terminal)
5. [Update Kali Linux](#part-5-update-kali-linux)
6. [Install Docker Inside Kali](#part-6-install-docker-inside-kali)
7. [Install & Run OWASP Juice Shop](#part-7-install--run-owasp-juice-shop)
8. [Managing Juice Shop](#part-8-managing-juice-shop)
9. [Troubleshooting](#part-9-troubleshooting)
10. [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)

---

## Part 1: Machine Requirements

A virtual machine (VM) is a computer running inside your computer. Your physical machine needs enough CPU, RAM, and disk space to run both your normal operating system **and** Kali Linux at the same time.

### Minimum vs Recommended Specs

| Resource | Minimum | Recommended | Why It Matters |
|----------|---------|-------------|----------------|
| **CPU** | 64-bit dual-core (Intel i3 / AMD Ryzen 3) | Quad-core or better (i5/i7, Ryzen 5/7, Apple M1/M2/M3/M4) | The VM and your host OS share the CPU. More cores = less slowdown. |
| **RAM** | 8 GB | 16 GB | Your host OS needs ~4 GB and Kali needs at least 4 GB. With only 8 GB total things will feel tight. |
| **Free Disk Space** | 30 GB | 60 GB+ | The Kali image, packages, Docker, and Juice Shop add up. Use an SSD if possible. |
| **Virtualization** | Required | Required | Must be **enabled** in your BIOS/UEFI. See below. |

### How to Check if Virtualization Is Enabled

**Windows:**

1. Press `Ctrl + Shift + Esc` to open **Task Manager**.
2. Click the **Performance** tab.
3. Click **CPU** on the left side.
4. Look in the bottom-right area for **"Virtualization: Enabled"**.
5. If it says **"Disabled"**, you need to enter your BIOS/UEFI settings (usually by pressing **F2**, **F12**, or **DEL** during boot) and enable **Intel VT-x** or **AMD-V / SVM**.

**Mac:**

Virtualization is always enabled on macOS. No action needed.

---

## Part 2: Download & Install VMware

For this class, everyone will use VMware as the hypervisor. VMware is **completely free** — no license key is required.

- **macOS users** will download **VMware Fusion Pro**
- **Windows users** will download **VMware Workstation Pro**

Both are available from the same page.

### Step 1 — Create a Free Broadcom Account

1. Go to: 👉 **<https://support.broadcom.com/group/ecx/free-downloads>**
2. If you do not have an account, click **Register** in the top-right corner.
3. Fill in your name, email, and create a password.
4. Check your email for a verification link and confirm your account.
5. Log in with your new credentials.

### Step 2 — Download the Installer

1. Once logged in, go back to: 👉 **<https://support.broadcom.com/group/ecx/free-downloads>**
2. Find your product in the list:
   - **Mac users →** Click **VMware Fusion Pro** and download the latest `.dmg` file.
   - **Windows users →** Click **VMware Workstation Pro** and download the latest `.exe` file.
3. Save the file to your **Downloads** folder.

### Step 3 — Install VMware

#### macOS Installation

1. Find the downloaded `.dmg` file in your Downloads folder and double-click it.
2. Drag the **VMware Fusion** icon into your **Applications** folder.
3. Open **Applications** → double-click **VMware Fusion**.
4. macOS will ask you to allow permissions — click **Open**, then **Allow** for each prompt.
5. Go to **System Settings → Privacy & Security** and approve any VMware-related permissions that appear.
6. Restart your Mac if prompted.

#### Windows Installation

1. Find the downloaded `.exe` file in your Downloads folder and double-click it.
2. If a security prompt appears, click **Yes** to allow the installer.
3. Click **Next** through the wizard and accept the license agreement.
4. Leave all default options selected and click **Install**.
5. When the installer finishes, click **Restart** if prompted.
6. After reboot, open **VMware Workstation Pro** from your Start menu.

> **⚠️ Hyper-V Conflict (Windows only):**
> If VMware shows a warning about Hyper-V, open **PowerShell as Administrator** and run:
> ```powershell
> bcdedit /set hypervisorlaunchtype off
> ```
> Restart your computer. VMware should now work. (To re-enable Hyper-V later: `bcdedit /set hypervisorlaunchtype auto`)

---

## Part 3: Download & Import the Kali Linux VM

Kali Linux provides pre-built VM images so you do not need to install an operating system from scratch.

### Step 1 — Download the Kali VM Image

1. Visit: 👉 **<https://www.kali.org/get-kali/#kali-virtual-machines>**
2. Scroll to the **Virtual Machines** section and find the **VMware** row.
3. Download the correct image for your machine:
   - **Windows or Intel Mac →** Download the **64-bit (AMD64)** VMware image.
   - **Apple Silicon Mac (M1/M2/M3/M4) →** Download the **Apple Silicon (ARM64)** VMware image.
4. The download is a `.7z` compressed file (~3 GB). Be patient — it may take a while.

### Step 2 — Extract the Downloaded File

You need a program that can open `.7z` files:

- **Windows:** Download and install [7-Zip](https://www.7-zip.org/) (free). Then right-click the `.7z` file → **7-Zip** → **Extract Here**.
- **Mac:** Download [The Unarchiver](https://theunarchiver.com/) from the App Store (free). Then double-click the `.7z` file to extract it.

After extracting, you will have a folder containing several files. The important one is the file ending in **`.vmx`** — this is the VM configuration file.

### Step 3 — Open the Kali VM in VMware

**macOS (Fusion Pro):**

1. Open **VMware Fusion**.
2. Go to **File → Open**.
3. Navigate to the extracted Kali folder and select the **`.vmx`** file.
4. Click **Open**.
5. If prompted with "Did you move or copy this virtual machine?", select **"I copied it"**.

**Windows (Workstation Pro):**

1. Open **VMware Workstation Pro**.
2. Go to **File → Open**.
3. Navigate to the extracted Kali folder and select the **`.vmx`** file.
4. Click **Open**.

### Step 4 — Adjust VM Settings (Before First Boot)

Before starting Kali, adjust the VM settings so it runs smoothly:

**macOS:** Click the VM → **Virtual Machine** menu → **Settings**

**Windows:** Right-click the VM in the library → **Settings**

| Setting | Where to Find It | Set It To |
|---------|-------------------|-----------|
| **Memory (RAM)** | Processors & Memory | **4 GB minimum** — set to 8 GB if your host has 16+ GB |
| **CPU Cores** | Processors & Memory | **2 cores minimum** — set to 4 if available |
| **Network Adapter** | Network Adapter | **NAT** (this is the default — it shares your host's internet) |

Click **Apply** or close the settings window.

### Step 5 — Start the VM and Log In

1. Click the green **▶ Play** button to start the Kali VM.
2. Wait for Kali to boot — you will see a login screen.
3. Enter the default credentials:
   - **Username:** `xxxxx`
   - **Password:** `xxxx`
4. Press **Enter** to log in.

> ⚠️ **Change your password immediately** by opening a terminal (see next section) and running:
> ```bash
> passwd
> ```
> You will be asked to type your current password, then your new password twice.

---

## Part 4: Start Kali Linux & Open the Terminal

### What Is a Terminal?

A terminal (also called the command line, shell, or console) is a text-based window where you type commands instead of clicking buttons. Everything from this point forward happens inside the terminal.

### How to Open the Terminal

After logging into the Kali desktop, use any of these methods:

**Method 1 — Click the icon (easiest):**
Look at the **top panel** of the Kali desktop. You will see a small black terminal icon that looks like `>_`. Click it.

**Method 2 — Application menu:**
Click **Applications** (top-left corner) → **System Tools** → **Terminal** (or **QTerminal**).

**Method 3 — Right-click the desktop:**
Right-click anywhere on the desktop → select **Open Terminal Here**.

**Method 4 — Keyboard shortcut:**
Press `Ctrl + Alt + T`.

### Understanding the Terminal Prompt

When the terminal opens you will see something like this:

```
┌──(kali㉿kali)-[~]
└─$
```

| Part | Meaning |
|------|---------|
| `kali` | Your username |
| `kali` (after `㉿`) | The computer's hostname |
| `~` | Your current directory (`~` = your home folder) |
| `$` | You are a normal user (`#` means root/admin) |

The blinking cursor after `$` is where you type.

### Terminal Survival Skills

| What You Want to Do | What You Type or Press |
|---|---|
| Run a command | Type it and press **Enter** |
| Cancel / stop a running command | `Ctrl + C` |
| Copy text in the terminal | `Ctrl + Shift + C` |
| Paste text into the terminal | `Ctrl + Shift + V` |
| Clear the screen | Type `clear` and press **Enter** |
| See your previous commands | Press the **↑** (up arrow) key |
| Auto-complete a file or folder name | Start typing and press **Tab** |

### Quick Test — Make Sure Your Terminal Works

Type this command and press **Enter**:

```bash
whoami
```

You should see `kali`. If you do, your terminal is working and you are ready.

---

## Part 5: Update Kali Linux

> **Run these commands every time you set up a fresh Kali install.** They make sure your system has the latest security patches and software.

Open a terminal and run each command below. Copy and paste them one at a time.

### Step 1 — Update package lists and upgrade all packages

```bash
sudo apt update && sudo apt upgrade -y
```

> **What does this do?**
> - `sudo` = run as admin
> - `apt update` = download the latest list of available software
> - `apt upgrade -y` = install all available updates (`-y` means "yes to all prompts")

When prompted for a password, type `kali` (or your new password if you changed it). You will **not** see the characters as you type — this is normal.

### Step 2 — Full distribution upgrade

```bash
sudo apt dist-upgrade -y
```

This catches deeper system updates that a regular upgrade might miss.

### Step 3 — Clean up unused packages

```bash
sudo apt autoremove -y
sudo apt autoclean
```

### Step 4 — Reboot (if a kernel update was installed)

```bash
sudo reboot
```

After the reboot, log back in and open the terminal again.

---

## Part 6: Install Docker Inside Kali

We will use Docker to run OWASP Juice Shop. Docker is a tool that runs applications inside lightweight containers — think of it as a mini-computer inside your Kali VM.

### Step 1 — Install Docker

```bash
sudo apt update
sudo apt install -y docker.io
```

### Step 2 — Start the Docker service

```bash
sudo systemctl enable docker --now
```

> `enable` makes Docker start automatically every time Kali boots. `--now` starts it immediately.

### Step 3 — Allow your user to run Docker without sudo (optional but recommended)

```bash
sudo usermod -aG docker $USER
```

**You must log out and log back in** for this to take effect. The easiest way:

```bash
sudo reboot
```

### Step 4 — Verify Docker is working

After logging back in, open a terminal and run:

```bash
docker --version
```

You should see something like `Docker version 24.x.x` or similar.

Now run the test container:

```bash
docker run hello-world
```

If you see **"Hello from Docker!"** in the output, Docker is installed and working correctly.

---

## Part 7: Install & Run OWASP Juice Shop

### What Is OWASP Juice Shop?

OWASP Juice Shop is an intentionally insecure web application built for security training. It contains over 100 hacking challenges covering the OWASP Top 10 vulnerabilities. You will attack it from inside your Kali VM to practice real-world penetration testing skills — legally and safely. Everything runs locally on your machine.

### Step 1 — Download the Juice Shop image

```bash
docker pull bkimminich/juice-shop
```

This downloads the latest Juice Shop image from Docker Hub (~400 MB). Wait for it to finish.

### Step 2 — Start Juice Shop

```bash
docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop
```

Here is what each piece does:

| Flag | Meaning |
|------|---------|
| `-d` | Run in the background (detached mode) so you can keep using the terminal |
| `-p 3000:3000` | Map port 3000 inside the container to port 3000 on your Kali machine |
| `--name juice-shop` | Give the container a friendly name so you can refer to it easily |
| `bkimminich/juice-shop` | The Docker image to use |

### Step 3 — Verify it is running

```bash
docker ps
```

You should see a line showing `juice-shop` with a status of `Up`. Wait about **10–15 seconds** for the app to fully start.

### Step 4 — Open Juice Shop in the browser

Open **Firefox** inside Kali (click the Firefox icon on the taskbar or find it in Applications) and go to:

```
http://localhost:3000
```

You should see the **OWASP Juice Shop** storefront page. 🎉

**Congratulations — your hacking lab is ready!**

---

## Part 8: Managing Juice Shop

These are the commands you will use day-to-day inside your Kali terminal to control Juice Shop.

### Start Juice Shop (after stopping it)

```bash
docker start juice-shop
```

### Stop Juice Shop

```bash
docker stop juice-shop
```

### Restart Juice Shop

```bash
docker restart juice-shop
```

### Check if Juice Shop is running

```bash
docker ps
```

If you see `juice-shop` in the output, it is running. If you see nothing, it is stopped.

### View Juice Shop logs (useful for debugging)

```bash
docker logs juice-shop
```

Follow logs in real-time (press `Ctrl + C` to stop):

```bash
docker logs -f juice-shop
```

### Reset Juice Shop (erase all progress, start fresh)

```bash
docker stop juice-shop
docker rm juice-shop
docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop
```

### Update Juice Shop to the latest version

```bash
docker stop juice-shop
docker rm juice-shop
docker pull bkimminich/juice-shop
docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop
```

---

## Part 9: Troubleshooting

### "Cannot connect to the Docker daemon"

Docker is not running. Start it:

```bash
sudo systemctl start docker
```

### "Port 3000 is already in use"

Something else is using port 3000. Find and stop it:

```bash
sudo lsof -i :3000
sudo kill -9 <PID>
```

Replace `<PID>` with the number shown in the output.

Or run Juice Shop on a different port instead:

```bash
docker run -d -p 4000:3000 --name juice-shop bkimminich/juice-shop
```

Then open `http://localhost:4000` in your browser.

### "docker: permission denied"

You forgot to add your user to the docker group, or you have not logged out yet:

```bash
sudo usermod -aG docker $USER
```

Then **log out and log back in** (or run `sudo reboot`).

Quick workaround (temporary — uses sudo):

```bash
sudo docker start juice-shop
```

### "Name already in use by container"

A container named `juice-shop` already exists. Remove it first:

```bash
docker rm juice-shop
```

If it is still running, stop it first:

```bash
docker stop juice-shop && docker rm juice-shop
```

### Browser shows "Unable to connect" at localhost:3000

1. Make sure the container is running: `docker ps`
2. Wait 15 seconds — Juice Shop takes a moment to start up.
3. Check the logs for errors: `docker logs juice-shop`

### "sudo apt update" is failing or very slow

Check your internet connection inside Kali:

```bash
ping -c 3 google.com
```

If that fails, your VM's network adapter may not be set correctly. Shut down the VM, go to VM Settings → Network Adapter, and make sure it is set to **NAT**. Then start the VM again.

### VMware shows a black screen or Kali will not boot

1. Shut down the VM completely.
2. Go to VM Settings → **Display** → uncheck "Accelerate 3D graphics".
3. Go to VM Settings → **Memory** → make sure at least **4 GB** is assigned.
4. Try starting the VM again.

---

## Quick Reference Cheat Sheet

```bash
# ── Update Kali ────────────────────────────────────
sudo apt update && sudo apt upgrade -y

# ── Docker Service ─────────────────────────────────
sudo systemctl start docker        # Start Docker
sudo systemctl stop docker         # Stop Docker
sudo systemctl status docker       # Check if Docker is running

# ── Juice Shop ─────────────────────────────────────
docker pull bkimminich/juice-shop   # Download / update image

docker run -d -p 3000:3000 \
  --name juice-shop \
  bkimminich/juice-shop             # First-time run

docker start juice-shop             # Start (after a stop)
docker stop juice-shop              # Stop
docker restart juice-shop           # Restart
docker logs juice-shop              # View logs
docker logs -f juice-shop           # Follow logs live (Ctrl+C to exit)

# ── Reset Juice Shop ──────────────────────────────
docker stop juice-shop && docker rm juice-shop
docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop

# ── Helpful Checks ─────────────────────────────────
docker ps                           # Show running containers
docker ps -a                        # Show all containers (including stopped)
docker images                       # Show downloaded images
ss -tulpn | grep 3000               # Check if port 3000 is in use
```

---

> **You're all set!** Open `http://localhost:3000` inside your Kali browser and start exploring the Juice Shop challenges. Happy hacking! 🧃🔓
