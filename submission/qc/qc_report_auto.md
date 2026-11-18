# Báo cáo QC tự động (nháp)

> **Gợi ý** do script tính; bạn xác nhận. Script chỉ đếm theo phiếu kiểm bằng mắt người.

## Mẫu
- 7 ảnh (2 ca khó + 5 ngẫu nhiên), 145 đối tượng.
- taxonomy v1.0

## Số đo
| Chỉ số | Giá trị | Mẫu số |
|---|---|---|
| Lỗi **theo đối tượng** | 8/145 = 5.5% | 145 đối tượng trong mẫu |
| **Ảnh có lỗi** (dùng cho ngưỡng) | 6/7 = 85.7% | 7 ảnh trong mẫu |
| Máy: accepted / edited / deleted | 444/507 = 87.6% / 26/507 = 5.1% / 25/507 = 4.9% | 507 box máy |
| Máy: added | 45/517 = 8.7% | 517 đối tượng sau duyệt |
| Bắt lỗi bẫy | 11/12 = 91.7% | 12 bẫy |

## Top 3 loại lỗi
| Loại lỗi | Số lỗi | Ảnh ví dụ |
|---|---|---|
| bo_sot | 5 | img_003.jpg, img_014.jpg, img_022.jpg |
| thua | 1 | img_003.jpg |
| sai_lop | 1 | img_006.jpg |

**Lỗi hệ thống**: 'bo_sot' lặp lại -> sửa quy tắc / recipe, không chỉ sửa từng ảnh.

## Giới hạn thống kê
- 6 ảnh lỗi trong 7 ảnh mẫu: cận trên 95% (Wilson) của tỉ lệ ảnh lỗi thật là **97%**. Ngưỡng 2% không thể được **chứng minh** bằng mẫu nhỏ như vậy: hãy nói rõ giới hạn này trong báo cáo.

## Gợi ý quyết định
**REWORK**

- ảnh có lỗi 85.7% > 2%
- lỗi hệ thống: 'bo_sot' chiếm 5/8 lỗi

Cây quyết định: Hold (guideline chưa rõ / dữ liệu hỏng) -> Rework (> ngưỡng, lỗi lặp, bắt bẫy / nhất quán thấp) -> Release.
