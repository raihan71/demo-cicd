# Ubuntu deployment

Push to `main` or run **Deploy to Ubuntu server** from GitHub Actions to upload
`index.html` to an Ubuntu server using SSH and rsync.

## Server setup

Install Nginx and rsync on the server:

```sh
sudo apt update
sudo apt install -y nginx rsync
```

Add the deployment SSH public key to the deployment user's `~/.ssh/authorized_keys`.
The corresponding private key must work without an interactive passphrase prompt.
Give that user write access to the site's document root. For example, for a
server using Nginx's default `/var/www/html` directory (replace `deploy` with your
SSH username):

```sh
sudo chown deploy:deploy /var/www/html
```

Ensure Nginx serves `index.html` from that directory and that the server allows
SSH from the GitHub runner and HTTP/HTTPS from visitors. No Nginx restart is
needed when updating this static HTML file.

## GitHub repository secrets

Under **Settings → Secrets and variables → Actions**, add:

| Secret | Value |
| --- | --- |
| `SSH_HOST` | Server IPv4 address or hostname |
| `SSH_USER` | Deployment SSH username |
| `SSH_PRIVATE_KEY` | Full private key matching the server's authorized public key |
| `SSH_KNOWN_HOSTS` | Verified server host-key entry in OpenSSH known_hosts format |
| `SSH_PORT` | Optional SSH port; defaults to `22` |
| `DEPLOY_PATH` | Existing writable document root, e.g. `/var/www/html` |

Obtain the host-key entry through a trusted connection and verify its fingerprint
against the server before saving `SSH_KNOWN_HOSTS`. For a nonstandard SSH port,
the entry should use `[hostname]:port`.

The workflow uploads only `index.html`; it does not publish repository metadata
or remove other server files. Add asset paths to the rsync command if the site
later includes separate stylesheets, scripts, or images.
