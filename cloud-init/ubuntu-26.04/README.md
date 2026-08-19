# Ubuntu 26.04 - Coolify Application Server

`coolify-app-server.yaml` provides a baseline Ubuntu deployment intended to be managed by an existing Coolify installation.

## IONOS DCD Deployment

1. Create the Ubuntu 26.04 server.
2. Configure a strong console/root password in IONOS.
3. Add the Coolify SSH public key in IONOS.
4. Paste `coolify-app-server.yaml` into the Cloud-Init/User Data field.
5. Change the `hostname` value for the target server, for example `SKYVPS02`.
6. Deploy the server.
7. Wait for cloud-init to finish.

## Validate

```bash
cloud-init status --long
/usr/local/bin/health-check
/usr/local/bin/server-info
```

## Add To Coolify

Use:

```text
Name: Server hostname
IP/Domain: Server public or private IP
Port: 22
User: root
Private Key: Matching private key
Build Server: Disabled
```

Then validate the server from Coolify.

## Production Hardening

The baseline UFW configuration permits TCP/22 from any source. Restrict SSH at the IONOS/network firewall to the Coolify management server, management VPN, private network, or approved administrative IP ranges where practical.

Do not commit passwords, private SSH keys, API keys, production secrets, or environment-specific credentials.
