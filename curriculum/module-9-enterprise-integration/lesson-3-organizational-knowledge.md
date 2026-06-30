---
type: lesson
module: 9
lesson: 3
title: "Organizational Knowledge"
duration: "30 phút"
prerequisites: ["module-9/lesson-2"]
narrative_phase: "enterprise integration"
migration_phase: "Phase 4: Codify tribal knowledge thành Knowledge Objects"
business_invariant: "Knowledge Object = codified, structured, linked artifact; Tribal Knowledge = risk (key person leaves = knowledge lost); MR = Meeting Record (Decisions + Actions only); Policy ≠ ADR: ADR = 1 technical decision, Policy = ongoing business rule"
---

# Lesson 9.3: Organizational Knowledge — "Tri thức tổ chức > tri thức cá nhân"

## 📍 Context — Bạn đang ở đây

> Lesson 9.2 xây Knowledge Graph — liên kết artifacts. Nhưng Graph chỉ chứa **explicit knowledge** (đã viết ra). Rất nhiều critical knowledge = **tribal knowledge** — chỉ ở trong đầu vài người. "Hỏi anh Minh, anh ấy biết." Nếu anh Minh nghỉ → knowledge mất. Module này codify tribal knowledge thành Knowledge Objects.

## 🔥 Tension — "Kiến thức nằm trong đầu 1 người"

Sprint 14. Minh nghỉ phép 2 tuần:

> **Dev B:** *"Allocation rule cho resource loại Contract khác gì FTE? Tôi nhớ có exception nhưng không nhớ chi tiết."*

> **Hà:** *"Hỏi anh Minh... nhưng anh ấy đang nghỉ phép."*

> **Dev B:** *"Thế rule này ở đâu? Tôi search Confluence — không có. Search Slack — 6 tháng trước Minh nói trong thread dài 50 messages. Tôi không biết đâu là final decision."*

> **PM Loan:** *"Tuần trước meeting review allocation policy — tôi nhớ có quyết định 'Contract resource tính theo hours, không %'. Nhưng meeting notes ai ghi?"*

**Tuấn:**
> *"Tribal knowledge = rủi ro. Key person nghỉ, chuyển team, nghỉ việc = knowledge mất. **Rule: Critical knowledge phải codified** — viết thành Knowledge Object (Glossary, ADR, Policy, Meeting Record). Nếu knowledge chỉ ở Slack/meeting/đầu ai đó → nó CHƯA tồn tại trong hệ thống."*

> 💭 **Câu hỏi:** "Hỏi anh X" là câu nguy hiểm nhất trong software development. Anh X = single point of failure. Knowledge codification = giảm bus factor.

## 🎓 Explanation — Knowledge Objects

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Knowledge Object = codified, structured, linked artifact. Tribal Knowledge = risk (key person leaves = knowledge lost). Every critical decision MUST become a Knowledge Object. MR (Meeting Record) = Decisions + Actions only (NOT transcript). Policy ≠ ADR: ADR = 1 technical decision with alternatives, Policy = ongoing business rule."*

### Knowledge Object Types

| Type | Purpose | Template | Ví dụ ITO | When Created |
|---|---|---|---|---|
| **Glossary** | Define domain term | Term + Rules + Related | `glossary/allocation.md` | Discovery phase |
| **ADR** | Record 1 technical decision | Context + Decision + Consequences | `decisions/adr-001.md` | Architecture decision |
| **Meeting Record** | Capture meeting OUTPUT | Decisions + Actions + References | `meetings/2026-06-28.md` | After meeting |
| **Policy** | Ongoing business rule | Scope + Statements + Exceptions | `policies/allocation-policy.md` | Rule formalized |
| **Onboarding Guide** | New member ramp-up | Steps + Prerequisites + Links | `onboarding/developer-guide.md` | First new hire |
| **Event Storm** | Process model | Timeline + Events + Commands | `event-storms/resource-mgmt.md` | Domain exploration |

### Meeting Record (MR) — Template

```markdown
# MR-2026-06-28: Resource Allocation Policy Review

## Attendees
- PM Loan, RM Lead Hà, VP Engineering, Tuấn (DDD Lead)

## Context
Review allocation policy sau 3 tháng triển khai.
Trigger: Dev team confused về Contract resource rules.

## Decisions Made
1. ✅ GIỮ % allocation cho FTE (confirmed ADR-001)
2. ✅ THÊM "soft allocation" — tentative 30 ngày trước confirm
3. ✅ Contract resource: tính theo hours (KHÔNG dùng %)
4. ❌ REJECT: auto-allocate bằng AI (chưa đủ data, review Q4)

## Action Items
- [ ] @Hà: Update glossary/allocation.md — thêm "soft allocation" + "contract hours"
- [ ] @Tuấn: Create ADR-003 cho soft allocation feature
- [ ] @PM Loan: Test soft allocation workflow trong Sprint 15
- [ ] @Dev B: Update AllocationPercentage VO → support hours variant

## Related
- [ADR-001: Allocation %](../decisions/adr-001.md)
- [Glossary: Allocation](../glossary/allocation.md)
- [POL-001: Allocation Policy](../policies/allocation-policy.md)

## Follow-up
Next review: 2026-09-28 (quarterly)
```

**Key Rule:** MR ghi **DECISIONS + ACTIONS** — KHÔNG ghi "ai nói gì". 2-hour meeting → MR chỉ 1 trang. Output matters, not process.

### Policy Document — Template

```markdown
# POL-001: Resource Allocation Policy

## Effective Date: 2026-07-01
## Last Reviewed: 2026-06-28 (MR-2026-06-28)

## Scope
Áp dụng cho tất cả Resource Managers, Project Managers, Delivery Leads.

## Policy Statements
1. FTE resource: allocation tính bằng % (refer ADR-001)
2. Tổng allocation KHÔNG vượt 100% per FTE resource
3. Contract resource: tính theo hours/week (KHÔNG dùng %)
4. Soft allocation tồn tại tối đa 30 ngày — sau đó confirm hoặc expire
5. Resource bench > 10 business days → escalate to VP

## Exceptions
- Intern: có thể allocate > 100% (training overlap cho phép)
- On-call rotation: 10% reserved, không tính vào project allocation

## Violations
- Allocate > 100% FTE → blocked bởi system (Aggregate invariant)
- Skip soft allocation step → escalate to RM Lead

## Related
- [ADR-001: Allocation %](../decisions/adr-001.md)
- [ADR-003: Soft Allocation](../decisions/adr-003.md)
- [MR-2026-06-28](../meetings/2026-06-28.md)
- [Glossary: Allocation](../glossary/allocation.md)
```

### ITO Knowledge Structure — Docs-as-Code

```
docs/
├── domain/                    ← Domain Knowledge (M2-M4)
│   ├── glossary/
│   │   ├── resource.md
│   │   ├── allocation.md
│   │   └── opportunity.md
│   ├── domain-map.md
│   ├── context-map.md         ← (M5)
│   └── event-storms/
│       ├── resource-mgmt.md
│       └── opportunity-lifecycle.md
├── decisions/                 ← Design Decisions (M6)
│   ├── adr-001-allocation-percentage.md
│   ├── adr-002-event-sourcing-opportunity.md
│   └── adr-003-soft-allocation.md
├── ea/                        ← Enterprise Architecture (M9.1)
│   └── capability-map.md
├── policies/                  ← Policy Knowledge (new)
│   ├── pol-001-allocation-policy.md
│   └── pol-002-staffing-sla.md
├── meetings/                  ← Meeting Decisions (new)
│   ├── 2026-06-28-allocation-review.md
│   └── 2026-07-15-q3-planning.md
└── onboarding/                ← Onboarding (new)
    ├── README.md              ← Start here
    ├── developer-guide.md     ← Setup + codebase tour
    ├── domain-overview.md     ← ITO business in 10 minutes
    └── architecture-guide.md  ← Technical architecture
```

### Logistics Knowledge Structure

```
docs/
├── domain/glossary/           ← Shipment, Route, POD, Driver
├── decisions/                 ← ADR: Route Algorithm, Vehicle Tracking Tech
├── policies/                  ← POL: Safety, Compliance, SLA, Driver Hours
├── meetings/                  ← Weekly ops review, monthly safety review
└── onboarding/                ← Driver onboarding, dispatcher guide
```

### ADR vs MR vs Policy — Khi nào dùng gì?

| Câu hỏi | Artifact |
|---|---|
| "Tại sao dùng % thay vì Man-Day?" | **ADR** — 1 quyết định kỹ thuật + alternatives considered |
| "Meeting quyết định gì?" | **MR** — decisions + action items from meeting |
| "Rule allocation đang là gì?" | **Policy** — ongoing business rules + exceptions |
| "Allocation nghĩa là gì?" | **Glossary** — domain term definition |
| "Dev mới vào cần làm gì?" | **Onboarding Guide** — step-by-step ramp-up |

---

## 🏋️ Exercise — Organizational Knowledge

### Phần A: Meeting Record (10 phút)

Viết MR cho 1 ITO meeting:
- Topic: quyết định integration strategy cho SAP (chọn ACL vs Conformist)
- Include: Decisions Made + Action Items + Related artifacts

### Phần B: Policy Document (10 phút)

Viết Policy cho ITO hoặc Logistics:
- Topic gợi ý: "Staffing SLA Policy" hoặc "Driver Safety Policy"
- Include: Policy Statements + Exceptions + Related

### Phần C: Onboarding Guide outline (5 phút)

Viết outline "Developer Onboarding Guide" cho ITO:
```
Day 1: _______________
Day 2: _______________
Day 3: _______________
Week 1: _______________
Week 2: _______________
```

---

## 🪞 Reflect

1. **Meeting Record cần chi tiết như Meeting Minutes?** → **KHÔNG!** MR ghi **DECISIONS + ACTIONS** — output. Meeting Minutes ghi "ai nói gì" — process. Output > process. 2-hour meeting → MR 1 trang. **If it's not a Decision or Action, don't write it.**

2. **Policy Document vs ADR?** → **ADR** = 1 quyết định kỹ thuật cụ thể, có alternatives + consequences, status (Accepted/Superseded). **Policy** = quy tắc business áp dụng ongoing, có scope + exceptions, reviewed quarterly. ADR-001 ("dùng %") → Policy POL-001 ("allocation rules").

3. **Tribal knowledge có luôn xấu?** → Không — team cần informal knowledge sharing. Nhưng **CRITICAL knowledge** (business rules, architecture decisions, domain definitions) PHẢI codified. **Rule of thumb:** nếu chỉ 1 người biết → phải codify ngay. Bus factor = 1 = danger.

---

## ✅ Completion Checklist
- [ ] **Recall:** Phân biệt 5 Knowledge Object types (Glossary, ADR, MR, Policy, Onboarding)
- [ ] **Apply:** Viết 1 MR + 1 Policy Document — đúng template, có Related links
- [ ] **Analyze:** Outline Onboarding Guide — trace qua các Knowledge Objects đã tạo

---

> 🔗 **Tiếp theo:** Module 9 hoàn tất Enterprise Integration — EA mapping, Knowledge Graph, Organizational Knowledge. Module 10 — *AI-Ready Documentation* — sẽ mang DDD vào thời đại AI: làm sao viết docs để AI Agent có thể consume, generate code, và maintain knowledge tự động?
