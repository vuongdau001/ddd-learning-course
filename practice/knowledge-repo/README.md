---
type: practice
module: 6
title: "Knowledge Repository"
case_study: "ITO CRM"
---

# Knowledge Repository — Bài làm Module 6

> Hướng dẫn: Sau Module 6, bạn sẽ thiết kế Knowledge Repository cho 1 Bounded Context — bao gồm folder structure, ADR, và AI Agent config.

---

## Phần 1: Folder Structure Design

Thiết kế folder structure cho 1 Bounded Context (VD: Resource Management):

```
resource-management/
├── docs/
│   ├── domain/
│   │   ├── glossary/
│   │   │   ├── (liệt kê terms)
│   │   │   └── ...
│   │   ├── domain-map.md
│   │   └── business-rules.md
│   ├── decisions/
│   │   ├── (liệt kê ADRs)
│   │   └── ...
│   └── event-storms/
│       └── (liệt kê flows)
├── src/
│   ├── domain/
│   ├── application/
│   ├── infrastructure/
│   └── api/
└── tests/
```

---

## Phần 2: ADR Template (copy từ Lesson 6.2)

```markdown
# ADR-___: _______________

## Status
Proposed / Accepted / Deprecated

## Date
YYYY-MM-DD

## Context
[Tình huống dẫn đến quyết định]

## Decision Drivers
- [Yếu tố 1]
- [Yếu tố 2]

## Options Considered
1. [Option A] — ưu/nhược
2. [Option B] — ưu/nhược

## Decision
Chọn [Option X] vì [lý do].

## Consequences
### Positive
- [Hệ quả tốt]
### Negative
- [Tradeoff chấp nhận]

## References
- [Link đến glossary, event storm]
```

---

## Phần 3: AI Agent Config (copy từ Lesson 6.3)

```yaml
# .ai/agent-config.yaml
agent:
  name: "_______________"
  scope: "_______________"
  
knowledge_sources:
  glossary: "docs/domain/glossary/"
  events: "docs/event-storms/"
  decisions: "docs/decisions/"
  context_map: "docs/domain/context-map.md"

permissions:
  can_read: [___]
  can_suggest: [___]
  cannot: [___]

behaviors:
  on_new_feature_request:
    1: "Check if term exists in glossary"
    2: "Identify which Bounded Context"
    3: "Reference relevant Events"
    4: "Check ADRs for constraints"
    5: "Generate code following Context patterns"
```

---

## Checklist

- [ ] Folder structure có docs/ + src/ + tests/
- [ ] Glossary terms đặt atomic (1 file = 1 term)
- [ ] Có ≥1 ADR hoàn chỉnh
- [ ] AI Agent Config có knowledge_sources + permissions
- [ ] Cross-links giữa glossary ↔ ADR ↔ event storm
