# SKYVPS02 Example Deployment

Example dedicated Coolify application server.

## Specification

```text
Hostname: SKYVPS02
Operating System: Ubuntu 26.04 LTS
CPU: 4 vCPU
Memory: 16 GB
Application Management: Coolify
SSH Port: 22
Docker: Installed/managed by Coolify
```

## IONOS Deployment Fields

```text
Password:
Set a unique break-glass root password in IONOS.

SSH Key:
Use the public portion of the dedicated Coolify server key.

Cloud-Init:
cloud-init/ubuntu-26.04/coolify-app-server.yaml
```

Before deployment, change:

```yaml
hostname: ubuntu-app-server
```

to:

```yaml
hostname: SKYVPS02
```

Do not document the actual password, private SSH key, or production IP address in this repository.

## Validation

```bash
cloud-init status --long
/usr/local/bin/sky-health-check
/usr/local/bin/sky-server-info
```

Expected cloud-init status:

```text
status: done
```
