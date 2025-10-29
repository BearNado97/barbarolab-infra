# BarbaroLab Infrastructure

Welcome to the BarbaroLab Infrastructure Repository — a modular, self-hosted environment for managing all core network, proxy, and authentication services powering the BarbaroLab ecosystem.

This repository is structured for clarity, scalability, and automation, ensuring that each service is self-contained, easy to deploy, and version-controlled.

---

## Directory Layout

barbarolab-infra/
├── traefik/ # Reverse proxy and SSL termination via Cloudflare DNS-01 challenge
│ ├── docker-compose.yml
│ ├── .env.example
│ ├── .gitignore
│ └── README.md
│
├── authelia/ # SSO (Single Sign-On) and 2FA authentication gateway
│ ├── docker-compose.yml
│ ├── configuration.example.yml
│ ├── users_database.example.yml
│ ├── .gitignore
│ └── README.md
│
├── plex-stack/ # (Upcoming) Plex media ecosystem — Radarr, Sonarr, etc.
│
└── README.md # You’re here


---

## Core Services

### Traefik Reverse Proxy
- Acts as the secure front-end entry point for all hosted services.
- Handles automatic SSL certificate generation using the Cloudflare DNS-01 challenge.
- Dynamically routes requests to backend containers via Docker labels.
- Supports middleware integration (rate limiting, authentication, redirect, compression).

**Key Features:**
- Automatic HTTPS for all subdomains under `*.barbarolab.com`
- Real-time container discovery
- Cloudflare API-based DNS verification

**Example Dashboard:**  
Accessible via: `https://traefik.barbarolab.com` (admin-restricted)

---

### Authelia
- Provides centralized authentication (SSO) with optional 2FA for all web services.
- Integrates directly with Traefik via the `forwardAuth` middleware.
- Stores credentials in a local SQLite or file-based user database.
- Fully supports TOTP (Authenticator App) and WebAuthn (FIDO2) MFA.

**Key Features:**
- Single Sign-On for all BarbaroLab apps
- Secure login portal: `https://auth.barbarolab.com`
- Configurable access control policies per service or domain
- Works seamlessly behind Traefik via forward authentication

**Auth Flow Example:**
1. User requests `https://meal.barbarolab.com`
2. Traefik forwards the request to Authelia for verification
3. Upon success, Authelia redirects back to the app with a valid session cookie

---

## Planned Additions

| Service | Purpose | Status |
|----------|----------|--------|
| meal-stack/ | Self-hosted meal planner and grocery automation | Planned |
| plex-stack/ | Media automation (Plex, Sonarr, Radarr, Lidarr, Overseerr) | Existing |
| grafana/ | Monitoring and analytics for infrastructure and services | Planned |
| home-assistant/ | Smart home hub integration and automation | Planned |

---

## Deployment Overview

1. Clone this repository:
   ```bash
   git clone git@github.com:BearNado97/barbarolab-infra.git
   cd barbarolab-infra

    Configure .env and configuration files under each stack:

        traefik/.env → Cloudflare credentials & domain configuration

        authelia/configuration.yml → User settings, SMTP, and secret keys

    Launch all core services:

docker compose -f traefik/docker-compose.yml -f authelia/docker-compose.yml up -d

Verify:

    Traefik dashboard → https://traefik.barbarolab.com

Authelia portal → https://auth.barbarolab.com
Middleware Integration

The next step is linking Authelia with Traefik via the forwardAuth middleware.
This allows any service behind Traefik (e.g., meal.barbarolab.com, flix.barbarolab.com, etc.) to require Authelia authentication before access is granted.

You’ll find this configuration defined in:

traefik/
└── dynamic_conf/
    └── middleware_authelia.yml

Notes

    All services are configured to use Docker internal networking (barbarolab network).

    Sensitive credentials (e.g., Cloudflare tokens, JWT secrets) are stored in .env files excluded via .gitignore.

    Commit messages follow a clear, descriptive pattern for traceability:

    Initial commit: Traefik reverse proxy setup
    Add Authelia SSO stack for BarbaroLab
    Restructure repo for unified Docker infra

Author

Frankie Barbaro
Cybersecurity Analyst | Home-Lab Engineer | Automation Enthusiast
https://github.com/BearNado97
