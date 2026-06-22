# 🏠 Home Lab Setup

A self-hosted homelab infrastructure managed using Docker Compose.

This repository contains the configuration files required to deploy and manage a personal home server environment with reverse proxy, local DNS, monitoring, dashboards, and media services.

The goal of this setup is to keep the infrastructure reproducible, portable, and easy to rebuild on a fresh machine.

---

# Architecture

```
                         Local Network

                              |
                              |
                         +----------+
                         |  Router  |
                         +----------+
                              |
                              |
                    192.168.1.250 (Server)
                              |
                              |
              +---------------+---------------+
              |
              |
        Docker Network
              |
   +----------+-----------+
   |                      |
 Caddy                 AdGuard
 Reverse Proxy         DNS Server
   |
   |
   +-------------------------------+
   |              |                |
   |              |                |
Jellyfin      Portainer        Uptime Kuma
Media         Docker Mgmt      Monitoring

                 |
                 |
              Homer
            Dashboard
```

---

# Services

| Service | Purpose | Address |
|---|---|---|
| Caddy | Reverse proxy | Internal |
| AdGuard Home | DNS + Ad blocking | `adguard.home.arpa` |
| Homer | Dashboard | `homer.home.arpa` |
| Portainer | Docker management | `portainer.home.arpa` |
| Uptime Kuma | Monitoring | `kuma.home.arpa` |
| Jellyfin | Media server | `jellyfin.home.arpa` |

---

# Local DNS

This setup uses the reserved local domain:

```
home.arpa
```

Example:

```
jellyfin.home.arpa
```

resolves to:

```
192.168.1.250
```

DNS is handled by AdGuard Home.

The `.arpa` namespace is intended for local/private networking use.

---

# Directory Structure

```
homelab/

├── docker-compose.yml

├── config/
│
│   ├── caddy/
│   │   └── Caddyfile
│   │
│   ├── adguard/
│   │   └── conf/
│   │       └── AdGuardHome.yaml
│   │
│   └── homer/
│       └── config.yml
│
├── data/
│
│   ├── caddy/
│   ├── adguard/
│   ├── jellyfin/
│   ├── kuma/
│   └── portainer/
│
├── media/
│
│   ├── movies/
│   ├── music/
│   └── tv/
│
└── README.md
```

---

# Deployment

## Requirements

- Linux server
- Docker
- Docker Compose plugin

Check:

```bash
docker --version
docker compose version
```

---

# Start Services

From the project directory:

```bash
docker compose up -d
```

---

# Stop Services

```bash
docker compose down
```

---

# Update Containers

Pull latest images:

```bash
docker compose pull
```

Restart:

```bash
docker compose up -d
```

---

# Logs

View running containers:

```bash
docker ps
```

View logs:

```bash
docker compose logs -f <service>
```

Example:

```bash
docker compose logs -f caddy
```

---

# Backup

Application state is stored inside:

```
data/
```

Important folders:

```
data/jellyfin
data/portainer
data/kuma
data/adguard
data/caddy
```

Containers themselves are disposable.

A restore requires:

1. Install Docker
2. Clone this repository
3. Restore the `data` directory
4. Start:

```bash
docker compose up -d
```

---

# Git Tracking

Tracked:

```
docker-compose.yml
config/
README.md
scripts/
```

Not tracked:

```
data/
media/
.env
logs/
```

Runtime data is intentionally excluded from Git.

---

# Network Design

Only required ports are exposed:

```
80   HTTP
443  HTTPS
53   DNS
```

All application containers communicate internally through Docker networking.

Traffic flow:

```
Client
  |
  v
Caddy
  |
  +--> Jellyfin
  +--> Portainer
  +--> Uptime Kuma
  +--> Homer
  +--> AdGuard
```

---

# Security Notes

- Services are not directly exposed.
- Caddy acts as the single entry point.
- Internal services use Docker networking.
- No unnecessary ports are published.
- Remote access should use a VPN solution such as Tailscale.

---

# Future Improvements

Possible additions:

- Automated backups
- NAS storage
- Tailscale remote access
- Immich photo server
- Vaultwarden password manager
- Syncthing file synchronization
- Infrastructure automation

---

# License

Personal homelab configuration.
