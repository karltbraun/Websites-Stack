# Websites Stack — AI Context

## Purpose

This repository is the shared infrastructure layer for five websites operated under KTBCS. It contains the nginx virtual host configuration and Portainer stack file that serve all sites from a single nginx container on a Vultr VM.

Each site lives in its own subdirectory and its own independent git repo. This repo tracks only the shared infrastructure files.

## Architecture

A single `nginx:alpine` Docker container handles all domains:

| Site | Subdirectory | Domain | How Served | SSL |
|---|---|---|---|---|
| karltbraun | `karltbraun/` | karltbraun.net | Static files | No (HTTP only) |
| ktbcs | `ktbcs/` | ktbcs.xyz | Static files | Yes |
| skinnereditorial | `skinnereditorial/` | skinnereditorial.com | Static files | Yes |
| SkinnerWilliamsBraun | `SkinnerWilliamsBraun/` | skinnerwilliamsbraun.net | Reverse proxy → :8080 | Yes |
| skinnerbraun | `skinnerbraun/` | skinnerbraun.com | Not yet configured | No |

**Static sites** (karltbraun, ktbcs, skinnereditorial, skinnerbraun): `DocumentRoot/` is bind-mounted read-only into the nginx container. File saves take effect immediately — no container restart needed.

**SkinnerWilliamsBraun** is an Astro + PocketBase application running on port 8080. nginx proxies incoming requests to it rather than serving static files.

## Directory Structure

```
Websites/                       ← this repo (infrastructure only)
├── CLAUDE.md                   ← AI context (this file)
├── README.md                   ← human-readable documentation
├── nginx.conf                  ← unified virtual host config for all domains
├── portainer-stack.yml         ← Docker Compose — paste into Portainer stack editor
├── karltbraun/                 ← own git repo (karltbraun.net)
├── ktbcs/                      ← own git repo (ktbcs.xyz)
├── skinnereditorial/           ← own git repo (skinnereditorial.com)
├── SkinnerWilliamsBraun/       ← own git repo (skinnerwilliamsbraun.net, Astro app)
└── skinnerbraun/               ← own git repo (domain TBD, no content yet)
```

## Domain and DNS Notes

| Domain | Registrar | DNS | Notes |
|---|---|---|---|
| ktbcs.xyz | Namecheap | — | Active, cert valid, certbot-managed |
| karltbraun.net | Namecheap | — | Active DNS, no cert |
| skinnereditorial.com | Namecheap | — | Active, cert valid, certbot-managed |
| skinnerwilliamsbraun.net | Namecheap | — | Active, cert valid, certbot-managed; cert lives at `/etc/letsencrypt/live/skinnerwilliamsbraun.net-0001/` (not `…/skinnerwilliamsbraun.net/`) — original dir existed when certbot took over |
| skinnerbraun.com | GoDaddy | Cloudflare | Registered, not yet live |
| skinnerbraun.xyz | Namecheap | Cloudflare | Lab/dev domain, not yet live |

## Host System

- **VM**: Vultr Ubuntu 22.04.5 LTS, hostname `vultr2`, IP `45.63.82.34`
- **Docker management**: Portainer extension on Docker Desktop (MacBook Pro)
- Stack management is done through the Portainer UI; `portainer-stack.yml` is kept here to paste into the Portainer stack editor
- **Git remotes must use SSH** — HTTPS auth is not configured on vultr2. If a push/pull fails with "No such device or address", switch the remote: `git remote set-url origin git@github.com:karltbraun/<repo>.git`

## GitHub Repositories

| Repo | GitHub URL |
|---|---|
| Infrastructure (this repo) | github.com/karltbraun/Websites-Stack |
| karltbraun | github.com/karltbraun/karltbraun-website |
| ktbcs | github.com/karltbraun/ktbcs-website |
| skinnereditorial | github.com/karltbraun/skinnereditorial-website |
| skinnerbraun | github.com/karltbraun/skinnerbraun-website |
| SkinnerWilliamsBraun | github.com/karltbraun/SkinnerWilliamsBraun-Website |

## Deployment Workflow

1. Edit `nginx.conf` or `portainer-stack.yml` as needed
2. Commit and push changes to this repo
3. On the VM: `git pull` to update files
4. In Portainer: paste updated `portainer-stack.yml` into the stack editor and redeploy
5. Verify in Portainer container logs

## SSL Certificates

Certificates are managed by certbot on the VM host and mounted from `/etc/letsencrypt` into the nginx container.

Manual renewal:
```bash
sudo certbot renew
docker restart websites-nginx
```

Auto-renewal is handled by a systemd timer (`certbot.timer`). A deploy hook at `/etc/letsencrypt/renewal-hooks/deploy/restart-nginx.sh` restarts the nginx container automatically after each successful renewal. To verify the timer is active:
```bash
systemctl status certbot.timer
```

## Adding a New Site

1. Create `Websites/<sitename>/DocumentRoot/` with site content
2. Initialize as a new git repo and push to GitHub
3. Add a server block to `nginx.conf`
4. Add a volume mount to `portainer-stack.yml`
5. Redeploy the `websites-nginx` stack in Portainer
6. Update the site table in this file and in `README.md`
