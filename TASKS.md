# Websites Stack — Task Checklist

Work tracked here spans sessions. Check items off as completed.

---

## Phase 1 — Fix Expired Certificates (Priority)

The HTTP→HTTPS redirects in `nginx.conf` block certbot's HTTP-01 ACME challenge, which is the likely cause of renewal failures for `ktbcs.xyz` and `skinnereditorial.com`.

- [x] Add `/.well-known/acme-challenge/` location block (before the redirect) to the HTTP server blocks for `ktbcs.xyz`, `skinnereditorial.com`, and `skinnerwilliamsbraun.net` in `nginx.conf`
- [x] Add `/var/www/certbot` volume mount to `portainer-stack.yml`
- [ ] Commit and push changes to this repo
- [ ] On vultr2: `git pull`
- [ ] Redeploy `websites-nginx` stack in Portainer
- [ ] On vultr2: `sudo certbot renew --force-renewal` — verify both certs issue successfully
- [ ] Confirm deploy hook fired and nginx restarted (`docker ps` — check restart time)
- [ ] On vultr2: `sudo certbot renew --dry-run` — confirm auto-renewal will succeed going forward
- [ ] Verify `skinnerwilliamsbraun.net` cert is valid and auto-renewal is working (`sudo certbot certificates`)
- [ ] Verify `systemctl status certbot.timer` is active on vultr2

---

## Phase 2 — Consistency Cleanup

Quick fixes while the stack is already being touched.

- [x] Remove unused ports `8082` and `8443` from `portainer-stack.yml`
- [ ] Verify that volume mount paths in `portainer-stack.yml` match the actual checkout path on vultr2 (currently assumes `/home/karl/Development/KTBCS/Websites/`)
- [ ] Add a placeholder HTTP-only server block for `skinnerbraun.com` to `nginx.conf` (makes cert addition trivial later)
- [ ] Add `skinnerbraun/DocumentRoot` volume mount to `portainer-stack.yml`
- [ ] Update domain/DNS table in `CLAUDE.md` with registrar info once confirmed

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
