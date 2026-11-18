# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603231 - Đinh Tuấn Thành

Chỉ ghi phần bổ sung và ca áp dụng v1.0. Người làm đã kiểm tra và chấp nhận ba quyết định ngày 2026-11-18; đối chiếu `submission/qc/calibration_decisions.csv`. Chốt quy tắc không đồng nghĩa mọi nhãn hiện tại đã được sửa theo quy tắc.

## 1. Câu hỏi mở đã chốt (từ mục 6 của v1.0)

| # | Câu hỏi | Quyết định | Ảnh ví dụ | `case_id` |
|---|---|---|---|---|
| 5 | Kính chắn gió/cửa sổ có thuộc mask xe không? | Kính thuộc mask xe, kể cả khi nhìn xuyên qua thấy nội thất hoặc người. Khoảng hở không kính nhìn ra nền phải loại nếu lớn hơn khoảng 5% diện tích vật; lỗ nhỏ có thể bỏ qua theo mục 4.2.2 v1.0. | img_013 | CAL-02 |

Bổ sung làm rõ mục 4.2.3 và câu hỏi 6.5; không đổi ngưỡng lỗ nhỏ của v1.0. Các câu hỏi mở 1, 2, 3, 4, 6, 7 chưa chốt trong vòng này. Nếu lô có ca liên quan chưa giải quyết, phải ghi nhận khi quyết định QC.

## 2. Ca khó mới (từ lỗi của máy, bẫy lọt, box `can_review`)

| Ảnh | Tình huống | Máy làm gì (model nào) | Quyết định của bạn | Quy tắc mới (vào mục nào của v1.0) |
|---|---|---|---|---|
| img_013 | Mask bám hình không bảo đảm detector tìm đủ xe hoặc có lớp đúng | Lượt thử YOLO26 + SAM chưa thấy polygon bao toàn bộ tuk-tuk; cần kiểm tra nhãn shape riêng trong Objects | Kiểm tra lớp độc lập với mask; tuk-tuk là three_wheeler; thêm nhãn sót hoặc sửa lớp sai | CAL-01: áp dụng mục 2 v1.0; không tạo định nghĩa lớp mới |
| img_004, img_017 | Tách người lái khỏi mask xe | SAM local nhận click dương/âm; cần kiểm tra nguy cơ lấy lẫn người và xe | Người là đối tượng riêng; dùng click âm để loại khỏi mask xe; nếu chưa sạch thì chỉnh biên thủ công | CAL-03: áp dụng mục 2 và 4.2.4 v1.0; không phải quy tắc ngữ nghĩa mới |

Minh hoạ: [img_004](../masks/sam_local_img004.png), [img_013](../masks/sam_local_img013.png), [img_017](../masks/sam_local_img017.png). Các ảnh là bằng chứng thao tác SAM local trong CVAT, không xác nhận mọi mask đã đạt quy tắc mới.

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do (ca nào, `case_id`) |
|---|---|---|
| v1.0 | Bản giảng viên | — |
| v1.1 | Làm rõ kính xe so với khoảng hở không kính; giữ quy tắc lỗ nhỏ | CAL-02, câu hỏi 6.5, img_013 |
| v1.1 | Ghi ca áp dụng lại quy tắc lớp và tách người/xe, không đổi định nghĩa | CAL-01, CAL-03 |
