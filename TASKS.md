# Websites Stack — Task Checklist

Work tracked here spans sessions. Check items off as completed.

---

## Phase 1 — Fix Expired Certificates (Priority)

The HTTP→HTTPS redirects in `nginx.conf` block certbot's HTTP-01 ACME challenge, which is the likely cause of renewal failures for `ktbcs.xyz` and `skinnereditorial.com`.

- [x] Add `/.well-known/acme-challenge/` location block (before the redirect) to the HTTP server blocks for `ktbcs.xyz`, `skinnereditorial.com`, and `skinnerwilliamsbraun.net` in `nginx.conf`
- [x] Add `/var/www/certbot` volume mount to `portainer-stack.yml`
- [x] Commit and push changes to this repo
- [x] On vultr2: `git pull` (was already up to date)
- [x] Create `/var/www/certbot` directory on vultr2
- [x] Removed stale manually-started `websites-nginx` container (was running old config from `/home/karl/Dev/KTBCS/Websites/`)
- [x] Deployed `ktbcs-websites-2026-04` stack in Portainer
- [x] On vultr2: `sudo certbot renew --force-renewal` — both certs renewed (valid until 2026-07-20)
- [x] Fixed deploy hook (`/etc/letsencrypt/renewal-hooks/deploy/reload-apache.sh`) — was restarting `lamp-php-httpd` (old LAMP stack), now restarts `websites-nginx`
- [x] Restarted `websites-nginx` to pick up new certs
- [x] Fixed renewal configs for `ktbcs.xyz` and `skinnereditorial.com` from `standalone` to `webroot` authenticator
- [x] On vultr2: `sudo certbot renew --dry-run` — both succeeded
- [ ] Verify `skinnerwilliamsbraun.net` cert status — **NOT managed by certbot**, cert files may exist at `/etc/letsencrypt/live/skinnerwilliamsbraun.net/` but source/expiry unknown. Next step: `sudo openssl x509 -in /etc/letsencrypt/live/skinnerwilliamsbraun.net/fullchain.pem -noout -dates`
- [ ] Get certbot managing `skinnerwilliamsbraun.net` cert with webroot authenticator
- [ ] Verify `systemctl status certbot.timer` is active on vultr2

---

## Phase 2 — Consistency Cleanup

Quick fixes while the stack is already being touched.

- [x] Remove unused ports `8082` and `8443` from `portainer-stack.yml`
- [x] Verified volume mount paths: repo is confirmed at `/home/karl/Development/KTBCS/Websites/` on vultr2
- [ ] Add a placeholder HTTP-only server block for `skinnerbraun.com` to `nginx.conf` (makes cert addition trivial later)
- [ ] Add `skinnerbraun/DocumentRoot` volume mount to `portainer-stack.yml`
- [ ] Update domain/DNS table in `CLAUDE.md` with registrar info once confirmed
- [ ] Clean up stale repo at `/home/karl/Dev/KTBCS/Websites/` on vultr2

---

## Phase 3 — Per-Container Architecture (Future)

Each site gets its own container; nginx becomes a pure reverse proxy. Cert management moves fully into Docker with `nginx-proxy` + `acme-companion`.

- [ ] Design the new `portainer-stack.yml` — one service per site, shared nginx-proxy network
- [ ] Migrate `ktbcs` static site to its own container
- [ ] Migrate `skinnereditorial` static site to its own container
- [ ] Migrate `karltbraun` static site to its own container
- [ ] Confirm `SkinnerWilliamsBraun` Astro/PocketBase app integrates cleanly with nginx-proxy
- [ ] Validate cert issuance and auto-renewal in new architecture (`--dry-run`)
- [ ] Decommission old single-container setup
- [ ] Update `CLAUDE.md` and `README.md` to reflect new architecture
