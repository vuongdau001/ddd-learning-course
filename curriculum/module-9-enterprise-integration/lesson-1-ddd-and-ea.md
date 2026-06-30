---
type: lesson
module: 9
lesson: 1
title: "DDD ↔ Enterprise Architecture"
duration: "35 phút"
prerequisites: ["module-8"]
narrative_phase: "enterprise integration"
migration_phase: "Phase 4: Align DDD domains với EA capability map"
business_invariant: "DDD Bounded Context ↔ TOGAF Business Capability; Core Domain = Build, Generic Domain = Buy/SaaS; EA roadmap drives DDD investment priority"
---

# Lesson 9.1: DDD ↔ Enterprise Architecture — "DDD không sống cô lập"

## 📍 Context — Bạn đang ở đây

> Module 1-8 xây ITO từ dưới lên: Domain → Bounded Context → Tactical model → Advanced patterns. Bạn biết cách model, implement, persist, và orchestrate. Nhưng ITO không sống trong chân không — CTO có EA roadmap (TOGAF), Finance đang dùng SAP, HR đang dùng SuccessFactors. DDD team và EA team phải **nói cùng ngôn ngữ**.

## 🔥 Tension — "DDD team và EA team nói khác framework"

Sprint 12. CTO quarterly planning:

> **CTO:** *"EA roadmap Q3: 'Improve Resource Matching Capability' và 'Migrate Billing to SAP S/4HANA'. DDD team, plan thế nào?"*
>
> **Dev A:** *"Resource Matching = chúng tôi đang build ResourceMatchingService (Domain Service) trong Resource Context."*
>
> **CTO:** *"EA team gọi đó là 'Business Capability: Resource Allocation'. Bạn gọi 'Bounded Context'. Billing migration — EA gọi 'Application Component Migration'. Bạn gọi 'Anti-Corruption Layer'. Tôi cần 1 bản mapping rõ ràng để cả 2 team hiểu nhau."*

**Minh:**
> *"DDD và EA (TOGAF) nhìn cùng 1 hệ thống từ lens khác nhau. Capability = WHAT (business ability). Bounded Context = HOW (implementation boundary). Mapping 2 framework = bridge giữa strategy (EA) và execution (DDD)."*

> 💭 **Câu hỏi:** DDD team model domain. EA team plan capabilities. CTO allocate budget. Nếu 3 nhóm không aligned — DDD build cái EA không cần, EA plan cái DDD không implement. Mapping = alignment tool.

## 🎓 Explanation — DDD ↔ EA Bridge

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"TOGAF Capability = business ability (WHAT). DDD Bounded Context = implementation boundary (HOW). Core Domain = Capability cần Build (competitive advantage). Generic Domain = Capability nên Buy/SaaS (commodity). EA roadmap drives DDD investment priority."*

### ITO Capability Map (TOGAF Level 1-2)

```
ITO Company Capability Map:

├── Customer Engagement                    ← DDD: Sales Domain
│   ├── Lead Management                   → Sales Context (Core)
│   ├── Opportunity Qualification          → Sales Context (Core)
│   └── Account Relationship               → Sales Context (Core)
├── Service Delivery                       ← DDD: Resource + Delivery Domain
│   ├── Resource Allocation                → Resource Context (Core)
│   ├── Project Execution                  → Delivery Context (Core)
│   └── Quality Assurance                  → Delivery Context (Supporting)
├── Financial Operations                   ← DDD: Finance Domain
│   ├── Billing & Invoicing                → Finance Context (Supporting)
│   ├── Revenue Recognition                → Finance Context (Supporting)
│   └── Cost Management                    → Finance Context (Generic → SAP)
└── Enabling                               ← DDD: Generic Domains
    ├── HR Administration                  → HR Context (Generic → SaaS)
    ├── IT Infrastructure                  → Not DDD (platform)
    └── Compliance                         → Compliance Context (Generic)
```

### Mapping Table — ITO

| TOGAF Capability | DDD Domain | DDD Classification | Bounded Context | Investment |
|---|---|---|---|---|
| Lead Management | Opportunity Mgmt | **Core** | Sales Context | **Build** (competitive advantage) |
| Opportunity Qualification | Opportunity Mgmt | **Core** | Sales Context | **Build + AI** |
| Resource Allocation | Resource Mgmt | **Core** | Resource Context | **Build** |
| Project Execution | Delivery Mgmt | **Core** | Delivery Context | **Build** |
| Billing & Invoicing | Finance | **Supporting** | Finance Context | **Integrate** (SAP) |
| HR Administration | HR Admin | **Generic** | HR Context | **Buy** (SaaS) |

### Mapping Table — Logistics

| TOGAF Capability | DDD Domain | DDD Classification | Investment |
|---|---|---|---|
| Fleet Management | Vehicle Mgmt | **Core** | **Build** |
| Order Fulfillment | Order Mgmt | **Core** | **Build** |
| Last-Mile Delivery | Transportation | **Core** | **Build + AI** |
| Warehouse Operations | Warehouse | **Supporting** | **Integrate** |
| Compliance & Safety | Compliance | **Generic** | **Buy** |

### EA Roadmap → DDD Priority

```
EA Roadmap Q3-Q4 2026:           DDD Translation:
─────────────────────────────────────────────────────────────
Q3: "Improve Resource Matching"  → Invest in ResourceMatchingService
    (Core Capability)              (Domain Service, Core Domain)
                                   Action: Enhance scoring algorithm

Q3: "Migrate Billing to SAP"    → Build ACL for SAP S/4HANA
    (Generic → Buy)                (Anti-Corruption Layer, Supporting)
                                   Action: Finance Context ACL layer

Q4: "AI-powered Qualification"  → Enhance OpportunityQualifier
    (Core + AI)                    (Domain Service, Core Domain)
                                   Action: Integrate ML model

→ EA investment roadmap ALIGNS với DDD domain priority
→ Core Domains = BUILD investment
→ Generic Domains = BUY/SaaS investment
→ Supporting Domains = INTEGRATE (ACL, adapters)
```

### Integration Pattern mapping EA → DDD

```
TOGAF Application:     DDD Bounded Context:    Integration Pattern (M5):
CRM Application     → Sales Context          → Open Host Service (OHS)
                    → Resource Context        → Customer-Supplier
ERP (SAP)           → Finance Context         → ACL (Anti-Corruption Layer)
SaaS HR             → HR Context              → Conformist (follow their model)
Google Maps API     → Transportation Context  → ACL (translate external data)
```

---

## 🏋️ Exercise — EA ↔ DDD Mapping

### Phần A: ITO — Capability Map (15 phút)

| TOGAF Capability | DDD Domain | Core/Supp/Generic | Bounded Context | Build/Buy/Integrate |
|---|---|---|---|---|
| | | | | |
| | | | | |
| | | | | |
| | | | | |

### Phần B: Logistics — EA Alignment (10 phút)

| Capability | Domain | Priority | Investment Decision |
|---|---|---|---|
| | | | Build / Buy / AI |
| | | | |
| | | | |

### Phần C: CTO Presentation (5 phút)

Viết 3 bullet points cho CTO — tại sao DDD + TOGAF complement nhau:
1. _______________
2. _______________
3. _______________

---

## 🪞 Reflect

1. **DDD team cần học TOGAF không?** → Không cần certification — nhưng biết mapping **Capability → Domain → Context** giúp communicate với EA team + CTO. Nếu enterprise > 500 người → nên biết TOGAF basics.

2. **Khi nào DDD Capability Decomposition (M1) đủ, không cần full TOGAF?** → Startup < 50 người, single product → DDD đủ. Enterprise > 500 người, multiple products, multiple teams → cần EA framework để align.

3. **SAP integration pattern?** → **ACL** — luôn luôn. SAP model rất khác domain model (German abbreviations, SAP-specific terms). ACL translate SAP language → ITO domain language. **Never Conformist with SAP** — bạn sẽ mất domain model.

---

## ✅ Completion Checklist
- [ ] **Recall:** Map TOGAF Capabilities → DDD Domains + classification (Core/Supp/Generic)
- [ ] **Apply:** Tạo mapping table cho ITO + Logistics
- [ ] **Analyze:** Viết CTO-facing explanation — alignment giữa EA roadmap và DDD investment

---

> 🔗 **Tiếp theo:** EA + DDD alignment = strategic level. Nhưng tất cả knowledge artifacts (Glossary, ADR, Event Storm, Capability Map) cần **liên kết** thành mạng lưới — không phải files rời rạc. Bài tiếp — *Knowledge Graph & Traceability* — sẽ trả lời: làm sao trace từ Business Goal → Test Case? Và Knowledge Graph giúp AI agent gì?
