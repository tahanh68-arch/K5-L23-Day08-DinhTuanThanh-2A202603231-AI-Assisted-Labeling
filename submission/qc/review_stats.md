# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 507; đối tượng sau duyệt: 517; ngưỡng accepted: IoU >= 0.9
- Đã **loại 22 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 444 | 444/507 = 87.6% | 507 box máy |
| edited (sửa) | 26 | 26/507 = 5.1% | 507 box máy |
| deleted (xoá) | 25 | 25/507 = 4.9% | 507 box máy |
| added (vẽ thêm) | 45 | 45/517 = 8.7% | 517 đối tượng sau duyệt |

Trong số deleted + added, **4 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 41.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 257 | 8 | 6 | 13 |
| bicycle | 6 | 0 | 3 | 2 |
| motorbike | 116 | 12 | 6 | 13 |
| car | 49 | 0 | 0 | 10 |
| bus | 3 | 0 | 0 | 1 |
| truck | 5 | 0 | 0 | 1 |
| three_wheeler | 8 | 6 | 10 | 5 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 50 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 35 | 38 | 15 | 47.9% (KTC 95%: 37–59%) | 70.0% (KTC 95%: 56–81%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 11/12 = 91.7% (KTC 95%: 65–99%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - wrong_class: 3/3
  - missing: 3/3
  - loose: 2/3
  - extra: 3/3
  - lọt: img_034.jpg (loose)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
