# Authelia (BarbaroLab SSO)

This stack provides authentication and access control for all public-facing services in BarbaroLab.

## What it does
- Exposes a login portal at `auth.barbarolab.com`
- Requires valid credentials before allowing access to protected services
- Issues/validates sessions via secure cookies on `barbarolab.com`
- Logs failed and successful login attempts (source IP, username attempted)

## Components
- `docker-compose.yml` runs Authelia in Docker on the shared `proxy` network with Traefik.
- `configuration.yml` (not committed) defines security secrets, cookie domain, policies.
- `users_database.yml` (not committed) defines local users and Argon2id password hashes.
- `configuration.example.yml` / `users_database.example.yml` are safe templates for reference.
- `.env` (not committed) holds secrets and hashes used in the real config.

## Flow
1. User hits a protected service (e.g. `meal.barbarolab.com`)
2. Traefik forwards the request to Authelia for verification
3. If not authenticated, the user is redirected to `auth.barbarolab.com` to log in
4. On success, Traefik lets the original request through

## Security notes
- All access is over HTTPS with Let's Encrypt certs via DNS-01 challenge through Cloudflare.
- Bruteforce attempts and bad credentials are logged with source IP.
- Access policy defaults to `deny` and explicitly allows only authenticated users under `*.barbarolab.com`.
