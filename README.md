# 📚 DDD Learning Course

> **Khóa học Domain-Driven Design bằng tiếng Việt** — AI-powered, interactive, dùng case study thực tế

[![License: CC BY-SA 4.0](https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-sa/4.0/)
[![Language](https://img.shields.io/badge/Language-Tiếng%20Việt-blue)](.)
[![Modules](https://img.shields.io/badge/Modules-6-green)](./curriculum/)
[![AI Powered](https://img.shields.io/badge/AI-Antigravity%20Agent-purple)](./agent/)

---

## 🤔 Tại sao nên học DDD?

Nếu bạn từng gặp những tình huống này:

- Build xong tính năng nhưng business nói sai yêu cầu
- Mỗi team dùng một từ khác nhau để chỉ cùng 1 thứ
- Thêm 1 tính năng nhỏ phải sửa 5 chỗ khác nhau
- AI agent không hiểu context dù đã cung cấp database schema

→ **DDD là giải pháp.** Đọc thêm: [docs/why-ddd.md](./docs/why-ddd.md)

---

## 🗺️ Tổng quan khóa học

**6 modules** · **19 lessons** · **50 câu quiz** · **AI instructor 24/7**

```
Module 1: Domain Discovery        ← Tìm ra lĩnh vực nghiệp vụ từ business
     ↓
Module 2: Ubiquitous Language     ← Xây ngôn ngữ chung cho cả team
     ↓
Module 3: Strategic Design        ← Xác định đâu là Core Domain
     ↓
Module 4: Event Storming          ← Mô hình hóa quy trình nghiệp vụ
     ↓
Module 5: Bounded Context         ← Thiết kế ranh giới và tích hợp
     ↓
Module 6: Knowledge Architecture  ← Tổ chức tri thức cho cả người lẫn AI
```

**Phương pháp học: CEER**

| Bước | Ý nghĩa | Diễn ra như thế nào |
|---|---|---|
| **C**oncept | AI giảng lý thuyết | Storytelling + Socratic questioning |
| **E**xample | Ví dụ thực tế | Case study ITO CRM + Logistics |
| **E**xercise | Bài tập thực hành | Bạn tự làm, AI review |
| **R**eflect | Phản tư | Câu hỏi "tại sao" và "nếu...thì sao" |

---

## ⚡ Bắt đầu ngay (5 phút setup)

### Bước 1 — Cài Antigravity (AI agent)

Khóa học này dùng **[Antigravity](https://antigravity.dev)** làm AI instructor.  
Cài theo hướng dẫn trên trang chủ, sau đó mở folder này trong Antigravity.

> **Tại sao cần Antigravity?** Vì khóa học được thiết kế để học theo kiểu *hỏi-đáp trực tiếp với AI*, không phải đọc tài liệu tĩnh. AI sẽ dạy, ra bài tập, chấm điểm và track tiến độ cho bạn.

### Bước 2 — Clone repo

```bash
git clone https://github.com/vuongdau001/ddd-learning-course.git
cd ddd-learning-course
```

### Bước 3 — Mở folder trong Antigravity

Mở Antigravity → **Open Folder** → chọn folder `ddd-learning-course`

Agent sẽ tự nhận diện project và sẵn sàng dạy.

### Bước 4 — Bắt đầu học

Gõ vào chat với AI:

```
Dạy module 1 lesson 1
```

**Xong.** AI sẽ dạy bạn từ đó. 🎉

---

## 💬 Các lệnh quan trọng

| Bạn nói với AI | AI làm gì |
|---|---|
| `Dạy module 1 lesson 1` | Bắt đầu dạy lesson cụ thể |
| `Học tiếp` | Tiếp tục lesson tiếp theo |
| `Giải thích Bounded Context` | Deep-dive vào 1 khái niệm |
| `Quiz module 1` | Tạo bài kiểm tra cuối module |
| `Review bài tập` | AI đánh giá output bạn làm trong `practice/` |
| `Xem tiến độ` | Báo cáo: đã học đến đâu, điểm mạnh/yếu |
| `Nên ôn gì?` | Gợi ý ôn tập theo spaced repetition |

---

## 📁 Cấu trúc dự án

```
ddd-learning-course/
│
├── .agent/                          ← Cấu hình AI agent (đừng sửa)
│   ├── AGENT.md                     ← Persona và rules chung
│   ├── rules/rulePreFlight.md       ← Safety checklist
│   └── skills/
│       ├── ddd-instructor/SKILL.md  ← Skill: Dạy + giải thích
│       ├── ddd-assessor/SKILL.md    ← Skill: Chấm điểm + review
│       └── ddd-tracker/SKILL.md    ← Skill: Tracking tiến độ
│
├── curriculum/                      ← Nội dung bài học (đọc tham khảo)
│   ├── README.md                    ← Index chương trình
│   ├── module-1-domain-discovery/
│   │   ├── overview.md
│   │   ├── lesson-1-domain-thinking.md
│   │   ├── lesson-2-value-chain.md
│   │   ├── lesson-3-discovery-techniques.md
│   │   ├── lesson-4-capability-decomposition.md
│   │   └── assessment.md
│   ├── module-2-ubiquitous-language/
│   ├── module-3-strategic-design/
│   ├── module-4-event-storming/
│   ├── module-5-bounded-context/
│   └── module-6-knowledge-architecture/
│
├── practice/                        ← BÀI LÀM CỦA BẠN (bạn sửa ở đây)
│   ├── progress.md                  ← Tiến độ học (AI tự cập nhật)
│   ├── domain-map.md                ← Output Module 1
│   └── glossary/                    ← Output Module 2 (ví dụ mẫu)
│       ├── account.md
│       ├── lead.md
│       ├── opportunity.md
│       └── ...
│
└── docs/
    └── why-ddd.md                   ← Tại sao phải học DDD?
```

---

## 🎯 Lộ trình học chi tiết

### Module 1 — Domain Discovery *(~1 tuần)*

> **Mục tiêu:** Tìm và tách domain từ business, không từ hệ thống.

| Lesson | Bạn sẽ học | Deliverable |
|---|---|---|
| 1.1 Domain Thinking | Domain ≠ System component; 3 tiêu chí kiểm tra | — |
| 1.2 Value Chain | Đi theo dòng tiền, vẽ value chain 10+ bước | — |
| 1.3 Discovery Techniques | 4 kỹ thuật khám phá domain thực tế | — |
| 1.4 Capability Decomposition | Tách domain → capabilities, chọn MVP, EA bridge | `practice/domain-map.md` |
| **Quiz** | 10 câu (Recall + Apply + Analyze) | Score ghi vào progress.md |

**Điều kiện sang Module 2:** Tự vẽ được Value Chain không cần nhìn tham khảo + score quiz ≥70%

---

### Module 2 — Ubiquitous Language *(~1 tuần)*

> **Mục tiêu:** Phát hiện xung đột thuật ngữ, xây ngôn ngữ chung cho cả team.

| Lesson | Bạn sẽ học | Deliverable |
|---|---|---|
| 2.1 Why Language Matters | 3 loại xung đột: Synonym, Homonym, Missing term | — |
| 2.2 Conflict Detection | Conflict Detection Matrix | — |
| 2.3 Glossary Writing | Template 8 phần, quy trình viết chuẩn DDD | `practice/glossary/` ≥6 entries |
| **Quiz** | 8 câu | Score ghi vào progress.md |

---

### Module 3 — Strategic Design *(~1 tuần)*

> **Mục tiêu:** Phân loại domain, ưu tiên đầu tư, quyết định Build vs Buy.

| Lesson | Bạn sẽ học | Deliverable |
|---|---|---|
| 3.1 Core/Supporting/Generic | Copy Test + Competitive Test | — |
| 3.2 Priority Matrix | 4 tiêu chí scoring, Migration Roadmap | — |
| 3.3 Build vs Buy vs AI | 5-Question Framework | `practice/domain-priority.md` |
| **Quiz** | 8 câu | Score ghi vào progress.md |

---

### Module 4 — Event Storming *(~1 tuần)*

> **Mục tiêu:** Mô hình hóa quy trình nghiệp vụ bằng Events, Commands, Policies.

| Lesson | Bạn sẽ học | Deliverable |
|---|---|---|
| 4.1 Events and Commands | 5 building blocks, naming rules | — |
| 4.2 Policies and Hot Spots | Automation rules, phát hiện nghiệp vụ ẩn | — |
| 4.3 Guided Workshop | Full workshop cho Core Domain | `practice/event-storm/` |
| **Quiz** | 8 câu | Score ghi vào progress.md |

---

### Module 5 — Bounded Context *(~1 tuần)*

> **Mục tiêu:** Vẽ ranh giới context, thiết kế integration pattern.

| Lesson | Bạn sẽ học | Deliverable |
|---|---|---|
| 5.1 Subdomain vs Context | Problem Space vs Solution Space | — |
| 5.2 Context Mapping | Upstream/Downstream, 7 integration patterns | — |
| 5.3 Integration Patterns | ACL, Shared Kernel, Open Host Service | `practice/context-map.md` |
| **Quiz** | 8 câu | Score ghi vào progress.md |

---

### Module 6 — Knowledge Architecture *(~1 tuần)*

> **Mục tiêu:** Tổ chức knowledge trong repo cho cả người và AI đọc được.

| Lesson | Bạn sẽ học | Deliverable |
|---|---|---|
| 6.1 Repository Structure | Docs-as-Code, Atomic Files, Knowledge Objects | — |
| 6.2 ADR and Traceability | Architecture Decision Records, Traceability Chain | — |
| 6.3 AI Context Design | 3 AI-readability levels, Agent config, Stage 3 preview | `practice/knowledge-repo/` |
| **Quiz** | 8 câu (Capstone liên kết 6 modules) | Score ghi vào progress.md |

---

## 🤖 AI Skills — Hoạt động như thế nào?

Khóa học có **3 AI skills** tích hợp sẵn, mỗi skill có vai trò riêng:

### 🎓 ddd-instructor — Giảng dạy

Persona: Giảng viên DDD senior 10+ năm.

- **Không** cho đáp án thẳng — dẫn dắt bạn tự tìm ra
- **Không** đọc nguyên văn lesson file — diễn đạt bằng giọng riêng
- Dùng storytelling + Socratic questioning
- Luôn có ví dụ từ ITO CRM và Logistics

### 📋 ddd-assessor — Chấm điểm & Review

Rubric quiz:

| Score | Hành động |
|---|---|
| ≥90% | Chuyển module tiếp |
| 70–89% | Đạt, ghi note ôn lại |
| 50–69% | Ôn 2–3 lessons → re-quiz |
| <50% | Học lại module |

Rubric review deliverable (thang 10):

| Score | Ý nghĩa |
|---|---|
| 8–10 | Dùng được trong dự án thực |
| 6–7 | Hiểu concept, thiếu detail |
| 4–5 | Có lỗ hổng concept cần bổ sung |
| 1–3 | Cần học lại |

### 📊 ddd-tracker — Theo dõi tiến độ

- Báo cáo tổng quan: bao nhiêu module/lesson/quiz hoàn thành
- Phân tích điểm mạnh / điểm yếu
- Đề xuất ôn tập theo **spaced repetition**
- Gợi ý next step phù hợp nhất

---

## ❓ Hỏi đáp thường gặp

**Q: Cần nền tảng gì để học?**  
A: Biết lập trình cơ bản, đã làm việc với software project (dev, BA, hoặc PM kỹ thuật). Không cần biết DDD trước.

**Q: Học bao lâu xong?**  
A: Mỗi module ~1 tuần nếu học đều. Toàn bộ Stage 1 (6 modules) ≈ 6–8 tuần tùy tốc độ.

**Q: Không có Antigravity thì dùng được không?**  
A: Vẫn đọc được tài liệu trong `curriculum/`, nhưng mất đi phần interactive (AI dạy, chấm bài, track progress). Khuyến khích dùng Antigravity để có trải nghiệm đầy đủ.

**Q: Case study ITO CRM là gì?**  
A: Công ty IT Outsourcing (ITO) quản lý Sales, Resource và Project Delivery. Đây là case study xuyên suốt khóa học — đủ phức tạp để áp dụng DDD thực tế, gần gũi với nhiều doanh nghiệp VN.

**Q: Tôi có thể fork và dùng case study riêng không?**  
A: Được! Fork repo, chỉnh `practice/` và khi dạy nói với AI: *"Dùng case study của tôi là [tên doanh nghiệp]"*.

---

## 🗂️ Ví dụ mẫu trong `practice/`

Thư mục `practice/` có sẵn **ví dụ mẫu** từ case study ITO CRM để bạn tham khảo:

- [`practice/domain-map.md`](./practice/domain-map.md) — Domain Map với 9 domains
- [`practice/glossary/`](./practice/glossary/) — 8 entries Ubiquitous Language mẫu (account, lead, resource, v.v.)

> **Lưu ý:** Đây là ví dụ mẫu của người tạo khóa học, không phải đáp án duy nhất. DDD khuyến khích bạn tự khám phá domain theo cách hiểu của mình.

---

## 🚀 Lộ trình phát triển

| Stage | Nội dung | Trạng thái |
|---|---|---|
| **Stage 1** | DDD Foundation (6 modules) | ✅ Hoàn thành |
| **Stage 2** | Knowledge Engineering (Tactical Design, CQRS, Event Sourcing) | 🔜 Sắp ra |
| **Stage 3** | AI-Native Development (Context Engineering, RAG từ DDD artifacts) | 📅 Tương lai |

---

## 📄 License

Nội dung trong repo này được phân phối theo **[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)**.  
Bạn được tự do: sử dụng, chia sẻ, chỉnh sửa — miễn là ghi nguồn và giữ cùng license.

---

*Made with ❤️ for the Vietnamese developer community*
