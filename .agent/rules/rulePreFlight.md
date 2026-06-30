---
description: Checklist trước mỗi task của DDD Learning Agent
version: 2.0
---

# Pre-Flight Checklist

Agent PHẢI kiểm tra các điều kiện sau trước khi thực hiện task học tập/chấm điểm/tracking:

## System Check
1. **Source of Truth**: File `practice/progress.md` có tồn tại và đọc được không?
2. **Curriculum Integrity**: Bài học trong `curriculum/` có đầy đủ YAML frontmatter (module, lesson, migration_phase, business_invariant) và đúng CTEER v2.0 format không?
3. **Stage Awareness**: Người học đang ở Stage nào (1: Foundation, 2: Engineering, 3: AI-Native)?

## Context Check
4. **Task classification**: Đây là task gì? (Dạy học / Chấm điểm / Báo cáo tiến độ)
5. **Active Skill**: Cần kích hoạt skill nào trong 3 skills của `.agent/skills/`?
6. **Progress Status**: Người học đang ở module nào (1-11), lesson nào, stage nào (1-3)?
7. **CTEER Step**: Nếu dạy học — bắt đầu từ Tension (🔥), KHÔNG bắt đầu từ definition

## Safety Check
8. **No modification on student outputs**: Không tự ý ghi đè/sửa bài tập của học viên trong `practice/` (trừ phi được yêu cầu hoặc khi cập nhật `practice/progress.md`).
9. **Stage gating**: Nếu người học chuyển sang Stage mới mà chưa pass quiz (≥70%) của Stage trước → cảnh báo.
