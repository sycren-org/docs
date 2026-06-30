# sycren-infra — TODO di Implementazione
> PRD v1.0 · Giugno 2026 · Fase 0–4 · Allineato a Bootstrap Roadmap v1.3

---

## Contesto (dalla Roadmap)

- Entrambi i founder hanno **16 anni 🕐**, nessuna partita IVA — Soluzione B: costruire tutto ora, vendere al **18° compleanno 🎂**
- `sycren-infra` centralizza Docker, Kubernetes, Terraform, Ansible e CI/CD per tutti i servizi della piattaforma
- La piattaforma include: `sycren-app` (SSO hub), `sycren-studio` (client portal), `sycren-cloud`, `sycren-domains`, `sycren-security`, `sycren-docs`
- L'infrastruttura segue l'ordine di costruzione dei servizi: Landing → `sycren-db` → `sycren-studio` → `sycren-app` → `sycren-cloud` → `sycren-domains` → `sycren-security`
- Costo operativo Fase 0: ~€0 (tutto su tier gratuito). Da Fase 2: ~€30-50/mese per cluster K8s
- L'infrastruttura DEVE essere riproducibile con un unico comando — niente configurazioni manuali

**Principio guida:**
> Build everything now. Sell at the right moment. Infrastructure follows revenue, not the other way around.

---

## Panoramica Fasi e Tempistiche

| Fase | Nome | Età 🕐 | Periodo | Priorità |
|---|---|---|---|---|
| 0 | Fondamenta Docker | 16 | Settimane 1–4 | 🔴 Alta |
| 1 | CI/CD | 16-17 | Mesi 1–2 | 🔴 Alta |
| 2 | Kubernetes Staging → Produzione | 17 | Mesi 3–5 | 🟡 Media |
| 3 | Infrastructure-as-Code | 17-18 | Mesi 5–8 | 🟡 Media |
| 4 | Monitoring & Osservabilità | 18+ | Post-lancio | 🟢 Bassa |

---

## Phase 0: Local Development — Fondamenta Docker
**Età: 🕐 16 · Priorità: 🔴 Alta · Dipende da: Servizi in fase di sviluppo**

### 0.1 Dockerfile Multi-Stage (un file per servizio)

- [ ] `sycren-db` — Dockerfile multi-stage con Prisma generate e migrazioni
  - Stage `base`: `node:20-alpine`, copia package.json, npm ci
  - Stage `build`: genera Prisma client, build TypeScript
  - Stage `production`: solo dist + node_modules produzione
  - Healthcheck: `pg_isready` o `node health.js`
- [ ] `sycren-studio` — Dockerfile multi-stage per Next.js app
  - Stage `dependencies`: npm ci (solo dependencies)
  - Stage `build`: next build, copia .next
  - Stage `production`: node server with standalone output
  - Healthcheck: HTTP GET `/api/health`
- [ ] `sycren-app` — Dockerfile multi-stage per SSO hub
  - Stessa struttura di sycren-studio (entrambi Next.js)
  - Variabili d'ambiente per OAuth providers
  - Healthcheck: HTTP GET `/api/health`
- [ ] `sycren-cloud` — Dockerfile multi-stage per cloud platform
  - Backend Fastify/NestJS
  - Stage `build`: tsc build
  - Stage `production`: node dist/main.js
  - Healthcheck: HTTP GET `/health`
- [ ] `sycren-domains` — Dockerfile multi-stage
  - API wrapper su Cloudflare API / DNS reseller
  - Healthcheck: HTTP GET `/health`
- [ ] `sycren-security` — Dockerfile multi-stage
  - Dashboard + API per Cloudflare WAF wrapper
  - Healthcheck: HTTP GET `/health`
- [ ] `sycren-docs` — Dockerfile per documentazione statica (docsify / Docusaurus)
  - Stage `build`: npm run build
  - Stage `production`: nginx:alpine serve static
  - Healthcheck: HTTP GET `/` → 200

**Verifica Dockerfile:**
- [ ] `docker build --target production -t sycren/{service}:latest .` passa
- [ ] `docker run --rm sycren/{service}:latest` si avvia e risponde a healthcheck
- [ ] `docker scout quickview sycren/{service}:latest` — 0 vulnerabilità CRITICAL
- [ ] `.dockerignore` presente in ogni repo (esclude node_modules, .git, .env, dist, .next/cache)

### 0.2 Docker Compose Centralizzato (sycren-infra)

- [ ] Creare `docker-compose.yml` nella root di `sycren-infra`
  ```yaml
  services:
    postgres:
      image: postgres:16-alpine
      volumes: pgdata:/var/lib/postgresql/data
      environment:
        POSTGRES_MULTIPLE_DATABASES: sycren_app,sycren_studio,sycren_cloud
    redis:
      image: redis:7-alpine
    mailhog:
      image: mailhog/mailhog
    sycren-db:
      build: ../sycren-db
      depends_on: [postgres]
    sycren-studio:
      build: ../sycren-studio
      depends_on: [sycren-db, redis]
      ports: ["3001:3000"]
    sycren-app:
      build: ../sycren-app
      depends_on: [sycren-db, redis]
      ports: ["3002:3000"]
    sycren-cloud:
      build: ../sycren-cloud
      depends_on: [sycren-db, redis]
      ports: ["3003:3000"]
    sycren-domains:
      build: ../sycren-domains
      depends_on: [sycren-db]
      ports: ["3004:3000"]
    sycren-security:
      build: ../sycren-security
      depends_on: [sycren-db, redis]
      ports: ["3005:3000"]
    sycren-docs:
      build: ../sycren-docs
      ports: ["3006:80"]
  ```
- [ ] Creare `docker-compose.override.yml` per sviluppo con hot-reload
  ```yaml
  services:
    sycren-studio:
      volumes: [../sycren-studio:/app, /app/node_modules]
      command: npm run dev
  ```
- [ ] Creare rete `sycren-net` per scoprire servizi via nome container
- [ ] Configurare `.env` con variabili locali (DB URL, Redis URL, secreti mock)
- [ ] Verificare comunicazione inter-servizio: `sycren-app → sycren-db → postgres`
- [ ] Verificare comunicazione: `sycren-studio → sycren-db, sycren-studio → redis`

### 0.3 Script di Utility

- [ ] `scripts/start-dev.sh` — `docker compose up --build -d`
- [ ] `scripts/stop-dev.sh` — `docker compose down`
- [ ] `scripts/reset-db.sh` — Drop e recreate database locale
- [ ] `scripts/logs.sh {service}` — Tail logs di un servizio specifico
- [ ] `scripts/healthcheck.sh` — Verifica che tutti i servizi rispondano
- [ ] `scripts/seed.sh` — Popola database con dati di test

### 0.4 Documentazione Setup Locale

- [ ] README.md aggiornato con prerequisiti (Docker, Git, Node.js 20)
- [ ] Istruzioni passo-passo per primo setup
- [ ] Troubleshooting comune (port conflicts, Docker Desktop vs Linux)
- [ ] Comandi utili per sviluppo quotidiano

---

## Phase 1: CI/CD — Automazione Pipeline
**Età: 🕐 16-17 · Priorità: 🔴 Alta · Dipende da: Phase 0 completata**

### 1.1 GitHub Container Registry Setup

- [ ] Abilitare GitHub Container Registry sull'org `sycren`
- [ ] Creare token di accesso classico (read/write packages) per GH Actions
- [ ] Configurare `ACTIONS_PAT` come GitHub Secret nell'org
- [ ] Verificare push manuale: `docker push ghcr.io/sycren/test:latest`

### 1.2 CI Workflow (`ci.yml`)

- [ ] Workflow base attivato su push a `develop` e `main`
  ```yaml
  name: CI
  on:
    push:
      branches: [develop, main]
    pull_request:
      branches: [develop]
  ```
- [ ] Job 1: Lint & Test
  - `npm ci` con caching node_modules
  - `npm run lint`
  - `npm run typecheck`
  - `npm run test` (se test presenti)
- [ ] Job 2: Build & Push Docker Image
  - `docker build --target production`
  - Tagging: `{ref_name}-{sha_short}`, `latest` (solo main)
  - Push su `ghcr.io/sycren/{service}`
- [ ] Job 3: Security Scan
  - `trivy image ghcr.io/sycren/{service}:{tag}`
  - Fail su vulnerabilità CRITICAL
  - Output report su artifact
- [ ] Job 4: Notifica su Discord/Telegram (opzionale)

### 1.3 Staging Deploy Workflow (`deploy-staging.yml`)

- [ ] Trigger: push su `develop`
- [ ] Checkout `sycren-infra`
- [ ] Setup kubectl config (KUBECONFIG staging in GitHub Secrets)
- [ ] `kustomize build k8s/overlays/staging/{service} | kubectl apply -f -`
- [ ] `kubectl rollout status deployment/{service} -n sycren-staging`
- [ ] Invia notifica su Telegram: `✅ {service} staging deployato (commit: {sha})`
- [ ] Rollback automatico su failure

### 1.4 Production Deploy Workflow (`deploy-production.yml`)

- [ ] Trigger: tag semantico `v*.*.*` su `main`
- [ ] Manual approval gate (environment protection)
- [ ] Setup kubectl config (KUBECONFIG production in GitHub Secrets)
- [ ] `kustomize build k8s/overlays/production/{service} | kubectl apply -f -`
- [ ] Rolling update con maxSurge=1, maxUnavailable=0
- [ ] `kubectl rollout status deployment/{service} -n sycren-prod`
- [ ] Smoke test: HTTP GET su endpoint pubblico → 200
- [ ] Notifica su Telegram: `🚀 {service} v{version} deployato in produzione`

### 1.5 Security Scan Workflow (`security-scan.yml`)

- [ ] Weekly schedule: ogni lunedì alle 08:00
- [ ] Trivy scan su tutte le immagini in GHCR
- [ ] Genera report e attacha come issue su GitHub
- [ ] Email alert ai founder per vulnerabilità CRITICAL

### 1.6 Cleanup Workflow (`cleanup.yml`)

- [ ] Trigger: PR chiusa/merged
- [ ] `kubectl delete namespace sycren-staging-pr-{number}` (se esiste)
- [ ] `ghcr.io/sycren/{service}:pr-{number}` rimossa dal registry

---

## Phase 2: Production Kubernetes
**Età: 🐝 17 · Priorità: 🟡 Media · Dipende da: Phase 1 completata**

### 2.1 Cluster Setup

- [ ] Creare VPS su Hetzner (CX22 o superiore) per staging
  - OS: Ubuntu 24.04 LTS
  - Docker installato
  - Fail2ban configurato
- [ ] Installare k3s su nodo staging
  - `curl -sfL https://get.k3s.io | sh -`
  - Config: `--write-kubeconfig-mode 644`
- [ ] Verificare cluster: `kubectl get nodes`
- [ ] Creare namespace: `sycren-staging`, `sycren-prod`, `monitoring`
- [ ] Creare cluster production (HA, 3+ nodi) su Hetzner
  - 1 server master + 2 server worker
  - k3s con embedded etcd (HA mode)
  - `curl -sfL https://get.k3s.io | sh -s - server --cluster-init`
- [ ] Configurare kubectl context per entrambi cluster
- [ ] Testare switching: `kubectl config use-context sycren-staging`

### 2.2 Kubernetes Manifests

- [ ] Struttura directory `k8s/` completata

**Per ogni servizio (`k8s/base/{service}/`):**

- [ ] `deployment.yaml`
  - Repliche: 2 (prod), 1 (staging)
  - Immagine da GHCR
  - Pull policy: `Always` (staging), `IfNotPresent` (prod)
  - Env da ConfigMap + Secret
  - Resources: requests (256m CPU, 256Mi RAM), limits (1 CPU, 1Gi RAM)
  - Probes: liveness + readiness su `/health`
  - securityContext: runAsNonRoot, readOnlyRootFilesystem
  - topologySpreadConstraints (prod): spread across zones
- [ ] `service.yaml`
  - ClusterIP (default)
  - Port naming: `http` (3000), `metrics` (9090 se presente)
- [ ] `hpa.yaml`
  - minReplicas: 2 (prod), 1 (staging)
  - maxReplicas: 10 (prod), 3 (staging)
  - targetCPUUtilizationPercentage: 70
  - targetMemoryUtilizationPercentage: 80
- [ ] `configmap.yaml`
  - `NODE_ENV`, `LOG_LEVEL`, `API_URL`, `SERVICE_URLS`
  - Feature flags per Fase
- [ ] `secret.yaml`
  - Template YAML con placeholder `${{ SECRET_NAME }}`
  - DB credentials, API keys, JWT secret
- [ ] `network-policy.yaml`
  - Default deny ingress + egress
  - Allow da Ingress controller
  - Allow a database e redis
- [ ] `pdb.yaml` (solo production)
  - minAvailable: 1 (per servizi con repliche ≥ 2)

### 2.3 Kustomize Overlays

**Staging (`k8s/overlays/staging/`):**
- [ ] Repliche override: 1
- [ ] Resources ridotte
- [ ] Ingress host: `staging.sycren.com`
- [ ] SSL: LetsEncrypt staging CA
- [ ] Nome immagini: `ghcr.io/sycren/{service}:develop-{sha}`

**Production (`k8s/overlays/production/`):**
- [ ] Repliche override: 2+
- [ ] Resources aumentate
- [ ] Ingress host: `*.sycren.com`
- [ ] SSL: LetsEncrypt production
- [ ] HPA attivo
- [ ] PDB attivo
- [ ] Nome immagini: `ghcr.io/sycren/{service}:v{version}`

### 2.4 Ingress & SSL

- [ ] Installare NGINX Ingress Controller via Helm
  ```bash
  helm upgrade --install ingress-nginx ingress-nginx \
    --repo https://kubernetes.github.io/ingress-nginx \
    --namespace ingress-nginx --create-namespace
  ```
- [ ] Installare cert-manager via Helm
  ```bash
  helm upgrade --install cert-manager cert-manager \
    --repo https://charts.jetstack.io \
    --namespace cert-manager --create-namespace \
    --set installCRDs=true
  ```
- [ ] ClusterIssuer per LetsEncrypt (staging + production)
- [ ] Ingress manifest per servizi pubblici:
  - `sycren-studio` → `studio.sycren.com`
  - `sycren-app` → `app.sycren.com`
  - `sycren-cloud` → `cloud.sycren.com`
  - `sycren-domains` → `domains.sycren.com`
  - `sycren-security` → `security.sycren.com`
  - `sycren-docs` → `docs.sycren.com`
- [ ] Annotazioni Ingress: rate limiting, CORS, body size, WAF
- [ ] Verifica SSL: `curl -vI https://studio.sycren.com` → 200 + TLS ok

### 2.5 Storage & Backup

- [ ] PVC per database (se non su Supabase esterno)
- [ ] Velero installato per backup cluster
- [ ] Backup automatico giornaliero su object storage (B2 o S3)
- [ ] Retention: 7 giorni (staging), 30 giorni (production)

---

## Phase 3: Infrastructure-as-Code
**Età: 🐝 17-18 · Priorità: 🟡 Media · Dipende da: Phase 2 completata**

### 3.1 Terraform — Hetzner

- [ ] Provider Hetzner configurato
- [ ] Modulo `hetzner-k8s`:
  - `hcloud_server` per nodi master/worker
  - `hcloud_firewall` per regole base
  - `hcloud_network` per subnet privata
  - `hcloud_placement_group` per spread
  - Remote exec o cloud-init per installare k3s
- [ ] Environment staging: 1 nodo (2 vCPU, 4GB RAM)
- [ ] Environment production: 3 nodi (4 vCPU, 8GB RAM)
- [ ] Terraform state: locale (Fase 0), Backblaze B2 (Fase 2+)
- [ ] `terraform plan` e `terraform apply` testati

### 3.2 Terraform — DigitalOcean (Alternativa)

- [ ] Provider DigitalOcean configurato
- [ ] Modulo `digitalocean-k8s`:
  - `digitalocean_kubernetes_cluster` (DOKS)
  - `digitalocean_kubernetes_node_pool`
  - `digitalocean_firewall`
  - `digitalocean_loadbalancer`
- [ ] Comparazione costi: Hetzner vs DigitalOcean documentata

### 3.3 Terraform — Cloudflare (DNS)

- [ ] Provider Cloudflare configurato
- [ ] DNS records per tutti i sottodomini:
  - `studio.sycren.com` → A/AAAA del cluster
  - `app.sycren.com` → A/AAAA
  - `cloud.sycren.com` → A/AAAA
  - `domains.sycren.com` → A/AAAA
  - `security.sycren.com` → A/AAAA
  - `docs.sycren.com` → A/AAAA
- [ ] WAF rules base (rate limiting, bot fight mode)
- [ ] Page rules per caching

### 3.4 Ansible Playbooks

- [ ] Inventory: `staging.yml`, `production.yml`
- [ ] `playbooks/bootstrap.yml`:
  - SSH hardening (port change, key only)
  - UFW con regole base
  - fail2ban installato e configurato
  - Docker Engine installato (da repo ufficiale)
  - unattended-upgrades configurato
  - Prometheus Node Exporter installato
  - Timezone, hostname, /etc/hosts
- [ ] `playbooks/k3s.yml`:
  - Installa k3s (server/agent)
  - Configura kubeconfig per utente deploy
  - Aggiungi label a nodi per scheduling
- [ ] `playbooks/monitoring.yml`:
  - Prometheus Node Exporter come systemd service
  - Telegraf (opzionale) per metriche custom

### 3.5 Architecture Documentation

- [ ] Diagramma architettura infrastruttura (draw.io / Mermaid)
  - Servizi e loro dipendenze
  - Flusso richieste: Cloudflare → Ingress → Service → Pod → DB
  - Network policies tra servizi
- [ ] Documento di disaster recovery:
  - Procedura di restore da backup
  - Procedura di ri-creazione cluster da Terraform
  - Time to recover stimato: < 4 ore
- [ ] Runbook per incidenti comuni:
  - Pod non parte (CrashLoopBackOff)
  - DB non raggiungibile
  - Certificato SSL scaduto
  - Disco pieno

---

## Phase 4: Monitoring & Observability
**Età: 🐝 18+ 🎂 · Priorità: 🟢 Bassa · Dipende da: Fase 2+3 completate**

### 4.1 Prometheus Stack

- [ ] Installare kube-prometheus-stack tramite Helm:
  ```bash
  helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
  helm upgrade --install prometheus prometheus-community/kube-prometheus-stack \
    --namespace monitoring --create-namespace \
    --values k8s/overlays/production/prometheus-values.yaml
  ```
- [ ] ServiceMonitor per ogni servizio (permette scraping automatico)
- [ ] PrometheusRules configurate per alerting
- [ ] Verifica: `kubectl port-forward svc/prometheus-grafana 3000:80`

### 4.2 Grafana Dashboards

- [ ] Dashboard Kubernetes: nodi, pod, risorse cluster
- [ ] Dashboard per servizio: request rate, error rate, latency, CPU/memory
- [ ] Dashboard business: utenti attivi, projects creati, deploy frequency
- [ ] Dashboard cost: node count, storage, network traffic
- [ ] Datasource: Prometheus, Loki
- [ ] Alert channels configurati (Telegram)

### 4.3 Centralized Logging (Loki)

- [ ] Installare Loki + Promtail tramite Helm:
  ```bash
  helm upgrade --install loki grafana/loki-stack \
    --namespace monitoring \
    --set grafana.enabled=false,prometheus.enabled=false
  ```
- [ ] Promtail DaemonSet su ogni nodo
- [ ] Pipeline stages per parsing log JSON strutturati
- [ ] LogQL queries predefinite per debugging comune
- [ ] Retention: 7gg (staging), 30gg (production)

### 4.4 Uptime Monitoring

- [ ] Uptime Kuma (self-hosted su VPS separato o Railway)
- [ ] Monitoraggio: tutti gli endpoint pubblici ogni 1 min
- [ ] Status page pubblica: `status.sycren.com`
- [ ] Notifiche Telegram per downtime

### 4.5 Cost Monitoring

- [ ] OpenCost installato via Helm
- [ ] Dashboard costi per namespace/servizio
- [ ] Alert su costi anomali (> 20% increase)

---

## Verification Criteria — Accettazione Finale

### Docker — Verifica Completa

- [ ] `docker compose up -d` avvia tutti e 7 i servizi in < 60 secondi
- [ ] `curl localhost:3001/api/health` → `{"status":"ok"}` (tutti i servizi)
- [ ] Comunicazione inter-servizio funzionante: sycren-studio → sycren-db → postgres
- [ ] `docker compose down -v` pulisce tutto senza errori
- [ ] `docker build` da zero senza cache completa in < 5 minuti

### CI/CD — Verifica Completa

- [ ] Push su `develop` → CI passa → immagine su GHCR → deploy staging
- [ ] Push tag `v1.0.0` → deploy production completato
- [ ] Security scan trova vulnerabilità e le segnala
- [ ] PR chiusa → namespace staging rimosso
- [ ] Immagini latest e versionate in GHCR accessibili
- [ ] Rollback automatico su deploy failure verificato

### Kubernetes — Verifica Completa

- [ ] `kubectl get pods -n sycren-staging` → tutti Running
- [ ] `kubectl get pods -n sycren-prod` → tutti Running (min 2 repliche)
- [ ] `curl https://studio.sycren.com` → 200 con SSL valido
- [ ] HPA funzionante: `kubectl run -it load-generator --image busybox -- /bin/sh` con stress test
- [ ] Network policy test: pod in namespace `default` NON può raggiungere pod in `sycren-prod`
- [ ] PodDisruptionBudget rispettato

### IaC — Verifica Completa

- [ ] `terraform destroy && terraform apply` crea cluster funzionante da zero
- [ ] `ansible-playbook playbooks/bootstrap.yml -i inventory/staging.yml` passa senza errori
- [ ] `ansible-playbook playbooks/k3s.yml -i inventory/staging.yml` installa k3s
- [ ] Dopo provision, `kubectl get nodes` mostra tutti i nodi attesi

### Monitoring — Verifica Completa

- [ ] Prometheus target: tutti UP (nessun target down)
- [ ] Grafana dashboards caricano dati storici
- [ ] Allarme Telegram testato: ferma un servizio → ricevi alert in < 2 min
- [ ] Log visibili in Grafana Explore per ogni servizio
- [ ] Uptime Kuma mostra tutti i servizi come UP

---

## Oltre l'Infrastruttura — Cosa Segue (con Età)

| # | Modulo | Età 🕐 | Dipende da Infra |
|---|---|---|---|
| 1 | Landing Page `sycren.com` | **16** | Solo Vercel (non infra) |
| 2 | Database Foundation `sycren-db` | **16** | Docker Compose locale ✅ |
| 3 | Client Portal `studio.sycren.com` | **16** | Docker Compose + CI/CD ✅ |
| 4 | Hub Centrale / SSO `app.sycren.com` | **16-17** | Docker Compose + K8s staging ✅ |
| 5 | Cloud Lite `cloud.sycren.com` | **17** | K8s production ✅ |
| 6 | Domains `domains.sycren.com` | **17-18** | K8s production + Terraform ✅ |
| 7 | Security `security.sycren.com` | **18+** | K8s + monitoring ✅ |
| 🎂 | **Lancio Commerciale (P.IVA, Vendite)** | **18** | Tutto pronto |

---

## Divisione del Lavoro

| Area | Angelo | Cosimo |
|---|---|---|
| Dockerfile multi-stage | Lead | Supporto |
| Docker Compose orchestrazione | Lead | |
| CI/CD GitHub Actions | Lead | Supporto |
| Kubernetes manifests | Lead | |
| Kustomize overlays | Lead | Supporto |
| Ingress / SSL / cert-manager | Lead | |
| Terraform configs | Lead | |
| Ansible playbooks | Lead | |
| Prometheus / Grafana / Loki | Lead | Supporto |
| Documentazione | Entrambi | Entrambi |
| Testing e verifica | Entrambi | Entrambi |

---

## Related Notes

| Document | Description |
|---|---|
| [[infra.README]] | sycren-infra overview |
| [[infra.requirements]] | Full PRD |
| [[app.README\|sycren-app]] | Service managed by infra |
| [[studio.README\|sycren-studio]] | Service managed by infra |
| [[cloud.README\|sycren-cloud]] | Service managed by infra |
| [[domains.README\|sycren-domains]] | Service managed by infra |
| [[security.README\|sycren-security]] | Service managed by infra |
| [[discord-bot.README\|sycren-discord-bot]] | Service managed by infra |
| [[landing.README\|sycren-landing]] | Deployed via Vercel (external) |
| [[db.README\|sycren-db]] | Database operations |
| [[foundation/ROADMAP\|Bootstrap Roadmap]] | Timeline |

---

*Sycren — Infrastructure TODO v1.0 · Giugno 2026 · Allineato a Bootstrap Roadmap v1.3*
*Documento interno. Non distribuire senza autorizzazione scritta.*
