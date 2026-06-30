---
type: lesson
module: 5
lesson: 1
title: "Subdomain vs Bounded Context"
duration: "30 phút"
prerequisites: ["module-4"]
narrative_phase: "kiến trúc"
migration_phase: "Phase 1: Định nghĩa ranh giới contexts"
business_invariant: "Subdomain = Problem Space (tồn tại tự nhiên); Bounded Context = Solution Space (team thiết kế); 1:1 mapping lý tưởng nhưng không bắt buộc"
---

# Lesson 5.1: Subdomain vs Bounded Context — "Vấn đề ≠ Giải pháp"

## 📍 Context — Bạn đang ở đây

> Module 4 cho bạn Event Storm hoàn chỉnh: Resource Management có ~15 events, 5+ policies, 3+ hot spots. Dev team bắt đầu implement. Nhưng nảy sinh vấn đề mới: **"Resource" trong Event Storm có 2 nghĩa.** Khi Tuấn nói "Resource" = con người có skill, kinh nghiệm, preference. Khi PM nói "Resource" trong sprint = FTE (full-time equivalent), allocable unit, cost/giờ.
>
> Cùng từ "Resource" — 2 model khác nhau — 2 team dùng khác nhau. Nếu code cả hai vào 1 class `Resource` → class sẽ phình to, mọi thay đổi RM đều ảnh hưởng PM, và ngược lại.

## 🔥 Tension — "Merge conflict mỗi ngày"

Sprint 3. Team Resource (3 dev) và team Delivery (2 dev) cùng code trên module `Resource`:

> **Dev A (team Resource):** *"Tôi thêm field `semanticSkills` vào class Resource để AI matching hoạt động."*
>
> **Dev B (team Delivery):** *"Tôi thêm field `sprintVelocity` vào class Resource để PM track performance."*
>
> **Dev A:** *"Merge conflict! Cả hai sửa cùng file `Resource.ts`. Mỗi lần tôi merge → phải test lại toàn bộ."*

**Minh** hỏi:
> *"Tại sao 2 team sửa cùng 1 file? Resource matching và sprint tracking là 2 việc khác nhau. Chúng ta đã phân tách domain mà?"*

Bạn nhìn lại Domain Map: Resource Management = 1 domain. Nhưng **solution** chỉ có 1 codebase, 1 class `Resource`, 1 database table. **Domain đã tách (problem space), nhưng code chưa tách (solution space).**

> 💭 **Câu hỏi:** Domain Map (Module 1) nói "Resource Management" là 1 domain. Nhưng thực tế cần 1 codebase hay 2? Ai quyết định — business hay kỹ thuật?

## 🎓 Explanation — Subdomain vs Bounded Context

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Subdomain = vấn đề (problem space), tồn tại khách quan — business không thể thay đổi việc 'cần quản lý resource'. Bounded Context = giải pháp (solution space), team kỹ thuật thiết kế — CÓ THỂ tách/gộp/refactor tùy team size, tech stack, và deployment needs."*

### Sự nhầm lẫn nguy hiểm nhất trong DDD

90% người học DDD lần đầu nhầm Subdomain và Bounded Context là một.

```
Subdomain = Problem Space  → "Vấn đề gì cần giải quyết?"
                              → Tồn tại tự nhiên trong business
                              → Resource Management LUÔN tồn tại (dù có phần mềm hay không)

Bounded Context = Solution Space → "Giải pháp được tổ chức thế nào?"
                                  → Team kỹ thuật tạo ra
                                  → Resource Context có thể tách thành 2, 3 contexts
```

### Analogy — Bản đồ thành phố

```
Subdomain    = Khu vực địa lý (Quận 1, Quận 7, Thủ Đức)
               → Tồn tại khách quan, bạn không tạo ra được
               → Được định nghĩa bởi thực tế (dân cư, kinh tế)

Bounded Context = Đơn vị hành chính (UBND Quận 1, Sở Giao thông)
                  → Do con người tạo ra, có thể thay đổi
                  → Có thể sáp nhập, tách, reorganize
```

Quận 1 luôn ở đó (Subdomain). Nhưng UBND Quận 1 có thể sáp nhập với Quận 3 (Bounded Context thay đổi).

### Định nghĩa chính thức

| | Subdomain | Bounded Context |
|---|---|---|
| **Thuộc về** | Problem Space | Solution Space |
| **Ai quyết định?** | Business (tồn tại tự nhiên) | Team kỹ thuật (thiết kế) |
| **Thay đổi?** | Ít khi (business thay đổi chậm) | Có thể (refactor, tách/gộp) |
| **Mối quan hệ** | 1 Subdomain → 1 hoặc N Contexts | N/A |
| **Ví dụ** | "Resource Management" (domain) | "Skill Inventory Context" + "Allocation Context" |

### 3 kiểu mapping Subdomain → Bounded Context

#### Kiểu 1: 1 Subdomain = 1 Bounded Context (Lý tưởng — bắt đầu ở đây)
```
[Resource Management] ──── [Resource Context]
[Opportunity Mgmt]    ──── [Sales Context]
```
Mỗi domain có 1 solution riêng. Clean nhất. **Bắt đầu ở đây trừ khi có lý do tách.**

#### Kiểu 2: 1 Subdomain = N Bounded Context (Tách khi cần)
```
[Resource Management] ──┬── [Skill Inventory Context]  ← AI semantic matching
                        ├── [Allocation Context]       ← business rules
                        └── [Utilization Context]      ← reporting/monitoring
```
Khi domain quá lớn → tách thành nhiều contexts. **Dấu hiệu: >30 events, >2 teams, tech stack khác nhau (AI vs CRUD vs real-time).**

→ Đây chính là solution cho merge conflict của Dev A và Dev B!

#### Kiểu 3: N Subdomain = 1 Bounded Context (Gộp — nợ kỹ thuật chấp nhận được)
```
[Contract Mgmt]  ──┐
[Billing]        ──┼── [Finance Context]
```
Khi team nhỏ, 2 domains closely related, chưa cần tách. **Chấp nhận được cho Supporting/Generic. Nguy hiểm cho Core.**

### Apply cho ITO — Giải quyết merge conflict

| Subdomain (Problem) | → Bounded Context(s) (Solution) | Kiểu | Lý do |
|---|---|:---:|---|
| Resource Management | Skill Inventory Context + Allocation Context | 1:2 | AI matching ≠ business rules → 2 teams, 2 tech stacks |
| Opportunity Mgmt | Sales Context | 1:1 | Core, 1 team, cần isolation |
| Delivery Mgmt | Delivery Context | 1:1 | 1 team, separate lifecycle |
| Contract + Billing | Finance Context | 2:1 | Supporting, team nhỏ (2 dev), gộp OK |
| Customer Success | Customer Success Context | 1:1 | Separate concern |
| Auth + Notification | Shared Infrastructure | 2:1 | Generic, Buy SaaS, không cần tách |

**Kết quả:** 8 Subdomains → 6 Bounded Contexts.

### Apply cho Logistics

| Subdomain (Problem) | → Bounded Context(s) (Solution) | Kiểu | Lý do |
|---|---|:---:|---|
| Route Optimization | Route Context | 1:1 | Core, AI-heavy, cần isolation |
| Fleet + Vehicle | Fleet Context | 2:1 | Closely related, 1 team |
| Order + POD | Order Context | 2:1 | Share lifecycle (order → delivery → POD) |
| Warehouse | Warehouse Context | 1:1 | Đội riêng, WMS riêng |
| Real-time Tracking | Tracking Context | 1:1 | Tech stack khác (WebSocket, real-time) |

### Dấu hiệu cần TÁCH

| # | Dấu hiệu | Ví dụ ITO |
|:---:|---|---|
| 1 | 2 teams hay merge conflict | Dev A (matching) vs Dev B (delivery) trên `Resource.ts` |
| 2 | Deploy 1 feature → break feature khác | Thêm `semanticSkills` → break sprint velocity report |
| 3 | Cùng entity nhưng 2 nghĩa | "Resource" = skill profile (RM) vs FTE unit (PM) |
| 4 | >30 events trong Event Storm | Resource Mgmt có ~15 events → OK. Nếu >30 → tách |
| 5 | 1 phần cần scale, phần khác không | AI matching = compute-heavy, CRUD allocation = light |

### Dấu hiệu nên GỘP

| # | Dấu hiệu | Ví dụ ITO |
|:---:|---|---|
| 1 | 2 contexts luôn deploy cùng nhau | Contract + Billing = luôn cùng release |
| 2 | Cross-context calls quá chatty | Nếu Billing gọi Contract 10 lần/request → gộp |
| 3 | Team chỉ 1-2 người | Finance team 2 dev → gộp Contract + Billing OK |

### ⚖️ Trade-offs — Tách vs Gộp

| | Tách nhiều contexts | Gộp ít contexts |
|---|---|---|
| **Được** | Teams độc lập, deploy riêng, scale riêng | Ít complexity, ít network calls, dễ debug |
| **Mất** | Complexity (service communication, eventual consistency) | Merge conflicts, coupling, khó scale |
| **Khi nào** | ≥2 teams, Core Domain, khác tech stack | 1 team, Supporting/Generic, startup phase |

---

## 🏋️ Exercise — Mapping Subdomain → Bounded Context

### Phần A: ITO (10 phút)

| Subdomain | → Bounded Context(s) | Kiểu (1:1/1:N/N:1) | Lý do |
|---|---|:---:|---|
| Resource Management | ? | ? | ? |
| Opportunity Mgmt | ? | ? | ? |
| Delivery Mgmt | ? | ? | ? |
| Contract Mgmt | ? | ? | ? |
| Customer Success | ? | ? | ? |
| Billing | ? | ? | ? |
| Knowledge Mgmt | ? | ? | ? |

Tổng Bounded Contexts: ___

### Phần B: Logistics (10 phút)

| Subdomain | → Bounded Context(s) | Kiểu | Lý do |
|---|---|:---:|---|
| Route Optimization | ? | ? | ? |
| Fleet Management | ? | ? | ? |
| Real-time Tracking | ? | ? | ? |
| Warehouse | ? | ? | ? |
| Order Management | ? | ? | ? |
| POD | ? | ? | ? |

---

## 🪞 Reflect

1. **N:1 mapping (gộp) thường là "nợ kỹ thuật" — tại sao?** → Vì 2 domains gộp = model bị trộn lẫn. Khi business thay đổi 1 domain → phải sửa code chia sẻ → risk break domain kia. **Acceptable khi:** team nhỏ, domains closely related, cả hai đều Supporting/Generic.

2. **Team size ảnh hưởng quyết định tách/gộp thế nào?** → Amazon's "Two Pizza Rule": 1 team ≤ 8 người, own 1-2 Bounded Contexts. Nếu 5 dev ITO → max 3-4 contexts ban đầu. Tách thêm khi team grow. **Lesson: Bounded Context boundary = team boundary.**

3. **Phát hiện context quá lớn SAU KHI code — refactor thế nào?** → **Strangler Fig Pattern**: tạo context mới, route dần traffic sang, cho đến khi context cũ rỗng → xóa. Không bao giờ rewrite Big Bang.

---

## ✅ Completion Checklist
- [ ] **Recall:** Phân biệt Subdomain (Problem Space) vs Bounded Context (Solution Space) + 3 kiểu mapping
- [ ] **Apply:** Map ≥7 ITO subdomains + ≥5 Logistics subdomains → Bounded Contexts với lý do
- [ ] **Analyze:** Giải thích tại sao merge conflict Dev A vs Dev B xảy ra — liên hệ Subdomain đã tách nhưng code chưa tách

---

> 🔗 **Tiếp theo:** Bạn đã định nghĩa ranh giới từng Bounded Context. Nhưng contexts không sống cô lập — Sales Context cần nói chuyện với Resource Context ("deal won → tìm resource"). Bài tiếp — *Context Mapping* — sẽ dạy bạn cách vẽ relationships giữa contexts: Upstream/Downstream, Partnership, Customer-Supplier, và anti-pattern Shared Kernel.
