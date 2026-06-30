---
type: lesson
module: 10
lesson: 2
title: "RAG from DDD Artifacts"
duration: "40 phút"
prerequisites: ["module-10/lesson-1"]
narrative_phase: "context engineering"
migration_phase: "Phase 5: Auto-retrieve DDD Knowledge Objects cho AI"
business_invariant: "RAG = Retrieval-Augmented Generation — auto-find relevant Knowledge Objects; DDD-aware RAG > Naive RAG (follow cross-links, not just text match); OKF Atomic Granularity = 1 file = 1 chunk = perfect boundary; Metadata schema = wiring diagram cho Knowledge Graph"
---

# Lesson 10.2: RAG from DDD Artifacts — "Cho AI tự tìm kiến thức phù hợp"

## 📍 Context — Bạn đang ở đây

> Lesson 10.1 thiết kế Context Window 3 layers. Layer 1-2 = static, manual. Nhưng Layer 3 (task context) thay đổi mỗi request — developer phải tự chọn relevant files. Khi knowledge base lớn (>50 files) → không biết file nào liên quan → copy-paste sai hoặc thiếu. **RAG** tự động tìm đúng Knowledge Objects cho mỗi task.

## 🔥 Tension — "Copy-paste context không scale"

Sprint 16. ITO knowledge base đã lớn:

> **Dev B:** *"Tôi cần tạo API cho Opportunity Qualification. Tôi biết cần copy context nhưng... glossary có 15 files, event-storm 5 files, ADR 8 files. File nào liên quan? Tôi copy glossary/opportunity.md, nhưng quên event-storms/sales.md và adr-003.md. AI thiếu context → output sai."*

> **Dev A:** *"Tôi mất 10 phút copy-paste mỗi lần. Lần trước quên ADR → AI generate Man-Day thay vì %. Phải sửa 2 lần."*

**Tuấn:**
> *"Manual context selection = O(n) effort, error-prone. RAG = auto-retrieve: developer hỏi 'Allocation API' → system tìm allocation.md, adr-001.md, event-storms/resource-mgmt.md tự động. Developer không cần biết file nào tồn tại — RAG tìm cho bạn."*

> 💭 **Câu hỏi:** Knowledge base lớn = manual selection không scale. RAG giải quyết bằng cách tự tìm relevant files. Nhưng naive text search vs DDD-aware retrieval cho kết quả rất khác nhau. DDD structure (cross-links, aggregate relationships) = better retrieval.

## 🎓 Explanation — RAG from DDD Artifacts

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"RAG = Retrieval-Augmented Generation (auto-find + inject relevant knowledge). DDD-aware RAG follows cross-links (Knowledge Graph, M9.2), not just text match. OKF Atomic Granularity (M6) = 1 file = 1 concept = 1 chunk = perfect boundary (no mid-concept splits). Metadata schema = wiring diagram giúp RAG traverse graph."*

### RAG Pipeline

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

### Naive RAG vs DDD-aware RAG

```
Query: "Allocation API"

Naive RAG (text similarity only):
  → glossary/allocation.md (text match: "allocation")
  → Done. 1 file.
  → AI thiếu: business rules, events, ADR → output generic

DDD-aware RAG (follow cross-links):
  → glossary/allocation.md      (direct text match)
  → glossary/resource.md        (allocation BELONGS TO Resource aggregate)
  → event-storms/resource-mgmt.md (has ResourceAllocated event)
  → decisions/adr-001.md        (allocation uses %, not Man-Day)
  → context-map.md              (Resource Context integration)

  HOW? Follow `## Related` cross-links (Knowledge Graph from M9.2)
  → AI nhận 5 relevant KOs → generate chính xác

→ DDD-aware RAG = 5x better context than naive RAG
```

### Chunking Strategy — OKF = Perfect Chunks

```
❌ Naive chunking (split by 500 tokens):
  glossary/resource.md bị cắt giữa → AI nhận nửa business rule → hiểu sai
  event-storms/sales.md bị split → mất event sequence continuity

✅ DDD-aware chunking (1 Knowledge Object = 1 chunk):
  Chunk 1: glossary/resource.md    (toàn bộ file = 1 chunk)
  Chunk 2: glossary/allocation.md  (toàn bộ file = 1 chunk)
  Chunk 3: event-storms/resource-mgmt.md (toàn bộ = 1 chunk)
  Chunk 4: decisions/adr-001.md    (toàn bộ = 1 chunk)

  → OKF Atomic Granularity (M6: 1 file = 1 concept) = perfect chunk boundary
  → Đây là LÝ DO Module 6 dạy atomic files!
  → No need for complex splitting logic
```

### Metadata Schema — RAG-ready YAML

```yaml
# glossary/resource.md — YAML frontmatter
---
id: KO-GLOSSARY-001
type: glossary                        # glossary | event-storm | adr | policy
owner: Resource Management            # Bounded Context sở hữu
domain: resource-management           # Subdomain
tags: [core-domain, high-conflict]    # Searchable tags

# Relationships — RAG follows these to expand retrieval
related_events: [ResourceAllocated, ResourceBenched, ResourceFreed]
related_contexts: [resource-management, delivery-management]
related_adrs: [adr-001-use-percentage]
related_apis: [POST /allocations, GET /resources/:id/availability]
related_tests: [test_allocation_validation, test_bench_detection]
---
```

**Why metadata?**
```
Không metadata:
  Query: "allocation rules"
  → glossary/allocation.md (text match only)
  → Thiếu: adr-001, ResourceAllocated event, validation tests

Có metadata:
  Query: "allocation rules"
  → glossary/allocation.md         (text match)
  → Follow related_adrs →          decisions/adr-001.md
  → Follow related_events →        event-storms/resource-mgmt.md
  → Follow related_tests →         test files
  → AI nhận ĐỦ context → output chính xác
```

### ITO CRM — RAG Example

```
Knowledge Base indexed:
  glossary/*.md          → 15 chunks (15 terms)
  event-storms/*.md      → 5 chunks (5 domain flows)
  decisions/*.md         → 8 chunks (8 ADRs)
  context-map.md         → 1 chunk
  domain-map.md          → 1 chunk
  policies/*.md          → 3 chunks

Query: "Implement bench detection logic"
Retrieved (via DDD-aware RAG):
  1. glossary/bench.md           (direct match, score: 0.95)
  2. glossary/allocation.md      (bench DEPENDS ON allocation, score: 0.87)
  3. glossary/utilization.md     (bench = 0% utilization, score: 0.82)
  4. event-storms/resource-mgmt.md (ResourceBenched event, score: 0.78)

→ AI receives 4 relevant KOs → generates accurate bench detection code
```

### Logistics — RAG Example

```
Query: "Optimize delivery route for Hanoi district"
Retrieved:
  1. glossary/route.md               (direct match)
  2. glossary/time-window.md         (delivery time constraints)
  3. decisions/adr-l02-route-algo.md (Route uses Hybrid approach)
  4. event-storms/transportation.md  (RoutePlanned, RouteOptimized events)
```

### ⚖️ Trade-offs — Manual vs RAG

| | Manual Context (M10.1) | RAG (M10.2) |
|---|---|---|
| **KB size** | < 30 files | > 30 files |
| **Team** | 2-3 devs (know all files) | > 10 devs |
| **Effort** | 5-10 min copy-paste | Automated |
| **Accuracy** | High (if dev knows KB) | High (if metadata + cross-links good) |
| **Risk** | Forget critical files | Retrieve irrelevant files (noise) |

---

## 🏋️ Exercise — Thiết kế RAG Pipeline

### Phần A: Chunking Design (10 phút)

| File | Chunk size | Metadata tags |
|---|---|---|
| glossary/resource.md | 1 file = 1 chunk | domain:resource-mgmt, type:glossary |
| | | |
| | | |
| | | |

### Phần B: Retrieval Simulation (15 phút)

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

1. **OKF Atomic Granularity (M6) giúp RAG thế nào?** → 1 file = 1 concept = **perfect chunk boundary**. Không cần split (mất context) hay merge (mixed concepts). M6 design decision pays off in M10 AI consumption.

2. **RAG sai khi nào?** → **Outdated docs** (glossary chưa update → AI dùng old rules), **ambiguous terms** (cùng tên khác nghĩa → retrieve sai file), **missing metadata** (no cross-links → miss related KOs). **Fix:** keep docs current + comprehensive metadata.

3. **Small team cần RAG?** → Team 2-3 devs, KB < 30 files → manual đủ. Team > 10, KB > 50 files → RAG justifiable. **Start simple, add RAG when manual becomes bottleneck.**

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích RAG pipeline + DDD-aware vs Naive RAG
- [ ] **Apply:** Thiết kế chunking strategy + simulate retrieval cho 3 queries
- [ ] **Analyze:** Giải thích tại sao OKF Atomic Granularity = good chunking boundary

---

> 🔗 **Tiếp theo:** Context Window (M10.1) + RAG (M10.2) = AI có đúng knowledge. Nhưng developer vẫn phải viết prompt mỗi lần — ad-hoc, inconsistent. **Structured Prompts** sẽ trả lời: làm sao tạo reusable prompt templates kết hợp DDD context, để mọi task type đều có consistent, high-quality AI output?
