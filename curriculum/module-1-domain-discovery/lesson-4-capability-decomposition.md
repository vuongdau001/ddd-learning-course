---
type: lesson
module: 1
lesson: 4
title: "Capability Decomposition"
duration: "30 phút"
prerequisites: ["module-1/lesson-3"]
narrative_phase: "khám phá"
migration_phase: "Pre-migration: Lên blueprint"
business_invariant: "Mỗi capability phải có owner rõ ràng; không có capability nào bị chia cho 2 team"
---

# Lesson 1.4: Capability Decomposition — "Một domain không phải là một cục"

## 📍 Context — Bạn đang ở đây

> Sau 1 tuần khảo sát (Lesson 1.1-1.3), bạn đã trình bày cho CTO **Minh**: Domain Map hoàn chỉnh gồm 8 domains, có số liệu impact cho từng domain (Resource Management đau nhất: ~700 triệu/quý). Minh ấn tượng, nhưng hỏi: *"OK, 8 domains. Budget chỉ đủ build 3 cái trong 6 tháng đầu. Mà 'Resource Management' nghe rộng quá — cụ thể gồm gì? Cái gì cần build trước, cái gì dùng tool có sẵn, cái gì chờ?"*
>
> Bạn nhận ra: Domain Map cho bạn bức tranh lớn, nhưng CTO cần **bức tranh nhỏ hơn** — phân rã từng domain thành khả năng cụ thể để ra quyết định đầu tư.

## 🔥 Tension — "Build hết hay Build gì trước?"

Bạn mời **Tuấn** (Resource Manager) vào phòng để hiểu rõ hơn domain Resource Management. Tuấn liệt kê:

> **Tuấn:** *"Team tôi cần:*
> 1. *Biết ai biết skill gì, cấp độ bao nhiêu*
> 2. *Biết tháng sau còn mấy người rảnh*
> 3. *Match người vào dự án khi Sales ký deal*
> 4. *Theo dõi ai đang bench quá lâu*
> 5. *Đo billable hours — utilization rate*
> 6. *Dự đoán 6 tháng tới thị trường cần skill gì*"

**Hà** (VP Sales) nghe xong, thêm:

> **Hà:** *"Tuấn nói 6 cái. Nhưng tôi chỉ cần đúng 1: khi ký deal xong, Tuấn có người cho tôi trong 48 giờ thay vì 5 ngày. Nếu chỉ build 1 cái, build cái match người."*

**Minh** tổng kết:
> *"Tuấn muốn 6 chức năng. Hà chỉ cần 1. Budget cho Phase 1 = 4 tháng × 1 team. Bạn sẽ đề xuất build gì?"*

> 💭 **Câu hỏi:** Build cả 6 thì không đủ thời gian. Build 1 thì bỏ lỡ. Có cách nào phân loại để biết cái nào MVP, cái nào Phase 2, cái nào Phase 3?

## 🎓 Explanation — Capability Decomposition

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Mỗi capability phải có owner rõ ràng (1 team, 1 người). Không có capability nào bị chia cho 2 team — nếu chia = xung đột quyết định."*

### Domain vs Capability vs Feature

| Cấp | Ví dụ | Mô tả |
|---|---|---|
| **Domain** | Resource Management | Phạm vi lớn — "chủ đề" |
| **Capability** | Project Staffing | 1 khả năng cụ thể trong domain |
| **Feature** | "Tìm resource theo skill" | 1 chức năng phần mềm |

```
Domain → Capability → Feature → User Story → Task
(lớn)                                        (nhỏ)
```

### Cách tách: Hỏi "domain này CÓ THỂ LÀM GÌ?"

Với mỗi domain, hỏi stakeholder: **"Cái gì có thể xảy ra trong domain này?"**

Mỗi câu trả lời dạng **động từ + danh từ** = 1 capability:

| Tuấn nói | Capability |
|---|---|
| "Tìm ai có skill Java Senior" | **Skill Inventory** |
| "Biết tháng sau còn bao nhiêu người rảnh" | **Capacity Planning** |
| "Match người vào dự án" | **Project Staffing** |
| "Theo dõi ai đang bench" | **Bench Management** |
| "Đo billable hours" | **Utilization Tracking** |
| "Dự đoán 6 tháng tới cần gì" | **Workforce Forecast** |

### Phân pha theo dependency và impact

Không phải 6 capabilities đều cần build cùng lúc. Phân loại:

```
Resource Management
├── MVP (Phase 1 — 4 tháng):
│   ├── Skill Inventory       ← Foundation: mọi thứ cần biết "ai biết gì"
│   ├── Project Staffing      ← Hà cần nhất: match người ≤ 48h
│   └── Bench Management      ← Tuấn cần: biết ai rảnh
│
├── Phase 2 (tháng 5-8):
│   ├── Utilization Tracking  ← Cần data từ Phase 1 (hours history)
│   └── Capacity Planning     ← Cần Skill Inventory + Bench data tích lũy
│
└── Phase 3 (tháng 9-12):
    └── Workforce Forecast    ← Cần 6+ tháng data + có thể dùng AI
```

**Logic phân pha:**
- **Phase 1:** Capability nào giải quyết pain point lớn nhất **VÀ** làm foundation cho phase sau
- **Phase 2:** Capability nào cần data tích lũy từ Phase 1
- **Phase 3:** Capability nào cần AI/ML hoặc data dài hạn

### Ví dụ — Logistics: Fleet Management

```
Fleet Management
├── MVP (Phase 1):
│   ├── Vehicle Registry       "Có bao nhiêu xe? Loại gì? Tải trọng?"
│   ├── Driver Assignment      "Tài xế nào lái xe nào?"
│   └── Maintenance Scheduling "Xe nào cần bảo trì? Khi nào?"
│
├── Phase 2:
│   ├── Fuel Management        "Mỗi xe tiêu bao nhiêu nhiên liệu?"
│   └── Compliance Tracking    "Xe nào hết đăng kiểm? Giấy phép?"
│
└── Phase 3:
    └── Fleet Optimization     "Cần thêm/bớt xe loại nào?" (cần AI/data)
```

### Quy tắc ngón tay cái

- **3-7 capabilities mỗi domain** — Ít hơn 3: domain quá nhỏ (gộp). Nhiều hơn 7: domain quá lớn (tách).
- **Mỗi capability có verb ngầm** — "Skill Inventory" = khả năng **quản lý** skill. Không tìm ra verb → chưa phải capability.
- **Capabilities phải independent** — Build Skill Inventory mà không cần Workforce Forecast. Nếu 2 capabilities luôn đi cùng → chúng là 1.
- **1 owner per capability** — Nếu 2 team cùng own 1 capability → cần tách nhỏ hơn hoặc giao rõ.

### ⚖️ Trade-offs — Granular vs Coarse

| | Tách quá nhỏ (>10 capabilities) | Tách vừa phải (3-7) | Không tách (domain = 1 cục) |
|---|---|---|---|
| **Được** | Chi tiết, assign team rõ | Balance | Nhanh, dễ hiểu |
| **Mất** | Overhead quản lý, dependencies chéo nhiều | — | Scope mờ, không ưu tiên được |
| **Khi nào** | Enterprise rất lớn (>10 team) | Hầu hết dự án | Chỉ khi domain nhỏ (1-2 dev) |

---

## 🏋️ Exercise — Tách Capabilities + Phân pha

### Phần A: Opportunity Management — ITO (15 phút)

VP **Hà** mô tả: *"Sales pipeline gồm: tìm lead, qualify lead, track opportunity qua stages, gửi proposal, phân tích win/loss. Ah, còn quản lý territory nữa — đừng để 2 người approach cùng 1 khách."*

Tách domain Opportunity Management:

```
Opportunity Management
├── _______________         "_______________"
├── _______________         "_______________"
├── _______________         "_______________"
├── _______________         "_______________"
├── _______________         "_______________"
└── _______________         "_______________"
```

**Phân pha:**
- MVP (Phase 1) gồm capabilities nào? _______________
- Phase 2 gồm gì? _______________
- Capabilities nào Hà muốn nhất? Capabilities nào cần data tích lũy?

<details>
<summary>💡 Gợi ý: Nghĩ về lifecycle của Opportunity</summary>

Từ Lead vào → qualify → track stages → gửi proposal → thắng/thua → phân tích.
Mỗi bước = 1 capability tiềm năng.
Territory Management = capability phòng ngừa trùng lặp (pain point: mất deal 2 tỷ từ Lesson 1.2).
</details>

### Phần B: Route Optimization — Logistics (10 phút)

Tách domain Route Optimization:

```
Route Optimization
├── _______________         "_______________"
├── _______________         "_______________"
├── _______________         "_______________"
├── _______________         "_______________"
└── _______________         "_______________"
```

MVP gồm capabilities nào? _______________

### Phần C: Self-check (5 phút)

| Kiểm tra | Phần A | Phần B |
|---|:---:|:---:|
| Có 3-7 capabilities? | ☐ | ☐ |
| Mỗi capability có verb ngầm? | ☐ | ☐ |
| Capabilities independent? | ☐ | ☐ |
| Chọn được MVP (2-3 caps)? | ☐ | ☐ |
| Mỗi capability có 1 owner? | ☐ | ☐ |

---

## 🪞 Reflect

1. **Workforce Forecast nằm ở Phase 3 — nhưng CEO muốn ngay. Bạn xử lý sao?** Gợi ý: bạn có thể build "fake forecast" bằng manual analysis từ data Phase 1 (Skill Inventory + Bench) — không cần ML ngay. Trade-off: accuracy thấp nhưng CEO có data sớm.

2. **"Real-time Tracking" trong Logistics — là domain riêng hay capability của Transportation Management?** Tiêu chí: nếu cần team riêng, tech stack riêng (IoT/GPS), business rules riêng → domain riêng. Nếu chỉ là feature nhỏ → capability.

3. **Khi nào capability nên "lên chức" thành Domain riêng?** Gợi ý: khi team phát triển nó cần >3 dev, hoặc quy tắc riêng phức tạp đến mức xung đột với domain mẹ. Ví dụ: Pricing trong Sales → tách thành Pricing domain khi có 50+ rules.

> [!TIP]
> **💡 Nâng cao: Capability trong Enterprise Architecture**
>
> Capability Decomposition không chỉ là kỹ thuật DDD. Trong **TOGAF** và **Business Architecture**:
> - **Business Capability Map** (TOGAF) ≈ **Domain Map + Capabilities** (DDD) ở Level 1-2
> - **Capability-based Planning** = ưu tiên đầu tư dựa trên capability gaps
> - Kỹ năng bạn vừa học ở đây **dùng trực tiếp trong EA workshops**.

---

## ✅ Completion Checklist
- [ ] **Recall:** Phân biệt Domain / Capability / Feature, nêu quy tắc 3-7
- [ ] **Apply:** Tách Opportunity Management (≥4 caps) và Route Optimization (≥4 caps) thành capabilities + chọn MVP
- [ ] **Analyze:** Giải thích tại sao phân pha thành 3 phase — logic dependency giữa các capabilities

---

## 🏁 Hoàn thành Module 1 — Domain Discovery

Qua 4 lessons, bạn đã cùng CTO **Minh** hoàn thành tuần khảo sát cho ITO Corp:

| Lesson | Bạn đã làm | Output |
|---|---|---|
| 1.1 Tư duy Domain-First | Phân biệt Domain vs UI Component | Biết không bắt đầu từ màn hình |
| 1.2 Value Chain Analysis | Vẽ dòng tiền ITO Corp | 6 domains trên value chain |
| 1.3 Kỹ thuật khám phá | 4 ống kính quét tổ chức | 8 domains + impact đo bằng số |
| 1.4 Capability Decomposition | Phân rã domain thành capabilities | Blueprint phân pha: MVP → Phase 2 → Phase 3 |

**Deliverable:** `practice/domain-map.md` — Domain Map + Capabilities cho ≥2 domain

> 🔗 **Tiếp theo — Module 2: Ubiquitous Language.** Bạn đã có Domain Map đẹp. Nhưng khi Hà nói "Khách hàng", cô ấy nghĩ "Account — công ty ký contract". Khi Tuấn nói "Khách hàng", anh ấy nghĩ "End-user — người dùng hàng ngày". Cùng 1 từ, 2 nghĩa khác nhau. Nếu bạn build hệ thống dùng chung 1 bảng `customers` — điều gì sẽ xảy ra? Module 2 sẽ giúp bạn **phát hiện và giải quyết xung đột ngôn ngữ** trước khi chúng trở thành bug.
