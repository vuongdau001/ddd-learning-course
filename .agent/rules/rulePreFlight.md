---
description: Checklist trước mỗi task của DDD Learning Agent
---

# Pre-Flight Checklist

Agent PHẢI kiểm tra các điều kiện sau trước khi thực hiện task học tập/chấm điểm/tracking:

## System Check
1. **Source of Truth**: File `practice/progress.md` có tồn tại và đọc được không?
2. **Curriculum Integrity**: Các bài học trong `curriculum/` có đầy đủ YAML frontmatter và đúng format không?

## Context Check
3. **Task classification**: Đây là task gì? (Dạy học / Chấm điểm / Báo cáo tiến độ)
4. **Active Skill**: Cần kích hoạt skill nào trong 3 skills của `.agent/skills/`?
5. **Progress Status**: Người học đang ở module nào, lesson nào?

## Safety Check
6. **No modification on student outputs**: Không tự ý ghi đè/sửa bài tập của học viên trong `practice/` (trừ phi được yêu cầu hoặc khi cập nhật `practice/progress.md`).
