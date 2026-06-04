# AutoBuilder — Product Requirements Document

**Project:** AutoBuilder (Singer Industrial / Unisource)  
**Client:** Tenexity Inc. for Singer Industrial  
**Date:** 2026-06-04  
**Version:** 1.0

---

## 1. Problem Statement

Unisource, a division of Singer Industrial, configures and sells made-to-order hose and flexible-connector assemblies. Today, recipes (assembly configurations) live inside two legacy character-mode Tribute programs: **OHSM** and **OLFM**. These systems:

- Lock configuration knowledge in 1–2 people's heads
- Offer no preview, no validation, no history
- Cannot be accessed outside the ERP
- Block Singer from scaling this model to other business units

The cost is compounding: incorrect quotes, delayed builds, margin erosion, and institutional knowledge at retirement risk.

**AutoBuilder** replaces OHSM and OLFM with a modern, web-based platform that any team member can operate safely.

---

## 2. Users

| Role | Goal |
|---|---|
| Sales rep | Configure and price an assembly in minutes; produce a customer-ready quote |
| Assembly author | Create or modify assembly recipes in a guided interface without making mistakes |
| Operations manager | View all assemblies, audit changes, approve quotes |
| IT administrator | Manage SSO access, users, and settings |

---

## 3. Value Proposition

- **Anyone can configure.** Guided interface replaces expert-only green-screen entry.
- **Quotes you can trust.** Matches Tribute expansion logic for all 5 assembly families.
- **Knowledge that stays.** Versioned, auditable assembly records—not in people's heads.
- **Foundation for scale.** Cloud-hosted, SSO-ready, with a clean data output for ERP (P21) consumption.

---

## 4. Competitor Landscape

### 4.1 Danfoss Hose Assembly Pro
**URL:** https://www.mdm.com/news/operations/manufacturing/danfoss-launches-self-service-hose-configuration-quoting-tool/

A self-service online configuration and quoting tool for hydraulic hose assemblies powered by Intelli.Build technology. Features 24/7 access, rules-based compatibility validation, analytics dashboards, and multi-device support. Deployed as an embeddable widget or hosted subdomain.

**Gaps vs. AutoBuilder needs:**
- No versioning or change history for assembly recipes
- Limited ERP integration (no P21/Tribute parity proof)
- Designed for distributor-facing external use, not internal authoring workflow
- Access is by request only—not self-administrable

### 4.2 Hive CPQ
**URL:** https://hivecpq.com/en/industries/cpq-for-machine-manufacturing

Smart configurator targeting industrial equipment manufacturers. Supports modular machine configuration, 2,500+ price lists, automatic BOM generation, assembly docs, and ERP/PIM/PLM integration. Claims 70% time savings on quote creation.

**Gaps vs. AutoBuilder needs:**
- Enterprise-scale complexity; configuration requires engineering resources
- No concept of matching a legacy expansion engine (Tribute parity requirement)
- Opaque pricing; 30-day trial only
- Overkill for a 5-family, one-division use case

### 4.3 Tacton CPQ
**URL:** https://global.tacton.com/

Gartner Magic Quadrant Leader for CPQ. Constraint-based modeling, CAD automation, visual configuration, goal-seeking pricing, margin controls. Used by ABB, Bosch, Caterpillar.

**Gaps vs. AutoBuilder needs:**
- Enterprise pricing ($100–500/user/month range)
- Requires extensive implementation engagement to encode assembly families
- No built-in concept of a parity verification workflow against a legacy system
- Significantly over-engineered for a single-division, 5-family deployment

---

## 5. Feature List (MVP Scope)

### Core Features

| # | Feature | Priority |
|---|---|---|
| F1 | **Assembly Configurator** — guided step-by-step configuration for all 5 families | Must |
| F2 | **Validation Rules** — prevent invalid component combinations (mirrors Tribute logic) | Must |
| F3 | **Quote Generation** — pricing calculation + customer-ready PDF/data export | Must |
| F4 | **Assembly Library** — browse, search, and manage all configured assemblies | Must |
| F5 | **Version History** — every assembly change tracked with diff and author | Must |
| F6 | **SSO / Auth** — single sign-on via Supabase Auth (SAML-ready) | Must |
| F7 | **Assembly Authoring** — admin interface to create/edit assembly recipes and rules | Must |
| F8 | **Parity Report** — run 50 test cases and compare AutoBuilder vs. Tribute output | Must |
| F9 | **ERP Data Export** — produce a structured file (JSON/CSV) that P21 can consume | Should |
| F10 | **Audit Log** — immutable log of all changes with timestamp and user | Should |

### Out of Scope (this engagement)
- Live two-way Tribute integration
- Automated order submission to ERP
- Customer-facing (external buyer) portal
- Other Singer business units beyond Unisource
- Native mobile app

---

## 6. Assembly Families (5 total)

| Family | Key Variables |
|---|---|
| MTL-MC with insulation | Hose type, insulation type, fitting style, length |
| Cryo no-armor | Cryo hose spec, fitting ends, length, temperature rating |
| Cryo with armor | Same as above + armor type and jacket |
| SF4500 standard | Hose ID, pressure rating, end fittings, length |
| SF4500 with armor | Same as above + armor/jacket configuration |

Each family has a **recipe** (the set of valid components and expansion rules) authored in the AutoBuilder admin interface, mirroring what OHSM/OLFM encode.

---

## 7. Screens / Primary User Journeys

### 7.1 Sales Rep — Configure & Quote (Happy Flow)

1. Log in via SSO → land on **Dashboard**
2. Click **New Configuration** → select assembly family
3. Step through guided configurator (dropdowns/inputs validated in real time)
4. View assembled BOM + calculated price
5. Click **Generate Quote** → download PDF or copy ERP export
6. Quote saved to **Assembly Library** with version 1

### 7.2 Assembly Author — Create/Edit Recipe

1. Log in as admin → go to **Recipe Manager**
2. Select family → view current recipe rules
3. Add/edit component options and validation rules
4. Save → creates new recipe version; prior configs reference old version

### 7.3 Operations Manager — Parity Check

1. Go to **Parity Report** section
2. Upload 50 Tribute reference outputs (CSV)
3. AutoBuilder runs all 50 configurations → compares outputs
4. Download report: pass/fail per case + diff table

---

## 8. Recommended Approach

**Architecture:** Single-service Railway deployment (Node.js/Express backend + React frontend), Supabase for PostgreSQL + Auth. No separate frontend hosting—keeps the deployment simple and within budget.

**Assembly logic:** Rule engine encoded in database (JSON constraints per family), evaluated server-side. Each config request is stateless and deterministic—matches Tribute's expansion approach.

**Quote output:** Server-rendered PDF via a lightweight library; JSON export for P21.

**Parity report:** Batch job comparing AutoBuilder output against uploaded Tribute CSV; results stored and displayed in-app.

---

## 9. Risks

| Risk | Mitigation |
|---|---|
| Tribute logic partially undocumented | Working sessions with OHSM SME in weeks 1, 4, 7 |
| SSO setup delays (IT bandwidth) | Auth falls back to email/password login; SSO added in M1 |
| 50-case parity set incomplete | Parity report designed to accept partial sets; flag gaps explicitly |
| Scope creep from "just add this one feature" | All additions require written change order per SOW §7 |

---

## 10. MVP Acceptance

AutoBuilder's output matches Tribute for **50 real production part numbers** across all 5 assembly families. Every difference is documented and signed off by Singer. **The parity report is the finish line.**
