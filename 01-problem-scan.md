Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

> [!TIP]
> **🤖 AI Prompts — Partner brainstorm:**
> Hãy sử dụng prompt sau để brainstorm các bài toán thực tế nếu bạn chưa có ý tưởng:
> *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng [Chọn một: VinFast / Xanh SM / Vinhomes / Vinmec]. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*

### 📝 List bài toán của tôi:
| # | Subsidiary (Vin Smart Future) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | VinFast | Lặp lại (Repetitive) | Phân loại và xử lý feedback/đánh giá khách hàng về xe điện từ nhiều kênh (Facebook, website, email) |
| 2 | VinFast | Tốn thời gian | Soạn thảo báo cáo chẩn đoán lỗi pin xe điện từ dữ liệu telemetry và log hệ thống |
| 3 | VinFast | AI-upgrade | Chatbot tư vấn khách hàng về các dòng xe, tính năng, giá cả và hỗ trợ đặt lịch lái thử |
| 4 | VinFast | Stakeholder Pain | Hệ thống dự đoán bảo trì pin xe điện chưa chính xác, khiến khách hàng bất ngờ về chi phí sửa chữa |
| 5 | VinFast | Tốn thời gian | Đối chiếu và xác minh hóa đơn sạc điện tại các trạm sạc với hệ thống thanh toán của khách hàng |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Phân loại và phản hồi tự động feedback   │
│                    khách hàng về xe điện VinFast             │
│ Công ty thành viên: [X] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH VinFast                 │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Thu thập feedback từ FB/web/email ──> 2. Đọc và phân   │
│   loại mức độ (khẩn/bình thường/khen) ──> 3. Chuyển đến     │
│   bộ phận xử lý phù hợp ──> 4. Soạn phản hồi ──> 5. Gửi    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2, 3, 4 (⏱ 10-15     │
│ phút/feedback, xử lý 80-120 feedback/ngày)                  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 (phân loại tự  │
│ động theo mức độ), Bước 4 (gợi ý draft phản hồi chuẩn mực)  │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   "Giảm thời gian xử lý từ 12 phút xuống 3 phút/feedback,   │
│    tăng khả năng xử lý từ 100 lên 250 feedback/nhân viên/ngày,│
│    đạt độ chính xác phân loại >90%"                         │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [X] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Tự động tạo báo cáo chẩn đoán lỗi pin xe │
│                    điện từ dữ liệu telemetry                 │
│ Công ty thành viên: [X] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Kỹ sư bảo trì và Kỹ thuật viên trạm    │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Nhận cảnh báo lỗi pin từ xe ──> 2. Tải dữ liệu log    │
│   telemetry ──> 3. Phân tích thủ công các chỉ số (điện áp,  │
│   nhiệt độ, chu kỳ sạc) ──> 4. Viết báo cáo chẩn đoán ──>   │
│   5. Gửi khuyến nghị sửa chữa cho khách hàng                │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 25-35 phút/ │
│ báo cáo, xử lý 15-20 case/ngày)                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 (phân tích dữ  │
│ liệu tự động, nhận diện pattern bất thường) và Bước 4 (tự   │
│ động soạn báo cáo với ngôn ngữ chuyên môn)                  │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   "Giảm thời gian chẩn đoán từ 30 phút xuống 8 phút, tăng   │
│    khả năng xử lý từ 18 lên 60 case/ngày, độ chính xác chẩn │
│    đoán đạt >85% so với kỹ sư senior"                       │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [X] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Chatbot tư vấn thông minh hỗ trợ khách   │
│                    hàng về sản phẩm xe và đặt lịch lái thử  │
│ Công ty thành viên: [X] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Khách hàng tiềm năng và Nhân viên sales│
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Khách hàng gọi hotline/đến showroom ──> 2. Tư vấn viên │
│   hỏi nhu cầu ──> 3. Giới thiệu các dòng xe phù hợp ──>     │
│   4. Đặt lịch lái thử ──> 5. Gửi thông tin qua email/Zalo   │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 1-3 (⏱ 15-20 phút/   │
│ khách, xử lý 20-30 khách/tư vấn viên/ngày)                  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Chatbot thay thế Bước │
│ 1-3 (tư vấn 24/7, so sánh dòng xe, tính chi phí), Bước 4    │
│ (tự động đặt lịch và đồng bộ với CRM)                       │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   "Xử lý 200+ yêu cầu tư vấn/ngày (tăng x10), giảm thời gian│
│    phản hồi từ 15 phút xuống <2 phút, tỷ lệ đặt lịch thành  │
│    công >70%, giảm tải cho nhân viên 60% câu hỏi cơ bản"    │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [ ] LLM  [X] Agent │
└─────────────────────────────────────────────────────────────┘
