# Homelab Infrastructure

Automated Fedora-based homelab management using Ansible. This repository provides a declarative way to bootstrap fresh machines, configure servers, and set up workstation environments with seamless Tailscale networking.

## Available Playbooks

- **`playbook_bootstrap.yml`**: The entry point for fresh Fedora installs. Handles hostname configuration, SSH hardening, and automated Tailscale authorization.
- **`playbook_server.yml`**: Configures basic essenstials like git and shell, then server-specific services including Spyglass. 
- **`playbook_workstation.yml`**: Sets up a full developer workstation environment including shell customizations, desktop apps, and programming languages.

# Usage Guide

## Prerequisites

Before running the bootstrap, ensure the following are configured:

### 1. Configure Secrets

- **Global Secrets**: Copy `group_vars/all/secrets.yml.example` to `group_vars/all/secrets.yml` and fill in your Tailscale and GitHub credentials.
- **Role Secrets**: Some roles (e.g., Spyglass) have their own `vars/secrets.yml.example`. Ensure these are copied to `vars/secrets.yml` before deployment.

### 2. Local Machine Setup

- Your Mac **must be connected to Tailscale**.
- Ensure `ansible` is installed locally.

## Initial Bootstrap (Fresh Machine)

### Step 1: Run the Bootstrap Command

Execute the following command from your Mac to set up a new machine:

```bash
ansible-playbook -i '<remote_ip>,' playbook_bootstrap.yml -e "target_hostname=<target_hostname>" -u <remote_user> --ask-pass -K
```

Then enter ssh and sudo passwords when prompted.

#### Arguments

- **`<remote_ip>`**: The IPv4 address of your new machine (e.g., `192.168.1.50`).
- **`<target_hostname>`**: The final name you want for your machine (e.g., `elitedesk`).
- **`-u <remote_user>`**: (Optional) The username on the fresh machine (e.g., `-u fedora`). Only required if the username is different from the local username.

#### What this command does

- **Hostname Configuration:** Changes the OS name to `<target_hostname>`.
- **Access Hardening:** Automatically detects or generates an SSH key on your Mac and deploys it for passwordless login.
- **Tailscale Integration:** Installs and authorizes Tailscale automagically.
- **Automatic Inventory:** On success, it appends `<target_hostname> ansible_host=<target_hostname>` to your `inventory.ini`.

---

## Day-to-Day Management

After bootstrapping, Tailscale will handle the networking. You can run your regular playbooks using your assigned hostname:

```bash
ansible-playbook playbook_server.yml
```

## Security Note

The `secrets.yml` files contain sensitive credentials (Tailscale, Cloudflare, etc.). These files are ignored by Git. Check your root `.gitignore` to verify protection is active.
