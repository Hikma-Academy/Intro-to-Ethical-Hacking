# Installing VMWare and Kali Linux - Setup Guide

## Overview

This guide will walk you through setting up your penetration testing environment using VMWare and Kali Linux.

---

## What You'll Need

### 1. VMWare - Your Virtual Environment

VMWare creates the **SPACE** within your laptop to start hacking.

**Important:** Choose the correct version for your operating system:
- **Apple Laptop** → Install **VMWare Fusion**
- **Windows Laptop** → Install **VMWare Workstation**

> ⚠️ **Note:** You do not need to install both. Choose one based on your operating system.

### 2. Kali Linux - Your Hacking Toolkit

Kali Linux is a "Swiss Army knife" for all security lovers. Instead of installing hundreds of security tools individually, Kali gives you everything in one package, ready to use and compatible with VMWare.

---

## Step 1: Create a Broadcom Account

Installing VMWare or VMWare Fusion requires you to create a **Broadcom Account**.

To download these products, you must register a basic user account.

### Registration Process

1. **Navigate to Portal:**
   - Go to the [Broadcom Support Portal](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion)
   - Click on the blue button that says **'DOWNLOAD NOW'**
   - On the next page, click **LOG-IN**, then **Register** in the top-right corner

2. **Email Verification:**
   - Enter your email address and solve the CAPTCHA
   - Broadcom will send a six-digit verification code to your inbox
   - Enter this code on the registration page to continue

3. **Complete Profile:**
   - Fill in your name, country, and a password
   - You may be asked for a job role; **"Personal Use"** or **"Other"** is sufficient

4. **Skip "Build My Profile":**
   - After clicking **Create Account**, you may be prompted to "Build My Profile" to unlock enterprise features
   - You can click **"I'm done"** or **"Later"** to proceed with free downloads

---

## Step 2: Download VMware Workstation or VMWare Fusion

Once your account is created:

1. Navigate to the same URL again: https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion

2. Click on **'Download Now'** again, and login if needed

### Download Process

1. **Login:**
   - Return to the Broadcom Support Portal
   - Log in with your new credentials

2. **Click the "Download Now" button**

3. **Access Free Software:**
   - Look for a link or banner that says **"Free Software Downloads available HERE"**

4. **Select Product:**
   - Search for **"Workstation"** or **"Fusion"** in the search bar
   - Select:
     - **VMware Workstation Pro** (for Windows/Linux), OR
     - **VMware Fusion 13** (for Mac)

5. **Verify & Download:**
   - Select the latest version (e.g., version 17.x for Workstation or 13.x for Fusion)
   - Click the **Terms and Conditions** link first
   - ⚠️ You must open and review the agreement before the "I agree" checkbox becomes active
   - You may be prompted for a **"Trade Compliance"** verification where you must provide your home address before the download starts
   - Click the **Cloud/Download icon** to get your installer

---

## Step 3: Download & Install Kali Linux Pre-Built Image
Note: Install Pre-Built Image ! 
### Download Kali Linux

1. **Visit Kali Website:**
   - Go to the official [Kali Linux Download Page](https://www.kali.org/get-kali/)

2. **Select "Virtual Machines":**
   - Click the box labeled **Virtual Machines**

3. **Choose VMware Version:**
   - Locate the **VMware** option
   - Click the **Download icon** (usually a down arrow) for the:
     - **64-bit** version (for Intel/AMD processors), OR
     - **Apple Silicon** version (if on a newer Mac with M1/M2/M3 chip)

### Extract and Run

1. **Extract the File:**
   - The file will download as a compressed archive (usually `.7z`)
   - Extract or 'Unzip' the folder using a tool like:
     - **7-Zip** (Windows)
     - **The Unarchiver** (Mac)

2. **Open in VMware:**
   - **Method 1:** Right-click the extracted folder and open with **'VMWARE'**, OR
   - **Method 2:** In VMware, go to **File > Open**, navigate to the extracted folder, and select the `.vmx` file to launch Kali

### Default Credentials

Once Kali Linux boots up:

- **Username:** `kali`
- **Password:** `kali`

> 💡 **Tip:** A pre-built image is an already installed version of Kali that you simply "open" in VMware.

---

## Video Tutorials

Use the following videos which walk you through the installation process:

- **Install VMWare:** [https://drive.google.com/file/d/1E4owSVXPTvrPnEg-A3BknpO9apH5eXQK/view?usp=sharing&t=5.386]
- **Download Kali:** [https://drive.google.com/file/d/1E4owSVXPTvrPnEg-A3BknpO9apH5eXQK/view?usp=sharing]

---

## Quick Reference

| Component | Windows | Mac |
|-----------|---------|-----|
| **VMWare Product** | VMware Workstation Pro | VMware Fusion 13 |
| **Download Source** | Broadcom Support Portal | Broadcom Support Portal |
| **Kali Linux** | 64-bit VMware image | 64-bit or Apple Silicon VMware image |
| **Default Credentials** | kali / kali | kali / kali |

---

## Troubleshooting

### Common Issues

- **Can't find download button?** Make sure you're logged into your Broadcom account
- **"I agree" checkbox is grayed out?** You must click and open the Terms and Conditions link first
- **Download won't start?** Complete the Trade Compliance verification with your address
- **Can't extract .7z file?** Download 7-Zip (Windows) or The Unarchiver (Mac)
- **Kali won't boot?** Make sure you opened the `.vmx` file, not another file type

---

## Next Steps

Once you have VMware and Kali Linux installed:

1. ✅ Boot up Kali Linux in VMware
2. ✅ Log in with credentials: `kali` / `kali`
3. ✅ Update your system: `sudo apt update && sudo apt upgrade -y`
4. ✅ Explore the pre-installed security tools
5. ✅ Start learning!

---

**Happy Hacking! 🔐**

*Remember: Only practice on systems you own or have explicit permission to test.*
