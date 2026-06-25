---
type: lesson
module: 3
lesson: 3
title: "Build vs Buy vs AI"
duration: "25 phút"
prerequisites: ["module-3/lesson-2"]
---

# Lesson 3.3: Build vs Buy vs AI

## 🎓 Concept — "Chiến lược đầu tư theo loại domain"

### Ma trận quyết định

| Loại Domain | Chiến lược | Tại sao | Ví dụ |
|---|---|---|---|
| **Core** | **Build** in-house | Lợi thế cạnh tranh → không thể outsource | Resource Matching Engine |
| **Supporting** | **Buy** + Customize | Cần nhưng standard → dùng tool + customize | Jira cho Delivery, HubSpot cho CRM |
| **Generic** | **Buy** SaaS / Open-source | Giống mọi nơi → không build | Auth0, SendGrid, S3 |

### 2026 Update: AI thay đổi luật chơi

Trước 2023: Core = build, Generic = buy. Rõ ràng.

Từ 2024+: AI tạo ra lựa chọn thứ 4 — **AI-Augmented**.

| Chiến lược | Nghĩa | Khi nào dùng |
|---|---|---|
| **Build** | Team viết code từ đầu | Core Domain, competitive advantage |
| **Buy** | Mua SaaS/license | Generic, không customize |
| **Customize** | Mua + sửa | Supporting, cần adapt |
| **AI-Augment** | Build core logic + AI xử lý phần phức tạp | Core Domain có pattern recognition |

### Ví dụ AI-Augment trong ITO

```
TRƯỚC (2023): Resource Matching = Build toàn bộ
  → Rule-based: if skill == "Java" && seniority >= "Senior" → match
  → Mất 3-6 tháng build, coverage 60%

SAU (2026): Resource Matching = Build core + AI-Augment
  → Core: Business rules (allocation ≤ 100%, bench threshold)
  → AI: Semantic skill matching, past performance prediction
  → Mất 2-3 tháng, coverage 85%

KẾT QUẢ: Team build core rules (1 tháng) + integrate AI (1 tháng)
         = faster + better + cheaper
```

### Ví dụ AI-Augment trong Logistics

```
TRƯỚC: Route Optimization = Operations Research team (PhD level)
  → Linear programming, constraint satisfaction
  → Mất 6-12 tháng, team 3 người

SAU: Route Optimization = Build constraints + AI solve
  → Core: Hard constraints (vehicle capacity, time windows, driver hours)
  → AI: Soft optimization (minimize distance, fuel, time)
  → Mất 3-4 tháng, better solutions

CÒN: Real-time Tracking = Buy (Google Maps) + AI-Augment (ETA prediction)
```

### Decision Framework: 5 câu hỏi

Với mỗi domain, hỏi 5 câu theo thứ tự:

```
1. "Nó là Generic?" → Yes → BUY (Auth0, SendGrid, S3)
                       No ↓

2. "Nó là Core?"   → No → Supporting → BUY + CUSTOMIZE
                     Yes ↓

3. "Có SaaS nào   → Yes → Evaluate: đủ 80% nhu cầu?
    giải quyết?"          → Yes → BUY + CUSTOMIZE (chấp nhận)
                          → No ↓

4. "AI có thể giải → Yes → BUILD core rules + AI-AUGMENT
    quyết phần                 (hybrid approach)
    phức tạp?"       No ↓

5. "Build toàn bộ"  → BUILD in-house (team giỏi nhất)
```

### Ví dụ: Apply Framework cho ITO

| Domain | Q1 | Q2 | Q3 | Q4 | Q5 | Quyết định |
|---|---|---|---|---|---|---|
| Auth | Generic ✅ | — | — | — | — | **Buy** (Auth0) |
| Notification | Generic ✅ | — | — | — | — | **Buy** (SendGrid) |
| Contract Mgmt | — | Core? No | — | — | — | **Buy+Customize** (PandaDoc) |
| Delivery Mgmt | — | Core? No | — | — | — | **Buy+Customize** (Jira) |
| Resource Mgmt | — | Core ✅ | SaaS? No fit | AI? ✅ | — | **Build + AI-Augment** |
| Opportunity Mgmt | — | Core ✅ | HubSpot? 70% fit | — | — | **Build** (HubSpot không đủ) |

---

## 🏋️ Exercise — Apply Decision Framework

### Phần A: ITO — 5 Questions (10 phút)

Áp dụng 5 câu hỏi cho mỗi domain:

| Domain | Quyết định | Lý do (câu hỏi nào quyết định?) |
|---|:---:|---|
| Resource Mgmt | | |
| Opportunity Mgmt | | |
| Customer Success | | |
| Delivery Mgmt | | |
| Billing | | |
| Knowledge Mgmt | | |

### Phần B: Logistics — 5 Questions (10 phút)

| Domain | Quyết định | Lý do |
|---|:---:|---|
| Route Optimization | | |
| Real-time Tracking | | |
| Fleet Mgmt | | |
| Warehouse | | |
| POD | | |
| Order Mgmt | | |

---

## 🪞 Reflect

1. **"Build + AI-Augment" có rủi ro gì?** Nghĩ về: AI model thay đổi, vendor lock-in, hallucination trong business logic.

2. **Khi nào thì "Build toàn bộ" vẫn đúng hơn "Build + AI"?** Gợi ý: domain nào cần 100% deterministic (tài chính, y tế)?

3. **Nếu bạn là CEO startup 10 người, bạn dùng bao nhiêu % effort cho Core vs Supporting vs Generic?** Gợi ý lý tưởng: 70% Core, 20% Supporting, 10% Generic.

---

## ✅ Hoàn thành lesson khi
- [ ] Áp dụng 5-question framework cho ≥5 domains
- [ ] Phân biệt Build vs Buy vs Customize vs AI-Augment
- [ ] Nhận diện khi nào AI-Augment phù hợp
- [ ] Trả lời ≥2/3 câu hỏi reflection

---

## 🏁 Hoàn thành Module 3

Bạn đã học:
- **Lesson 3.1:** Phân loại Core/Supporting/Generic + Copy Test
- **Lesson 3.2:** Priority Matrix + Migration Roadmap
- **Lesson 3.3:** Build vs Buy vs AI-Augment + 5-Question Framework

**Deliverable:** `practice/domain-priority.md`

→ Tiếp: **Quiz Module 3** rồi sang **Module 4: Event Storming**
