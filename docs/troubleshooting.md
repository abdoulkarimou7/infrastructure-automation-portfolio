# Troubleshooting Log

This document records technical issues encountered throughout this portfolio and the steps taken to investigate and resolve them.

The objective is to document the troubleshooting process, improve future debugging efficiency, and demonstrate a structured problem-solving approach.

---

## Incident 01 — GitHub Pages Deployment

### Context

Portfolio deployment to GitHub Pages was failing after configuring the GitHub Actions workflow.

### Symptoms

* Deployment completed successfully.
* The website returned a 404 page.
* Static assets were not loaded correctly.

### Root Cause

The Vite `base` path did not match the GitHub repository name, causing incorrect asset URLs after deployment.

### Resolution

* Updated the `base` option in the Vite configuration.
* Verified the GitHub Pages workflow.
* Redeployed the application.
* Confirmed that all assets loaded correctly.

### Lessons Learned

* Always configure the correct `base` path for GitHub Pages deployments.
* Validate deployment locally before publishing.
* Review generated asset paths when troubleshooting static sites.

---

## Incident 02 — Docker Published Port Refused

### Context

A Caddy reverse proxy was configured with Docker Compose to publish container port `443` to host port `444`.

### Symptoms

The Compose configuration contained:

```yaml
ports:
  - "127.0.0.1:444:443"
```

However:

```bash
curl -k -I https://127.0.0.1:444
```

returned:

```text
curl: (7) Failed to connect to 127.0.0.1 port 444
```

`docker inspect` showed the port binding configuration, but `docker ps` did not show an active host mapping.

### Investigation

The following checks were used:

* `docker compose config`
* `docker compose ps`
* `docker inspect`
* `docker port`
* `ss -ltnp`
* `docker network inspect`

The Docker network was configured as:

```yaml
internal:
  internal: true
```

Both Caddy and Vaultwarden were connected only to this internal network.

The network inspection showed:

* `Internal: true`
* Caddy connected to the internal bridge network
* No effective host listener on port `444`

### Root Cause

Caddy was attached only to an isolated Docker network marked `internal: true`.

The network topology did not provide the required external connectivity for the reverse proxy.

### Resolution

The architecture was redesigned using two networks:

* `internal` — isolated backend network
* `frontend` — normal bridge network

Caddy was connected to both networks:

```text
Caddy
├── frontend
└── internal
```

Vaultwarden remained connected only to:

```text
Vaultwarden
└── internal
```

After recreating the stack, Docker reported:

```text
127.0.0.1:444->443/tcp
```

and:

```text
LISTEN 0 4096 0.0.0.0:444
```

### Lessons Learned

* `HostConfig.PortBindings` does not by itself prove that a host listener is active.
* `docker ps`, `docker port`, `ss`, and `docker network inspect` provide different layers of evidence.
* A reverse proxy can require access to both an external-facing network and an isolated backend network.
* Backend services should not be unnecessarily published to the host.

---

## Incident 03 — TLS Handshake Failure with `127.0.0.1`

### Context

After fixing Docker networking, the TCP connection to Caddy succeeded, but HTTPS requests using `127.0.0.1` failed during the TLS handshake.

### Symptoms

```bash
curl -vk https://127.0.0.1:444
```

returned:

```text
TLS alert, internal error
```

However:

```bash
curl -vk https://localhost:444
```

successfully completed the TLS handshake and returned:

```text
HTTP/2 200
via: 1.1 Caddy
server: Rocket
```

### Investigation

The Caddyfile contained:

```caddy
localhost {
    reverse_proxy vaultwarden:80
}
```

Caddy logs confirmed automatic TLS certificate management for:

```text
localhost
```

The Caddy configuration was also validated successfully with:

```bash
docker exec caddy caddy validate --config /etc/caddy/Caddyfile
```

### Root Cause

The TLS request names were different:

```text
Caddy configuration → localhost
Client request       → 127.0.0.1
```

The successful `localhost` request matched the configured site and its locally managed certificate. The `127.0.0.1` request used a different TLS server name/SNI.

### Resolution

The local endpoint was kept as:

```text
https://localhost:444
```

which matches the Caddy configuration.

No Docker networking changes were required.

### Lessons Learned

* TCP connectivity and TLS negotiation are separate troubleshooting layers.
* A successful port mapping does not guarantee a successful TLS handshake.
* Hostname/SNI selection can affect Caddy's TLS configuration and certificate selection.
* `curl -vk` is useful for distinguishing connection failures from TLS negotiation failures.

---

## Incident 04 — Reverse Proxy Startup Ordering

### Context

Caddy depended on Vaultwarden, but basic `depends_on` only established startup ordering.

### Initial Configuration

```yaml
depends_on:
  - vaultwarden
```

This ensured Vaultwarden was started before Caddy, but did not guarantee that Vaultwarden was ready to serve requests.

### Resolution

Vaultwarden already had a healthcheck. The dependency was changed to:

```yaml
depends_on:
  vaultwarden:
    condition: service_healthy
```

### Result

Docker Compose now waits for Vaultwarden to reach `healthy` before starting Caddy.

Observed startup sequence:

```text
Vaultwarden starts
      ↓
Healthcheck runs
      ↓
Vaultwarden becomes healthy
      ↓
Caddy starts
```

### Lessons Learned

* A running container is not necessarily a ready service.
* `depends_on` and `healthcheck` solve different problems.
* `service_healthy` is useful when a dependent service must wait for application readiness.
* Troubleshooting should distinguish process state, health state, networking, and application state.

---

## Current Architecture

```text
                         Host
                          │
                   127.0.0.1:444
                          │
                          ▼
                       Caddy
                      /     \
                     /       \
              frontend      internal
                              │
                              ▼
                         Vaultwarden
```

Current isolation model:

```text
Caddy       → frontend + internal
Vaultwarden → internal only
```

Current application flow:

```text
Host
  ↓
Caddy:443
  ↓
Vaultwarden:80
```

Vaultwarden has no published host port.

---

## Future Incidents

Additional troubleshooting reports will be documented here as new projects are developed.

