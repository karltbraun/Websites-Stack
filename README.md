# KTBCS Websites Stack

Shared nginx infrastructure for five family and business websites, running as a single Docker container on a Vultr VM.

## Sites

| Site | Domain | Status |
|---|---|---|
| karltbraun | karltbraun.net | Active (HTTP only) |
| ktbcs | ktbcs.xyz | Active (HTTPS) |
| skinnereditorial | skinnereditorial.com | Active (HTTPS) |
| SkinnerWilliamsBraun | skinnerwilliamsbraun.net | Active (HTTPS, Astro app) |
| skinnerbraun | TBD | Placeholder, not yet live |

## How It Works

A single `nginx:alpine` container handles all domains. Static sites (`karltbraun`, `ktbcs`, `skinnereditorial`, `skinnerbraun`) have their `DocumentRoot/` folders bind-mounted read-only into the container. `SkinnerWilliamsBraun` is an Astro + PocketBase application running on port 8080; nginx proxies requests to it.

Each site is maintained as its own git repository under this directory.

## Repository Contents

| File | Purpose |
|---|---|
| `nginx.conf` | Virtual host configuration for all domains |
| `portainer-stack.yml` | Docker Compose file — paste into Portainer stack editor to deploy |

## Prerequisites

- Docker running on the Vultr VM
- Portainer (managed via Docker Desktop Portainer extension on Mac)
- SSL certificates at `/etc/letsencrypt` on the VM (via certbot)

## Deploying / Updating

1. Edit `nginx.conf` or `portainer-stack.yml` as needed
2. Commit and push changes to this repo
3. On the VM: `git pull`
4. In Portainer: paste `portainer-stack.yml` into the stack editor → deploy/update the `websites-nginx` stack
5. Confirm the container is running in Portainer logs

## Testing nginx Config Locally

```bash
docker run --rm -v "$PWD/nginx.conf:/etc/nginx/nginx.conf:ro" nginx:alpine nginx -t
```

## SSL Certificate Renewal

```bash
certbot renew
docker restart websites-nginx
```

## Adding a New Site

1. Create `<sitename>/DocumentRoot/` with site content
2. Initialize as a git repo and push to GitHub
3. Add a server block to `nginx.conf`
4. Add a volume mount to `portainer-stack.yml`
5. Redeploy the stack in Portainer
6. Update the sites table above and in `CLAUDE.md`
