### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 |Xanhsm |Lặp lại |So khớp và phân bổ lại cuốc xe khi khách hàng thay đổi điểm đón/điểm đến giữa chuyến (xảy ra hàng nghìn lần/ngày, dispatch phải route lại thủ công). |
| 2 |VinFast |Tốn thời gian |Kỹ thuật viên service center phải đọc bus log và tra manual để chẩn đoán lỗi EV (mất ~20–45 phút/xe). |
| 3 |Vinhomes |Pain từ người khác |Cư dân phàn nàn thời gian xử lý ticket bảo trì quá chậm vì complaint bị route sai team vận hành/kỹ thuật. |
| 4 |VinMec |AI tốt hơn |Tổng đài chăm sóc bệnh nhân chưa thể tự động tóm tắt bệnh sử và điều hướng đúng chuyên khoa từ cuộc gọi đầu vào. |
| 5 |VinFast |Lặp lại |So khớp hóa đơn sạc điện và đối soát dữ liệu charging station với đối tác vận hành hằng ngày (hàng trăm nghìn session/ngày). |

---

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Tự động phân loại và route complaint cư   │
│ dân đúng team kỹ thuật/vận hành để giảm SLA xử lý ticket.   │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ - Cư dân                                                    │
│ - CSKH                                                      │
│ - Team vận hành/kỹ thuật                                    │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Cư dân gửi complaint                                   │
│      ──> 2. CSKH đọc ticket                                 │
│      ──> 3. Manual classify issue                           │
│      ──> 4. Route đúng team                                 │
│      ──> 5. Theo dõi SLA                                    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                            │
│ - Manual classify + route ticket                            │
│ - ⏱ ~5–12 phút/ticket                                      │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                       │
│ - Intent classification                                     │
│ - Urgency detection                                         │
│ - Auto routing                                              │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ - Giảm routing time từ 10 min -> <1 min                     │
│ - Giảm wrong-routing tickets 40–60%                         │
│ - Giảm average SLA xử lý 20–30%                             │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM [x] Agent  │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Tổng đài AI tự động tóm tắt bệnh sử và    │
│ điều hướng bệnh nhân đúng chuyên khoa ngay từ cuộc gọi đầu. │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ - Bệnh nhân                                                 │
│ - Call center agent                                         │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Patient gọi hotline                                    │
│      ──> 2. Hỏi triệu chứng                                 │
│      ──> 3. Ghi chú thủ công                                │
│      ──> 4. Chuyển chuyên khoa                              │
│      ──> 5. Re-explain nhiều lần                            │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                            │
│ - Hỏi về các dấu hiệu/triệu chứng bệnh                      │
│ - ~7–15 phút/cuộc gọi                                       │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                       │
│ - Speech-to-text                                            │
│ - Medical summarization                                     │
│ - Specialty routing                                         │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ - Giảm average call handling 15 min -> <5 min               │
│ - Giảm call transfer rate                                   │
│ - Tăng booking conversion                                   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM [x] Agent  │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Tự động đối soát charging session và hóa  │
│ đơn sạc điện với đối tác vận hành để giảm mismatch thủ công │
│                                                             │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ - Bộ phận vận hành tài chính (Finance Ops)                  │
│ - Bộ phận vận hành trạm sạc                                 │
│ - Bộ phận chăm sóc khách hàng                               │
│                                                             │
│ Quy trình thủ công hiện tại (3-5 bước):                     │
│   1. Thu thập log/dữ liệu các phiên sạc                     │
│      ──> 2. Xuất dữ liệu hóa đơn thanh toán                 │
│      ──> 3. So sánh các phiên bị sai lệch                   │
│      ──> 4. Kiểm tra thủ công các trường hợp bất thường     │
│      ──> 5. Xác nhận đối soát với đối tác vận hành          │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                            │
│ - Manual anomaly review                                     │
│ - ~5–15 phút/case                                           │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                            │
│ - Kiểm tra thủ công các trường hợp bất thường               │
│ - ~5–15 phút/trường hợp                                     │
│                                                             │
│ AI có thể hỗ trợ ở bước nào?                                │
│ - Phát hiện bất thường                                      │
│ - Phát hiện gian lận                                        │
│ - Dự đoán lỗi/sự cố trạm sạc                                │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ - Giảm 70–80% khối lượng công việc đối soát thủ công        │
│ - Giảm 50% số lượng billing mismatch                        │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [ ] LLM [x] Agent  │
└─────────────────────────────────────────────────────────────┘
```