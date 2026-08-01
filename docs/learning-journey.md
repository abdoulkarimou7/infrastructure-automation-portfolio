# Learning Journey

This document tracks my continuous progression toward Linux, Infrastructure Automation, DevOps, and DevSecOps through hands-on projects.

My objective is to build production-ready skills by designing, deploying, troubleshooting, and documenting real systems rather than relying solely on theoretical learning.

Each milestone represents practical work completed in this repository.

---

# Daily Log

## Day 1

* Git setup
* Repository structure

## Day 2

* Git problem resolution
* README structure

## Day 3

* `grep`, `find`, `sed`
* Cron automation
* Git stash & aliases

## Day 4

* SSH & key-based authentication
* Networking fundamentals (`ping`, `dig`, `curl`)
* Bash error handling
* Portfolio improvements

## Day 5

* Environment variables
* PATH
* Bash functions
* Logging
* Health-check script
* Repository cleanup

## Day 6

* React portfolio initialization (Vite)
* Hero, About, Skills, Projects, Contact sections

## Day 7

* Portfolio styling
* Sticky navigation
* Languages section

## Day 8

* Docker multi-stage build
* First GitHub Actions workflow

## Day 9

* GitHub Pages deployment
* Complete CI/CD cycle
* Base path debugging

## Day 10

* Express + PostgreSQL Task Manager API
* Docker Compose
* Persistent storage

## Day 11

* CI pipeline
* Jest & Supertest
* PostgreSQL service
* Docker image build

## Day 12

* Continuous Deployment
* GHCR image publishing
* Trivy security scanning
* Workflow debugging

## Day 13

* GitHub Actions from scratch
* Docker Compose (MySQL)
* YAML anchors

## Day 14

* Advanced YAML exercises
* GitHub Actions practice
* Docker Compose practice

## Day 15

* Complete CI/CD project
* Dockerfile
* Docker Compose
* GitHub Actions
* URL Shortener with Redis

## Day 16

* Local deployment of Vaultwarden
* Docker Compose practice

## Day 17

* Vaultwarden secure architecture
* Internal bridge network
* Environment variables externalization
* Custom Docker image
* Container health checks

## Day 18

* Caddy reverse proxy integration & TLS configuration
* Advanced Docker port mapping & IP binding troubleshooting (`127.0.0.1`)
* Low-level network inspection (`docker inspect`, `docker port`)
* `Caddyfile` routing analysis & connection debugging

## Day 19

* Docker Compose multi-network architecture
* Frontend/backend network separation
* Isolating Vaultwarden on an internal network
* Exposing only Caddy on `127.0.0.1`
* `depends_on` with `service_healthy`
* Healthcheck-driven service startup ordering
* Docker network validation with `docker network inspect`
* TLS/SNI troubleshooting (`localhost` vs `127.0.0.1`)
* Layered troubleshooting: host → Docker port publishing → Caddy → Vaultwarden
* Git commit and remote synchronization

## Day 20

* HTB CTF Apocalypse — Web Exploitation (Challenge 1: "Gatery")
* Reverse-engineered a minified React bundle to reconstruct the API contract (endpoints, payload shapes)
* Identified broken session authentication: cookie signing configured at the app level but never enforced per-route
* Session forgery via raw cookie value — bypassed authentication entirely without valid credentials
* curl-based API testing workflow: JSON payloads, cookie jars, header/status inspection

## Day 21

* HTB CTF Apocalypse — Web Exploitation (Challenge 2: "Rookery")
* Identified stored XSS via unescaped template rendering (EJS `<%- %>` vs `<%= %>`)
* CSP bypass technique: third-party JSONP reflection abuse (whitelisted external domain)
* HttpOnly cookie bypass via same-origin `fetch` exfiltration (targeting an automated admin bot)
* Debugged a payload failure caused by upstream Unicode escaping (`>` → `\u003e`) corrupting JS syntax
* Wrote full technical writeups (root cause + remediation) for both challenges

---

# Milestones

* ✅ Bash & Linux Foundations
* ✅ Git & GitHub
* ✅ React Portfolio
* ✅ Docker
* ✅ Docker Compose
* ✅ GitHub Actions
* ✅ End-to-End CI/CD Project
* ✅ Vaultwarden Local Deployment
* ✅ Docker Networking & Reverse Proxy Troubleshooting
* ✅ Web Application Security Fundamentals (broken auth, XSS, CSP bypass — CTF-based)

---

# Next Milestones

* ⏳ Terraform
* ⏳ Kubernetes
* ⏳ Monitoring & Observability
* ⏳ DevSecOps
* ⏳ Production Infrastructure Portfolio
