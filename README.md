# How to Create and Secure a VPS on DigitalOcean (Complete Beginner Guide)

> A step-by-step guide to creating your first DigitalOcean Droplet and securing it properly.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Last Updated](https://img.shields.io/badge/Last%20Updated-November%202026-blue)]()

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [1. Create a DigitalOcean Account](#1-create-a-digitalocean-account)
- [2. Generate an SSH Key](#2-generate-an-ssh-key)
- [3. Create a Droplet](#3-create-a-droplet)
- [4. Connect to Your Server via SSH](#4-connect-to-your-server-via-ssh)
- [5. Essential Security Hardening](#5-essential-security-hardening)
- [6. Recommended Additional Setup](#6-recommended-additional-setup)
- [7. Common Issues & Solutions](#7-common-issues--solutions)
- [Useful Resources](#useful-resources)
- [Contributing & License](#contributing--license)

---

## Prerequisites

Before you begin, make sure you have:

- A DigitalOcean account ([Sign up here](https://www.digitalocean.com))
- Basic familiarity with the terminal/command line
- A computer running Windows, macOS, or Linux
- About 10–15 minutes

> **Note:** This guide uses **Ubuntu 24.04 LTS**, which is currently the recommended and most stable choice.

---

## 1. Create a DigitalOcean Account

1. Go to [DigitalOcean](https://www.digitalocean.com) and click **Sign Up**.
2. Register using your email or GitHub/Google account.
3. Verify your email address.
4. Add a payment method when prompted.

---

## 2. Generate an SSH Key

Using an SSH key is much more secure than password authentication.

### On Linux & macOS:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Your keys will be saved in `~/.ssh/`:
- Private key: `id_ed25519` → **Never share this**
- Public key: `id_ed25519.pub`

Copy your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

### On Windows:

You can use **Windows Terminal**, **PowerShell**, or **PuTTYgen**. The same `ssh-keygen` command works in modern Windows.

---

## 3. Create a Droplet

1. Log in to your DigitalOcean account.
2. Click **Create** → **Droplets**.
3. Configure your Droplet with these recommended settings:

| Setting               | Recommended Choice                          |
|-----------------------|---------------------------------------------|
| **Image**             | Ubuntu 24.04 (LTS)                          |
| **Plan**              | Basic – Start with $6/month (1 vCPU + 1 GB) |
| **Datacenter Region** | Closest region to your users                |
| **Authentication**    | **SSH Key** (do not use password)           |
| **Additional Options**| Enable Monitoring and IPv6                  |
| **Hostname**          | Choose a name (e.g. `my-first-vps`)         |

4. Click **Create Droplet**.
5. Wait a few seconds until the Droplet is ready and note the public IP address.

![Create Droplet](screenshots/create-droplet.png)
<!-- Replace with your own screenshot -->

---

## 4. Connect to Your Server via SSH

Once the Droplet is ready, connect using:

```bash
ssh root@YOUR_SERVER_IP
```

Example:

```bash
ssh root@164.92.xxx.xxx
```

When connecting for the first time, type `yes` to accept the fingerprint.

---

## 5. Essential Security Hardening

**Do not skip this section.** A newly created server should never be left with default settings.

### 5.1 Update the System

```bash
apt update && apt upgrade -y
```

### 5.2 Create a New User (Avoid Using Root)

```bash
adduser yourusername
usermod -aG sudo yourusername
```

Copy your SSH key to the new user:

```bash
rsync --archive --chown=yourusername:yourusername ~/.ssh /home/yourusername
```

Now log in with the new user:

```bash
ssh yourusername@YOUR_SERVER_IP
```

### 5.3 Harden SSH Configuration

Edit the SSH config file:

```bash
sudo nano /etc/ssh/sshd_config
```

Find and change these lines:

```bash
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

Restart the SSH service:

```bash
sudo systemctl restart ssh
```

> **Important:** Before closing your current session, open a **new terminal** and test that you can still log in with the new user.

### 5.4 Enable the Firewall (UFW)

```bash
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status
```

---

## 6. Recommended Additional Setup

### Install Fail2Ban (Protection against brute-force attacks)

```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

### Set Timezone

```bash
sudo timedatectl set-timezone Asia/Tehran
# Or use your preferred timezone, e.g. UTC, Europe/Berlin, etc.
```

### Install Useful Tools

```bash
sudo apt install curl wget git htop nano -y
```

---

## 7. Common Issues & Solutions

| Problem                              | Possible Solution                                      |
|--------------------------------------|--------------------------------------------------------|
| Cannot connect via SSH               | Check the IP address, port 22, and SSH key             |
| Permission denied (publickey)        | Public key not added correctly or wrong user           |
| Locked out after changing SSH config | Use the DigitalOcean web console to fix the settings   |
| Server feels slow                    | Upgrade your plan or check resource usage              |

---

## Useful Resources

- [Official DigitalOcean Droplet Documentation](https://docs.digitalocean.com/products/droplets/)
- [Recommended Droplet Setup](https://docs.digitalocean.com/products/droplets/getting-started/recommended-droplet-setup/)
- [How to Set Up a Firewall with UFW](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu)

---

## Contributing & License

Found a mistake or have a suggestion? Feel free to open an Issue or submit a Pull Request.

This project is licensed under the **MIT License**.

---

**Made with ❤️ for the community**
```
