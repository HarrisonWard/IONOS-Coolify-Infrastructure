# IONOS-Coolify-Infrastructure

Paste one file into IONOS when you build a server, and it comes up patched, locked down, and ready to hand to Coolify.

Secure, repeatable IONOS infrastructure deployments for Coolify, including Ubuntu cloud-init, server hardening, deployment guidance, and configuration examples.

These baselines create clean, repeatable Linux servers with a sensible security configuration before application management platforms such as Coolify take over.

## What It Does

This repo is for anyone running apps with [Coolify](https://coolify.io) on IONOS cloud servers who wants every new server set up the same safe way, without logging in and doing it by hand each time.

The problem it solves is simple: a fresh cloud server is wide open and out of date. Before Coolify starts putting apps on it, somebody has to install updates, turn on a firewall, stop password logins, block people guessing at SSH, keep the clock right, and turn on automatic security patches. Doing that by hand is slow and easy to get wrong, and no two servers end up quite the same.

Here is how it works end to end. When you create the server in the IONOS Data Center Designer (DCD), you set the root password and SSH key in IONOS, then paste the cloud-init file into the User Data box. On first boot, Ubuntu reads that file and does all the setup on its own. When it finishes, you run two small built in commands to confirm everything is healthy and to print the exact settings to type into Coolify. Coolify then connects over SSH, installs Docker itself, and takes it from there.

No passwords, keys, or secrets live in this repo or in the cloud-init file. Those always stay in IONOS and Coolify.

## Features

### Server Basics

- Sets the server's hostname (change `hostname` for each server before you deploy)
- Keeps the hostname in `/etc/hosts` in sync automatically
- Sets the time zone to America/Chicago
- Refreshes the package list and installs every available update on first boot
- Cleans up leftover packages and the download cache at the end

### Tools Installed

- Everyday tools: curl, wget, git, jq, unzip, htop, nano, vim
- Network troubleshooting: net-tools, DNS lookup tools (bind9-dnsutils), traceroute
- Package signing and certificate helpers: ca-certificates, gnupg, lsb-release
- Security and upkeep: Fail2Ban, UFW, chrony, unattended-upgrades

### SSH Lockdown

- Password logins over SSH are turned off, keys only
- Keyboard style (interactive) password prompts are turned off too
- Empty passwords are never allowed
- Root can log in with a key, but never with a password
- The root account is kept (not disabled) so Coolify can connect as root and the IONOS console password still works for emergencies
- X11 forwarding (remote graphical apps) is turned off
- TCP forwarding (SSH tunnels) stays on
- Idle sessions are checked every 5 minutes and dropped after 2 missed checks
- A connection gets at most 5 login tries
- All of this lives in its own file, `/etc/ssh/sshd_config.d/99-server-hardening.conf`, so it is easy to find and change
- Makes sure `/root/.ssh` exists with owner only permissions, and tightens `authorized_keys` if it is there
- Tests the SSH config for mistakes before restarting SSH

### Blocking Login Guessing

- Fail2Ban watches SSH logins through the system journal
- 5 failed tries within 10 minutes gets that address banned for 1 hour
- Turned on and started at boot

### Firewall

- UFW blocks all incoming traffic by default
- All outgoing traffic is allowed
- Opens only three ports: 22 (SSH), 80 (HTTP), and 443 (HTTPS)
- Turned on without stopping to ask for confirmation

### Automatic Updates And Time

- Unattended upgrades refresh the package list and install updates every day
- Chrony keeps the server clock accurate, turned on and started at boot

### Docker Left To Coolify

- Docker is not installed on purpose
- Coolify installs Docker and looks after it from then on, so there is only ever one thing in charge of it

### Built In Helper Commands

- `/usr/local/bin/health-check`: one screen summary of cloud-init status, whether SSH, Fail2Ban, and chrony are running, the full firewall rules, disk space, memory, and network addresses
- `/usr/local/bin/server-info`: prints the hostname, public IP (falls back to the local IP if the lookup fails), architecture, OS, kernel, CPU count, and memory
- `server-info` also prints a ready to copy block of Coolify settings (name, IP, port 22, user root, build server off)
- Leaves a timestamped `/root/cloud-init-complete.txt` so you can tell the build finished
- Saves the server info to `/root/SERVER-INFO.txt`
- Shows a "Server Build Complete" message at the end of the build with the commands to run next

### No Secrets In Source

- No passwords, private keys, API keys, or production secrets in the yaml
- Root password and SSH public key are set in IONOS, and the private key stays in Coolify
- Example docs use placeholders instead of real IPs or passwords

## How It Works

1. Create an Ubuntu 26.04 server in IONOS DCD
2. Set the root password and add the Coolify SSH public key in IONOS
3. Paste `coolify-app-server.yaml` into the Cloud-Init / User Data field and change the hostname
4. Deploy. On first boot the server updates itself, installs tools, locks down SSH, starts Fail2Ban and chrony, and turns on the firewall
5. Run `health-check` and `server-info` to confirm it worked
6. Add the server in Coolify with the settings `server-info` prints, and let Coolify install Docker

## Current Baselines

### Ubuntu 26.04 - Coolify Application Server

Location:

`cloud-init/ubuntu-26.04/coolify-app-server.yaml`

Designed for:

- Dedicated Coolify deployment servers
- Docker application hosts
- IONOS DCD deployments
- Key-only SSH administration
- Internet-facing HTTP/HTTPS applications

The baseline includes:

- System updates
- Common administration utilities
- Chrony
- Fail2Ban
- UFW
- Unattended upgrades
- SSH hardening
- Key-only SSH authentication
- HTTP/HTTPS firewall rules
- Coolify server information utility
- Server health-check utility

Docker is intentionally not installed by cloud-init. Coolify should remain responsible for Docker installation and lifecycle management.

## Credentials

Do not store credentials in this repository.

For IONOS DCD deployments:

- Configure the console/root password through IONOS.
- Configure the SSH public key through IONOS.
- Keep the corresponding private key in Coolify.
- Do not place passwords, private keys, API keys, or production secrets in cloud-init.

## Coolify Deployment

After the server finishes cloud-init:

```bash
cloud-init status --long
/usr/local/bin/health-check
/usr/local/bin/server-info
```

Typical Coolify configuration:

```text
Name: <SERVER-HOSTNAME>
IP/Domain: <SERVER-IP>
Port: 22
User: root
Build Server: No
```

## Security Model

- No SSH password authentication
- SSH public-key authentication
- Root console access retained for break-glass use
- Default-deny inbound firewall
- Ports 80 and 443 exposed for application traffic
- SSH should be restricted further at the network firewall where possible
- No credentials embedded in source control

## Disclaimer

These configurations are examples and should be reviewed before use in production.

Firewall rules, network ranges, packages, patching requirements, and administrative access should be adapted to the environment where they are deployed.

## License

MIT License
