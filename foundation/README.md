# SYCREN
### Company Founding Document v1.0
> Architecture, Strategy and Operating Model — June 2026 — Confidential to Founders

> **CURRENT STATUS:** Both founders are 16 years old — Phase 0 (pre-commercial). Landing page in development with waitlist. See [ROADMAP.md](./ROADMAP.md) for the bootstrap plan and [repos/](./repos/) for each repository's documentation.

---

## Table of Contents

1. [Company Identity](#1-company-identity)
2. [Business Model and Intellectual Property](#2-business-model-and-intellectual-property)
3. [Investment and Initial Financial Structure](#3-investment-and-initial-financial-structure)
4. [Headquarters and Expansion Strategy](#4-headquarters-and-expansion-strategy)
5. [Operating Divisions](#5-operating-divisions)
6. [Unified Platform](#6-unified-platform--appsycrencom)
7. [Team Structure](#7-team-structure)
8. [Tech Stack](#8-tech-stack)
9. [GitHub Architecture](#9-github-architecture)
10. [Digital Presence](#10-digital-presence-and-communication-channels)

---

## 1. Company Identity

### 1.1 Mission

> Sycren was born to unify all the digital services a business or professional needs into a single integrated platform: software development, cloud infrastructure, domain management and cybersecurity. The goal is to eliminate technological fragmentation by offering a consistent, transparent and scalable user experience.

### 1.2 Vision

To become the global reference point for businesses and professionals seeking a complete, reliable technology partner oriented toward sustainable growth. Sycren is not a service provider — it is a **strategic ally**.

### 1.3 Core Values

| Value | Description | Practical Application |
|---|---|---|
| **Total Transparency** | No hidden costs, no opaque processes. | Public pricing, periodic client reports, documented decisions. |
| **Real Partnership** | The client is not a number: they are a strategic ally. | Dedicated account, shared roadmap, continuous feedback integrated into the product. |
| **Sustainable Scalability** | Growth driven by real demand, not financial pressure. | Elastic infrastructure, team that grows with the business, no over-engineering. |
| **Operational Excellence** | Quality, efficiency and reliability in every division. | Defined SLAs, 24/7 monitoring, standardized and documented processes. |

### 1.4 Company Information

| Field | Value |
|---|---|
| Legal Name | Sycren |
| Main Domain | sycren.com |
| Operating Languages | English, Arabic, French, Hindi, Italian |
| Business Model | SaaS + Agency + Managed Services |
| Target Market | **International** — SMBs, startups, digital professionals, agencies |
| Geographic Target | Global |

---

## 2. Business Model and Intellectual Property

### 2.1 Core Principle — License of Use

Sycren **does not sell the source code** of its products and platforms. Each solution developed internally or as part of Sycren Studios is granted to the client exclusively under a **license of use**.

> The client purchases the right to use the software, not the ownership of the software itself. The source code, architecture, components and application logic remain the exclusive intellectual property of Sycren.

### 2.2 License Structure

| License Type | Description | Included by Default |
|---|---|---|
| **Standard License of Use** | The client uses the developed product/service. No code access. | ✅ Yes — included in the project price |
| **Extended License of Use** | Read-only access to code for internal audits or third-party integration. | ❌ No — paid, separate contract |
| **Source Code Transfer** | Complete delivery of source code with technical documentation. | ❌ No — paid, separate contract |
| **Copyright Assignment** | Full transfer of intellectual property to the client. | ❌ No — paid, separate contract |

### 2.3 Acquiring Code and Copyright

A client wishing to obtain the source code and/or copyright of a project developed by Sycren must:

- Formally request the transfer through their Account Manager
- Receive and accept a **dedicated quote** for the valuation of the intellectual property
- Sign a **specific transfer contract** that defines: scope of the transferred code, warranties, usage limitations, any residual royalties and confidentiality conditions

> **Legal note:** Partial or total copyright transfer is never included in standard development contracts or SaaS plans. Each such agreement is individually negotiated and documented in a separate deed.

### 2.4 Intellectual Property Protection

- All code developed by Sycren is protected by automatic copyright upon creation
- Client repositories in `sycren-clients` contain project code but ownership remains with Sycren until a contractual transfer
- Standard contracts include no reverse-engineering, no redistribution and no sub-licensing clauses
- The **Sycren** brand, logos, design systems and technical documentation are protected assets and cannot be transferred except by explicit agreement

---

## 3. Investment and Initial Financial Structure

### 3.1 Founding Capital

The total initial investment for the foundation and operational start of Sycren is set at:

> **€15,000 (fifteen thousand euros)**

This capital covers all expenses necessary for the bootstrap phase, from technical infrastructure to legal setup, up to the first marketing activities.

### 3.2 Budget Allocation

| Expense Item | Estimated Budget | Notes |
|---|---|---|
| Company setup and legal advisory | €5,000 | Notary, deed of incorporation, trademark registration |
| Cloud infrastructure and servers (year 1) | €4,000 | VPS, CDN, storage, domains, SSL, monitoring |
| Marketing and launch | €2,500 | Digital campaigns, content, initial social media period |
| Operating reserve | €3,500 | Buffer for unforeseen events and opportunities in the first weeks |
| **Total** | **€15,000** | |

### 3.3 Financial Management Principles

- No unplanned discretionary spending without CEO and CFO approval
- Monthly expense reporting against allocated budget
- Capital is preserved as long as possible: absolute priority on revenue generation before new significant expenses
- No external financing in the initial phase except for strategic opportunities evaluated by the board

---

## 4. Headquarters and Expansion Strategy

### 4.1 Initial Operational Approach — Remote-First

In the initial phase, Sycren adopts a **fully remote** model for the internal team. No private offices or dedicated spaces will be rented until the company achieves adequate financial stability.

> **Physical expansion threshold:** Sycren will not open its own offices (dedicated private spaces) until **€100,000 monthly revenue has been achieved and maintained for at least 3 consecutive months**.

This choice is deliberate and strategic: preserve initial capital, maintain operational flexibility, and avoid tying the cost structure to fixed expenses in a phase where the focus must be on product and client growth.

### 4.2 Public Headquarters — Outside Italy

Before reaching the expansion threshold, Sycren will have a **public institutional headquarters located outside Italy**. This headquarters — typically a coworking space or registered business address — serves the following functions:

- Legal address and international institutional representation
- Credibility with international clients and partners
- Potential venue for occasional meetings or client meetings
- Consistency with global brand positioning (not perceived as exclusively Italian)

The choice of jurisdiction and city for the public headquarters will be defined by the board evaluating: tax advantages, target market reputation, ease of setup and access to international talent pool.

### 4.3 Target Market — International

Sycren's target market is **global**. Positioning, communications, product and pricing are designed for an international audience.

| Aspect | Strategy |
|---|---|
| **Languages** | English as primary language for product and external and internal team communications |
| **Pricing** | In international currency (EUR/USD), compatible with European, Anglo-Saxon and emerging markets |
| **Support** | Timezone and coverage designed for clients in Europe, UK, USA and Middle East |
| **Marketing** | Content and campaigns in English as default; secondary localization for specific markets |
| **Legal** | Contracts in English as international standard, with Italian translation on request |

---

## 5. Operating Divisions

Sycren is structured into four specialized divisions, each with a dedicated platform, converging into a single central dashboard (`app.sycren.com`).

---

### 5.1 Sycren Studios — `studio.sycren.com`

**Positioning:** Agency and custom development division. Serves businesses and professionals needing tailor-made digital solutions: websites, applications, management software, API integrations and technical consulting.

#### Platform Features

- Quote request portal with structured form (project type, budget, deadlines, attachments)
- Client onboarding system with guided requirements collection
- Project dashboard: progress tracking, milestones, deliverables, changelog
- File and asset sharing area (design, briefs, technical documentation)
- Dedicated client ↔ team communication channel (chat + email notifications)
- Quote acceptance and digital contract signing
- Completed project history and final documentation

#### Services Offered

- Web development (landing pages, business websites, e-commerce, portals)
- Web and mobile application development (React, Next.js, Node.js)
- Technical consulting and audit of existing systems
- API integrations and automations
- Maintenance and ongoing support
- UI/UX design (Figma → implementation)

#### KPIs

| Metric | Target |
|---|---|
| Average quote response time | < 24 business hours |
| Post-project client satisfaction | > 4.5 / 5 |
| Percentage of projects delivered on time | > 90% |

---

### 5.2 Sycren Cloud — `cloud.sycren.com`

**Positioning:** Cloud infrastructure and managed hosting division. Offers hosting, VPS, storage and application deployment solutions with a focus on performance, reliability and ease of management.

#### Platform Features

- Hosting control panel: file, database, domain and SSL management
- Self-service VPS provisioning with resource selection (CPU, RAM, disk, region)
- Automated deployment from GitHub repositories (integrated CI/CD)
- Real-time uptime and performance monitoring (graphs, alerts)
- Automatic backups with configurable retention and one-click restore
- SSL certificate management (issuance, auto-renewal)
- Access logs and application diagnostics
- Horizontal and vertical resource scaling
- Object storage (S3-compatible) for static files, media, backups

#### Service Plans

| Plan | Target | Key Features |
|---|---|---|
| **Starter** | Developers, personal portfolios | Shared hosting, 1 domain, SSL, weekly backup |
| **Business** | SMBs, growing SaaS | Dedicated VPS, auto-deploy, daily backup, monitoring |
| **Enterprise** | Established companies | Managed cluster, 99.9% SLA, priority support, auto-scaling |
| **Custom** | High volume or specific needs | Tailored architecture, on-premise hybrid, dedicated support |

#### KPIs

| KPI | Target |
|---|---|
| Guaranteed uptime | 99.9% (contractual SLA) |
| Recovery Time Objective (RTO) | < 1 hour |
| Recovery Point Objective (RPO) | < 24 hours |
| New VPS provisioning time | < 5 minutes |

---

### 5.3 Sycren Domains — `domains.sycren.com`

**Positioning:** Domain registrar with advanced DNS management. Allows users to search, register, renew and transfer domains, with professional tools for DNS configuration.

#### Platform Features

- Domain availability search engine (multiple TLDs: .com, .it, .eu, .io, .dev, etc.)
- Domain registration with guided flow and integrated checkout
- Advanced DNS panel: A, AAAA, CNAME, MX, TXT, NS, SRV record management
- Auto-renewal with pre-expiry notifications (90, 30, 7 days)
- Domain transfer in and out (EPP code)
- WHOIS privacy protection (hides registrant data)
- Integrated DNS propagation checker
- URL redirect and URL masking
- Direct integration with Sycren Cloud (auto-pointing)

#### Cross-Platform Integrations

- Direct connection with `cloud.sycren.com` to associate domain with hosting/VPS
- Connection with `security.sycren.com` for enabling DDoS protection on the domain
- Unified notifications via `app.sycren.com`

#### KPIs

| KPI | Target |
|---|---|
| DNS propagation time | < 15 minutes (global < 48h) |
| DNS panel availability | 99.95% |
| Domain pre-expiry notification | Automatic at 90 / 30 / 7 days |

---

### 5.4 Sycren Security — `security.sycren.com`

**Positioning:** Managed security layer for digital infrastructures. Protects domains, servers and applications through firewall, anti-DDoS, continuous monitoring and vulnerability analysis.

#### Platform Features

- Unified security dashboard: threat overview, events, alerts
- Web Application Firewall (WAF): malicious traffic filtering, custom rules
- Anti-DDoS protection: automatic mitigation of volumetric and application-layer attacks
- 24/7 continuous monitoring: uptime, traffic anomalies, intrusion attempts
- Centralized logs: access, errors, security events with search and export
- Alerting system: real-time notifications via email, Telegram, webhook
- Vulnerability assessment: periodic scanning of attack surfaces
- Monthly security reports with executive summary and recommendations
- Advanced SSL/TLS management: cipher suites, HSTS, certificate transparency

#### Advanced Services

- Penetration testing on request (white-box, black-box)
- Incident response: support in case of breach or ongoing attack
- GDPR and regulatory compliance consulting
- Security audit of applications developed by Sycren Studios

#### KPIs

| KPI | Target |
|---|---|
| Threat detection time | < 60 seconds |
| DDoS attack mitigation time | < 3 minutes |
| Monitoring system availability | 99.99% |
| Security report frequency | Monthly + on-demand |

---

## 6. Unified Platform — `app.sycren.com`

The heart of the Sycren ecosystem: a single dashboard that aggregates and manages all services across the four divisions through a **single sign-on (SSO)**.

### 6.1 Strategic Objective

The unified app eliminates the fragmentation that characterizes purchasing digital services from multiple providers. The user accesses a single environment where they can manage Studios projects, monitor Cloud servers, renew domains and control their infrastructure security.

### 6.2 Core Features

#### Authentication and Identity
- Single Sign-On (SSO): one account to access all divisions
- Two-factor authentication (2FA) mandatory for business accounts
- Role and permission management: owner, admin, developer, viewer
- Access logs and active sessions

#### Main Dashboard
- Active services status overview (cloud, domains, security, projects)
- Customizable widgets for each division
- Unified notification feed (security alerts, domain expirations, project updates)
- Quick actions: deploy, domain renewal, ticket opening, quote request

#### Billing and Payments
- Invoice and payment history for all services
- Multiple payment methods (card, SEPA, wire transfer)
- Unified monthly billing or per-service billing
- Digital quotes with in-app acceptance
- Consumption threshold alerts (cloud computing, storage)

#### Support and Communication
- Ticket system categorized by division and priority
- Live chat with the team (business hours)
- Knowledge base and technical documentation
- Push, email and Telegram notifications configurable by event type

### 6.3 Subdomain Architecture

| Subdomain | Main Function | Target Users |
|---|---|---|
| `app.sycren.com` | Central hub: SSO, dashboard, billing, notifications | All registered users |
| `studio.sycren.com` | Quotes, proposals, custom project management | Studios clients, dev team |
| `cloud.sycren.com` | Hosting, VPS, deploy, storage, monitoring | Developers, sysadmins, PMs |
| `domains.sycren.com` | Search, registration, DNS management and renewals | All users |
| `security.sycren.com` | Firewall, anti-DDoS, logs, alerts, reports | Admins, CTOs, security teams |

---

## 7. Team Structure

### 7.1 Founding Leadership

| Name | Role | Abbreviation | Main Responsibilities |
|---|---|---|---|
| Paciello Angelo | Founder & CEO | CEO | Strategic vision, company representation, investor relations, general management |
| Russo Cosimo | Co-Founder & Managing Director | MD | Daily operations, team management, strategy execution, HR |
| Gallo Rocco Pio | Co-Founder & Chief Product Officer | CPO | Product, UX/UI, unified platform roadmap, client feedback loop |
| Nicolò D'Aniello | Chief Technology Officer | CTO | Technical architecture, stack selection, infrastructure, systems security |

### 7.2 Operational Structure

#### Engineering & Development

| Role | Responsibilities |
|---|---|
| Lead Developer | Technical team guidance, code review, application architecture, developer mentoring |
| Frontend Developer | UI/UX implementation (Next.js, React, Tailwind), design system, accessibility |
| Backend Developer | RESTful/GraphQL APIs, business logic, third-party integrations (Node.js, Fastify, NestJS) |
| DevOps Engineer | CI/CD pipelines (GitHub Actions), containerization (Docker/Kubernetes), infrastructure automation |
| Database Administrator | Schema design, query optimization, backup, data migration (PostgreSQL, MongoDB, Redis) |

#### Product & Design

| Role | Responsibilities |
|---|---|
| Product Manager | Roadmap management, sprint planning, backlog prioritization, stakeholder coordination |
| UI/UX Designer | Wireframes, prototyping (Figma), design system, user testing, developer handoff |

#### Cloud & Infrastructure

| Role | Responsibilities |
|---|---|
| Cloud Engineer | VPS provisioning and management, application deployment, storage, cloud cost optimization |
| System Administrator | Server maintenance, SSH, uptime, patch management, disaster recovery |

#### Security

| Role | Responsibilities |
|---|---|
| Security Engineer | Firewall/WAF configuration, 24/7 monitoring, alert management, system hardening |
| Penetration Tester | Vulnerability assessment, ethical hacking, technical reports, remediation guidelines |

#### Business, Marketing & Growth

| Role | Responsibilities |
|---|---|
| Chief Marketing Officer (CMO) | Brand identity, growth strategies, paid/organic campaigns, positioning |
| Social Media Manager | Channel management (Instagram, TikTok, X, YouTube), content calendar, community |
| Content Creator | Blog articles, newsletter, video tutorials, case studies, public documentation |
| Sales Manager | New client acquisition, quote pipeline management (Studios), partnerships |
| Account Manager | Existing client management, upselling, churn prevention, NPS |

#### Finance & Legal

| Role | Responsibilities |
|---|---|
| CFO / Finance Manager | Budget, automated billing, payments, financial forecasting, reporting |
| Legal Advisor | Client and supplier contracts, privacy policy, GDPR compliance, IP protection |

---

## 8. Tech Stack

### 8.1 Frontend

| Technology | Usage |
|---|---|
| HTML5 / CSS3 | Base markup and styling for static pages and templates |
| JavaScript (ES2024+) | Client-side logic, interactivity, API integration |
| React | UI library for reusable components and SPAs |
| Next.js | React framework for SSR, SSG, routing, SEO optimization |
| Vite + React | Fast development and optimized build for SPA applications |
| Tailwind CSS | Utility-first CSS for consistent design system and rapid prototyping |
| Bootstrap | CSS framework for responsive layouts and standard UI components |

### 8.2 Backend

| Technology | Usage |
|---|---|
| Node.js | Server-side JavaScript runtime, base for all backend services |
| Express.js | Lightweight microservices, simple APIs, HTTP middleware |
| Fastify | High-performance APIs with schema validation (JSON Schema) |
| NestJS | Enterprise applications with modular architecture (DI, decorators) |
| Prisma ORM | Type-safe database access, migrations, schema-first development. Centralized schema in `sycren-db` shared across all applications |

### 8.3 Databases

| Database | Use Case |
|---|---|
| PostgreSQL | Main relational database: users, billing, contracts, domains |
| MySQL / MariaDB | Client applications requiring MySQL compatibility |
| SQLite | Local development, testing, lightweight embedded applications |
| MongoDB | Unstructured data: logs, configurations, CMS content |
| Redis | Cache, sessions, message queues, real-time pub/sub |

### 8.4 Programming Languages

| Language | Usage in Sycren |
|---|---|
| Python | Automations, data processing, DevOps scripting, future ML/AI |
| Rust | High-performance components, security-critical code, CLI tools |
| Go | Cloud-native microservices, system tools, concurrent applications |
| C | Low-level optimizations, hardware/kernel interaction |

### 8.5 DevOps & Tooling

| Tool | Role |
|---|---|
| Docker | Containerization of all services for environment consistency |
| Kubernetes | Container orchestration in production, auto-scaling, rolling deployments |
| Git | Version control for all internal and client source code |
| GitHub Actions | CI/CD: automated tests, build, deploy on push/merge |
| SSH | Secure server access, tunneling, remote infrastructure management |
| Figma | Collaborative design, prototyping, developer handoff |

---

## 9. GitHub Architecture

Sycren's source code is organized into **two separate GitHub organizations**, with differentiated access policies to protect both internal systems and client projects.

### 9.1 Organization: `sycren`

**Purpose:** Contains all repositories for Sycren's internal systems: unified platform, DevOps tools, shared libraries, technical documentation and infrastructure.

#### Repositories

| Repository | Visibility | Description |
|---|---|---|
| `sycren-landing` | Public | Landing page codebase (sycren.com) |
| `sycren-app` | Private | Central hub codebase (app.sycren.com): SSO, dashboard, billing |
| `sycren-studio` | Private | Studios platform: quote management, projects, client portal |
| `sycren-cloud` | Private | Cloud panel: VPS provisioning, deploy, monitoring, storage |
| `sycren-domains` | Private | Domains system: registrar, DNS panel, auto-renewals |
| `sycren-security` | Private | Security platform: WAF config, monitoring, alerting, log viewer |
| `sycren-docs` | Public | Public documentation for developers and clients |
| `sycren-design-system` | Private | Shared UI components, design tokens, Storybook |
| `sycren-db` | Private | Shared package: Prisma schema, migrations, types and DB utilities for all applications |
| `sycren-infra` | Private | Kubernetes configurations, Docker Compose, IaC (Terraform/Ansible) |
| `sycren-discord-bot` | Private | Private Discord bot with AI for moderation, tickets, community management and server automation |

#### Teams

| Team | Members | Repositories | Permissions |
|---|---|---|---|
| `founders` | CEO, MD, CPO, CTO | All | Admin |
| `engineering` | Lead Dev, Frontend, Backend, DevOps, DBA | All internal repos | Write |
| `product-design` | PM, UI/UX Designer | app, studio, design-system, docs | Write |
| `cloud-infra` | Cloud Engineer, SysAdmin | cloud, infra, security | Write |
| `community` | Social Media Manager, Content Creator | discord-bot, docs | Write |
| `security` | Security Engineer, Pen Tester | security, infra | Write |
| `readonly` | CMO, Sales, Account, Legal, Finance | docs | Read |

---

### 9.2 Organization: `sycren-clients`

**Purpose:** Contains repositories for projects developed for Sycren Studios clients. Each client has a dedicated private repository, accessible only to the assigned developers and the client themselves.

#### Repositories

| Naming Convention | Visibility | Description |
|---|---|---|
| `client-name/project-name` | Private | Dedicated repository for the client's individual project |

#### Teams

| Team | Members | Repositories | Permissions |
|---|---|---|---|
| `admin` | CEO, MD, CTO, Lead Dev | All client repositories | Admin — full access for supervision and support |
| `[client]-team` | Client account + assigned developers | Only the specific client's repository | Write — exclusive and isolated access per project |

> **Security principle:** Each client team is completely isolated. A developer assigned to project A has no visibility of project B. Access is automatically revoked upon project completion. The `admin` team maintains full visibility to ensure quality and operational continuity.

---

## 10. Digital Presence and Communication Channels

### 10.1 Social Channels

| Channel | Type | Main Objective | Content |
|---|---|---|---|
| **Instagram** | Visual social | Brand awareness, project portfolio | Visual case studies, behind the scenes, UI showcase |
| **TikTok** | Short-form video | Youth reach, virality | Tech tips, quick tutorials, company culture |
| **X (Twitter)** | Microblogging | Tech community, product updates | Technical threads, announcements, industry opinions |
| **YouTube** | Long-form video | Education, technical authority | Tutorials, webinars, product presentations |
| **Telegram** | Messaging | Direct user updates | Service status, release notes, client offers |
| **WhatsApp** | Business messaging | Direct client support | Quick assistance, commercial follow-ups |
| **GitHub** | Developer platform | Open source, technical credibility | Public contributions, documentation, tools |
| **Discord** | Community | Developer community engagement | Peer-to-peer support, product feedback, events |
| **Newsletter** | Email marketing | Retention, in-depth updates | Monthly release notes, technical articles, offers |
| **Blog** | Content marketing | SEO, thought leadership | Technical guides, case studies, company updates |

---

## Related Notes

| Category | Related Documents |
|---|---|
| **Executive Roadmap** | [[foundation/ROADMAP\|ROADMAP — Bootstrap Roadmap v2.0]] |
| **Database Foundation** | [[db.README]] · [[db.requirements]] · [[db.todo]] |
| **Landing Page** | [[landing.README]] · [[landing.requirements]] · [[landing.todo]] |
| **Central Hub (SSO)** | [[app.README]] · [[app.requirements]] · [[app.todo]] |
| **Client Portal** | [[studio.README]] · [[studio.requirements]] · [[studio.todo]] |
| **Cloud Infrastructure** | [[cloud.README]] · [[cloud.requirements]] · [[cloud.todo]] |
| **Domain Management** | [[domains.README]] · [[domains.requirements]] · [[domains.todo]] |
| **Security** | [[security.README]] · [[security.requirements]] · [[security.todo]] |
| **Discord Bot** | [[discord-bot.README]] · [[discord-bot.requirements]] · [[discord-bot.todo]] |
| **Design System** | [[design-system.README]] · [[design-system.requirements]] · [[design-system.todo]] |
| **Documentation** | [[docs.README]] · [[docs.requirements]] · [[docs.todo]] |
| **Infrastructure** | [[infra.README]] · [[infra.requirements]] · [[infra.todo]] |
| **Navigation Hub** | [[foundation/HOME\|HOME — Knowledge Base Root]] |

---

*SYCREN — Company Founding Document v1.0 | sycren.com | June 2026*
*Confidential document for founders and management. Do not distribute without written authorization.*
