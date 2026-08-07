# Google Cloud Professional Cloud Architect (PCA) Study Repository

[![Certification](https://img.shields.io/badge/Google%20Cloud-Professional%20Cloud%20Architect-4285F4?style=for-the-badge&logo=googlecloud&logoColor=white)](https://cloud.google.com/certification/cloud-architect)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

This repository contains structured notes, course artifacts, architectural reference designs, and case study breakdowns for the **Google Cloud Professional Cloud Architect (PCA)** certification path.

---

## 📁 Repository Structure

```
.
├── 01. Preparing for your Professional Cloud Architect Journey/
├── 02. Google Cloud Fundamentals Core Infrastructure/
├── 03. Essential Google Cloud Infrastructure Foundation/
├── 04. Essential Google Cloud Infrastructure Core Services/
├── 05. Elastic Google Cloud Infrastructure Scaling and Automation/
├── 06. Getting Started with Google Kubernetes Engine/
├── 07. Logging and Monitoring in Google Cloud/
├── 08. Observability in Google Cloud/
├── 09. Getting Started with Terraform for Google Cloud/
├── 10. Reliable Google Cloud Infrastructure Design and Process/
├── 11. Optimize Costs for Google Kubernetes Engine/
├── 12. Developing a Google SRE Culture/
├── 13. Cloud Architecture Design Implement and Manage/
├── 14. Deploy and Manage Apigee X/
├── AI Infrastructure Introduction to AI Hypercomputer/
├── Developing Applications with Cloud Run on Google Cloud Fundamentals/
├── Introduction to AI and Machine Learning on Google Cloud/
├── Introduction to Data Engineering on Google Cloud/
├── Networking in Google Cloud Hybrid and Multicloud/
├── Networking in Google Cloud Network Architecture/
└── Case Studies & Exam Documents/
    ├── master_case_study_ehr_healthcare.pdf
    ├── v6.1_pca_altostrat_media_case_study_english.pdf
    ├── v6.1_pca_cymbal_retail_case_study_english.pdf
    └── v6.1_pca_knightmotives_automotive_case_study_english.pdf
```

---

## 🎯 Official Exam Case Studies Breakdown

The PCA exam assesses real-world cloud architecture decisions through four core enterprise case studies:

| Case Study | Sector | Key Focus & Architectural Patterns |
| :--- | :--- | :--- |
| **EHR Healthcare** | Healthcare / HealthTech | Legacy migration, HIPAA compliance, Hybrid Cloud Interconnect, Cloud Healthcare API, FHIR interoperability, multi-region disaster recovery. |
| **Altostrat Media** | Media & Entertainment | High-throughput global video streaming, low-latency transcoding, Cloud CDN, Cloud Storage lifecycle rules, GKE microservices, BigQuery analytics. |
| **Cymbal Retail** | Retail / E-Commerce | Scalable e-commerce platform, flash sale autoscaling with Cloud Run/GKE, Cloud Spanner for global ACID transactions, AI/ML product recommendations. |
| **KnightMotives Automotive** | Automotive / Connected Vehicles | Telematics IoT data streaming (Pub/Sub -> Dataflow -> Bigtable), AI Hypercomputer model training, hybrid network security, Apigee API management. |

---

## 🧠 Gemini Notebook / NotebookLM Integration

This repository is optimized to serve as a primary grounding source for **Gemini Notebook / NotebookLM**. 

### How to generate AI Study Guides:
1. Push this repo to GitHub or upload the folder markdown/text files into **Gemini Notebook**.
2. Run the following prompt to generate deep architectural notes and study matrices:

> *"Act as an expert Google Cloud Authorized Trainer. Based on my uploaded sources (including course modules, certification plans, and official case studies for EHR Healthcare, Altostrat Media, Cymbal Retail, and KnightMotives Automotive), build a comprehensive domain-by-domain master summary. For each of the 4 case studies, detail their current state, business goals, technical requirements, and recommended target GCP architecture."*

---

## 📚 Exam Domains & Key Topics Covered

* **Domain 1: Designing & Planning Cloud Solution Architectures**
  * High availability, multi-region failover, capacity planning, cost optimization.
* **Domain 2: Managing & Provisioning Infrastructure**
  * Terraform (IaC), VPC design, Shared VPC, HA VPN, Dedicated Interconnect, Cloud Load Balancing.
* **Domain 3: Security & Compliance**
  * IAM granular roles, Workload Identity, CMEK/KMS, Cloud Armor, Security Command Center.
* **Domain 4: Reliable & Resilient Cloud Operations**
  * Google SRE principles, SLIs/SLOs/SLAs, Cloud Operations (Logging, Monitoring, Trace, Profiler).
* **Domain 5: Data Architecture & AI Integration**
  * Storage selection (Cloud SQL, Cloud Spanner, Bigtable, BigQuery), Dataflow, Vertex AI & AI Hypercomputers.

---

## 🛠️ Usage

To clone and work with this repository locally:

```bash
git clone https://github.com/YOUR_GITHUB_USERNAME/Google-Professional-Cloud-Architect.git
cd Google-Professional-Cloud-Architect
```

---

## 📄 License

This repository is maintained for personal study and certification preparation purposes. Course materials retain copyright of Google Cloud.
