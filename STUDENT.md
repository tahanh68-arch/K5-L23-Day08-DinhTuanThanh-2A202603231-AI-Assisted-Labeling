# Thông tin học viên

- Họ và tên: Đinh Tuấn Thành
- MSHV: 2A202603231
- Lớp (L2 / L3 / khác): L2
- Tài khoản GitHub: tahanh68-arch
- Repo bài nộp (URL): https://github.com/tahanh68-arch/K5-L23-Day08-DinhTuanThanh-2A202603231-AI-Assisted-Labeling
- Commit chốt bài: commit có thông điệp `complete` trên nhánh `main`; tra hash bằng `git log -1 --format=%H` hoặc trang Commits của repo. Không tự nhúng hash của chính commit vào nội dung file.
- Máy: Windows + Ubuntu/WSL, không có GPU (CPU only)
- CVAT (Docker): 2.81.0 — xác nhận qua API localhost:8080/api/server/about ngày 2026-11-18

## Kết quả tóm tắt (điền cuối bài; số lấy từ `submission/qc/*`, kèm mẫu số)

- Máy: accepted 444/507 = 87.6%; edited 26/507 = 5.1%; deleted 25/507 = 4.9% (507 box máy); added 45/517 = 8.7% (517 đối tượng trong profile; nguồn: submission/qc/review_stats.json, bản review55).
- So với gold: precision của **máy** 35/73 = 47.9%; recall 35/50 = 70.0% (4 ảnh gold, 50 đối tượng; TP 35, FP 38, FN 15).
- Bắt bẫy: vòng đầu 4/12 = 33.3%; sau sửa có phản hồi: review3 8/12; review44 10/12; bản cuối review55 11/12 = 91.7%, đạt 80%. Vòng sau không phải phép đo duyệt mù độc lập.
- Thời gian: gán tay 10/4 = 2.50 phút/ảnh; duyệt có pre-label vòng đầu 40/24 = 1.67 phút/ảnh. Chưa có thời gian sửa bổ sung của bản cuối.
- Lỗi QC: 6/7 ảnh = 85.7%; theo đối tượng ít nhất 8/145 = 5.5% (cận dưới vì nhóm vật sót chưa đếm đầy đủ; xem submission/qc/QC_LIMITATIONS.md).
- **Quyết định: Rework (2026-11-18)** — QC có lỗi ở 6/7 ảnh và bỏ sót lặp; đạt 11/12 bẫy không đồng nghĩa đủ chất lượng Release.
