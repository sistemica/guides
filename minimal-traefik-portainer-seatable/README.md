# SeaTable with Traefik and Portainer

This repository contains Docker Compose configurations to set up SeaTable with Traefik as a reverse proxy and Portainer for container management.

## Overview

The setup consists of the following components:

- **Traefik**: Acts as a reverse proxy, handling HTTPS certificates via Let's Encrypt
- **Portainer**: Web interface for managing Docker containers
- **SeaTable**: Self-hosted database solution with a spreadsheet interface
  - MariaDB: Database for SeaTable
  - Redis: Cache for SeaTable

## Prerequisites

- Docker and Docker Compose installed on your server
- A domain name pointing to your server
- Basic knowledge of Docker and networking

## Installation Steps

1. Create the project directory structure:

```bash
mkdir -p seatable-compose
cd seatable-compose
```

2. Create the necessary storage directories:

```bash
mkdir -p /opt/seatable-server /opt/mariadb letsencrypt
```

3. Create the Docker network:

```bash
docker network create frontend-net
```

4. Copy all configuration files to the seatable-compose directory:
   - `.env.example` as `.env`
   - `docker-compose.yml`
   - `traefik.yml`
   - `seatable-server.yml`

5. Modify domain names and credentials in the configuration files (see Configuration section)

6. Start the services:

```bash
docker compose up -d
```

## Configuration

### Required Domain Configuration Changes

Before starting the services, you need to update the following:

1. In the `.env` file:
   - `SEATABLE_SERVER_HOSTNAME`: Set to your SeaTable domain

2. In the `traefik.yml` file:
   - Update the Traefik dashboard domain: `traefik.http.routers.dashboard.rule=Host(\`traefik.example.com\`)`
   - Update the Portainer domain: `traefik.http.routers.portainer.rule=Host(\`portainer.example.com\`)`
   - Update the Let's Encrypt email: `--certificatesresolvers.letsencrypt.acme.email=admin@example.com`

### Security Configuration

It's critical to update these security-sensitive values:

1. In the `.env` file:
   - `SEATABLE_ADMIN_PASSWORD`: Strong password for the SeaTable admin
   - `SEATABLE_MYSQL_ROOT_PASSWORD`: Secure password for MariaDB

## Multi-Domain Deployment Strategy

If you need to deploy multiple instances of these services across different domains, consider implementing a consistent label naming strategy to avoid conflicts.

### Option 1: Domain-Based Prefixes

```yaml
# For domain1.com
- "traefik.http.routers.domain1-seatable.rule=Host(`seatable.domain1.com`)"

# For domain2.com
- "traefik.http.routers.domain2-seatable.rule=Host(`seatable.domain2.com`)"
```

### Option 2: Environment-Based Prefixes

```yaml
# For production
- "traefik.http.routers.prod-seatable.rule=Host(`seatable.example.com`)"

# For development
- "traefik.http.routers.dev-seatable.rule=Host(`seatable-dev.example.com`)"
```

## Service Access

After successful deployment, you can access:

- SeaTable: `https://seatable.example.com` (or your configured domain)
- Traefik Dashboard: `https://traefik.example.com` (or your configured domain)
- Portainer: `https://portainer.example.com` (or your configured domain)

## Maintenance

### Backup Important Data

These directories contain persistent data that should be backed up:
- `/opt/seatable-server`: SeaTable data
- `/opt/mariadb`: Database files
- `./letsencrypt`: SSL certificates

### Updating Services

To update to newer versions:

```bash
docker compose pull
docker compose up -d
```

### Troubleshooting

If you encounter issues:
- Check container logs: `docker logs container_name`
- Verify network connectivity: `docker network inspect frontend-net`
- Check Traefik routing logs: `docker logs traefik`

## Additional Resources

- [SeaTable Documentation](https://docs.seatable.io)
- [Traefik Documentation](https://doc.traefik.io/traefik/)
- [Portainer Documentation](https://docs.portainer.io/)