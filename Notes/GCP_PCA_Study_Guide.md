# Google Cloud Professional Cloud Architect (PCA) — Complete Study Guide

---

## How to Use This Guide

The PCA exam is scenario-based, not trivia-based. Every question tests whether you can pick the *right* service or design given constraints (cost, compliance, latency, existing investment, team skill, migration timeline). This guide is organized by the six official exam domains, plus the Well-Architected Framework and the four official case studies, because Google explicitly draws exam scenarios from those case studies.

---

# 0. Google Cloud Well-Architected Framework (Foundational Lens)

The framework underlies almost every domain below. Its five pillars:

| Pillar | Core Idea | Key Services/Practices |
|---|---|---|
| **Operational Excellence** | Automate, observe, respond fast | Cloud Monitoring, Logging, Cloud Deploy, IaC (Terraform) |
| **Security, Privacy & Compliance** | Least privilege, defense in depth, shift-left | IAM, VPC-SC, CMEK, Security Command Center |
| **Reliability** | Design for failure, meet SLOs | Multi-region, health checks, MIGs, SLOs/error budgets |
| **Cost Optimization** | Pay for value, right-size | CUDs, autoscaling, rightsizing recommender |
| **Performance Optimization** | Elasticity, proximity to users | Global LB, caching (CDN/Memorystore), autoscaling |

**Exam tip:** When a question gives you a trade-off ("minimize cost" vs "maximize availability"), identify which pillar the business is prioritizing in the scenario/case study before choosing.

---

# Domain 1: Designing and Planning a Cloud Solution Architecture

### Key Concepts and Services
- **Business & technical requirements gathering** — translating stakeholder goals (CEO wants growth, CTO wants reliability) into architecture.
- **Compute options:**
  - Compute Engine (VMs) — full control, legacy/lift-and-shift
  - Google Kubernetes Engine (GKE) — containerized, microservices, portability
  - Cloud Run — serverless containers, stateless, scale-to-zero
  - Cloud Functions — event-driven, single-purpose
  - App Engine — legacy PaaS, still tested occasionally
- **Storage options:**
  - Cloud Storage (object) — classes: Standard, Nearline, Coldline, Archive
  - Persistent Disk / Hyperdisk — block storage for VMs
  - Filestore — managed NFS
  - Cloud SQL (MySQL/PostgreSQL/SQL Server) — relational, regional
  - Cloud Spanner — global, strongly consistent, horizontally scalable relational
  - Bigtable — wide-column NoSQL, high-throughput time series/IoT
  - Firestore — document NoSQL, mobile/web apps
  - BigQuery — analytics warehouse, serverless
- **Networking building blocks:** VPC, subnets, Shared VPC, VPC Peering, Cloud Interconnect/VPN, Cloud Load Balancing (global/regional, L4/L7), Cloud CDN, Cloud DNS, Cloud NAT.
- **Migration strategies:** Rehost (lift-and-shift), Replatform, Refactor/Re-architect, Rebuild, Replace (the "5 R's").
- **Hybrid/multi-cloud:** Anthos, GKE Enterprise, Cloud Interconnect, Cloud VPN.

### Important Design Trade-offs
- **Compute Engine vs GKE vs Cloud Run vs Cloud Functions** — control/flexibility decreases, operational simplicity increases, in that order.
- **Cloud SQL vs Spanner** — Cloud SQL is cheaper/simpler but regional and vertically limited; Spanner costs more but gives global scale + strong consistency (use when scenario says "global financial transactions," "no downtime for schema changes," "scale beyond single region with consistency").
- **Bigtable vs BigQuery vs Firestore** — Bigtable = high-throughput operational NoSQL; BigQuery = analytical OLAP; Firestore = low-latency operational document store for apps.
- **Regional vs multi-regional resources** — cost vs availability/DR trade-off.
- **Monolith vs microservices** — microservices aid team autonomy/scaling but add complexity (service mesh, observability needs).

### Best Practices
- Always map requirements to a *decision table* (throughput, consistency, latency, structure of data) before picking a database.
- Prefer managed/serverless services unless a stated constraint (custom kernel modules, licensing, specific OS) forces IaaS.
- Design landing zones with a resource hierarchy (Organization → Folders → Projects) before deploying workloads.
- Use Shared VPC for centralized network administration across many projects/teams.

### Common Exam Scenarios and Pitfalls
- A question describes a legacy monolith needing "quick migration with minimal code change" → Compute Engine rehost, not a rewrite to GKE.
- A question mentions "unpredictable traffic spikes, pay only for what you use, stateless HTTP service" → Cloud Run, not GKE.
- A question mentions "global consistency across regions for a ledger" → Spanner, not Cloud SQL with read replicas.
- Pitfall: choosing Firestore for heavy analytical queries (wrong — that's BigQuery).
- Pitfall: forgetting that App Engine Standard has language/runtime restrictions vs Flexible.

### Links to Other Domains
- Feeds Domain 2 (provisioning the chosen services) and Domain 3 (security zones/IAM boundaries follow from the resource hierarchy designed here).

---

# Domain 2: Managing and Provisioning a Solution Infrastructure

### Key Concepts and Services
- **Infrastructure as Code:** Terraform (primary tool tested), Deployment Manager (legacy), Config Connector (Kubernetes-native GCP resource management).
- **Resource hierarchy & governance:** Organization policies, folders, projects, labels, quotas.
- **Compute provisioning:** Managed Instance Groups (MIGs) with autoscaling, instance templates, custom images, sole-tenant nodes, Spot VMs, committed use discounts (CUDs), sustained use discounts.
- **Storage provisioning:** lifecycle management rules, storage classes, disk types (pd-standard, pd-ssd, pd-balanced, Hyperdisk).
- **Networking provisioning:** subnets/CIDR planning, firewall rules (hierarchical, network, priority), Private Google Access, Private Service Connect, Cloud NAT for egress-only.
- **Data pipeline provisioning:** Pub/Sub (ingestion), Dataflow (stream/batch processing), Dataproc (managed Hadoop/Spark), Cloud Composer (orchestration).
- **CI/CD:** Cloud Build, Artifact Registry, Cloud Deploy.

### Important Design Trade-offs
- **Terraform vs Deployment Manager** — Terraform is multi-cloud, has larger community, is what Google now recommends (exam skews Terraform-friendly, though it tests concepts, not syntax).
- **MIGs vs GKE for scaling stateless apps** — MIGs simpler for VM-based apps; GKE better for containerized microservices needing fine-grained scaling.
- **Spot VMs vs standard VMs vs CUDs** — Spot = cheapest but preemptible (good for fault-tolerant batch); CUDs = discount for guaranteed steady-state usage; mixing instance provisioning models is a common cost-optimization answer.
- **Dataflow vs Dataproc** — Dataflow = serverless, unified batch/stream (Apache Beam), less ops; Dataproc = when you have existing Hadoop/Spark code to lift-and-shift.

### Best Practices
- Automate everything with IaC; avoid manual console changes ("ClickOps") for production.
- Use labels and folders for cost allocation and policy inheritance.
- Set organization policies (e.g., restrict public IPs, restrict resource locations) at the folder/org level, not per-project.
- Use instance templates + MIGs for repeatable, autoscaled, self-healing compute.
- Right-size continuously using Recommender API/rightsizing recommendations.

### Common Exam Scenarios and Pitfalls
- Scenario: "Provision infrastructure consistently and repeatably across dev/test/prod" → IaC/Terraform, versioned in source control.
- Scenario: "Batch job that can tolerate interruption, minimize cost" → Spot VMs.
- Scenario: "Predictable, long-running workload for 3 years" → Committed Use Discounts.
- Pitfall: choosing Dataproc when the described workload has no existing Hadoop/Spark code (Dataflow is usually the "cloud-native" answer).
- Pitfall: forgetting Cloud NAT is required for private instances needing outbound internet access without public IPs.

### Links to Other Domains
- Directly implements Domain 1's design choices; intersects Domain 3 via firewall/org policy security controls; intersects Domain 6 via monitoring/alerting on provisioned resources.

---

# Domain 3: Designing for Security and Compliance

### Key Concepts and Services
- **Identity:** Cloud Identity, IAM (roles: basic/primitive, predefined, custom), Workforce/Workload Identity Federation, service accounts, short-lived credentials.
- **Network security:** VPC Firewall rules, Firewall Policies (hierarchical), Cloud Armor (WAF/DDoS), Private Google Access, VPC Service Controls (perimeter around APIs to prevent data exfiltration), Identity-Aware Proxy (IAP) for zero-trust access without VPN.
- **Data protection:** encryption at rest by default, Customer-Managed Encryption Keys (CMEK) via Cloud KMS, Customer-Supplied Encryption Keys (CSEK), Confidential Computing (encryption in use), DLP API for sensitive data discovery/redaction.
- **Compliance & governance:** Assured Workloads, Access Transparency, Access Approval, Organization Policy Service, Security Command Center (SCC), audit logs (Admin Activity, Data Access, System Event, Policy Denied).
- **Key management:** Cloud KMS, Cloud HSM, External Key Manager (EKM).

### Important Design Trade-offs
- **IAM predefined vs custom roles** — predefined = easier maintenance; custom = least privilege but more admin overhead.
- **VPC-SC vs firewall rules** — firewalls control network traffic; VPC-SC controls data exfiltration at the API/service boundary (different problem — commonly confused on exam).
- **CMEK vs CSEK vs Google-managed keys** — increasing customer control and increasing operational burden, in that order.
- **IAP vs Cloud VPN/Interconnect for remote access** — IAP gives per-app zero-trust access without exposing a network tunnel; VPN/Interconnect extends the on-prem network into GCP.

### Best Practices
- Apply least privilege via IAM at the smallest reasonable scope (avoid Owner/Editor in production).
- Use groups, not individual user bindings, for IAM at scale.
- Enable Org Policies to prevent risky configurations (e.g., disallow external IPs, require CMEK, restrict resource locations for data residency).
- Use VPC-SC around sensitive data services (BigQuery, Cloud Storage) to prevent exfiltration even by compromised credentials.
- Centralize logging and enable Security Command Center for continuous posture management.
- For regulated workloads (HIPAA, PCI, FedRAMP), map controls to Assured Workloads and Access Transparency.

### Common Exam Scenarios and Pitfalls
- Scenario: "Prevent a compromised insider from copying BigQuery data to a personal project" → VPC Service Controls, not just IAM.
- Scenario: "Employees need to SSH into VMs without a bastion host or public IP" → Identity-Aware Proxy (IAP TCP forwarding).
- Scenario: "Data must stay encrypted with keys we control and can revoke" → CMEK (Cloud KMS), possibly Cloud HSM for hardware-backed keys.
- Scenario: "Data residency requirement — data must not leave the EU" → Organization Policy resource location constraints + regional resources.
- Pitfall: choosing IAM alone to prevent data exfiltration (IAM controls *who* can act, not *where data can go*).
- Pitfall: confusing Data Access audit logs (off by default for most services, must be explicitly enabled) with Admin Activity logs (always on).

### Links to Other Domains
- Security constraints shape Domain 1 architecture choices and Domain 2 provisioning (org policies, firewall automation); feeds Domain 6 (SCC findings feed operational response).

---

# Domain 4: Analyzing and Optimizing Technical and Business Processes

### Key Concepts and Services
- **Cost management:** Billing exports to BigQuery, Budgets & alerts, Cost breakdown reports, Recommender (rightsizing, idle resource, CUD purchase recommendations).
- **Process analysis:** identifying bottlenecks in CI/CD, data pipelines, or dev workflows; SRE principles (toil reduction).
- **Business continuity:** Recovery Time Objective (RTO) and Recovery Point Objective (RPO) definitions drive backup/DR architecture choices.
- **Automation of business processes:** Workflows, Cloud Scheduler, Eventarc for event-driven automation, Apigee for API management/monetization.
- **Data-driven decision-making:** BigQuery ML, Looker/Looker Studio for dashboards feeding business decisions.

### Important Design Trade-offs
- **RTO/RPO near-zero vs cost** — active-active multi-region is expensive; backup-restore is cheap but has high RTO/RPO. Choose based on stated business tolerance, not "best possible."
- **Build vs buy for API management** — Apigee (managed, full lifecycle) vs custom-built gateway.
- **Manual approval vs full automation** — regulated industries may require human-in-the-loop steps even where automation is technically possible.

### Best Practices
- Always tie technical recommendations back to a business metric (cost saved, revenue enabled, time-to-market) — the exam rewards business-outcome framing.
- Use the billing export + BigQuery + Looker Studio pattern for cost visibility and chargeback across teams.
- Establish RTO/RPO explicitly with the business before selecting a DR pattern (see DR patterns below).
- Continuously apply recommender insights to control cost drift.

### DR Patterns (RTO/RPO ladder — frequently tested)
| Pattern | RTO/RPO | Cost |
|---|---|---|
| Backup & Restore | Hours–days | Lowest |
| Cold standby (deploy infra on failure) | Tens of minutes–hours | Low |
| Warm standby (scaled-down replica running) | Minutes | Medium |
| Hot standby / Active-active multi-region | Near zero | Highest |

### Common Exam Scenarios and Pitfalls
- Scenario: "Finance wants monthly cost breakdown by team/project" → Billing export to BigQuery + Looker Studio dashboard + labels for allocation.
- Scenario: "Business can tolerate 4 hours of downtime, minimize cost" → Warm or cold standby, not active-active (which would overspend on the stated requirement).
- Pitfall: recommending the most technically resilient solution when the business explicitly asked to minimize cost — the exam penalizes over-engineering just as much as under-engineering.

### Links to Other Domains
- Cost/process analysis outcomes often loop back into Domain 1 (redesign) and Domain 5 (change management for optimizations).

---

# Domain 5: Managing Implementations of Cloud Architecture

### Key Concepts and Services
- **Migration execution:** Migrate to Virtual Machines (VM migration), Migrate for Compute Engine, Database Migration Service (DMS), Storage Transfer Service, Transfer Appliance (offline bulk transfer).
- **Application deployment:** Cloud Build (CI), Cloud Deploy (CD to GKE/Cloud Run/GCE), Artifact Registry, canary/blue-green/rolling deployment strategies.
- **Data migration approaches:** online (DMS, Storage Transfer Service) vs offline (Transfer Appliance) depending on data volume and available bandwidth.
- **Testing and validation:** staged rollouts, feature flags, load testing before cutover.
- **Change management:** phased migration waves, rollback plans, communication plans.

### Important Design Trade-offs
- **Online vs offline data transfer** — determined by data volume vs available network bandwidth and timeline (a classic exam calculation: "how long to transfer X TB over Y Mbps" — if it exceeds the timeline, use Transfer Appliance).
- **Big-bang vs phased migration** — phased reduces risk but takes longer and requires temporary hybrid connectivity; big-bang is faster but riskier.
- **Blue-green vs canary vs rolling deployment** — blue-green = instant cutover/rollback but double infra cost briefly; canary = gradual exposure, lowest risk, slower; rolling = balance of both, some capacity reduction during rollout.

### Best Practices
- Always calculate bandwidth/time-to-transfer explicitly before recommending an online migration tool.
- Use Database Migration Service for minimal-downtime relational DB migrations (supports continuous replication until cutover).
- Pilot migrations with a low-risk workload before migrating critical systems.
- Automate deployments through Cloud Build/Cloud Deploy pipelines rather than manual pushes.
- Maintain a documented rollback plan for every migration wave.

### Common Exam Scenarios and Pitfalls
- Scenario: "500 TB of data, 100 Mbps link, migrate within a week" → Math shows this is infeasible online → use Transfer Appliance.
- Scenario: "Migrate production MySQL with near-zero downtime" → Database Migration Service with continuous replication.
- Scenario: "Release new version to a small percentage of users first" → Canary deployment.
- Pitfall: recommending Storage Transfer Service for on-premises-to-cloud transfer when the source is not object storage/HTTP/S3-compatible (Transfer Appliance or Transfer Service for on-premises data may be more appropriate depending on version tested).
- Pitfall: ignoring stated downtime tolerance and picking a migration tool that doesn't meet it.

### Links to Other Domains
- Executes the plan from Domain 1; relies on Domain 2 provisioned infrastructure being ready; hands off to Domain 6 for ongoing operations post-cutover.

---

# Domain 6: Ensuring Solution and Operations Excellence

### Key Concepts and Services
- **Observability:** Cloud Monitoring (metrics, uptime checks, alerting policies, dashboards), Cloud Logging (log-based metrics, log sinks, log router), Cloud Trace (distributed tracing), Cloud Profiler, Error Reporting.
- **Reliability engineering (SRE):** SLIs, SLOs, SLAs, error budgets, incident management, postmortems (blameless).
- **Scalability & self-healing:** autoscaling policies (CPU, custom metrics, schedule-based), health checks, automatic instance restart, regional MIGs for zone failure resilience.
- **Cost & performance operations:** ongoing rightsizing, autoscaling tuning, caching layers (Cloud CDN, Memorystore).
- **Support & escalation:** Google Cloud support tiers, release notes monitoring, quota management.

### Important Design Trade-offs
- **Tight SLOs vs cost/velocity** — a 99.999% SLO demands redundant multi-region architecture and slows feature velocity (error budget policy formalizes this trade-off).
- **Centralized logging/monitoring project vs per-project** — centralized aggregation (via log sinks to a central project/BigQuery) simplifies org-wide visibility but requires careful IAM scoping.
- **Reactive alerting vs proactive SLO-based alerting** — SLO burn-rate alerts reduce noise vs simple threshold alerts.

### Best Practices
- Define SLIs/SLOs collaboratively with the business, and use error budgets to gate release velocity.
- Use log-based metrics and alerting policies tied to *symptoms* users feel (latency, error rate), not just infrastructure metrics (CPU).
- Route all logs to a centralized aggregated sink for security/ops visibility (organization-level log sink into BigQuery or Cloud Storage).
- Build dashboards for the four golden signals: latency, traffic, errors, saturation.
- Practice game days/chaos testing to validate resilience assumptions.
- Conduct blameless postmortems after incidents to drive systemic fixes.

### Common Exam Scenarios and Pitfalls
- Scenario: "Alert only when user experience degrades, not on every CPU spike" → SLO-based alerting on latency/error-rate SLIs.
- Scenario: "Centralize security-relevant logs from 200 projects" → Aggregated organization-level log sink.
- Scenario: "Reduce alert fatigue while still catching real problems" → burn-rate/error-budget alerting instead of static thresholds.
- Pitfall: setting SLOs to 100% (unrealistic and blocks all release velocity — the exam tests recognizing 100% is the wrong target).
- Pitfall: relying solely on infrastructure-level monitoring (CPU/memory) rather than application/user-facing SLIs.

### Links to Other Domains
- Closes the loop with Domain 4 (operational data drives further cost/process optimization) and Domain 1 (recurring reliability issues can trigger re-architecture).

---

# Official Case Studies — Summary and Recommended Architectures

Google's PCA exam scenario questions are frequently modeled on these four official case studies. Know each company's business goals, technical requirements, and existing environment.

## 1. EHR Healthcare
**Business:** SaaS platform for healthcare organizations (EHR software), used by clinics/hospitals/insurance.
**Key requirements:** Solid uptime SLAs, HIPAA compliance, ability to scale predictably (seasonal enrollment periods), reduce time to onboard new customers, hybrid connectivity to a mix of on-prem data centers and multiple existing clouds.
**Existing environment:** Mix of on-prem (colo) data centers, some workloads already on other clouds, monolithic and newer microservices coexisting.
**Recommended architecture themes:**
- Migrate/rehost stable workloads to Compute Engine or GKE; refactor customer-facing services to microservices on GKE for elasticity during enrollment spikes.
- Cloud SQL/Spanner for relational data depending on scale needs; Cloud Storage for medical records/attachments with lifecycle policies.
- Hybrid connectivity via Cloud Interconnect/VPN during migration; Cloud DNS for hybrid name resolution.
- Strong IAM, VPC-SC, CMEK, and audit logging for HIPAA compliance; Assured Workloads if applicable.
- Cloud Monitoring/Logging with SLO-based alerting to meet uptime SLAs.

## 2. Helicopter Racing League (HRL)
**Business:** Media company broadcasting live/on-demand helicopter racing video, ingesting large volumes of telemetry and video from racing helicopters.
**Key requirements:** Real-time video/telemetry ingestion at scale, global low-latency content delivery, rapid scaling for live events, machine learning for predictive insights/race analytics, minimizing cost between events (spiky usage pattern).
**Recommended architecture themes:**
- Pub/Sub for telemetry ingestion; Dataflow for real-time stream processing; BigQuery for analytics.
- GKE or Cloud Run for the scalable, spiky application/API tier (scale-to-zero between events saves cost).
- Cloud CDN + global external HTTP(S) Load Balancing for low-latency global video delivery; Cloud Storage for video archive.
- Vertex AI / BigQuery ML for predictive analytics on race data.
- Autoscaling tightly tuned around event schedules; use of Cloud Scheduler for pre-warming resources ahead of known live events.

## 3. Mountkirk Games
**Business:** Game developer building large-scale multiplayer games, moving from on-prem to GCP for global scale.
**Key requirements:** Support millions of concurrent global users, minimize latency, real-time leaderboards/analytics, rapid deployment of new game features, structured relational data for game state plus fast NoSQL for session data.
**Recommended architecture themes:**
- Global architecture: multi-region deployment behind Global External HTTP(S)/TCP Load Balancing for lowest-latency routing to nearest region.
- Bigtable for high-throughput low-latency game state/leaderboard writes; BigQuery for game analytics.
- GKE for game backend services enabling rapid feature deployment via CI/CD (Cloud Build + Cloud Deploy).
- Pub/Sub + Dataflow for real-time event/analytics pipeline.
- Cloud Spanner if strongly consistent global player/transaction data is required (e.g., in-game purchases).

## 4. TerramEarth
**Business:** Manufacturer of heavy equipment (agricultural/mining vehicles) with hundreds of thousands of IoT-connected vehicles worldwide, many with intermittent connectivity.
**Key requirements:** Ingest and analyze telemetry from vehicles (some connected in real time, some batch-uploaded due to poor connectivity), predictive maintenance via ML, support for a dealer network application, data residency/processing at the edge in some cases.
**Recommended architecture themes:**
- Pub/Sub for real-time telemetry ingestion where connectivity allows; Cloud Storage + batch upload workflows for intermittently connected vehicles.
- Dataflow for stream/batch telemetry processing; BigQuery for long-term analytics; Bigtable for high-frequency time-series sensor data if needed.
- Vertex AI for predictive maintenance models trained on historical telemetry; consider deploying trained models to the edge (Edge TPU / on-vehicle inference) for offline prediction.
- Cloud SQL or Spanner for dealer network transactional application data.
- Strong emphasis on data pipeline reliability and reprocessing/backfill design since data arrives out-of-order or delayed.

---

# Quick-Reference Decision Cheat Sheet

| If the scenario says... | Consider... |
|---|---|
| "No code changes, fastest migration" | Compute Engine (rehost) |
| "Stateless HTTP service, scale to zero, pay-per-use" | Cloud Run |
| "Containerized microservices, need fine-grained orchestration" | GKE |
| "Event-driven, single function, small piece of glue code" | Cloud Functions |
| "Global strong consistency, relational, massive scale" | Cloud Spanner |
| "Simple relational DB, regional" | Cloud SQL |
| "High-throughput time-series/IoT writes" | Bigtable |
| "Mobile/web app real-time sync" | Firestore |
| "Ad hoc SQL analytics on huge datasets" | BigQuery |
| "Prevent data exfiltration from managed services" | VPC Service Controls |
| "Zero-trust access without VPN" | Identity-Aware Proxy |
| "Customer-controlled/revocable encryption keys" | Cloud KMS (CMEK) |
| "Data must stay in a specific region" | Org Policy resource location constraint |
| "Fault-tolerant batch job, minimize cost" | Spot VMs |
| "Steady 1-3 year workload, minimize cost" | Committed Use Discounts |
| "Huge data volume, limited bandwidth/timeline" | Transfer Appliance |
| "Near-zero downtime DB migration" | Database Migration Service |
| "Gradual, low-risk release" | Canary deployment |
| "Alert on user-facing symptoms, not noise" | SLO/error-budget-based alerting |
| "Centralize logs across many projects" | Aggregated org-level log sink |

---

*Study tip: For every exam question, first identify (1) which case study/company context applies if any, (2) the primary constraint being tested (cost, latency, compliance, availability, or migration risk), then (3) pick the option that satisfies the constraint without over-engineering beyond what was asked.*
