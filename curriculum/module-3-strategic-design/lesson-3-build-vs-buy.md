---
type: lesson
module: 3
lesson: 3
title: "Build vs Buy vs AI"
duration: "25 phút"
prerequisites: ["module-3/lesson-2"]
narrative_phase: "chiến lược"
migration_phase: "Pre-migration: Quyết định đầu tư"
business_invariant: "Core → Build; Supporting → Buy+Customize; Generic → Buy SaaS; AI-Augment khi Core có pattern recognition"
---

# Lesson 3.3: Build vs Buy vs AI — "Chiến lược đầu tư theo loại domain"

## 📍 Context — Bạn đang ở đây

> Priority Matrix (Lesson 3.2) cho kết quả: Phase 1 = Opportunity Mgmt + Resource Mgmt MVP. Phase 2 = Resource Mgmt full + Delivery. CTO **Minh** hỏi câu cuối cùng trước khi ký duyệt: *"OK, build Opportunity và Resource trước. Nhưng CHO MỖI domain, tôi cần biết: build from scratch, mua tool rồi customize, hay dùng AI? CFO cần ROI estimate cho từng lựa chọn."*
>
> Bạn cần decision framework: **Build vs Buy vs Customize vs AI-Augment** — cho từng domain.

## 🔥 Tension — "Salesforce hay build?"

Cuộc họp với CFO và Hà. Hai luồng ý kiến:

> **CFO:** *"Salesforce = $150/user/tháng × 30 users = $4,500/tháng = ~1.3 tỷ/năm. Đắt, nhưng deploy trong 2 tuần. Build = 5 dev × 4 tháng × 30 triệu/dev = 600 triệu + risk."*
>
> **Hà:** *"Salesforce fit 70% flow của ITO. Nhưng 30% còn lại — đặc biệt Territory Management (tránh 2 người approach cùng khách) và Deal Scoring (predict win rate) — Salesforce KHÔNG CÓ cho ITO model. Chúng ta sẽ phải ép flow vào tool → Sales team sẽ bypass → lại quay về Excel."*
>
> **Tuấn** thêm: *"Resource Matching thì chắc chắn build — không SaaS nào match resource theo skill + availability + cost + client preference kiểu ITO. NHƯNG phần 'tìm người có skill tương tự' — AI có thể giúp. Thay vì hardcode 'Java Senior ≈ Kotlin Senior', để AI tính semantic similarity."*

**Minh** tổng kết:
> *"Hà nói Salesforce thiếu 30% quan trọng nhất. Tuấn nói Resource Matching = build + AI. CFO cần con số. Cho tôi framework quyết định — không dựa vào cảm tính."*

> 💭 **Câu hỏi:** Salesforce 70% fit — đủ hay không đủ? 30% thiếu = 30% ít hay 30% quan trọng nhất? Làm sao biết?

## 🎓 Explanation — Decision Framework

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Core Domain → Build in-house (lợi thế cạnh tranh, không outsource). Supporting → Buy + Customize (cần nhưng standard). Generic → Buy SaaS (giống mọi nơi). AI-Augment khi Core có pattern recognition mà rules-based không cover."*

### Ma trận quyết định cơ bản

| Loại Domain | Chiến lược | Tại sao | Ví dụ ITO |
|---|---|---|---|
| **🔴 Core** | **Build** in-house | Lợi thế cạnh tranh → không thể outsource logic | Resource Matching Engine |
| **🟡 Supporting** | **Buy + Customize** | Cần nhưng standard → dùng tool + adapt | Jira cho Delivery, PandaDoc cho Contract |
| **⚪ Generic** | **Buy** SaaS/Open-source | Giống mọi nơi → không build | Auth0, SendGrid, S3 |

### 2026 Update: AI thay đổi luật chơi

Từ 2024+, AI tạo ra lựa chọn thứ 4 — **AI-Augmented:**

| Chiến lược | Nghĩa | Khi nào dùng |
|---|---|---|
| **Build** | Team viết toàn bộ code | Core Domain, business rules phải 100% deterministic |
| **Buy** | Mua SaaS/license | Generic, không customize |
| **Customize** | Mua + sửa thêm | Supporting, cần adapt vào workflow cụ thể |
| **AI-Augment** | Build core rules + AI xử lý phần phức tạp | Core Domain có pattern recognition mà rules-based không cover |

### Ví dụ AI-Augment — ITO Resource Matching

```
TRƯỚC (2023): Resource Matching = Build toàn bộ
  → Rule-based: if skill == "Java" && seniority >= "Senior" → match
  → Mất 3-6 tháng build, coverage 60%
  → Problem: "Java Spring" ≈ "Java EE" ≈ "Kotlin"? Rules không cover

SAU (2026): Resource Matching = Build core + AI-Augment
  → Core (Build): Business rules
    • allocation ≤ 100%
    • bench threshold = 5 ngày
    • seniority match ≥ client requirement
  → AI (Augment): Phần không rule-based
    • Semantic skill matching ("React" ≈ "Next.js" = 85%)
    • Past performance prediction (resource A + client B = 4.2/5 dựa trên history)
  → Mất 2-3 tháng, coverage 85%

KẾT QUẢ: Build core rules (1 tháng) + integrate AI (1 tháng) = faster + better
```

### Ví dụ AI-Augment — Logistics Route Optimization

```
TRƯỚC: Route Optimization = Operations Research (PhD-level math)
  → Linear programming, constraint satisfaction
  → Mất 6-12 tháng, team 3 PhD

SAU: Route Optimization = Build constraints + AI solve
  → Core (Build): Hard constraints
    • Vehicle capacity ≤ max weight
    • Driver hours ≤ 10h/ngày
    • Time windows (pickup 8-10AM, delivery 2-4PM)
  → AI (Augment): Soft optimization
    • Minimize total distance
    • Minimize fuel consumption
    • Predict traffic patterns
  → Mất 3-4 tháng, better solutions
```

### 5-Question Decision Framework

Với mỗi domain, hỏi 5 câu **theo thứ tự:**

```
1. "Nó là Generic?"    → Yes → BUY SaaS (Auth0, SendGrid, S3)
                         No ↓

2. "Nó là Core?"       → No → Supporting → BUY + CUSTOMIZE
                         Yes ↓

3. "Có SaaS nào         → Yes → SaaS đủ 80%+ nhu cầu Core?
    giải quyết 80%?"           → Yes → BUY + CUSTOMIZE (chấp nhận 80%)
                                → No ↓ (Salesforce 70% ≠ đủ)

4. "AI có thể xử lý     → Yes → BUILD core rules + AI-AUGMENT
    phần phức tạp               (hybrid approach)
    không rule-based?"    No ↓

5. BUILD toàn bộ         → BUILD in-house (team giỏi nhất, budget cao nhất)
```

### Apply cho ITO

| Domain | Q1 Generic? | Q2 Core? | Q3 SaaS 80%? | Q4 AI? | Quyết định |
|---|---|---|---|---|---|
| Auth | ✅ | — | — | — | **Buy** (Auth0) |
| Notification | ✅ | — | — | — | **Buy** (SendGrid) |
| Contract Mgmt | — | No (Supporting) | — | — | **Buy+Customize** (PandaDoc) |
| Delivery Mgmt | — | No (Supporting) | — | — | **Buy+Customize** (Jira) |
| Resource Mgmt | — | ✅ Core | No SaaS fit | ✅ AI: semantic matching | **Build + AI-Augment** |
| Opportunity Mgmt | — | ✅ Core | Salesforce 70% (❌ <80%) | No (rules-based đủ) | **Build** in-house |

### ⚖️ Trade-offs — Build + AI-Augment

| | Build 100% rule-based | Build + AI-Augment | Buy SaaS cho Core |
|---|---|---|---|
| **Được** | Deterministic 100%, dễ debug | Coverage cao hơn (85% vs 60%), development nhanh hơn | Deploy nhanh (2 tuần), zero dev effort |
| **Mất** | Coverage thấp (60%), edge cases miss | AI hallucination risk, vendor dependency (model changes) | Mất lợi thế cạnh tranh (giống đối thủ) |
| **Khi nào** | Regulated domain (banking, healthcare) | Pattern recognition + business rules hybrid | Validation phase, startup chưa biết Core |

---

## 🏋️ Exercise — Apply Decision Framework

### Phần A: ITO — 5 Questions (10 phút)

| Domain | Quyết định | Câu hỏi nào quyết định? | Chi phí ước tính |
|---|---|---|---|
| Resource Mgmt | ? | ? | ? |
| Opportunity Mgmt | ? | ? | ? |
| Customer Success | ? | ? | ? |
| Delivery Mgmt | ? | ? | ? |
| Billing | ? | ? | ? |
| Knowledge Mgmt | ? | ? | ? |

### Phần B: Logistics — 5 Questions (10 phút)

| Domain | Quyết định | Câu hỏi nào quyết định? | Chi phí ước tính |
|---|---|---|---|
| Route Optimization | ? | ? | ? |
| Real-time Tracking | ? | ? | ? |
| Fleet Mgmt | ? | ? | ? |
| Warehouse | ? | ? | ? |
| POD | ? | ? | ? |
| Order Mgmt | ? | ? | ? |

---

## 🪞 Reflect

1. **"Build + AI-Augment" có rủi ro gì?** → AI model thay đổi API → code break. Hallucination trong business logic (AI nói "Java ≈ Python" = sai). Vendor lock-in (GPT-4 → GPT-5 không backward compatible). **Mitigation:** Business rules LUÔN là code (deterministic). AI chỉ làm phần "fuzzy" (matching, prediction). Fallback khi AI fail = rule-based.

2. **Khi nào "Build toàn bộ" đúng hơn "Build + AI"?** → Domain cần **100% deterministic**: tài chính (sai 1 đồng = audit failure), y tế (AI suggest sai thuốc = mạng người), legal (contract terms phải exact). → Trong ITO: Billing = Build toàn bộ (số tiền phải chính xác). Resource Matching = Build + AI (matching "đủ tốt" OK).

3. **CEO startup 10 người — phân bổ effort thế nào?** Gợi ý: 70% Core (Build), 20% Supporting (Buy+Customize), 10% Generic (Buy SaaS). Nếu team 10 người → 7 người làm Core, 2 người integrate Supporting, 1 người setup SaaS.

---

## ✅ Completion Checklist
- [ ] **Recall:** Phân biệt Build / Buy / Customize / AI-Augment + 5-Question Framework
- [ ] **Apply:** Apply framework cho ≥5 domains ITO + ≥5 domains Logistics
- [ ] **Analyze:** Giải thích khi nào AI-Augment phù hợp vs khi nào cần Build 100% — dùng ví dụ Resource Matching vs Billing

---

## 🏁 Hoàn thành Module 3 — Strategic Design

| Lesson | Bạn đã làm | Output |
|---|---|---|
| 3.1 Core / Supporting / Generic | Phân loại domain bằng Copy Test | 8 domains classified: 3 Core, 3 Supporting, 2 Generic |
| 3.2 Priority Matrix | Đánh giá 4 tiêu chí, phân pha | Migration Roadmap: Phase 1 → 2 → 3 |
| 3.3 Build vs Buy vs AI | Quyết định đầu tư cho từng domain | Investment Map: Build/Buy/Customize/AI-Augment |

**Deliverable:** `practice/domain-priority.md` — Bảng phân loại + Priority Matrix + Investment Map

> 🔗 **Tiếp theo — Module 4: Event Storming.** Bạn đã có blueprint hoàn chỉnh: Domain Map (M1) + Glossary (M2) + Strategic Classification + Investment Map (M3). Nhưng tất cả đều **tĩnh** — mô tả "có gì" chứ không mô tả "xảy ra gì". Khi **Hà** ký deal, chuyện gì diễn ra theo trình tự? Ai nhận thông báo? Hệ thống nào phải cập nhật? Dữ liệu chảy đi đâu? Module 4 sẽ dạy bạn **Event Storming** — kỹ thuật khám phá luồng sự kiện, biến Domain Map tĩnh thành **bản đồ hành vi sống** của tổ chức.
