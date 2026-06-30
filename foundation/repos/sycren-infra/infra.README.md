# sycren-infra

> Infrastructure-as-Code: Kubernetes configs, Docker Compose, Terraform, and Ansible for Sycren.

## Description

Central infrastructure repository containing all configuration files for Sycren's cloud infrastructure. Includes Kubernetes manifests, Docker Compose files, Terraform configurations, and Ansible playbooks.

## Tech Stack

- Docker / Docker Compose
- Kubernetes
- Terraform
- Ansible
- GitHub Actions (CI/CD)

## Repository

`github.com/sycren/sycren-infra` (private)

## Services Managed

| Service | Environment | Status |
|---|---|---|
| [[app.README\|sycren-app]] | app.sycren.com | Dev / Staging / Prod |
| [[studio.README\|sycren-studio]] | studio.sycren.com | Dev / Staging / Prod |
| [[cloud.README\|sycren-cloud]] | cloud.sycren.com | Dev / Staging / Prod |
| [[domains.README\|sycren-domains]] | domains.sycren.com | Dev / Staging / Prod |
| [[security.README\|sycren-security]] | security.sycren.com | Dev / Staging / Prod |
| [[discord-bot.README\|sycren-discord-bot]] | Discord Bot | Prod |
| [[db.README\|sycren-db]] | Database | All environments |
| [[landing.README\|sycren-landing]] | sycren.com | Prod (Vercel) |

## Documentation

- [[infra.requirements]] — Full PRD
- [[infra.todo]] — Implementation plan
