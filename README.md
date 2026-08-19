# IONOS-Coolify-Infrastructure

Secure, repeatable IONOS infrastructure deployments for Coolify, including Ubuntu cloud-init, server hardening, deployment guidance, and configuration examples.

These baselines create clean, repeatable Linux servers with a sensible security configuration before application management platforms such as Coolify take over.

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
/usr/local/bin/sky-health-check
/usr/local/bin/sky-server-info
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
