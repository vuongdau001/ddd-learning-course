---
type: lesson
module: 10
lesson: 2
title: "RAG from DDD Artifacts"
duration: "40 phút"
prerequisites: ["module-10/lesson-1"]
---

# Lesson 10.2: RAG from DDD Artifacts

## 🎓 Concept — "Cho AI tự tìm kiến thức phù hợp"

### Vấn đề: Manual context selection không scale

```
Developer request: "Tạo API cho Opportunity Qualification"

Manual approach:
  1. Dev tự copy glossary/opportunity.md vào prompt
  2. Dev tự copy event-storm/sales.md vào prompt
  3. Dev tự copy adr-003.md vào prompt
  → Mất 10 phút copy-paste
  → Có thể quên file quan trọng
  → Khi knowledge base lớn → không biết file nào liên quan
```

### RAG = Retrieval-Augmented Generation

```
User: "Tạo API cho Opportunity Qualification"
     ↓
Retriever: Tìm trong Knowledge Base
     ↓
Found relevant:
  - glossary/opportunity.md (score: 0.95)
  - glossary/lead.md (score: 0.82)
  - event-storms/sales.md (score: 0.88)
  - decisions/adr-003.md (score: 0.75)
     ↓
Augmented Prompt:
  System: [Project Context]
  Retrieved: [opportunity.md + lead.md + sales-events.md + adr-003.md]
  User: "Tạo API cho Opportunity Qualification"
     ↓
AI: Generates API with correct business rules, terminology, events
```

### RAG Pipeline cho DDD

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Knowledge    │     │ Embedding    │     │ Vector       │
│ Objects      │────▶│ Model        │────▶│ Database     │
│ (Markdown)   │     │ (text→vector)│     │ (search)     │
└──────────────┘     └──────────────┘     └──────────────┘
                                                │
┌──────────────┐     ┌──────────────┐           │
│ User Query   │────▶│ Query        │───────────┘
│              │     │ Embedding    │     ┌──────────────┐
└──────────────┘     └──────────────┘     │ Top-K        │
                                          │ Results      │
                                          └──────┬───────┘
                                                 │
                                          ┌──────▼───────┐
                                          │ Augmented    │
                                          │ Prompt → LLM │
                                          └──────────────┘
```

### DDD-aware Retrieval — Không chỉ text search

Naive RAG tìm theo text similarity. DDD-aware RAG tìm theo **domain structure:**

```
Query: "Allocation API"

Naive RAG:
  → glossary/allocation.md (text match: "allocation")
  → Done.

DDD-aware RAG:
  → glossary/allocation.md (direct match)
  → glossary/resource.md (allocation BELONGS TO resource aggregate)
  → event-storms/resource-mgmt.md (has ResourceAllocated event)
  → decisions/adr-001.md (allocation uses %)
  → context-map.md (Resource Management context)
  
  → HOW? Follow cross-links (Knowledge Graph from Module 9)
```

### Chunking Strategy cho DDD

```
❌ Naive chunking (split by 500 tokens):
  glossary/resource.md bị cắt giữa chừng
  → AI nhận nửa business rule → hiểu sai

✅ DDD-aware chunking (split by Knowledge Object):
  Chunk 1: glossary/resource.md (toàn bộ file = 1 chunk)
  Chunk 2: glossary/allocation.md (toàn bộ file = 1 chunk)
  Chunk 3: event-storms/resource-mgmt.md (toàn bộ file = 1 chunk)
  
  → Atomic granularity (OKF) = perfect chunking strategy!
  → Đây là lý do Module 6 dạy "1 file = 1 concept"
```

### Ví dụ — ITO CRM RAG

```
Knowledge Base (indexed):
  glossary/*.md          → 8 chunks (8 terms)
  event-storms/*.md      → 3 chunks (3 domain flows)
  decisions/*.md         → 3 chunks (3 ADRs)
  context-map.md         → 1 chunk
  domain-map.md          → 1 chunk
  policies/*.md          → 2 chunks

Query: "Implement bench detection logic"
Retrieved:
  1. glossary/bench.md (direct match)
  2. glossary/allocation.md (bench DEPENDS ON allocation)
  3. glossary/utilization.md (bench = 0% utilization)
  4. event-storms/resource-mgmt.md (ResourceBenched event)
  
→ AI receives 4 relevant Knowledge Objects → generates accurate code
```

### Ví dụ — Logistics RAG

```
Query: "Optimize delivery route for Hanoi district"
Retrieved:
  1. glossary/route.md
  2. glossary/time-window.md (delivery time constraints)
  3. decisions/adr-002.md (Route Optimization uses Hybrid approach)
  4. event-storms/transportation.md (RoutePlanned event)
```

---

## 🏋️ Exercise — Thiết kế RAG Pipeline

### Phần A: Chunking Design (10 phút)

Liệt kê tất cả Knowledge Objects trong ITO repo và thiết kế chunks:

| File | Chunk size | Metadata tags |
|---|---|---|
| glossary/resource.md | 1 file = 1 chunk | domain:resource-mgmt, type:glossary |
| | | |
| | | |
| | | |

### Phần B: Retrieval Simulation (15 phút)

Cho 3 developer queries, dự đoán top-3 retrieved chunks:

**Query 1:** "Create unit test for allocation validation"
- Chunk 1: _______________
- Chunk 2: _______________
- Chunk 3: _______________

**Query 2:** "Design Opportunity stage transition workflow"
- Chunk 1: _______________
- Chunk 2: _______________
- Chunk 3: _______________

**Query 3:** "Why do we use % instead of Man-Day?"
- Chunk 1: _______________
- Chunk 2: _______________
- Chunk 3: _______________

### Phần C: Cross-link retrieval (5 phút)

Query: "Implement ResourceBenched event handler"
- Direct match: _______________
- Cross-link 1: _______________ (vì _____)
- Cross-link 2: _______________ (vì _____)

---

## 🪞 Reflect

1. **OKF Atomic Granularity (1 file = 1 concept) giúp RAG thế nào?** Gợi ý: perfect chunk boundaries — không cần split hay merge.

2. **RAG có thể sai không? Khi nào retrieved context misleading?** Gợi ý: outdated docs, ambiguous terms → RAG trả về file sai.

3. **Small team (2-3 devs) có cần RAG pipeline không?** Gợi ý: chưa — manual context selection đủ. RAG cho team > 10 hoặc knowledge base > 100 files.

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích RAG pipeline bằng diagram
- [ ] Thiết kế chunking strategy cho ITO
- [ ] Simulate retrieval cho 3 queries
- [ ] Hiểu tại sao Atomic Granularity = good chunking
