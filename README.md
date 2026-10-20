# Talia — AI-Assisted SOC / NOC / GRC Operations Platform

**Status:** V1–V2 prototype (Phases 1–3 complete, Jul–Sep 2025). Not production-ready; no customer deployments.

Talia is an experiment in building security, network, and compliance operations as a set of small, independently deployable AI-assisted services ("Acolytes") coordinated by a central registry ("Master") and rolled out to customer sites by an on-premise agent ("Inquisitor"). The goal was to test whether a single operator could stand up SOC, NOC, and GRC tooling from one platform, with AI handling parsing and triage and a human keeping authority over every decision.

## Architecture

```
                 ┌──────────────────────────────┐
   cloud         │  Master AI + Acolyte Registry │
                 │  (central, provider-hosted)   │
                 └──────────────┬───────────────┘
                                │ check-in / tasks
                                ▼
   customer      ┌──────────────────────────────┐
   on-prem       │  The Inquisitor (agent)       │  pulls + rolls out Acolyte images via Docker SDK
                 └──────────────┬───────────────┘
                                │
      ┌──────────┬──────────────┼──────────────┬────────────┐
      ▼          ▼              ▼              ▼            ▼
  SOC Acolyte  NOC Acolyte  GRC Acolyte  Gamification   Apprentice UI
                                            Acolyte      (dashboard)
```

Every Acolyte is a containerized **FastAPI + Pydantic** service with **SQLAlchemy/Alembic** migrations on **PostgreSQL 14**, orchestrated with **Docker Compose**.

## Services

| Repo | What it does |
| --- | --- |
| [SOC-Acolyte](https://github.com/Baderholdjr/SOC-Acolyte) | Log ingestion; YAML-driven detection rules engine with UEBA (anomalous-login-time); VirusTotal CTI enrichment; log anonymization; alert CRUD/summary and response-action endpoints; LLM-based parsing of unstructured logs into structured events |
| [NOC-Acolyte](https://github.com/Baderholdjr/NOC-Acolyte) | nmap-based asset discovery, SNMP polling, per-asset metrics, anomaly detection |
| [GRC-Acolyte](https://github.com/Baderholdjr/GRC-Acolyte) | Control-framework and policy ingestion, asset-to-control linkage, rule-based policy-to-control mapping — precursor to the ONI GRC-as-code engine |
| [Gamification-Acolyte](https://github.com/Baderholdjr/Gamification-Acolyte) | Security-awareness training modules, quizzes, and scoring |
| [apprentice-ui](https://github.com/Baderholdjr/apprentice-ui) | Status dashboard aggregating health across all Acolyte services |
| [The-Inquisitor](https://github.com/Baderholdjr/The-Inquisitor) | On-prem agent: checks in with the Master registry, receives deployment tasks, pulls and rolls out Acolyte images with the Docker SDK |

## Development phases

| Phase | Scope | Status |
| --- | --- | --- |
| 1 | Core V1 — Master AI with Acolyte Registry, SOC Acolyte, NOC Acolyte | Complete |
| 2 | Integration, UI & V2 — Inquisitor V1 secure messenger, Apprentice UI V2 dashboard, SOC/NOC V2 | Complete |
| 3 | GRC, Gamification & production readiness — hardened Inquisitor, GRC Acolyte V1, Gamification Acolyte V1 | Complete |
| 4 | Advanced AI & machine learning | Planned |
| 5 | Full-featured UI/UX | Planned |
| 6+ | Deployment & continuous improvement | Planned |

Phases 1–3 represent roughly 960 engineering hours.

## Beta infrastructure baseline

| Tier | Minimum |
| --- | --- |
| Provider-hosted platform | 16 vCPU, 64 GB RAM, 500 GB SSD; self-hosted PostgreSQL; VPC; Docker Compose |
| Customer on-prem (Inquisitor) | 8 CPU cores, 32 GB RAM, 1 TB SSD; modern Linux; Docker Engine |

## Known gaps (why this is a prototype)

- No automated test suites or CI in the service repos
- No authentication layer between services beyond API-key env vars
- Secrets management is env-file based and needs replacing before any real deployment

Lessons from Talia fed directly into the next two projects: **ONI** (GRC-as-code with GitOps review gates and a 244-test suite) and **Aderhold A.R.C.** (AI-adoption governance framework). See the profile README for both.

## Author

Bernard A. Aderhold Jr., DBA — [LinkedIn](https://www.linkedin.com/in/bernard-aderhold-jr-dba-963ab9305)
