# Installing Odoo 20 (2026 release)

Set up **Odoo 20** in a single command using Docker Compose — with support for running multiple Odoo instances on one server.

> **Default master password:** `minhng.info` — change it before going live.

## Quick Start

Install [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/) first, then run the following to set up your first Odoo instance at `localhost:10020`:

```bash
curl -s https://raw.githubusercontent.com/minhng92/odoo-20-docker-compose/master/run.sh \
  | bash -s -- --destination odoo-one --port 10020 --chat 20020
```

and/or run the following to set up another Odoo instance at `localhost:11020`:

```bash
curl -s https://raw.githubusercontent.com/minhng92/odoo-20-docker-compose/master/run.sh \
  | bash -s -- --destination odoo-two --port 11020 --chat 21020
```

If `curl` is missing, install it:

```bash
sudo apt-get install curl     # Debian / Ubuntu
sudo yum install curl         # RHEL / CentOS
```

### Arguments

| Flag | Required | Example | Meaning |
|------|:--------:|---------|---------|
| `--destination` | Yes | `odoo-one` | Name of the deploy folder where the stack is cloned |
| `--port` | Yes | `10020` | Odoo web port exposed on the host |
| `--chat` | Yes | `20020` | Live-chat port exposed on the host |
| `--password` | No | `mymaster` | Odoo master password (**admin_passwd**). Defaults to the value in **etc/odoo.conf** |
| `--db-password` | No | `dbSecret` | PostgreSQL password (**POSTGRES_PASSWORD**/**PASSWORD**). Defaults to `odoo20@2026` |

### Examples with custom passwords

Custom master password:

```bash
curl -s https://raw.githubusercontent.com/minhng92/odoo-20-docker-compose/master/run.sh \
  | bash -s -- --destination odoo-one --port 10020 --chat 20020 --password mymaster
```

Custom master + database passwords:

```bash
curl -s https://raw.githubusercontent.com/minhng92/odoo-20-docker-compose/master/run.sh \
  | bash -s -- --destination odoo-one --port 10020 --chat 20020 \
    --password mymaster --db-password dbSecret
```

## Usage

Start the container:

```bash
cd odoo-one
docker-compose up -d
```

Then open <http://localhost:10020> to access Odoo 20.

## Tips & Troubleshooting

### Fix permission issues

If the container can't read your folders, open them up:

```bash
sudo chmod -R 777 addons etc postgresql
```

### Change the Odoo port

Edit **docker-compose.yml** in the parent directory:

```yaml
ports:
  - "10020:8069"
````

### Run in detached mode

Keep Odoo running after you close the terminal:

```bash
docker-compose up -d
```

### Set a restart policy

In **docker-compose.yml**, set the `restart` key on a service to one of:

- `no` — don't restart
- `on-failure[:max-retries]` — restart on crash, optionally capped
- `always` — always restart
- `unless-stopped` — always restart, except when stopped by you

```yaml
restart: always   # run as a service
```

### Run multiple instances on one machine

Raise the inotify watch limit (Ubuntu) to avoid errors when several instances watch the same tree:

```bash
if grep -qF "fs.inotify.max_user_watches" /etc/sysctl.conf; then
  echo $(grep -F "fs.inotify.max_user_watches" /etc/sysctl.conf)
else
  echo "fs.inotify.max_user_watches = 524288" | sudo tee -a /etc/sysctl.conf
fi
sudo sysctl -p
```

## Custom Addons

Drop your own addons into the **addons/** folder. It's mounted into Odoo as `/mnt/extra-addons`, so they load automatically.

## Configuration & Logs

- **Configuration:** edit [`etc/odoo.conf`](etc/odoo.conf)
- **Server log:** `etc/odoo-server.log`
- **Default admin password:** `admin_passwd = minhng.info` in [`etc/odoo.conf`](etc/odoo.conf) — override at setup time with `--password`

## Container Management

| Action  | Command                 |
|---------|-------------------------|
| Run     | `docker-compose up -d`  |
| Restart | `docker-compose restart`|
| Stop    | `docker-compose down`   |

## Live Chat

The live-chat port (default **20020**) is exposed on the host. Behind a reverse proxy (e.g. nginx), forward `/longpolling/`:

```nginx
server {
  # ...
  location /longpolling/ {
    proxy_pass http://0.0.0.0:20020/longpolling/;
  }
  # ...
}
```

## Versions

| Service     | Image        |
|-------------|--------------|
| Odoo        | `odoo:20`    |
| PostgreSQL  | `postgres:18`|

## Screenshots

<p align="center">
<img src="screenshots/odoo-20-welcome-screenshot.jpg" alt="Odoo 20 welcome screen" width="50%">
</p>

<p align="center">
<img src="screenshots/odoo-20-apps-screenshot.jpg" alt="Odoo 20 apps grid" width="100%">
</p>

<p align="center">
<img src="screenshots/odoo-20-discuss.jpg" alt="Odoo 20 discuss" width="100%">
</p>

<p align="center">
<img src="screenshots/odoo-20-sales-screen.jpg" alt="Odoo 20 sales screen" width="100%">
</p>

<p align="center">
<img src="screenshots/odoo-20-product-form.jpg" alt="Odoo 20 product form" width="100%">
</p>

---

## ☕ Buy Me a Coffee

If this saves you time, consider buying me a coffee.

<a href="https://buymeacoffee.com/minhng.info" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" style="height: 60px !important;width: 217px !important;" ></a>
