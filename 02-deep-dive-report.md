# Báo cáo Deep-Dive — Nhóm VinABC (Xanh SM Bus / Vận hành xe buýt điện)

> Báo cáo phân tích sâu dự án AI mà nhóm đã thống nhất chọn, hoàn thiện **Phase 3 (DEEP-DIVE)** và **Phase 5 (EVALUATE)** từ `01-problem-scan.md`, trình bày theo chuẩn đầu ra của Vin Smart Future.

## Thông tin nhóm

**Tên nhóm:** VinABC

| #   | Họ và tên               |
| --- | ----------------------- |
| 1   | Phạm Thị Tuyết Nga      |
| 2   | Nguyễn Đức Toàn         |
| 3   | Nguyễn Thái Hoàng       |
| 4   | Nguyễn Ngô Huy Tùng Anh |

---

## 🏛️ Bối cảnh: Chúng tôi là ai?

Chúng tôi là nhóm **AI Engineer** tại **Vin Smart Future**, được giao phối hợp với Khối Vận Hành mảng **xe buýt điện (Xanh SM Bus)** để tìm kiếm cơ hội tối ưu hóa bằng trí tuệ nhân tạo.

Qua khảo sát thực địa tại Trung tâm Điều độ, nhóm nhận thấy một nỗi đau rõ rệt: **biểu đồ chạy (headway) được đặt cố định, ít điều chỉnh theo nhu cầu thực tế**. Hệ quả là xe **chạy rỗng giờ thấp điểm** (tốn km/điện vô ích) trong khi **quá tải giờ cao điểm** (mất khách, giảm trải nghiệm). Bài toán nhóm mang vào buổi Lab đến từ chính quan sát thực tế này.

---

# 🗳️ Quyết định lựa chọn của nhóm

Nhóm quyết định chọn bài toán **"Tối ưu biểu đồ chạy động (Dynamic Headway) cho mạng xe buýt điện Xanh SM Bus"** để thực hiện Deep-Dive.

## Lý do lựa chọn:

- **Impact cao:** Tác động trực tiếp lên cả 3 trục — **chi phí** (km/điện chạy rỗng), **doanh thu** (khách bỏ chuyến giờ cao điểm), và **trải nghiệm** (thời gian chờ).
- **Feasibility tốt:** Dữ liệu cần thiết đã có sẵn (lịch sử lên/xuống khách theo trạm × giờ, GPS thời gian thực, lịch chạy hiện tại, thời tiết & sự kiện) → đủ để dự báo nhu cầu và đề xuất điều chỉnh headway.
- **Rủi ro kiểm soát được:** AI chỉ **đề xuất**, người duyệt mới áp dụng → không gây gián đoạn vận hành.

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm)

## 3.1. Current-State Workflow

Quy trình lập & điều chỉnh biểu đồ chạy hiện tại của điều phối viên Xanh SM Bus (hoàn toàn thủ công, theo kinh nghiệm):

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Đặt headway  │     │ Vận hành     │     │ Phát hiện    │     │ Chỉnh tay    │
│ CỐ ĐỊNH cho  │ ──→ │ theo lịch    │ ──→ │ lệch tải     │ ──→ │ lịch (cảm    │
│ cả mùa/quý   │     │ cứng cả ngày │     │ (rỗng/quá tải)│     │ tính, trễ)  │
│ Ai: Điều phối│     │ Ai: Hệ thống │     │ Ai: Điều phối│     │ Ai: Điều phối│
│ ⏱ 1 lần/quý  │     │ ⏱ liên tục   │     │ ⏱ phản ứng 🔴│     │ ⏱ 30-60'/lần🔴│
│ In: KN cũ    │     │ In: Lịch cứng│     │ In: Khiếu nại│     │ In: Cảm tính │
│ Out: Lịch cứng│    │ Out: Chuyến  │     │ Out: Báo cáo │     │ Out: Lịch sửa│
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Lặp lại khi  │
                                                               │ có sự cố mới │
                                                               │ Ai: Điều phối│
                                                               │ ⏱ bị động    │
                                                               └──────────────┘
🔴 = Bottlenecks (phản ứng SAU khi đã lệch tải, không có dự báo trước)
⏱ Việc điều chỉnh chỉ xảy ra bị động, sau khi đã xảy ra quá tải/khiếu nại.
```

> _Lưu ý: Sơ đồ vẽ tay chi tiết của Phase 3.1 được nộp riêng trong file `04-workflow-diagram.png` theo yêu cầu._

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field                       | Nội dung                                                                                                                                                                                                                                                                                                                                                                                                     |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **1. Actor / Operator**     | **Điều phối viên vận hành** (Operations Dispatcher) của Xanh SM Bus — người lập và điều chỉnh biểu đồ chạy hằng ngày. (Hưởng lợi gián tiếp: hành khách & bộ phận tài chính.)                                                                                                                                                                                                                                 |
| **2. Current Workflow**     | Đầu mỗi mùa/quý, điều phối viên đặt headway **cố định** cho từng tuyến (ví dụ 10 phút/chuyến cả ngày, hoặc 2 mức thô cao/thấp điểm theo giờ hành chính). Lịch gần như **không đổi** nhiều tuần, bất kể mưa/sự kiện/biến động nhu cầu. Điều chỉnh (nếu có) làm **thủ công, theo cảm tính**, phản ứng _sau khi_ đã quá tải hoặc có khiếu nại.                                                                  |
| **3. Bottleneck**           | Bước 3 & 4: Không có cơ chế **dự báo nhu cầu theo trạm × giờ** và **đề xuất headway tối ưu** kịp thời. Việc chỉnh lịch phụ thuộc trí nhớ/kinh nghiệm cá nhân, không tận dụng dữ liệu lịch sử + thời tiết + sự kiện; điều phối viên không đủ thời gian tính lại lịch cho hàng chục tuyến mỗi ngày (30–60 phút/lần chỉnh).                                                                                     |
| **4. Business Impact**      | (1) **Chi phí lãng phí:** xe rỗng giờ thấp điểm → ước tính lãng phí ~10–20% số km vận hành (tốn điện, hao pin, khấu hao, nhân sự). (2) **Mất doanh thu:** quá tải giờ cao điểm → mất ~5–15% lượt khách tiềm năng do khách bỏ chuyến. (3) **Giảm trải nghiệm & uy tín:** chờ lâu, chen chúc → khiếu nại, giảm gắn bó.                                                                                         |
| **5. Success Metric**       | 1. Giảm **km/kWh chạy rỗng** giờ thấp điểm ≥ **15%** (Cost).<br>2. Giảm **tỷ lệ chuyến quá tải** (load factor > 90%) giờ cao điểm ≥ **20%** (Quality).<br>3. Tỷ lệ đề xuất AI được điều phối viên chấp nhận ≥ **70%** (Adoption). Đo qua A/B test (tuyến áp dụng vs. đối chứng) trong 4–6 tuần.                                                                                                              |
| **6. Operational Boundary** | AI được phép: truy xuất dữ liệu lịch sử/GPS/thời tiết, dự báo nhu cầu, **soạn đề xuất điều chỉnh headway dạng nháp** trong khung cho phép (headway ∈ [5, 20] phút). **CẤM:** AI **không** tự áp dụng lịch khi chưa có điều phối viên duyệt (bắt buộc HITL); **không** đổi tuyến/điểm dừng; **không** vi phạm ràng buộc cứng (số xe & tài xế khả dụng, ca làm/giờ nghỉ theo luật, dung lượng pin & lịch sạc). |

---

## 3.3. Future-State Flow & AI Fit

- **AI Fit:** Chọn **LLM Feature + Rule** (chưa cần Agent tự trị hoàn toàn). Phần dự báo & sinh đề xuất dùng AI; phần ràng buộc cứng (pin, ca tài xế, số xe) dùng Rule tất định để đảm bảo an toàn. Có thể tiến hóa lên **Agentic Loop** ở bước giám sát khi hệ thống đã tin cậy.
- **Quy trình tương lai (Future-State):**

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Auto thu thập│     │ 🔵 AI dự báo │     │ 🔵 AI draft  │     │ ⚙️ Rule check│
│ dữ liệu (15'/│ ──→ │ nhu cầu trạm │ ──→ │ đề xuất      │ ──→ │ ràng buộc    │
│ lần): lịch sử│     │ × giờ (T+1..3h)│   │ headway + lý │     │ cứng (xe,    │
│ GPS, thời tiết│    │              │     │ do + tác động│     │ tài xế, pin) │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                          ┌────────────────────┐
                                                          │ Bước 5             │
                                                          │ 🟢 HITL — Điều phối│
                                                          │ viên DUYỆT/SỬA/    │
                                                          │ TỪ CHỐI đề xuất    │
                                                          └────────────────────┘
                                                            │ duyệt      │ từ chối
                                                            ▼            ▼
                                                   ┌──────────────┐  (giữ lịch cũ
                                                   │ Bước 6       │   + ghi feedback
                                                   │ Áp dụng lịch │   để cải thiện)
                                                   │ → hệ điều độ │
                                                   └──────────────┘
                                                            │
                                                            ▼
                                                   🔁 Bước 7: Giám sát & học lại
                                                   (đo metric, feedback loop)

🔵 = AI Step (LLM Feature)   ⚙️ = Rule   🟢 = Human-in-the-loop   🔁 = Agentic loop nhẹ
↩️ Fallback: Nếu AI lỗi/không phản hồi → hệ thống TỰ ĐỘNG GIỮ biểu đồ cố định
   hiện tại (baseline an toàn), gắn cờ "độ tin cậy thấp", cảnh báo đội kỹ thuật.
   Nếu thiếu dữ liệu → hạ cấp về đề xuất theo trung bình lịch sử (rule).
```

### Phân loại mức độ ứng dụng AI theo từng bước

| Bước                    | Mức AI                  | Giải thích                                                          |
| ----------------------- | ----------------------- | ------------------------------------------------------------------- |
| 1. Thu thập dữ liệu     | **Rule (ETL)**          | Lấy & làm sạch dữ liệu định kỳ — logic cố định.                     |
| 2. Dự báo nhu cầu       | **LLM Feature / Model** | Dự báo lượng khách theo trạm × giờ tới.                             |
| 3. Sinh đề xuất headway | **LLM Feature**         | Tổng hợp dự báo → đề xuất headway kèm lý do & tác động ước tính.    |
| 4. Kiểm ràng buộc cứng  | **Rule**                | If-then thuần: xe, ca tài xế, ngưỡng pin — tất định, không giao AI. |
| 5. Phê duyệt            | **Human-in-the-loop**   | Con người ra quyết định cuối (bắt buộc).                            |
| 6. Áp dụng              | **Rule**                | Đẩy lịch đã duyệt xuống hệ điều độ.                                 |
| 7. Giám sát & học lại   | **Agentic Loop (nhẹ)**  | Đo kết quả → so dự báo → đề xuất lại định kỳ.                       |

---

# 🏁 Phase 5 — EVALUATE (Nhóm)

## AI Readiness Checklist

| Tiêu chí                                                     | Đánh giá            | Ghi chú                                                                                                                                                     |
| ------------------------------------------------------------ | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1. Có sẵn dữ liệu mẫu/logs sạch để test?                     | ✅ **Có**           | Lịch sử lên/xuống khách theo trạm × giờ, GPS thời gian thực, lịch chạy, thời tiết & sự kiện — đủ để dự báo & đề xuất. Cần kiểm tra chất lượng dữ liệu trạm. |
| 2. Rủi ro khi AI sai có trong tầm kiểm soát (HITL/Fallback)? | ✅ **Có**           | AI chỉ đề xuất; người duyệt mới áp dụng; Fallback giữ lịch baseline khi lỗi; ràng buộc cứng chặn bằng rule → rủi ro thấp.                                   |
| 3. Bài toán đủ hẹp & đo được?                                | ✅ **Có**           | Chỉ đề xuất headway cho 2–3 tuyến pilot, khung 5:00–23:00, không đổi tuyến. Metric định lượng + A/B test.                                                   |
| 4. Stakeholders sẵn sàng đổi quy trình cũ?                   | ⚠️ **Cần xác nhận** | Điều phối viên cần được đào tạo dùng màn hình duyệt; cần buy-in từ Khối Vận Hành (giai đoạn pilot có kiểm soát giúp giảm e ngại).                           |
| 5. ROI dương?                                                | ✅ **Dự kiến có**   | Tiết kiệm điện/km rỗng + giữ khách giờ cao điểm > chi phí xây dựng & vận hành (xem ước lượng dưới).                                                         |

## Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future

> ✅ **GO (Bắt đầu xây dựng Prototype với scope hẹp — pilot 2–3 tuyến).**

### Justification (Lý giải dựa trên bằng chứng kỹ thuật & chi phí)

**Luận điểm kỹ thuật:**

1. **Khả thi dữ liệu:** Toàn bộ tín hiệu đầu vào đã sẵn có; dự báo nhu cầu trạm × giờ là bài toán chuỗi thời gian quen thuộc, có baseline rõ để so sánh.
2. **Rủi ro được kiểm soát:** Kiến trúc "AI đề xuất → người duyệt → áp dụng" + Fallback giữ lịch baseline + ràng buộc cứng bằng rule → rủi ro vận hành thấp, không gây gián đoạn.
3. **Phạm vi hẹp, đo được:** Pilot 2–3 tuyến với metric định lượng + A/B test → chứng minh giá trị nhanh trước khi mở rộng.
4. **Mở rộng dần:** Bắt đầu ở mức Rule + LLM Feature; khi tỷ lệ chấp nhận & độ tin cậy đạt mục tiêu, nâng dần lên Agentic Loop.

**Ước lượng chi phí (pilot ~3 tháng, 2–3 tuyến):**

| Hạng mục                     | Ước lượng             | Ghi chú                                                                               |
| ---------------------------- | --------------------- | ------------------------------------------------------------------------------------- |
| Pipeline dữ liệu (ETL)       | ~2–3 tuần-người       | Kết nối nguồn có sẵn, làm sạch.                                                       |
| Model dự báo + logic đề xuất | ~3–4 tuần-người       | Model chuỗi thời gian + rule ràng buộc + lớp LLM giải thích.                          |
| Giao diện duyệt (HITL)       | ~2 tuần-người         | Màn hình đề xuất/lý do/tác động, duyệt-sửa-từ chối.                                   |
| Chi phí gọi LLM (vận hành)   | Thấp                  | Gọi 15–30'/tuyến, prompt ngắn → token nhỏ; dự báo nặng dùng model chuyên biệt rẻ hơn. |
| Hạ tầng & giám sát           | ~1 tuần-người + cloud | Log, cảnh báo, đo A/B.                                                                |
| **Tổng nhân lực pilot**      | **~8–10 tuần-người**  | 1–2 kỹ sư trong ~2–2.5 tháng.                                                         |

**Lợi ích kỳ vọng:** Giảm 15% km rỗng giờ thấp điểm + giảm 20% chuyến quá tải giờ cao điểm trên tuyến pilot → tiết kiệm điện/khấu hao + giữ doanh thu. Với mạng buýt điện quy mô đô thị, khoản tiết kiệm này dự kiến **hoàn vốn chi phí pilot trong vài tháng sau khi mở rộng** → ROI dương, củng cố quyết định **GO**.

**Điều kiện kèm theo để GO an toàn:**

- Giữ Human-in-the-loop trong toàn bộ giai đoạn pilot.
- Định nghĩa rõ baseline & cơ chế A/B trước khi chạy.
- Luôn có Fallback giữ lịch cố định; theo dõi tỷ lệ chấp nhận đề xuất ≥ 70% như cổng quyết định mở rộng.

---

## 🏁 Kết luận từ buổi Lab

Dự án đạt mức **GO**: bài toán cụ thể, metric có số rõ ràng, giải pháp công nghệ vừa sức (LLM Feature + Rule), rủi ro được kiểm soát chặt qua HITL + Fallback, và ROI dự kiến dương.
