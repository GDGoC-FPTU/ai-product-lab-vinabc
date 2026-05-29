# 03 — Nhật Ký Chiêm Nghiệm: AI Làm Thought-Partner Trong Buổi Học Day 02

**Học viên:** [Tên học viên]  
**Vai trò mô phỏng:** AI Engineer, Vin Smart Future (Vingroup)  
**Ngày:** 29/05/2026  
**Công cụ AI sử dụng:** Claude (Anthropic) — vai trò Thought-partner chính trong buổi học

---

## Phần 1 — AI Giúp Gì

### 1.1 Brainstorm pain point theo đúng domain

Nhiệm vụ đầu tiên tôi đặt ra cho Claude là: *"Tìm 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất cho VinFast — kèm con số thống kê ước tính về tổn thất."*

Claude không chỉ liệt kê danh sách chung chung. Nó đã tự áp dụng đúng khung **Discovery Interview** từ bài giảng Day 02 — mỗi pain point đều được trình bày với đủ 3 câu hỏi cốt lõi:

- **Pain point là gì?** (xảy ra bao nhiêu lần/ngày)
- **Workflow hiện tại ra sao?** (bước nào, ai hand-off cho ai)
- **Cost là gì?** (mất bao nhiêu phút, tiền, hay SLA)

Kết quả gồm 5 pain point có cấu trúc rõ: phân loại bảo hành, QC cuối dây chuyền, CSKH sau bán, dự báo phụ tùng tồn kho, và phân tích telematics xe điện. Đặc biệt, Claude tự phân loại đúng kiến trúc AI phù hợp cho từng bài toán theo AI-Fit Matrix (Rule → Workflow → LLM Feature → Agent) mà không cần tôi nhắc.

**Giá trị thực sự:** Tôi tiết kiệm được khoảng 40–50 phút tư duy ban đầu. Thay vì ngồi liệt kê từ đầu, tôi có ngay một "bản nháp chất lượng" để phản biện và tinh chỉnh.

### 1.2 Hỗ trợ cấu trúc hóa Problem Statement

Sau khi có danh sách pain point, tôi dùng Claude để viết thử Problem Statement hoàn chỉnh cho pain point #3 (CSKH sau bán). Claude lập tức áp dụng đúng khung **6 thành phần bắt buộc** từ bài giảng:

1. Actor / Operator
2. Current Workflow
3. Bottleneck
4. Business Impact
5. Success Metric
6. Operational Boundary

Điều tôi đánh giá cao: Claude chủ động thêm mục **Operational Boundary** — phần mà nhiều học viên hay bỏ quên — và tự đặt ra các ranh giới như *"AI không được tự ý hoàn tiền, không được tự ý hủy đơn"*. Đây chính xác là điểm then chốt mà bài giảng Day 02 nhấn mạnh về rủi ro Agent tự trị.

### 1.3 Giải thích khái niệm kỹ thuật khó

Khi tôi hỏi *"tại sao anomaly detection lại phù hợp hơn supervised learning cho bài toán telematics?"*, Claude đưa ra phân tích theo hai chiều thực tiễn:

- **Chiều dữ liệu:** Lỗi pin/motor là sự kiện hiếm (class imbalance nghiêm trọng), không có đủ labeled examples để train supervised model.
- **Chiều vận hành:** Anomaly detection không cần label — model học từ pattern bình thường rồi flagging deviation, phù hợp khi VinFast chưa có hệ thống gắn nhãn lỗi lịch sử.

Đây là loại reasoning tôi cần khi pitch với stakeholder không có nền kỹ thuật sâu.

---

## Phần 2 — AI Sai Gì

### 2.1 Hallucination về con số thống kê VinFast

Đây là điểm tôi phát hiện rõ nhất trong buổi làm việc.

Khi tôi hỏi: *"VinFast hiện có bao nhiêu xe đang lưu hành tại Việt Nam và Mỹ tính đến Q1/2026?"*, Claude trả lời với một con số cụ thể: khoảng **50.000 xe kết nối đang phát dữ liệu thực**. Nghe có vẻ hợp lý và tự tin.

Vấn đề: Đây là **ước tính suy diễn** từ dữ liệu training — không phải số liệu kinh doanh thực tế. VinFast không công bố số xe đang vận hành theo thời gian thực, và con số này có thể sai lệch đáng kể tùy thị trường, tùy thời điểm recall, tùy phiên bản OTA.

Nếu tôi đưa con số này vào báo cáo đề xuất dự án mà không kiểm chứng, tôi có thể mất uy tín ngay với stakeholder nội bộ đầu tiên biết số liệu thật.

**Bài học rút ra:** Với các con số kinh doanh nội bộ (doanh số, fleet size, SLA thực tế), AI không phải nguồn đáng tin. Tôi phải xem đây là *baseline ước tính để bắt đầu câu chuyện*, không phải *số liệu để đưa vào slide thuyết trình*.

### 2.2 Đề xuất giải pháp kỹ thuật quá phức tạp ở lần đầu

Khi tôi hỏi về giải pháp cho bài toán QC cuối dây chuyền (pain point #2), Claude lập tức đề xuất một kiến trúc đầy đủ: **Computer Vision pipeline với YOLOv8 + CLIP encoder + LLM-based defect explanation + human-in-the-loop dashboard**.

Về mặt kỹ thuật, đây là kiến trúc hợp lý. Nhưng theo AI-Fit Matrix trong bài giảng Day 02, đây là một Agent phức tạp — đòi hỏi cơ chế kiểm soát rủi ro cực kỳ nghiêm ngặt.

Claude đã vi phạm nguyên tắc cốt lõi: **"Luôn ưu tiên giải pháp từ trái sang phải. Chỉ chuyển sang Agent khi giá trị nghiệp vụ lớn hơn đáng kể so với chi phí và rủi ro."**

Đáng ra Claude nên hỏi: *"Hiện tại có hệ thống rule-based hay threshold-based nào đang chạy chưa? Baseline tỉ lệ phát hiện lỗi là bao nhiêu?"* — trước khi nhảy thẳng vào đề xuất Agent.

---

## Phần 3 — Sửa Đổi Ra Sao

### 3.1 Điều chỉnh prompt để ép AI dừng lại ở đúng mức kiến trúc

Với vấn đề kiến trúc quá phức tạp, tôi thêm ràng buộc rõ ràng vào prompt:

**Prompt gốc (dẫn đến hallucination kiến trúc):**
> "Đề xuất giải pháp AI cho bài toán QC dây chuyền VinFast."

**Prompt được sửa:**
> "Đề xuất giải pháp AI cho bài toán QC dây chuyền VinFast. **Yêu cầu:** (1) Bắt đầu từ mức đơn giản nhất có thể — Rule/Script trước, chỉ leo lên LLM Feature hoặc Agent nếu có lý do cụ thể. (2) Với mỗi mức, nêu rõ điều kiện tiên quyết để triển khai (data có sẵn, baseline đo được chưa?). (3) Không đề xuất Agent nếu chưa xác nhận được baseline thủ công."

Kết quả sau khi sửa: Claude đề xuất theo 3 giai đoạn tăng dần — bắt đầu từ **threshold-based rule** (độ lệch khe hở > ngưỡng X → reject), sau đó **Computer Vision supervised model** khi đã có 10.000+ ảnh labeled, cuối cùng mới là **LLM explanation layer**. Đây là lộ trình thực tiễn hơn nhiều.

### 3.2 Thêm boundary để ép AI phân biệt ước tính và dữ liệu thực

Với vấn đề hallucination con số, tôi bổ sung instruction vào đầu prompt:

> "Trong mọi câu trả lời, nếu bạn sử dụng con số thống kê (doanh số, fleet size, chi phí vận hành...), hãy **đánh dấu rõ** là: (a) Số liệu công bố chính thức, (b) Ước tính từ benchmark ngành, hoặc (c) Ước tính suy diễn — cần xác nhận với stakeholder nội bộ. Không được dùng con số tự suy diễn mà không có nhãn."

Sau khi thêm boundary này, Claude bắt đầu tự gắn tag vào từng con số — ví dụ: *"~50.000 xe (ước tính suy diễn từ báo cáo Q3/2025 — cần xác nhận)"*. Điều này giúp tôi biết ngay chỗ nào cần cross-check trước khi đưa vào tài liệu chính thức.

### 3.3 Dùng AI như một Devil's Advocate, không phải người phê duyệt

Bài học quan trọng nhất: tôi học cách dùng AI theo chiều **phản biện**, không chỉ chiều khẳng định.

Thay vì hỏi *"Pain point này có đáng làm không?"* (câu hỏi dễ dẫn đến AI đồng ý một chiều), tôi hỏi: *"Hãy chỉ ra 3 lý do tại sao dự án AI cho bài toán này có thể thất bại trong 6 tháng đầu."*

Claude lập tức đưa ra các phản biện sắc bén: thiếu baseline sạch, đội vận hành chưa sẵn sàng thay đổi workflow, chi phí labeling dữ liệu ảnh QC bị underestimate. Đây chính xác là framework **Go/No-Go Decision** mà bài giảng yêu cầu — và AI làm tốt vai trò này khi được frame đúng.

---

## Tổng Kết

Buổi học Day 02 đã thay đổi cách tôi nhìn nhận AI như một công cụ. Trước đây, tôi có xu hướng dùng AI để **lấy câu trả lời nhanh**. Sau buổi học này, tôi hiểu rằng giá trị thực sự của AI là ở chỗ giúp tôi **tư duy nhanh hơn** — nhưng quyết định vẫn phải là của người engineer.

Ba quy tắc tôi sẽ áp dụng từ nay:

1. **Luôn gắn ranh giới kiến trúc vào prompt** — "bắt đầu từ Rule/Script, chỉ leo lên Agent khi có lý do rõ ràng."
2. **Luôn yêu cầu AI phân biệt số liệu thực và ước tính** — không bao giờ dùng con số từ AI vào báo cáo mà chưa kiểm chứng.
3. **Dùng AI như Devil's Advocate** — hỏi "tại sao có thể thất bại?" song song với "tại sao nên làm?"

> *"Thất bại của sản phẩm AI hiếm khi đến từ bản thân mô hình. Nó xuất phát từ chuỗi quyết định thiết kế sai lầm ngay từ đầu."* — Day 02 Slide 2

Câu trích dẫn này cũng áp dụng cho việc dùng AI làm Thought-partner: không phải AI mạnh hay yếu — mà là engineer có biết đặt câu hỏi đúng hay không.

---

*Tổng thời gian tương tác với AI trong buổi học: ~2 giờ | Số lần phải sửa prompt: 4 lần | Số lần AI hallucinate đáng kể: 2 lần*