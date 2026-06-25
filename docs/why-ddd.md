# Tại sao phải học Domain-Driven Design (DDD)?

> **Dành cho:** Developer, Architect, BA/PO muốn hiểu hệ thống từ góc độ nghiệp vụ  
> **Thời gian đọc:** ~10 phút

---

## Vấn đề thực tế — Trước khi có DDD

### Bạn có nhận ra những tình huống này không?

```
❌ "Tôi build xong tính năng rồi, nhưng business nói sai yêu cầu"
❌ "Code hoạt động đúng kỹ thuật, nhưng không giải quyết được vấn đề thật"
❌ "Mỗi team dùng một từ khác nhau để chỉ cùng 1 thứ"
❌ "Thêm 1 tính năng nhỏ phải sửa 5 chỗ khác nhau"
❌ "AI agent không hiểu context của hệ thống dù đã cung cấp database schema"
```

Tất cả những vấn đề này đều có **một nguyên nhân chung**: Code không phản ánh đúng ngôn ngữ và tư duy của nghiệp vụ.

**DDD là giải pháp cho bài toán đó.**

---

## DDD là gì — Định nghĩa thực tế

Domain-Driven Design **không phải** là một framework hay công nghệ.  
DDD là **tư duy** — cách tiếp cận phát triển phần mềm bắt đầu từ **domain** (lĩnh vực nghiệp vụ) thay vì từ database hay UI.

```
Cách thông thường:   Database Schema → Code → Business Logic
DDD:                 Domain → Model → Code → Database
```

**Domain** = lĩnh vực mà phần mềm được tạo ra để phục vụ.  
Ví dụ: E-commerce domain, Healthcare domain, IT Outsourcing domain.

---

## Tại sao DDD quan trọng với AI và Agent?

Đây là lý do **đặc biệt quan trọng** trong thời đại AI:

### AI đọc Domain Model tốt hơn Database Schema

```
Database Schema (AI khó hiểu context):
  users → orders → order_items → products → categories

Domain Model (AI hiểu ngay):
  Customer places Order
  Order contains OrderLine
  OrderLine references Product from Catalog domain
  Pricing domain calculates final amount
```

Khi bạn mô tả hệ thống bằng Domain Language, AI agent có thể:
- Hiểu **tại sao** mỗi entity tồn tại, không chỉ **cái gì**
- Suggest đúng solution thay vì chỉ generate code
- Không "hallucinate" context vì ngôn ngữ rõ ràng, nhất quán

### Ubiquitous Language = Context Engineering cho AI

DDD yêu cầu tất cả mọi người (dev, BA, business) dùng **cùng một ngôn ngữ**.  
Glossary đó chính là **context chất lượng cao** để inject vào AI prompt.

---

## Khi nào NÊN dùng DDD?

| Tình huống | DDD phù hợp? |
|---|---|
| Domain phức tạp, nhiều nghiệp vụ chồng chéo | ✅ Rất phù hợp |
| Cần nhiều team làm việc độc lập | ✅ Rất phù hợp |
| Hệ thống sống lâu dài (>2 năm) | ✅ Rất phù hợp |
| Cần AI/Agent hiểu và làm việc với codebase | ✅ Rất phù hợp |
| CRUD đơn giản, ít nghiệp vụ | ⚠️ Overkill |
| Prototype, POC, hackathon | ❌ Không cần |
| Team 1-2 người, deadline gấp | ❌ Không cần |

---

## DDD khác gì Database Design?

Đây là **misconception phổ biến nhất**:

| | Database Design | Domain-Driven Design |
|---|---|---|
| **Bắt đầu từ** | Cấu trúc lưu trữ | Nghiệp vụ thực tế |
| **Đơn vị** | Table, Column, Index | Domain, Entity, Aggregate |
| **Ngôn ngữ** | Technical (users, orders) | Business (Customer, Order) |
| **Ranh giới** | Foreign Key | Bounded Context |
| **Thay đổi** | Schema migration | Model evolution |

> **Ví dụ thực tế:**  
> Database: `users` table phục vụ cả Sales và HR  
> DDD: `Customer` trong Sales domain ≠ `Employee` trong HR domain — dù cùng lưu "người"

---

## Lộ trình học trong repo này

Repo này dạy DDD theo framework **CEER**:

```
Concept  → Hiểu lý thuyết, tại sao tồn tại
Example  → Xem ví dụ thực tế từ ITO CRM case study
Exercise → Tự làm bài tập áp dụng
Reflect  → Viết lại những gì đã học bằng lời của mình
```

### 6 Modules theo thứ tự bắt buộc:

```
Module 1: Domain Discovery
  "Công ty có những lĩnh vực nghiệp vụ nào?"
       ↓
Module 2: Ubiquitous Language
  "Mọi người có đang hiểu cùng 1 nghĩa không?"
       ↓
Module 3: Strategic Design
  "Lĩnh vực nào đáng đầu tư nhất?"
       ↓
Module 4: Event Storming          ← Chỉ làm cho Core Domain
  "Nghiệp vụ Core vận hành như thế nào?"
       ↓
Module 5: Bounded Context
  "Ranh giới mô hình ở đâu?"
       ↓
Module 6: Knowledge Architecture
  "Tổ chức tri thức sao cho AI + người đều đọc được?"
```

> **Nguyên tắc:** Thứ tự này không phải ngẫu nhiên — mỗi module xây trên nền tảng của module trước.

---

## Bắt đầu ngay

```
→ Đọc curriculum/README.md để hiểu toàn bộ chương trình
→ Bắt đầu học: curriculum/module-1-domain-discovery/
→ Làm bài tập: practice/domain-map.md
```

Nói với AI instructor (Antigravity):
```
"Dạy module 1 lesson 1"
```

---

## Tại sao repo này dùng AI Agent?

DDD yêu cầu **tư duy**, không chỉ đọc tài liệu. AI Instructor trong repo này:

- **Không cho đáp án ngay** — hỏi ngược lại để bạn tự tư duy
- **Đánh giá theo rubric** — không chỉ "đúng/sai" mà phân tích chất lượng reasoning
- **Track progress** — nhớ bạn đã học đến đâu, gợi ý ôn tập spaced repetition
- **Dùng case study thực tế** — ITO CRM (IT Outsourcing) để bài học có context cụ thể

---

*"DDD không phải kỹ thuật lập trình — đó là kỹ năng giao tiếp giữa business và technology."*
