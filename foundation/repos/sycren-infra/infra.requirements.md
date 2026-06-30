# sycren-infra — Requisiti di Infrastruttura
> PRD v1.0 — Giugno 2026 — Uso interno — Allineato alla Bootstrap Roadmap v1.3

---

## 1. Contesto e Obiettivo

Sycren è una piattaforma unificata composta da più microservizi — Studios, Cloud, Domains, Security, App (SSO), Docs e Database Foundation. Ogni servizio è un repository indipendente nell'organizzazione GitHub `sycren`.

Il repository `sycren-infra` centralizza tutta l'infrastruttura necessaria per eseguire questi servizi in ambienti riproducibili, dalle macchine locali dei founder fino al cluster di produzione.

**Obiettivo primario:** Garantire che ogni servizio possa essere deployato in modo identico e riproducibile su tre ambienti (dev, staging, prod) con un unico comando o trigger CI/CD.

**Obiettivo secondario:** Automatizzare completamente provisioning, deploy e scaling dell'infrastruttura in modo che due founder sedicenni possano gestire l'intera piattaforma senza operation manuali.

**Stato attuale (Giugno 2026):**
- [x] Organizzazione GitHub `sycren` creata
- [x] Repository `sycren-infra` inizializzato
- [ ] Docker Compose locale funzionante per tutti i servizi
- [ ] CI/CD attivo su GitHub Actions
- [ ] Cluster Kubernetes attivo (staging/prod)
- [ ] Terraform configs per cloud provider
- [ ] Ansible playbooks per provisioning
- [ ] Monitoring e osservabilità implementati

**Principio guida:**
> Costruisci tutto ora. Vendi al momento giusto. L'infrastruttura segue i ricavi, non li precede.

---

## 2. Environment Strategy

### 2.1 Panoramica Ambienti

| Ambiente | Scopo | Infrastruttura | Chi lo usa | Accesso |
|---|---|---|---|---|
| `development` | Sviluppo locale feature | Docker Compose su macchina locale | Founder (Angelo, Cosimo) | Locale |
| `staging` | Preview e testing pre-prod | K8s cluster ridotto (1-2 nodi) | Founder + CI/CD | GitHub Actions / VPN |
| `production` | Produzione clienti reali | K8s cluster HA (3+ nodi) | Founder + CI/CD | GitHub Actions / VPN |

### 2.2 Development (Locale)

- Docker Compose con tutti i servizi necessari
- Hot-reload per sviluppo frontend/backend
- Database locale (PostgreSQL via container)
- Niente auth SSO — bypass per sviluppo
- Email via MailHog o Resend test mode
- Volumi mounted per codice locale
- Rete condivisa `sycren-net` per comunicazione inter-servizio

### 2.3 Staging

- K8s namespace dedicato `sycren-staging`
- Deploy automatico da branch `develop` / PR preview
- Database staging (Supabase progetto separato)
- Ingress base con SSL self-signed o LetsEncrypt staging
- Monitoring minimale (Prometheus + Grafana base)
- Secrets mock / staging-specific
- Resilienza ridotta (repliche = 1)

### 2.4 Production

- K8s cluster HA multi-nodo (Hetzner o DigitalOcean)
- Namespace dedicato `sycren-prod`
- Deploy solo da tag semantico via GitHub Releases
- Database production (Supabase progetto production)
- Ingress con SSL LetsEncrypt production, WAF Cloudflare
- Monitoring completo (Prometheus + Grafana + alerting)
- Secrets via GitHub Secrets o HashiCorp Vault
- HPA configurato per ogni servizio
- Network policies restrittive tra namespace
- Backup automatici e disaster recovery plan

---

## 3. Docker Requirements

### 3.1 Multi-Stage Dockerfiles

Ogni servizio deve avere un Dockerfile multi-stage nella propria root del repository:

| Servizio | Repository | Base Image | Build Stage | Runtime Stage |
|---|---|---|---|---|
| sycren-app | `sycren/sycren-app` | `node:20-alpine` | `npm ci && npm run build` | `node:20-alpine` con dist |
| sycren-studio | `sycren/sycren-studio` | `node:20-alpine` | `npm ci && npm run build` | `node:20-alpine` con dist |
| sycren-cloud | `sycren/sycren-cloud` | `node:20-alpine` | `npm ci && npm run build` | `node:20-alpine` con dist |
| sycren-domains | `sycren/sycren-domains` | `node:20-alpine` | `npm ci && npm run build` | `node:20-alpine` con dist |
| sycren-security | `sycren/sycren-security` | `node:20-alpine` | `npm ci && npm run build` | `node:20-alpine` con dist |
| sycren-docs | `sycren/sycren-docs` | `node:20-alpine` | `npm ci && npm run build` | `nginx:alpine` (static) |

**Regole comuni Dockerfile:**
- Multi-stage obbligatorio: `base` → `dependencies` → `build` → `production`
- Mai esporre chiavi o token nelle build args dei layer intermedi
- Utente non-root in runtime (`USER node`)
- Healthcheck con `curl` o `wget` via `HEALTHCHECK` instruction
- `.dockerignore` presente in ogni repo (escludere `node_modules`, `.git`, `dist`, `.env`)
- Labels: `org.sycren.service`, `org.sycren.version`, `org.sycren.environment`

### 3.2 Docker Compose — Local Development

Un file `docker-compose.yml` centralizzato in `sycren-infra` che orchestra tutti i servizi:

**Servizi inclusi:**
- `sycren-app` — Hub centrale SSO
- `sycren-studio` — Client portal
- `sycren-cloud` — Cloud platform
- `sycren-domains` — Domains manager
- `sycren-security` — Security dashboard
- `sycren-docs` — Documentazione
- `postgres` — Database condiviso (dev)
- `redis` — Cache e session store
- `mailhog` — Email catching per test

**Configurazione:**
- Rete: `sycren-net` (bridge)
- Volumi: `pgdata`, `redis-data` named volumes
- Porte mappate su localhost con conflitti evitati
- File `.env` locale per override variabili
- `docker-compose.override.yml` per hot-reload mounts

### 3.3 Docker Compose — Production Override

`docker-compose.prod.yml` per testare la configurazione production-like in locale:
- Nessun volume mount del codice
- Build con target `production`
- Healthcheck attivi
- Limiti di risorse (CPU/memory) attivi
- Porte non esposte all'esterno (solo rete interna)

---

## 4. Kubernetes Requirements

### 4.1 Cluster Setup

| Provider | Ambiente | Nodi | Specs Minime | Costo Stimato |
|---|---|---|---|---|
| Hetzner (k3s) | Staging | 2 nodi | 2 vCPU, 4GB RAM | ~€8/mese |
| Hetzner (k3s) | Production | 3+ nodi | 4 vCPU, 8GB RAM | ~€30+/mese |
| DigitalOcean (DOKS) | Alternativa | 3 nodi | 2 vCPU, 4GB RAM | ~$36/mese |

### 4.2 Manifests per Servizio (sotto `k8s/`)

Ogni servizio ha una directory dedicata con:

```
k8s/
├── base/
│   └── {service}/
│       ├── deployment.yaml    — Repliche, image, env, probes, resources
│       ├── service.yaml       — ClusterIP per comunicazione interna
│       ├── hpa.yaml           — Autoscaling basato su CPU/memory
│       ├── configmap.yaml     — Config non sensibili
│       ├── secret.yaml        — Template placeholder (valori da ENV)
│       ├── network-policy.yaml— Restrizione traffico in/out
│       └── pdb.yaml           — PodDisruptionBudget (solo prod)
├── overlays/
│   ├── staging/
│   │   └── {service}/
│   │       └── kustomization.yaml — Override staging
│   └── production/
│       └── {service}/
│           └── kustomization.yaml — Override prod
└── ingress/
    ├── staging-ingress.yaml
    └── production-ingress.yaml
```

**Requisiti specifici Deployment:**
- `resources.requests` e `resources.limits` definiti per ogni container
- `readinessProbe` e `livenessProbe` con HTTP GET su endpoint `/health`
- `imagePullSecrets` configurato per GitHub Container Registry
- `terminationGracePeriodSeconds` ≥ 30
- `securityContext` con `runAsNonRoot: true`, `readOnlyRootFilesystem: true`
- `topologySpreadConstraints` per distribuzione nodi (production)

**Requisiti Service:**
- ClusterIP per default
- Headless service per servizi Stateful (se necessario)
- Port naming standardizzato: `http`, `grpc`, `metrics`

**Requisiti HPA:**
- Min repliche: 2 (production), 1 (staging)
- Max repliche: 10
- Metriche: CPU al 70%, Memory all'80%
- Custom metrics opzionali per request/sec

### 4.3 Ingress & SSL

- Controller: Traefik o NGINX Ingress Controller
- TLS: LetsEncrypt con cert-manager
- Staging cert: LetsEncrypt staging CA
- Production cert: LetsEncrypt production con auto-rinnovo
- Rate limiting e IP whitelist per admin endpoints
- Annotation per CORS, timeout, body size

### 4.4 ConfigMaps e Secrets

**ConfigMaps:**
- Config non sensibili per ogni servizio (es. log level, feature flags, API URLs)
- Caricate come env vars o mounted volume

**Secrets:**
- Mai hard-coded nei manifest
- Template `.yaml` con placeholder per orchestrazione
- Popolati via:
  - GitHub Actions → `kubectl create secret` durante deploy
  - External Secrets Operator (futuro) per vault esterno

### 4.5 Network Policies

Per namespace, policy di default `DenyAll` con eccezioni esplicite:

| Servizio | Permette Traffico Da | Permette Traffico A |
|---|---|---|
| sycren-app | Ingress, sycren-studio, sycren-cloud | DB, Redis, sycren-studio |
| sycren-studio | Ingress, sycren-app | DB, Redis |
| sycren-cloud | Ingress, sycren-app | DB, Redis |
| Tutti | — | DNS (udp 53), Monitoring |

---

## 5. CI/CD Requirements

### 5.1 GitHub Actions Workflows

| Workflow | Trigger | Azioni |
|---|---|---|
| `ci.yml` | Push su `develop`, `main` | Lint, test, build immagini, push su GHCR |
| `deploy-staging.yml` | Push su `develop` | Deploy su namespace staging via kubectl |
| `deploy-production.yml` | Tag semantico (`v*.*.*`) | Deploy su namespace production via kubectl |
| `security-scan.yml` | Weekly schedule + PR | Trivy scan immagini, dependency audit |
| `cleanup.yml` | PR merged/chiusa | Rimuovi preview namespace staging |

### 5.2 Container Registry

- GitHub Container Registry (`ghcr.io/sycren`)
- Naming convention: `ghcr.io/sycren/{service}:{tag}`
- Tag strategy:
  - `latest` — ultimo commit su `main`
  - `develop-{sha}` — commit su `develop`
  - `v{major}.{minor}.{patch}` — release versionata
  - `pr-{number}` — preview PR
- Retention: ultime 50 immagini per servizio

### 5.3 Workflow Dettaglio

**ci.yml:**
1. Checkout repository
2. Setup Node.js 20
3. Cache node_modules
4. `npm ci`
5. `npm run lint`
6. `npm run typecheck`
7. `npm run test`
8. Build Docker image (multi-stage, target `production`)
9. Push su GHCR con tag appropriato

**deploy-staging.yml:**
1. Checkout `sycren-infra`
2. Configure kubectl con KUBECONFIG staging
3. `kustomize build k8s/overlays/staging/{service} | kubectl apply -f -`
4. Verify rollout: `kubectl rollout status deployment/{service}`

**deploy-production.yml:**
1. Checkout `sycren-infra`
2. Configure kubectl con KUBECONFIG production
3. `kustomize build k8s/overlays/production/{service} | kubectl apply -f -`
4. Verify rollout
5. Notify su Telegram/Discord del deploy

---

## 6. Infrastructure-as-Code

### 6.1 Terraform

Directory `terraform/` con configurazioni per provider cloud:

```
terraform/
├── modules/
│   ├── hetzner-k8s/        — Modulo cluster k3s su Hetzner
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── digitalocean-k8s/   — Modulo DOKS cluster
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── environments/
│   ├── staging/
│   │   ├── main.tf         — Chiamata al modulo con vars staging
│   │   ├── terraform.tfvars
│   │   └── backend.tf      — State su S3-compatible o locale
│   └── production/
│       ├── main.tf
│       ├── terraform.tfvars
│       └── backend.tf
└── provider.tf              — Provider config condiviso
```

**Risorse Terraform:**
- Cluster Kubernetes (k3s su Hetzner o DOKS)
- Node pool (worker nodes)
- Firewall rules
- Load balancer (se necessario)
- DNS records (Cloudflare provider)
- Object storage per backups (S3-compatible)

### 6.2 Ansible

Directory `ansible/` con playbooks per provisioning server:

```
ansible/
├── inventory/
│   ├── staging.yml         — Host staging
│   ├── production.yml      — Host production
│   └── group_vars/
│       ├── all.yml         — Variabili comuni
│       ├── staging.yml
│       └── production.yml
├── playbooks/
│   ├── bootstrap.yml       — Setup iniziale server (Docker, fail2ban, UFW)
│   ├── k3s.yml             — Installazione e configurazione k3s
│   ├── monitoring.yml      — Deploy Prometheus Node Exporter
│   └── security.yml        — Hardening OS (SSH key only, automatic updates)
└── roles/
    ├── common/             — Base packages, timezone, users
    ├── docker/             — Installazione Docker Engine
    ├── k3s/                — Installazione k3s agent/server
    └── hardening/          — CIS benchmark hardening
```

### 6.3 Environment Management

- Dev: Nessun provisioning — Docker Compose locale
- Staging: `terraform apply -auto-approve -var-file=environments/staging/terraform.tfvars`
- Production: `terraform apply -auto-approve -var-file=environments/production/terraform.tfvars`

**State file:**
- Locale per Fase 0 (nessun costo)
- Remote su Backblaze B2 o S3-GitHub integration in Fase 2+

---

## 7. Security Requirements

### 7.1 Secrets Management

| Tipo | Dove sono | Accesso |
|---|---|---|
| API Keys servizi | GitHub Secrets | GitHub Actions runtime |
| DB credentials | Kubernetes Secrets (create da CI/CD) | Solo pod autorizzati |
| TLS keys | cert-manager (auto) | Automatico |
| Cloud provider tokens | GitHub Secrets + 1Password | Founder via 1Password |
| Docker Hub / GHCR token | GitHub Secrets | GitHub Actions |

**Regole:**
- Nessun commit di `.env` o secret files
- `.gitignore` include `.env`, `*.key`, `secrets.yaml`
- git-crypt o sops per encryptare file sensibili nel repo (opzionale)
- Rotazione trimestrale delle chiavi

### 7.2 Image Scanning

- Trivy scanner in pipeline CI
- Scan ogni build di Docker image
- Scan settimanale completo del registry
- Politica: blocca merge con vulnerabilità CRITICAL senza fix

### 7.3 Network Security

- Network policies K8s restrittive per namespace
- Cloudflare WAF in front all'Ingress production
- Rate limiting per IP su endpoint pubblici
- SSH solo via key, porta non standard, fail2ban
- Automatic updates via unattended-upgrades (Ansible ruolo `hardening`)

### 7.4 Audit Logging

- Kubernetes audit log abilitato su cluster production
- Log di accesso GitHub Actions persistiti
- Cloudflare audit log per DNS/CDN
- kubectl access logging via `kubectl auth can-i --list`

### 7.5 Secure Defaults

- `securityContext` restrittivo in ogni pod
- `readOnlyRootFilesystem: true`
- `runAsNonRoot: true`
- Container con user `node` (UID 1000)
- Seccomp profile, AppArmor (se supportato dal provider)

---

## 8. Monitoring & Observability

### 8.1 Stack

| Componente | Strumento | Deployment |
|---|---|---|
| Metric collection | Prometheus + kube-prometheus-stack | Helm chart in cluster |
| Dashboards | Grafana | ConfigMaps con dashboard JSON |
| Log aggregation | Loki + Promtail | Helm chart + DaemonSet |
| Uptime monitoring | Uptime Kuma o Checkly | Container esterno al cluster |
| Alerting | Grafana Alerting + Telegram/Discord | Configurato in Grafana |
| Cost monitoring | OpenCost | Helm chart |

### 8.2 Metriche da Monitorare

**Per servizio:**
- CPU/Memory usage vs limits
- Request/sec, error rate (5xx), p50/p95/p99 latency
- Active connections
- Pod restart count

**Per cluster:**
- Node CPU/Memory/Disk pressure
- Pod status (Pending, CrashLoopBackOff, OOMKilled)
- etcd leader changes
- API server latency

**Per business:**
- Utenti attivi per servizio (custom metric)
- Deploy frequency
- Build pipeline duration

### 8.3 Alert Rules

| Alert | Severity | Soglia | Canale |
|---|---|---|---|
| Pod CrashLoop | Critical | Immediata | Telegram |
| CPU > 80% | Warning | > 5 min | Telegram |
| Disk > 85% | Critical | Immediata | Telegram + Email |
| Service down | Critical | > 30s | Telegram + Phone? |
| SSL expiring | Warning | < 30gg | Telegram |

### 8.4 Centralized Logging

- Tutti i container loggiano su stdout/stderr (strutturati JSON)
- Promtail raccoglie e invia a Loki
- Grafana Explore per query log
- Retention: 7 giorni (staging), 30 giorni (production)

---

## 9. Completion Criteria

### Fase 0 — Fondamenta (🕐 16 anni)

- [ ] Docker Compose locale funzionante per tutti i 6 servizi
- [ ] Multi-stage Dockerfile per ogni servizio nel rispettivo repo
- [ ] Rete Docker `sycren-net` creata e funzionante
- [ ] `.env.example` e documentazione setup locale

### Fase 1 — CI/CD (🕐 16-17 anni)

- [ ] `ci.yml` workflow passa su ogni push su `develop`
- [ ] Immagini Docker buildate e pushatte su GHCR
- [ ] `deploy-staging.yml` deploya su K8s staging
- [ ] `deploy-production.yml` deploya su K8s production
- [ ] Security scan settimanale attivo

### Fase 2 — K8s (🕐 17 anni)

- [ ] Cluster staging attivo (k3s su Hetzner)
- [ ] Cluster production attivo (HA, 3+ nodi)
- [ ] Tutti i servizi deployati e funzionanti su staging
- [ ] Ingress con SSL LetsEncrypt funzionante
- [ ] HPA configurato per servizi a carico variabile
- [ ] Network policies attive e testate

### Fase 3 — IaC (🕐 17-18 anni)

- [ ] Terraform configurazione per Hetzner completa
- [ ] Ansible playbook bootstrap funzionante
- [ ] Provisioning cluster da zero verificato
- [ ] Disaster recovery procedure documentata e testata

### Fase 4 — Monitoring (🕐 18+ anni)

- [ ] Prometheus + Grafana funzionanti su entrambi gli ambienti
- [ ] Dashboard per ogni servizio create
- [ ] Alerti Telegram attivi e testati
- [ ] Loki centralizzato log collection attivo
- [ ] Uptime monitoring esterno configurato

---

## 10. Stack Tecnologico — Riepilogo

| Layer | Tecnologia | Costo Fase 0 | Costo Fase 2+ |
|---|---|---|---|
| Container Runtime | Docker | Gratuito | Gratuito |
| Orchestration | Kubernetes (k3s) | Gratuito | Gratuito |
| Infrastruttura Cloud | Hetzner / DigitalOcean | — | ~€30-50/mese |
| Container Registry | GitHub Container Registry | Gratuito | Gratuito |
| CI/CD | GitHub Actions | Gratuito | Gratuito |
| Provisioning | Terraform + Ansible | Gratuito | Gratuito |
| Secrets | GitHub Secrets → K8s Secrets | Gratuito | Gratuito |
| Monitoring | Prometheus + Grafana + Loki | Gratuito (self-hosted) | Gratuito |
| DNS + CDN + WAF | Cloudflare | Gratuito | Gratuito |
| Image Scanning | Trivy (Open Source) | Gratuito | Gratuito |

---

## 11. Age & Evoluzione

Il repository `sycren-infra` evolve insieme alla piattaforma:

| Età 🕐 | Stato | Focus |
|---|---|---|
| **16** | Setup iniziale | Docker Compose, Dockerfiles, CI/CD base |
| **16-17** | Consolidamento | K8s staging, GHCR, pipeline complete |
| **17** | Scale up | K8s production, HPA, network policies |
| **17-18** | Automazione | Terraform, Ansible, provisioning da zero |
| **18+** 🎂 | Maturità | Monitoring, alerting, disaster recovery |

---

## Related Notes

| Document | Description |
|---|---|
| [[infra.README]] | sycren-infra overview |
| [[infra.todo]] | Implementation plan |
| [[app.requirements\|sycren-app requirements]] | Service managed by infra |
| [[studio.requirements\|sycren-studio requirements]] | Service managed by infra |
| [[cloud.requirements\|sycren-cloud requirements]] | Service managed by infra |
| [[domains.requirements\|sycren-domains requirements]] | Service managed by infra |
| [[security.requirements\|sycren-security requirements]] | Service managed by infra |
| [[landing.requirements\|sycren-landing requirements]] | Deployed via Vercel (external infra) |
| [[discord-bot.requirements\|sycren-discord-bot requirements]] | Hosting and CI/CD |
| [[db.requirements\|sycren-db requirements]] | Database deployment |
| [[foundation/ROADMAP\|Bootstrap Roadmap]] | Build order and timeline |

---

*Sycren — Infrastructure Requirements PRD v1.0 — Giugno 2026*
*Documento interno. Non distribuire senza autorizzazione scritta.*
