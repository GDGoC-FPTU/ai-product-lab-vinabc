### 📝 List bài toán của tôi:

### PHARSE 1

| #   | Subsidiary (VinFast/Xanh SM...) | Lens       | Mô tả ngắn bài toán                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| --- | ------------------------------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Vinbus                          | Cost       | Hiện trạng thủ công: Biểu đồ chạy cố định, ít điều chỉnh theo nhu cầu thực => Dẫn tới xe rỗng giờ thấp điểm (tốn km/điện vô ích) + quá tải giờ cao điểm (mất khách, giảm trải nghiệm) => Hướng AI: Forecasting nhu cầu hành khách + tối ưu headway động.                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| 2   | VinPearl                        | Cost       | Vận hành điều hoà/điện các phòng khách sạn phòng khi vẫn làm mát => Tổn thất: Năng lượng chiếm ~25–30% opex vận hành, HVAC chiếm phần lớn; tối ưu thông minh có thể giảm 10–20% điện năng + giảm downtime hỏng hóc đột xuất. => Hướng AI: Tối ưu HVAC theo cảm biến                                                                                                                                                                                                                                                                                                                                                                                                                      |
| 3   | VinPearl                        | Experience | Hiện trạng thủ công: Mọi yêu cầu trong kỳ nghỉ của khách (xin thêm khăn, đặt bàn nhà hàng, book spa/shuttle, hỏi giờ hoạt động VinWonders, đổi phòng…) đều dồn về lễ tân/tổng đài qua điện thoại hoặc xếp hàng tại quầy, xử lý thủ công và phân tán nhiều bộ phận => Tổn thất: Giờ cao điểm (check-in 14h, sáng sớm đi tour) khách chờ lâu/máy bận → request rơi rớt, phản hồi chậm, đa ngôn ngữ (khách Hàn/Trung/Nga) khó đáp ứng. Trải nghiệm chờ đợi là một trong các nguyên nhân rớt điểm hài lòng (guest score) hàng đầu => Hướng AI: Trợ lý ảo đa ngôn ngữ (LLM) trên app/Zalo/in-room — tự động trả lời FAQ, nhận & định tuyến request đến đúng bộ phận, theo dõi trạng thái SLA; |
| 4   | Vinfast                         | Experience | Hiện trạng thủ công: Hỗ trợ khách trong lúc sử dụng xe & trạm sạc (xe báo lỗi, tìm/đặt trụ sạc, sự cố hết pin giữa đường) xử lý qua tổng đài cứu hộ thủ công, phán đoán theo kinh nghiệm. => Tổn thất: Khách hoảng khi hết pin/lỗi xe mà chờ tổng đài lâu, điều phối cứu hộ chậm → trải nghiệm sở hữu EV xấu, lan truyền tiêu cực Hướng AI: Co-pilot điều phối sự cố chuẩn đoán nhanh, gợi ý trạm sạc trong tầm pin an toàn, tự kích hoạt điều xe sạc lưu động khi pin tới hạn                                                                                                                                                                                                           |
| 5   | Xanh SM                         | Experience | Hiện trạng thủ công: Ghép tài xế – khách & ước tính thời gian chờ (ETA) chủ yếu theo khoảng cách/quy tắc đơn giản; giờ cao điểm hoặc khu vực lệch cung-cầu phải điều phối thủ công. => Tổn thất: Khách chờ lâu, ETA không chính xác, hủy chuyến vì đợi → mất chuyến & giảm hài lòng. => Hướng AI: Dispatch/matching tối ưu theo dự báo cung-cầu real-time + ETA chính xác hơn, cân bằng đội xe theo vùng.                                                                                                                                                                                                                                                                                |

---

### PHARSE 2

## PROBLEM CARD 1 — Tối ưu lịch chạy xe buýt động

| Trường                 | Nội dung                                                                |
| ---------------------- | ----------------------------------------------------------------------- |
| **Tên bài toán**       | Forecasting nhu cầu hành khách & tối ưu headway (giãn cách chuyến) động |
| **Công ty thành viên** | **VinBus**                                                              |
| **Lens**               | Cost                                                                    |

**Tác nhân gặp khó (Actor/Operator)**

- **Nhân viên điều độ (Dispatcher)** tại trung tâm điều hành — người lập & chỉnh biểu đồ chạy xe.
- Tài xế (chịu tải giờ cao điểm), hành khách (chờ lâu/lỡ chuyến).

**Sơ đồ quy trình thủ công hiện tại**

```
Lập biểu đồ cố định theo kinh nghiệm
        ↓
Áp dụng cứng cả ngày (headway cố định)
        ↓
Quan sát thực tế qua camera/báo cáo tài xế
        ↓
Điều chỉnh thủ công (chậm, theo cảm tính) → quá trễ
```

**Bước tốn thời gian / gây lỗi nhiều nhất**

- **Bước "Điều chỉnh thủ công theo quan sát"**: dispatcher phản ứng sau khi sự cố đã xảy ra (xe đã rỗng/đã quá tải). Một lần rà soát & ra quyết định tăng/giảm chuyến mất **~15–30 phút**, và thường trễ so với nhu cầu thực → quyết định lỗi thời.

**Bước AI tham gia**

- AI **dự báo nhu cầu hành khách** theo tuyến/khung giờ/thời tiết/sự kiện (forecasting) → tự động đề xuất **headway tối ưu** cho từng khung giờ, cảnh báo sớm điểm quá tải/rỗng để điều phối _trước_ khi xảy ra.

**Metric đo thành công (con số cụ thể)**

- Giảm **km chạy rỗng giờ thấp điểm 15–20%** (tiết kiệm điện/khấu hao).
- Giảm thời gian chờ trung bình giờ cao điểm **từ ~12 phút xuống dưới 6 phút**.
- Thời gian ra quyết định điều chỉnh **từ 15–30 phút xuống dưới 1 phút** (tự động đề xuất).

**Kiến trúc sơ bộ**

- **Rule + ML Forecasting (không cần LLM).** Mô hình time-series (XGBoost/Prophet/LSTM) dự báo lượng khách → lớp tối ưu (rule/optimization) sinh headway → dashboard gợi ý cho dispatcher duyệt. _Mức: No-AI → Rule + ML._

---

## PROBLEM CARD 3 — Trợ lý ảo đa ngôn ngữ cho khách nghỉ dưỡng

| Trường                 | Nội dung                                                       |
| ---------------------- | -------------------------------------------------------------- |
| **Tên bài toán**       | Trợ lý ảo đa ngôn ngữ tiếp nhận & định tuyến yêu cầu của khách |
| **Công ty thành viên** | **VinPearl**                                                   |
| **Lens**               | Experience                                                     |

**👤 Tác nhân gặp khó (Actor/Operator)**

- **Lễ tân / nhân viên tổng đài (Front-desk & Call-center agent)** — đầu mối nhận mọi request.
- Khách quốc tế (Hàn/Trung/Nga) gặp rào cản ngôn ngữ.

**Sơ đồ quy trình thủ công hiện tại**

```
Khách gọi điện / xếp hàng tại quầy
        ↓
Lễ tân nghe & ghi yêu cầu (đa ngôn ngữ → khó)
        ↓
Phân loại thủ công → gọi/nhắn đúng bộ phận (spa, F&B, shuttle…)
        ↓
Theo dõi thủ công, khách hỏi lại nhiều lần → request rơi rớt
```

**Bước tốn thời gian / gây lỗi nhiều nhất**

- **Bước "Tiếp nhận + phân loại + định tuyến thủ công"** vào giờ cao điểm (check-in 14h, sáng đi tour): máy bận, hàng dài. Mỗi request mất **~8–10 phút** chờ + xử lý; tỷ lệ rơi rớt/đáp ứng chậm cao, nhất là với khách ngoại ngữ.

**Bước AI tham gia**

- **Trợ lý ảo LLM đa ngôn ngữ** trên app/Zalo/in-room: tự trả lời FAQ (giờ hoạt động VinWonders, dịch vụ…), nhận request bằng tiếng mẹ đẻ của khách, **tự định tuyến đến đúng bộ phận** và theo dõi trạng thái SLA.

**Metric đo thành công (con số cụ thể)**

- Thời gian phản hồi request **từ 8–10 phút xuống dưới 2 phút**.
- **Tự động xử lý ≥ 60% FAQ** không cần con người.
- Giảm tỷ lệ request rơi rớt **xuống dưới 5%**; tăng guest satisfaction score **+0.3–0.5 điểm**.

**Kiến trúc sơ bộ**

- **LLM + Agent (RAG + tool-calling).** LLM đa ngôn ngữ + RAG trên tài liệu dịch vụ resort; lớp Agent gọi API các hệ thống bộ phận (spa booking, F&B, housekeeping) & tra cứu trạng thái SLA. _Mức: LLM → Agent._

---

## PROBLEM CARD 5 — Tối ưu ghép tài xế & ETA real-time

| Trường                 | Nội dung                                                      |
| ---------------------- | ------------------------------------------------------------- |
| **Tên bài toán**       | Dispatch/matching tối ưu theo dự báo cung–cầu & ETA chính xác |
| **Công ty thành viên** | **Xanh SM**                                                   |
| **Lens**               | Experience                                                    |

**Tác nhân gặp khó (Actor/Operator)**

- **Nhân viên điều phối khu vực (Fleet/Dispatch operator)** — điều xe thủ công khi cung–cầu lệch.
- Tài xế (chạy rỗng tìm khách), khách (chờ lâu, ETA sai).

**Sơ đồ quy trình thủ công hiện tại**

```
Khách đặt chuyến
        ↓
Hệ thống ghép theo khoảng cách/quy tắc đơn giản
        ↓
ETA tính theo khoảng cách thẳng (bỏ qua kẹt xe/cung-cầu)
        ↓
Giờ cao điểm/vùng lệch cung-cầu → điều phối viên can thiệp thủ công
        ↓
Khách chờ lâu → hủy chuyến
```

**Bước tốn thời gian / gây lỗi nhiều nhất**

- **Bước "Ghép tài xế + ước tính ETA"** giờ cao điểm: quy tắc khoảng cách đơn giản chọn sai tài xế, ETA lệch thực tế. Điều phối viên xử lý thủ công vùng lệch cung-cầu mất **~5–8 phút/khu vực**, trong khi khách chỉ chờ được **~2–3 phút trước khi hủy**.

**Bước AI tham gia**

- **Dispatch/matching tối ưu**: dự báo cung–cầu real-time theo vùng, ghép tài xế–khách tối ưu (tối thiểu chờ + cân bằng đội xe), **ETA chính xác** dựa trên giao thông thực tế; tự cân bằng đội xe sang vùng thiếu cung.

**Metric đo thành công (con số cụ thể)**

- Sai số ETA **từ ±5 phút xuống dưới ±1.5 phút**.
- Thời gian chờ ghép chuyến trung bình **từ ~4 phút xuống dưới 2 phút**.
- Giảm **tỷ lệ hủy do chờ lâu 25–30%**; tăng tỷ lệ ghép thành công giờ cao điểm.

**Kiến trúc sơ bộ**

- **ML + Optimization (không cần LLM).** Mô hình dự báo cung–cầu (time-series/ML) + thuật toán matching (bipartite/Hungarian/RL) + ETA model dùng dữ liệu giao thông real-time. _Mức: Rule → ML + Optimization (Agent điều phối tự động)._
