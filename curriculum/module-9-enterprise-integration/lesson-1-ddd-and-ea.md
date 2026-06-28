---
type: lesson
module: 9
lesson: 1
title: "DDD ↔ Enterprise Architecture"
duration: "35 phút"
prerequisites: ["module-8"]
---

# Lesson 9.1: DDD ↔ Enterprise Architecture

## 🎓 Concept — "DDD không sống cô lập"

### Vấn đề: DDD team và EA team nói khác ngôn ngữ

```
DDD team:   "Bounded Context", "Aggregate", "Domain Event"
EA team:    "Business Capability", "Application Component", "Integration Flow"
CTO:        "Khi nào build xong? Tích hợp SAP thế nào?"

→ Cùng nói về 1 hệ thống nhưng framework khác nhau
→ DDD team không biết EA roadmap
→ EA team không hiểu DDD model
```

### TOGAF & Business Architecture — Cầu nối

**Business Capability Map (TOGAF):**
```
ITO Company Capability Map (Level 1-2):

├── Customer Engagement
│   ├── Lead Management
│   ├── Opportunity Qualification
│   └── Account Relationship
├── Service Delivery
│   ├── Resource Allocation
│   ├── Project Execution
│   └── Quality Assurance
├── Financial Operations
│   ├── Billing & Invoicing
│   ├── Revenue Recognition
│   └── Cost Management
└── Enabling
    ├── HR Administration
    ├── IT Infrastructure
    └── Compliance
```

**Mapping sang DDD:**

| TOGAF Capability (Level 2) | DDD Domain | DDD Phân loại | Bounded Context |
|---|---|---|---|
| Lead Management | Opportunity Mgmt | Core | Sales Context |
| Opportunity Qualification | Opportunity Mgmt | Core | Sales Context |
| Resource Allocation | Resource Mgmt | Core | Resource Context |
| Project Execution | Delivery Mgmt | Core | Delivery Context |
| Billing & Invoicing | Finance | Supporting | Finance Context |
| HR Administration | HR Admin | Generic | HR Context |

### Ví dụ — ITO CRM

```
TOGAF Level 1:        DDD Domain:           DDD Concept:
Customer Engagement → Opportunity Mgmt    → Core Domain
                     ↓
Service Delivery   → Resource Mgmt        → Core Domain
                     ↓
Financial Ops      → Finance              → Supporting Domain

TOGAF Application:    DDD Bounded Context:  Integration:
CRM Application    → Sales Context         → Open Host Service
                  → Resource Context       → Customer-Supplier
ERP (SAP)         → Finance Context        → ACL (Anti-Corruption Layer)
```

### Ví dụ — Logistics

```
TOGAF Capability:          DDD Domain:
Fleet Management        → Vehicle Mgmt (Core)
Order Fulfillment       → Order Mgmt (Core)
Last-Mile Delivery      → Transportation Mgmt (Core)
Warehouse Operations    → Warehouse Mgmt (Supporting)
Compliance & Safety     → Compliance (Generic)
```

### Capability-based Planning → DDD Priority

```
EA Roadmap:                      DDD Priority Matrix:
Q1: "Improve Resource Matching"  → Invest in Resource Mgmt (Core)
Q2: "Automate Billing"           → Migrate Finance to SaaS (Generic)
Q3: "AI-powered Qualification"   → Enhance Opportunity Mgmt (Core)

→ EA investment roadmap ALIGNS với DDD domain priority
→ Core Domains get build investment
→ Generic Domains get buy/SaaS investment
```

---

## 🏋️ Exercise — EA ↔ DDD Mapping

### Phần A: ITO — Capability Map (15 phút)

Vẽ Business Capability Map Level 1-2 cho ITO, rồi map sang DDD:

| TOGAF Capability | DDD Domain | Core/Supp/Generic | Bounded Context |
|---|---|---|---|
| | | | |
| | | | |
| | | | |
| | | | |

### Phần B: Logistics — EA Alignment (10 phút)

Map TOGAF capabilities sang DDD cho Logistics company:

| Capability | Domain | Priority | Investment Decision |
|---|---|---|---|
| | | | Build / Buy / AI |
| | | | |
| | | | |

### Phần C: CTO Presentation (5 phút)

Viết 3 bullet points giải thích cho CTO tại sao DDD và TOGAF complement nhau:

1. _______________
2. _______________
3. _______________

---

## 🪞 Reflect

1. **DDD team có cần học TOGAF không?** Gợi ý: Không cần certification, nhưng biết mapping Capability → Domain → Context giúp communicate với EA team.

2. **Khi nào DDD Capability Decomposition (Module 1) đủ, không cần full TOGAF?** Gợi ý: startup < 50 người, single product → DDD đủ. Enterprise > 500 người, multiple products → cần EA framework.

3. **SAP integration thường dùng pattern gì?** Gợi ý: ACL — vì SAP model rất khác domain model.

---

## ✅ Hoàn thành lesson khi
- [ ] Map TOGAF Capabilities sang DDD Domains cho ITO
- [ ] Map cho Logistics
- [ ] Viết CTO-friendly explanation
- [ ] Trả lời ≥2/3 câu hỏi reflection
