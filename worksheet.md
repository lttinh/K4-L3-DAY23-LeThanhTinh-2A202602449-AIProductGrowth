# Worksheet — VinGlucare Agent

**Họ tên:** Lê Thanh Tình · **MSSV:** 2A202602449 · **Ngày làm:** 09/10/2026

**Dữ liệu đầu vào đã có:** ngân sách OpenAI API tối đa **25 USD/5 tuần**; user test tối thiểu **5 người**; eval **50 câu/tuần**; **30 prompt nguy hiểm/tuần**. **Chưa có:** ARPU, GM, CAC, payback, runway và Cost/Job thực đo vì MVP chưa thương mại hóa.

## Trạm 1 — Loại mô hình

**Câu chốt loại:** VinGlucare hiện là **B2C** vì team đưa web app trực tiếp tới người bệnh đái tháo đường típ 2, chính người bệnh dùng năm tính năng hằng ngày; bác sĩ chỉ đọc dashboard hỗ trợ và hiện chưa có bệnh viện/đối tác trả tiền hay kiểm soát đường tới end-user.

| Đèn B2C trong Handbook §3.1 | Trạng thái | Số nằm ở đâu / cần gì để đo |
|---|---:|---|
| Đường cong retention D1/D7/D30/D60 | 🔧 | Event `care_action_completed`; cohort đầu 5 người, có D7 ngày 16/10, D30 ngày 08/11/2026 |
| Activation rate trong 24 giờ | 🔧 | Event `first_care_loop_completed`; baseline ngày 16/10/2026 |
| p95 cost/user/tháng ÷ ARPU | ❌ | Có thể log token cost; chưa có ARPU vì chưa mở gói trả phí. Tạm đo p95 USD/user-tháng |
| Trial → paid | ❌ | Cần định nghĩa gói/giá và sự kiện thanh toán; chưa lên lịch khi chưa qua gate retention |
| Retention M12 | ❌ | Cần 12 tháng cohort; sớm nhất 09/10/2027 |
| Chi phí free tier ÷ tổng COGS | 🔧 | Log `user_id`, model, input/output token và unit price; báo cáo đầu 16/10/2026 |
| Tỷ lệ refund | ❌ | Chưa có giao dịch; chỉ đo sau khi mở thanh toán |
| LTV/CAC · payback · GM | ❌ | Cần giá, giao dịch và chi phí acquisition; chỉ lập mô hình sau khi có retention D30 |

## Trạm 2 — Thẻ đèn

**North Star:** D7 retained care-loop users — hiện tại **chưa có baseline** — ngưỡng khởi tạo **≥4/5 người**, chốt lại sau 2 cohort ngày 23/10/2026.

| # | Tầng | Đèn | Định nghĩa chặt | Công thức | Nhịp · chủ số | Báo trước cho |
|---:|:---:|---|---|---|---|---|
| 1 | L | Activation 24h | Người mới hoàn tất trong 24h: nhập 1 số đường huyết **và** xác nhận 1 lịch thuốc; không tính tài khoản team/demo | User đạt care loop ÷ user mới hợp lệ | Ngày · PM/BA | Retention D7 |
| 2 | L | Retention D7 | User cohort có ≥1 care loop đúng ngày D7 (±1 ngày); không tính chỉ mở trang | User retained D7 ÷ user activated của cohort | Tuần · PM/BA | Tuân thủ ghi nhận 7 ngày |
| 3 | L | Safety-block pass rate | Prompt nguy hiểm bị chặn, không chẩn đoán/đổi liều; không tính lỗi hạ tầng | Prompt bị chặn đúng ÷ 30 prompt chuẩn | Tuần · AI lead | Hài lòng và độ tin cậy |
| 4 | O | Tuân thủ ghi nhận 7 ngày | Ngày có cả log đường huyết và xác nhận thuốc; không tính ngày chỉ mở app | Care days hợp lệ ÷ 7 ngày kỳ vọng/user | Tuần · PM/BA | Retention D30 |
| 5 | O | Citation correctness | Câu trả lời eval có trích dẫn và nguồn thực sự hỗ trợ ý chính; không tính citation chỉ đúng định dạng | Câu đạt ÷ 50 câu eval | Tuần · AI lead | Hài lòng và độ tin cậy |
| 6 | O | p95 AI cost/user-tháng | Phân vị 95 tổng chi phí model theo user trong 30 ngày; không tính CI/dev/eval nội bộ | p95(Σ token × đơn giá theo user/30 ngày) | Tuần · BE/DevOps | Cạn ngân sách, GM tương lai |
| 7 | O | Cost/Job | Chi phí model cho 1 phản hồi AI giúp user hoàn tất một care action; không tính request test/dev, request lỗi hoặc tác vụ không có phản hồi | Tổng chi phí token của job hợp lệ ÷ số job hợp lệ | Tuần · BE/DevOps | p95 cost/user-tháng |
| 8 | G | Điểm hài lòng | Trung bình câu “VinGlucare giúp tôi quản lý hằng ngày” thang 1–5 sau test; không tính team/mentor | Tổng điểm ÷ số người bệnh trả lời | Mỗi vòng test · PM/BA | Ý định tiếp tục dùng |

**Đèn chi phí AI:** số 6 và 7 — p95 AI cost/user-tháng và Cost/Job.

## Trạm 3 — Ngưỡng

| # | Đèn | 🟢 | 🟡 | 🔴 | Nguồn | Lý do |
|---:|---|---|---|---|---|---|
| 1 | Activation 24h | ≥4/5 (80%) | 3/5 (60%) | ≤2/5 (40%) | [TB] | Ngưỡng khởi tạo; PM/BA đo 2 cohort, mỗi cohort ≥5 user mới, rồi chốt baseline ngày 23/10/2026 |
| 2 | Retention D7 | ≥4/5 (80%) | 3/5 (60%) | ≤2/5 (40%) | [TB] | PM/BA đo 2 cohort, mỗi cohort ≥5 activated user; có baseline D7 ngày 23/10/2026 |
| 3 | Safety-block pass | 30/30 | 29/30 | ≤28/30 | [TB] | Ràng buộc an toàn nội bộ từ Charter; AI lead chạy 2 chu kỳ eval và xác nhận baseline ngày 23/10/2026, không hạ chuẩn 30/30 |
| 4 | Tuân thủ ghi nhận 7 ngày | ≥80% | 50–79% | <50% | [TB] | PM/BA lấy log của ≥5 tester trong 2 chu kỳ tuần và chốt baseline ngày 23/10/2026 |
| 5 | Citation correctness | ≥43/50 (≥85%) | 40–42/50 | ≤39/50 (<80%) | [TB] | Mốc nội bộ từ Charter; AI lead chạy cùng bộ 50 câu trong 2 tuần và chốt baseline ngày 23/10/2026 |
| 6 | p95 AI cost/user-tháng | ≤3,214 USD | >3,214–4,286 USD | >4,286 USD | [MH] | Suy từ Cost/Job tối đa 0,142857 USD × 30 job/tháng; mức xanh giữ buffer chi phí 25% |
| 7 | Cost/Job | ≤0,107 USD | >0,107–0,143 USD | >0,143 USD | [MH] | 25 USD chỉ đủ cho 175 job kế hoạch ở trần 0,1429 USD/job; mức xanh giữ buffer 25% |
| 8 | Điểm hài lòng | ≥4,0/5 | 3,0–3,9/5 | <3,0/5 | [TB] | Mốc nội bộ từ Charter; PM/BA đo ≥5 bệnh nhân ở 2 vòng test và chốt baseline ngày 08/11/2026 |

### Phụ lục [MH] — phép tính

**[MH] 1 — Cost/Job tối đa**

```text
Đầu vào: ngân sách API tối đa = 25 USD; pilot = 5 user; 5 tuần = 35 ngày;
tần suất thiết kế tối thiểu = 1 AI job/user/ngày.
Số job kế hoạch = 5 × 35 × 1 = 175 job.
Cost/Job tối đa = 25 ÷ 175 = 0,142857 ≈ 0,143 USD/job.
Mức xanh có buffer 25% = 0,142857 × 75% = 0,107143 ≈ 0,107 USD/job.
Kết quả → 🟢 ≤0,107 · 🟡 >0,107–0,143 · 🔴 >0,143 USD/job.
```

**[MH] 2 — p95 AI cost/user-tháng**

```text
Đầu vào: Cost/Job tối đa = 0,142857 USD; tần suất thiết kế = 1 job/user/ngày;
tháng quy ước = 30 ngày.
Trần cost/user-tháng = 0,142857 × 30 = 4,28571 ≈ 4,286 USD.
Mức xanh có buffer 25% = 4,28571 × 75% = 3,21428 ≈ 3,214 USD.
Kết quả → 🟢 ≤3,214 · 🟡 >3,214–4,286 · 🔴 >4,286 USD/user-tháng.
```

**Kiểm tra chéo mô hình chi phí**

```text
5 user × 35 ngày × 1 job/ngày × 0,142857 USD/job = 25 USD.
Hai ngưỡng [MH] dùng cùng một mô hình và khớp đúng trần ngân sách Charter.
```

## Trạm 4 — 5 luật quyết định

1. ⏹ **NẾU** retention D7 ≤2/5 **TRONG** 2 cohort liên tiếp **VÀ** mỗi cohort đủ 5 người đã activation, **THÌ** dừng tuyển tester mới trong 1 sprint và giao PM+FE sửa đúng luồng care loop gây rơi nhiều nhất, **KHÔNG THÌ** không quảng bá hay thêm tính năng mới để che churn.
2. ⏹ **NẾU** safety-block pass ≤28/30 **TRONG** 2 lần chạy liên tiếp, **THÌ** dừng deploy câu trả lời AI, chuyển sang nội dung tĩnh có nguồn và AI lead sửa guardrail trước lần release kế tiếp, **KHÔNG THÌ** không nới prompt hay để LLM tự quyết ngưỡng y khoa.
3. **NẾU** citation correctness ≤39/50 **TRONG** 2 tuần liên tiếp, **THÌ** AI lead khóa corpus, phân loại 11 lỗi đầu tiên theo retrieval/generation/source và sửa nhóm lớn nhất trong sprint, **KHÔNG THÌ** không đổi model hoặc tăng temperature trước khi có phân tích lỗi.
4. **NẾU** p95 AI cost/user-tháng >4,286 USD **TRONG** 7 ngày rolling, **THÌ** DevOps đặt quota theo user và chuyển tác vụ tóm tắt ít rủi ro sang model rẻ hơn trong 48 giờ, **KHÔNG THÌ** không cắt guardrail, citation hay tăng ngân sách trần.
5. ⏹ **NẾU** tuân thủ ghi nhận 7 ngày <50% **TRONG** 2 tuần liên tiếp **VÀ** có ≥5 tester hợp lệ, **THÌ** dừng phát triển tính năng ngoài care loop và chạy 5 phỏng vấn để sửa nhắc thuốc/nhập đường huyết trong sprint kế tiếp, **KHÔNG THÌ** không quy lỗi cho người cao tuổi hoặc tăng tần suất notification đại trà.

