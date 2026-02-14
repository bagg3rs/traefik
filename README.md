# Traefik v2 docker-compose example

This `Docker-Compose` file is used to configure Traefik, a popular reverse proxy and load balancer, in a Docker environment.
It includes support for DNS Challenge with Cloudflare, which automatically creates wildcard certificates for labelled containers.

<img width="1469" alt="image" src="https://user-images.githubusercontent.com/20583399/163053200-7ca3506a-eec0-4ec1-80d9-35645bf872f4.png">

## Features

- ✅ Automatic HTTPS with Let's Encrypt via Cloudflare DNS challenge
- ✅ HTTP to HTTPS redirect
- ✅ Secure dashboard with basic auth
- ✅ Docker service auto-discovery
- ✅ Pinned Traefik version (v2.10)
- ✅ Example service included ([LibreSpeed](https://github.com/librespeed/speedtest))

<img width="769" alt="image" src="https://user-images.githubusercontent.com/20583399/163053549-d759b3cd-6725-4151-b28c-90758a39d504.png">

## Prerequisites

- Docker & Docker Compose installed
- Cloudflare account with domain
- Cloudflare API tokens ([generate here](https://go-acme.github.io/lego/dns/cloudflare/))

## Quick Start

### 1. Create the proxy network

```bash
docker network create proxy-net
```

### 2. Configure environment variables

Copy `.env.example` to `.env`:

```bash
cp .env.example .env
```

Edit `.env` and configure:

**Required:**
- `CF_DNS_API_TOKEN` - Cloudflare API token with DNS edit permissions
- `CF_ZONE_API_TOKEN` - Cloudflare Zone API token
- `EMAIL` - Your email for Let's Encrypt notifications
- `DASHBOARD` - Dashboard domain (e.g., `traefik.yourdomain.com`)
- `SPEEDTEST` - LibreSpeed domain (e.g., `speedtest.yourdomain.com`)

**Dashboard Auth:**

Generate a password hash:

```bash
# Install htpasswd (Debian/Ubuntu)
sudo apt install apache2-utils

# Generate password
htpasswd -nb admin your_password

# Copy output to .env file, escaping $ as $$
# Example: admin:$$apr1$$xyz...
```

### 3. Start Traefik

```bash
docker-compose up -d
```

### 4. Verify

- Dashboard: `https://traefik.yourdomain.com` (login with credentials from step 2)
- LibreSpeed: `https://speedtest.yourdomain.com`

## Adding Services

Add labels to any service you want Traefik to proxy:

```yaml
services:
  myapp:
    image: myapp:latest
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.myapp.rule=Host(`myapp.yourdomain.com`)"
      - "traefik.http.routers.myapp.entrypoints=websecure"
      - "traefik.http.routers.myapp.tls=true"
      - "traefik.http.routers.myapp.tls.certresolver=myresolver"
    networks:
      - proxy-net

networks:
  proxy-net:
    external: true
```

## Configuration

### Test Mode (Let's Encrypt Staging)

Uncomment this line in `docker-compose.yaml` to test without rate limits:

```yaml
- "--certificatesresolvers.myresolver.acme.caserver=https://acme-staging-v02.api.letsencrypt.org/directory"
```

### Log Level

Change `LOG=INFO` in `.env` to:
- `DEBUG` - Verbose logging
- `INFO` - Normal logging (default)
- `WARN` - Warnings only
- `ERROR` - Errors only

## Troubleshooting

**Network not found:**
```bash
docker network create proxy-net
```

**Certificate errors:**
- Check Cloudflare API tokens have correct permissions
- Verify DNS records exist for domains
- Check logs: `docker-compose logs -f traefik`

**Dashboard 404:**
- Verify `DASHBOARD` domain in `.env`
- Check DNS points to your server
- Wait a few minutes for certificate generation

**Port already in use:**
```bash
# Check what's using port 80/443
sudo netstat -tlnp | grep ':80\|:443'
```

## Security Notes

- Dashboard is secured with basic auth (configure in `.env`)
- Never expose Traefik API without authentication
- Keep Cloudflare API tokens secure (use `.env`, never commit them)
- Consider firewall rules to restrict dashboard access

## License

MIT
