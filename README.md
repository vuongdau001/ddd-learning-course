# DDD Learning System — Tài liệu tổng quan

> **Mục đích:** Hệ thống học Domain-Driven Design có AI hỗ trợ, sử dụng 2 case study song song (ITO CRM + Logistics).
> **Người học:** Frankie — background kỹ thuật tốt, mới học DDD.
> **Ngày tạo:** 25/06/2026
> **Trạng thái:** Stage 1 hoàn thành (6 modules, 19 lessons, 50 câu quiz, 3 AI skills)

---

## 1. Tổng quan dự án

### 1.1 Mục tiêu
Xây dựng hệ thống học DDD **interactive** với AI đóng vai giảng viên, thay vì tài liệu tĩnh tự đọc. Người học tương tác bằng ngôn ngữ tự nhiên (tiếng Việt), AI dạy theo phương pháp sư phạm CEER.

### 1.2 Phương pháp sư phạm: CEER
Mỗi lesson tuân theo 4 bước:

| Bước | Ý nghĩa | Hoạt động |
|---|---|---|
| **C**oncept | Giảng viên giải thích | Storytelling + Socratic questioning |
| **E**xample | Ví dụ thực tế | ITO CRM (chính) + Logistics (phụ) |
| **E**xercise | Thực hành | Bài tập có hướng dẫn, self-check |
| **R**eflect | Phản tư | 2-3 câu hỏi "tại sao" và "nếu...thì sao" |

### 1.3 Dual Case Study
Mọi lesson đều áp dụng lý thuyết vào 2 ngành khác nhau để đảm bảo kiến thức transferable:

| Case Study | Ngành | Vai trò |
|---|---|---|
| **ITO CRM** | IT Outsourcing (quản lý resource, sales, delivery) | Chính — ví dụ chi tiết |
| **Logistics** | Vận tải / kho vận (fleet, route, shipment) | Phụ — kiểm tra transferability |

---

## 2. Cấu trúc thư mục

```
DDD/
├── README.md                          ← FILE NÀY — tài liệu tổng quan
│
├── .agent/
│   ├── AGENT.md                       ← Master agent persona
│   ├── rules/
│   │   └── rulePreFlight.md           ← Pre-flight checklists
│   └── skills/                        ← 3 AI SKILLS
│       ├── ddd-instructor/            
│       │   └── SKILL.md               ← Dạy, giải thích, hướng dẫn bài tập
│       ├── ddd-assessor/              
│       │   └── SKILL.md               ← Quiz, review bài tập, chấm điểm
│       └── ddd-tracker/               
│           └── SKILL.md               ← Tracking tiến độ, spaced repetition
│
├── curriculum/                        ← NỘI DUNG DẠY (32 files)
│   ├── README.md                      ← Index chương trình học
│   ├── module-1-domain-discovery/     ← 4 lessons + assessment
│   ├── module-2-ubiquitous-language/  ← 3 lessons + assessment
│   ├── module-3-strategic-design/     ← 3 lessons + assessment
│   ├── module-4-event-storming/       ← 3 lessons + assessment
│   ├── module-5-bounded-context/      ← 3 lessons + assessment
│   └── module-6-knowledge-architecture/ ← 3 lessons + assessment
│
├── practice/                          ← BÀI TẬP + OUTPUT CỦA NGƯỜI HỌC
│   ├── progress.md                    ← TRACKING tiến độ (source of truth)
│   ├── domain-map.md                  ← Output Module 1
│   └── glossary/                      ← Output Module 2 (8 entries)
│       ├── resource.md
│       ├── allocation.md
│       ├── utilization.md
│       ├── bench.md
│       ├── opportunity.md
│       ├── lead.md
│       ├── account.md
│       └── project.md
│
└── learning-roadmap/                  ← ARCHIVE — tài liệu gốc (giữ tham khảo)
```

---

## 3. Chương trình học — 6 Modules

### Flow học

```
M1 Domain Discovery ──→ M2 Ubiquitous Language
        ↓                        ↓
M3 Strategic Design    (Gate: biết Core Domain)
        ↓
M4 Event Storming ────→ M5 Bounded Context
                               ↓
                 M6 Knowledge Architecture
```

### Chi tiết từng Module

#### Module 1: Domain Discovery (4 lessons)
> **Mục tiêu:** Tìm và tách domain từ business, không từ hệ thống.

| Lesson | Nội dung chính | Kỹ năng |
|---|---|---|
| 1.1 Domain Thinking | Domain ≠ System Component, 3 tiêu chí kiểm tra | Tư duy domain-first |
| 1.2 Value Chain | Đi theo dòng tiền, vẽ value chain 10+ bước | Phân tích chuỗi giá trị |
| 1.3 Discovery Techniques | 4 kỹ thuật: Value Chain, Org Analysis, System Inventory, Pain Point | Kỹ năng discovery |
| 1.4 Capability Decomposition | Tách domain → capabilities, chọn MVP | Phân rã năng lực |
| **Assessment** | 10 câu (3 Recall, 4 Apply, 3 Analyze) | |
| **Deliverable** | `practice/domain-map.md` | |

#### Module 2: Ubiquitous Language (3 lessons)
> **Mục tiêu:** Phát hiện xung đột thuật ngữ, viết Glossary chuẩn DDD.

| Lesson | Nội dung chính | Kỹ năng |
|---|---|---|
| 2.1 Why Language Matters | 3 loại xung đột (Synonym, Homonym, Missing) | Nhận diện vấn đề |
| 2.2 Conflict Detection | Conflict Detection Matrix — quét hệ thống | Phát hiện xung đột |
| 2.3 Glossary Writing | Template 8 phần, quy trình viết | Viết glossary chuẩn |
| **Assessment** | 8 câu | |
| **Deliverable** | `practice/glossary/` (≥6 entries) | |

#### Module 3: Strategic Design (3 lessons)
> **Mục tiêu:** Phân loại domain, ưu tiên đầu tư, quyết định Build vs Buy.

| Lesson | Nội dung chính | Kỹ năng |
|---|---|---|
| 3.1 Core/Supporting/Generic | Copy Test, Competitive Test | Phân loại chiến lược |
| 3.2 Priority Matrix | 4 tiêu chí scoring, Migration Roadmap | Quyết định ưu tiên |
| 3.3 Build vs Buy vs AI | 5-Question Framework, AI-Augment | Quyết định đầu tư |
| **Assessment** | 8 câu | |
| **Deliverable** | `practice/domain-priority.md` | |

#### Module 4: Event Storming (3 lessons)
> **Mục tiêu:** Mô hình hóa quy trình bằng Events, Commands, Policies.

| Lesson | Nội dung chính | Kỹ năng |
|---|---|---|
| 4.1 Events and Commands | 5 building blocks, naming rules, 3 approaches | Event modeling |
| 4.2 Policies and Hot Spots | Automation rules, 3 loại Hot Spot | Phát hiện nghiệp vụ ẩn |
| 4.3 Guided Workshop | Full workshop cho Resource Management | Workshop facilitation |
| **Assessment** | 8 câu | |
| **Deliverable** | `practice/event-storm/` | |

#### Module 5: Bounded Context (3 lessons)
> **Mục tiêu:** Vẽ ranh giới context, thiết kế integration.

| Lesson | Nội dung chính | Kỹ năng |
|---|---|---|
| 5.1 Subdomain vs Context | Problem Space vs Solution Space, 3 mapping types | Phân biệt khái niệm |
| 5.2 Context Mapping | Upstream/Downstream, 7 integration patterns | Vẽ Context Map |
| 5.3 Integration Patterns | ACL, Shared Kernel, OHS deep-dive | Thiết kế integration |
| **Assessment** | 8 câu | |
| **Deliverable** | `practice/context-map.md` | |

#### Module 6: Knowledge Architecture (3 lessons)
> **Mục tiêu:** Tổ chức knowledge trong repo cho con người và AI đều đọc được.

| Lesson | Nội dung chính | Kỹ năng |
|---|---|---|
| 6.1 Repository Structure | Docs-as-Code, Atomic Files, Cross-linking | Thiết kế repo |
| 6.2 ADR and Traceability | Architecture Decision Records template | Ghi nhận quyết định |
| 6.3 AI Context Design | 3 AI-readability levels, Agent config | AI-native docs |
| **Assessment** | 8 câu (gồm Capstone liên kết 6 modules) | |
| **Deliverable** | `practice/knowledge-repo/` | |

---

## 4. AI Skills — Cách hoạt động

### 4.1 Tổng quan 3 Skills

| Skill | File | Vai trò |
|---|---|---|
| **Instructor** | `.agent/skills/ddd-instructor/SKILL.md` | Dạy lesson, giải thích concept, hướng dẫn exercise |
| **Assessor** | `.agent/skills/ddd-assessor/SKILL.md` | Chấm quiz, review deliverable, cho feedback |
| **Tracker** | `.agent/skills/ddd-tracker/SKILL.md` | Báo cáo tiến độ, đề xuất ôn tập, phân tích kết quả |

### 4.2 Instructor — Chi tiết

**Persona:** Giảng viên DDD senior 10+ năm, consulting tại VN. Giọng thân thiện, hay dùng Socratic questioning, storytelling, không bao giờ nói "sai" mà dẫn dắt người học tự nhận ra.

**4 Workflows:**

| Command người học | Workflow | Mô tả |
|---|---|---|
| "Dạy module X lesson Y" | `teach` | Đọc progress → đọc lesson → dạy Concept → hướng dẫn Exercise → hỏi Reflect → update progress |
| "Giải thích [concept]" | `explain` | Tìm trong curriculum + glossary → giải thích 4 tầng (Analogy → ITO → Logistics → Formal) |
| "Review bài tập" | `review` | Đọc practice output → đánh giá theo criteria → feedback (✅/🔧/❌) |
| "Quiz module X" | `quiz` | Tạo artifact quiz → chấm khi có comment trả lời → ghi score |

**Quy tắc cốt lõi:** KHÔNG cho đáp án trước khi người học thử. KHÔNG đọc nguyên văn lesson file — diễn đạt lại bằng giọng riêng.

### 4.3 Assessor — Chi tiết

**Persona:** Công bằng, khuyến khích, feedback actionable.

**Rubric chấm quiz:**
| Score | Hành động |
|---|---|
| ≥90% | Chuyển module tiếp |
| 70-89% | Đạt, ghi note ôn lại |
| 50-69% | Ôn lại 2-3 lessons → re-quiz |
| <50% | Học lại module |

**Rubric review deliverable (thang 10):**
| Score | Đánh giá |
|---|---|
| 8-10 | Dùng được trong dự án thực |
| 6-7 | Hiểu concept nhưng thiếu detail |
| 4-5 | Có lỗ hổng concept |
| 1-3 | Cần học lại |

**4 chiều đánh giá:** Completeness, Accuracy, Depth, Transferability.

### 4.4 Tracker — Chi tiết

**3 Workflows:**

| Command | Output |
|---|---|
| "Xem tiến độ" | Báo cáo tổng quan: modules/lessons/quizzes completed, điểm mạnh/yếu, next step |
| "Nên ôn gì?" | Phân tích spaced repetition: module nào học lâu, score thấp → kế hoạch ôn |
| "Phân tích kết quả" | Learning analytics: knowledge profile, skill gaps, action items |

---

## 5. Cách sử dụng

### 5.1 Bắt đầu học
```
Người học nói: "Dạy module 1 lesson 1"
AI (Instructor): 
  1. Đọc progress.md → biết chưa học gì
  2. Đọc lesson-1-domain-thinking.md
  3. Dạy bằng giọng giảng viên, storytelling
  4. Hỏi kiểm tra nhanh sau concept
  5. Hướng dẫn exercise step-by-step
  6. Hỏi reflection
  7. Cập nhật progress.md
```

### 5.2 Làm quiz
```
Người học nói: "Quiz module 1"
AI (Assessor):
  1. Tạo artifact từ assessment.md
  2. Người học comment trả lời trên artifact
  3. AI chấm từng câu, cho feedback chi tiết
  4. Ghi score vào progress.md
```

### 5.3 Xem tiến độ
```
Người học nói: "Xem tiến độ" 
AI (Tracker):
  1. Đọc progress.md
  2. Tổng hợp: X/6 modules, Y/19 lessons, Z/6 quizzes
  3. Highlight điểm mạnh + cần cải thiện
  4. Đề xuất next step
```

### 5.4 Các lệnh khác
| Lệnh | Skill | Hành động |
|---|---|---|
| "Học tiếp" | Instructor | Dạy lesson tiếp theo theo progress |
| "Giải thích Bounded Context" | Instructor | Deep-dive 4 tầng |
| "Review bài tập" | Assessor | Đánh giá practice output |
| "Nên ôn gì?" | Tracker | Đề xuất ôn tập (spaced repetition) |
| "Phân tích kết quả" | Tracker | Learning analytics |

---

## 6. Tiến độ hiện tại

### Content đã tạo
| Hạng mục | Số lượng | Trạng thái |
|---|:---:|:---:|
| Modules | 6 | ✅ Đầy đủ |
| Lessons | 19 | ✅ Đầy đủ |
| Assessments | 6 (50 câu) | ✅ Đầy đủ |
| AI Skills | 3 | ✅ Đầy đủ |
| Glossary entries | 8 | ✅ Đầy đủ |
| Domain Map | 1 | ✅ Đầy đủ |
| Progress tracker | 1 | ✅ Đầy đủ |

### Người học chưa bắt đầu
Tất cả content (curriculum + skills) đã sẵn sàng. Người học chưa bắt đầu học chính thức (chưa làm quiz nào). Xem chi tiết tại `practice/progress.md`.

### Practice outputs còn thiếu (by design)
Các file sau sẽ được tạo khi người học hoàn thành exercise tương ứng:
- `practice/domain-priority.md` (Module 3)
- `practice/event-storm/` (Module 4)
- `practice/context-map.md` (Module 5)
- `practice/knowledge-repo/` (Module 6)

---

## 7. Quy ước và Design Decisions

### 7.1 Ngôn ngữ
- Nội dung: **Tiếng Việt**
- Thuật ngữ DDD: **Giữ nguyên tiếng Anh** (Domain, Bounded Context, Event Storming, ACL...)
- Code/technical terms: **Tiếng Anh**

### 7.2 File format
- Tất cả content là **Markdown (.md)**
- Lessons có **YAML frontmatter** (type, module, lesson, title, duration, prerequisites)
- Atomic: **1 file = 1 concept** (glossary), **1 file = 1 lesson**

### 7.3 Assessment design
- 3 cấp độ: 🟢 Recall (nhớ) → 🟡 Apply (áp dụng) → 🔴 Analyze (phân tích)
- Passing score: ≥70%
- Câu hỏi Analyze không có đáp án duy nhất — đánh giá tư duy

### 7.4 Progress tracking
- `practice/progress.md` là **source of truth**
- AI phải đọc TRƯỚC khi dạy và cập nhật SAU khi dạy xong
- Mọi quiz score, deliverable review đều ghi vào đây

---

## 8. Glossary — 8 thuật ngữ ITO đã định nghĩa

| Term | Bounded Context | Xung đột | File |
|---|---|:---:|---|
| Resource | Resource Management | 🔴 Cao (5 cách hiểu) | `practice/glossary/resource.md` |
| Allocation | Resource Management | 🔴 Cao | `practice/glossary/allocation.md` |
| Utilization | Resource Management | 🔴 Cao | `practice/glossary/utilization.md` |
| Bench | Resource Management | 🟠 Trung bình | `practice/glossary/bench.md` |
| Opportunity | Sales | 🟠 Trung bình | `practice/glossary/opportunity.md` |
| Lead | Sales | 🟡 Thấp | `practice/glossary/lead.md` |
| Account | Sales / Finance / Contract | 🔴 Cao (4 contexts) | `practice/glossary/account.md` |
| Project | Multi-context | 🟠 Trung bình | `practice/glossary/project.md` |

### Câu hỏi mở (chưa chốt)
- [ ] Freelancer/Vendor có phải Resource không?
- [ ] Allocation đo bằng % hay Man-Day? (Đề xuất: %)
- [ ] Presales effort tính billable hay non-billable?
- [ ] Win Probability: AI tự tính hay Sales manual?
- [ ] Stalled bao lâu thì tự chuyển Lost?

---

## 9. Lộ trình phát triển

### Stage 1: DDD Foundation ✅ (Đã hoàn thành)
6 modules, từ Domain Discovery → Knowledge Architecture

### Stage 2: Knowledge Engineering (Tương lai)
- Tactical Design (Entity, Value Object, Aggregate)
- CQRS & Event Sourcing
- Domain Service & Application Service

### Stage 3: AI-Native Development (Tương lai)
- AI-driven code generation từ DDD artifacts
- Automated testing từ business rules
- Continuous knowledge refinement

---

## 10. Danh sách file đầy đủ (32 curriculum + 3 skills + 10 practice)

### Skills (3 files)
```text
.agent/AGENT.md                         — Master agent persona
.agent/rules/rulePreFlight.md           — Pre-flight checklist
.agent/skills/ddd-instructor/SKILL.md   — Persona giảng viên, 4 workflows
.agent/skills/ddd-assessor/SKILL.md     — Chấm quiz + review, rubric
.agent/skills/ddd-tracker/SKILL.md      — Progress report, spaced repetition, analytics
```

### Curriculum (32 files)
```
curriculum/README.md

curriculum/module-1-domain-discovery/
  overview.md | lesson-1-domain-thinking.md | lesson-2-value-chain.md
  lesson-3-discovery-techniques.md | lesson-4-capability-decomposition.md | assessment.md

curriculum/module-2-ubiquitous-language/
  overview.md | lesson-1-why-language-matters.md | lesson-2-conflict-detection.md
  lesson-3-glossary-writing.md | assessment.md

curriculum/module-3-strategic-design/
  overview.md | lesson-1-core-supporting-generic.md | lesson-2-priority-matrix.md
  lesson-3-build-vs-buy.md | assessment.md

curriculum/module-4-event-storming/
  overview.md | lesson-1-events-and-commands.md | lesson-2-policies-and-hotspots.md
  lesson-3-guided-workshop.md | assessment.md

curriculum/module-5-bounded-context/
  overview.md | lesson-1-subdomain-vs-context.md | lesson-2-context-mapping.md
  lesson-3-integration-patterns.md | assessment.md

curriculum/module-6-knowledge-architecture/
  overview.md | lesson-1-repository-structure.md | lesson-2-adr-and-traceability.md
  lesson-3-ai-context-design.md | assessment.md
```

### Practice (10 files)
```
practice/progress.md          — Tracking tiến độ
practice/domain-map.md        — Domain Map ITO (9 domains)
practice/glossary/README.md
practice/glossary/resource.md | allocation.md | utilization.md | bench.md
practice/glossary/opportunity.md | lead.md | account.md | project.md
```
