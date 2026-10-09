# OPERATING DASHBOARD — VinGlucare Agent

**B2C hiện tại** · **09/10/2026** · **Lê Thanh Tình — 2A202602449**  
**NORTH STAR:** D7 retained care-loop users — chưa có baseline — ngưỡng khởi tạo **≥4/5**, chốt 23/10/2026

> Người bệnh dùng trực tiếp; chưa có tổ chức/partner trả tiền. Mọi số hiện tại là **chưa đo**, không được hiểu là 0.

### Leading — nhìn hằng ngày/tuần

| Đèn | Hiện | 🟢 · 🟡 · 🔴 | Nguồn | Báo trước cho |
|---|---:|---|---|---|
| Activation 24h | Chưa đo | ≥4/5 · 3/5 · ≤2/5 | [TB] 23/10 | Retention D7 |
| Retention D7 | Chưa đo | ≥4/5 · 3/5 · ≤2/5 | [TB] 23/10 | Tuân thủ 7 ngày |
| Safety-block pass | Chưa đo | 30/30 · 29/30 · ≤28/30 | [TB] 23/10 | Tin cậy/hài lòng |

### Operating — nhìn hằng tuần

| Đèn | Hiện | 🟢 · 🟡 · 🔴 | Nguồn | Báo trước cho |
|---|---:|---|---|---|
| Tuân thủ care-day/7 ngày | Chưa đo | ≥80% · 50–79% · <50% | [TB] 23/10 | Retention D30 |
| Citation đúng / 50 câu | Chưa đo | ≥43 · 40–42 · ≤39 | [TB] 23/10 | Tin cậy/hài lòng |
| Cost/Job | Chưa đo | ≤$0,107 · >$0,107–$0,143 · >$0,143 | [MH] | p95 cost/user-tháng |
| p95 AI cost/user-tháng | Chưa đo | ≤$3,214 · >$3,214–$4,286 · >$4,286 | [MH] | Ngân sách/GM |

### Lagging — sau mỗi vòng test

| Đèn | Hiện | 🟢 · 🟡 · 🔴 | Nguồn |
|---|---:|---|---|
| Hài lòng (≥5 bệnh nhân) | Chưa đo | ≥4,0 · 3,0–3,9 · <3,0/5 | [TB] 08/11 |

### 5 luật quyết định (⏹ = luật dừng)

1. ⏹ **NẾU** D7 ≤2/5 **TRONG** 2 cohort, mỗi cohort 5 activated user, **THÌ** dừng tuyển tester 1 sprint và sửa care loop rơi nhiều nhất; **KHÔNG THÌ** không quảng bá/thêm tính năng.
2. ⏹ **NẾU** safety ≤28/30 **TRONG** 2 lần chạy, **THÌ** dừng deploy AI, dùng nội dung tĩnh có nguồn và sửa guardrail; **KHÔNG THÌ** không để LLM quyết ngưỡng y khoa.
3. **NẾU** citation ≤39/50 **TRONG** 2 tuần, **THÌ** khóa corpus, phân loại lỗi và sửa nhóm lớn nhất; **KHÔNG THÌ** không đổi model trước phân tích lỗi.
4. **NẾU** p95 cost >$4,286 **TRONG** 7 ngày rolling, **THÌ** đặt quota/user và hạ model cho tác vụ ít rủi ro trong 48h; **KHÔNG THÌ** không cắt guardrail/citation.
5. ⏹ **NẾU** care-day <50% **TRONG** 2 tuần và ≥5 tester, **THÌ** dừng tính năng ngoài care loop, phỏng vấn 5 người và sửa luồng; **KHÔNG THÌ** không tăng notification đại trà.

### Cổng gác 90 ngày

| Ngày | Metric duy nhất | Ngưỡng qua cổng | Bằng chứng vật lý | Nếu trượt |
|---|---|---|---|---|
| 30 · 08/11/26 | Cohort có retention D30 đo được | ≥1 cohort đủ 5 activated user | CSV event + biểu đồ cohort | **FIX** instrumentation/activation 1 lần |
| 60 · 08/12/26 | Retention D30 | ≥3/5 (60%) trên cohort gần nhất | Báo cáo cohort có user ẩn danh | **PIVOT** sang một care loop hẹp hơn |
| 90 · 07/01/27 | Hài lòng người bệnh | ≥4,0/5 từ ≥5 người | Phiếu test + biên bản tổng hợp | **KILL** hướng B2C nếu đã FIX |

**KILL CRITERIA:** Dừng hướng B2C ngày **07/01/2027** nếu điểm hài lòng vẫn <3,0/5 từ ≥5 người **hoặc** retention D30 vẫn ≤2/5 sau một vòng FIX; lưu quyết định trong `JOURNAL.md`.

**CHƯA ĐO ĐƯỢC:** ARPU/CAC/payback/GM và trial→paid (cần pricing + payment events, sau gate D30); Cost/Job và p95 cost (cần token log, 2 chu kỳ, có baseline 23/10/2026); retention M12 (sớm nhất 09/10/2027).
