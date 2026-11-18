# Phạm vi QC và cách đọc số liệu

- Bản được kiểm: review55.zip, chuyển CVAT XML sang COCO; 24 ảnh, 550 đối tượng gồm ignore_region.
- Mẫu seed 42: 7 ảnh, 145 đối tượng không tính ignore; nội dung của cả 7 ảnh mẫu không đổi so với bản người dùng đã kiểm. ID box được đối chiếu trước khi dùng lại nhận xét.
- Người dùng kiểm 7 ảnh và báo lỗi trong hội thoại. Codex đối chiếu ảnh và file COCO; bổ sung lỗi hình học của box 129 ở img_013. Không sửa nhãn để che lỗi.
- Các mã MISSING-... là mã tham chiếu trong phiếu QC cho vật chưa có annotation, không phải ID của nhãn trong COCO.
- Do số xe bị sót ở img_003, img_022, img_033 chưa được đếm đầy đủ, mỗi nhóm báo sót chỉ ghi cận dưới 1 đối tượng. Vì vậy số lỗi theo đối tượng trong qc_report_auto.md là **cận dưới**, không phải tỷ lệ chính xác. Không suy ra số vật sót thực từ số dòng nhóm.
- Một số lỗi chưa phân loại hoặc ID chưa khớp được giữ ở qc_unresolved.csv, không chuyển thành ảnh sạch và không cộng bừa vào thống kê. Box 11 ở img_003 chỉ tính một lần là thừa/trùng.
- Mẫu có lỗi tại 6/7 ảnh; 035 là ảnh người kiểm chưa thấy lỗi. Phiếu ghi ít nhất 8 đối tượng/ca lỗi trên mẫu có 145 đối tượng đã gán, tức cận dưới 8/145 = 5.5% theo quy ước mẫu số của bài. Vật bỏ sót chưa nằm trong mẫu số 145; đây không phải tỷ lệ lỗi trên tổng vật thật.
- Kết luận vòng QC: Rework do tỷ lệ ảnh lỗi vượt 2%, dù bắt bẫy sau sửa có phản hồi đạt 11/12. Các câu hỏi guideline còn mở chưa được kiểm chứng đầy đủ; nếu gặp ca chưa chốt phải giữ lại làm rõ. Không tuyên bố Release.
