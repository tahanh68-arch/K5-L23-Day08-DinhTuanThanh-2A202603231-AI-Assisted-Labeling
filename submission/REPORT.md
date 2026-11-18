# Báo cáo Day 08 — 2A202603231 - Đinh Tuấn Thành

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao (dẫn `run_id` trong `model_log.csv`) |
|---|---|---|---|
| person / bicycle / motorbike / car / bus / truck | YOLO26m; nguồn cache R01 | 0.30 | Detector cho 6 lớp ánh xạ COCO. Không lớp nào được chọn ngưỡng riêng trong threshold_sweep; dùng ngưỡng mặc định. Chưa có số đo tốc độ local của lượt này. |
| three_wheeler | YOLOE-26s; prompt tuk-tuk / cyclo / cargo tricycle; nguồn cache R02 | 0.30 | Bổ sung lớp không có trong danh mục COCO của YOLO26. Khi gộp, file YOLOE ưu tiên trước YOLO26 để giảm box car/truck trùng xe ba bánh. |

- Không có nhật ký nguyên văn lệnh gộp ban đầu. Lệnh tái lập dưới đây đã được kiểm chứng tạo đúng 28 ảnh và toàn bộ 591 annotations của `work/prelabel/merged_review/pre.coco.json` (chỉ khác thông tin đường dẫn nguồn). Trên Ubuntu/WSL dùng Python trong venv đã cài; chạy từ gốc repo:

```bash
python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json
```

- Không thêm `--thr` riêng vì output `threshold_sweep.md` chỉ gợi ý mặc định cho mọi lớp. Lệnh kiểm chứng ghi vào `work/repro_merged/`, không ghi đè nhãn gốc.
- **SAM:** Part 2 dùng model CVAT có tên `SAM 2.1 base + YOLO26 polygons (CPU)`, threshold 0.3, trên img_004 và img_013 (R03). Part 5 dùng SAM local nhận click trong CVAT, người làm xác nhận không phải MobileSAM nhưng chưa xác nhận phiên bản; không suy phiên bản Part 5 từ model Part 2. Mục đích thử là quan sát detector tạo box rồi segmenter tạo biên, và mask thay đổi theo click; chưa có đo đạc để tuyên bố model nhanh hơn hoặc tốt nhất.
- Đổi ngưỡng không thêm lớp three_wheeler vào detector COCO. Quyết định CAL-01 đã được người làm chấp nhận: kiểm tra lớp riêng, tuk-tuk gán three_wheeler; thêm nhãn bị sót hoặc sửa lớp sai. Mask bám hình vẫn có thể mang nhãn sai từ detector.
- Ví dụ điểm cao nhưng có lỗi: trên ảnh QC img_003, box 11 three_wheeler có conf 0.80 nhưng trùng chiếc xe đã có box 10 (conf 0.90); người làm báo box 11 thừa và đã được đối chiếu ảnh. Điểm tin cậy không loại trừ lỗi trùng. Chưa có ví dụ điểm thấp mà đúng được kiểm chứng để bổ sung.
- Quét ngưỡng trên chính 4 ảnh gold làm số đo có giới hạn in-sample; chỉ có 50 đối tượng gold. Person, motorbike, car, three_wheeler không đạt recall 90% tại các ngưỡng quét phù hợp; bicycle có 3 vật, truck 2, bus 0 nên không đủ mẫu chọn ngưỡng riêng. Mặc định 0.30 là lựa chọn dự phòng, không phải khẳng định đã đạt recall mục tiêu.
- Bằng chứng Part 2 còn hạn chế: R01/R02 là cache, R03 là lượt chạy CVAT được người làm xác nhận, R04 chưa có export/log riêng để xác minh. Bốn dòng CSV không đồng nghĩa đã chứng minh bốn lượt inference thực tế. Không tính cache hoặc một cấu hình dự kiến thành lượt đã tự chạy.

## 2. AI error profile (từ `qc/review_stats.md`)

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | 444 | 444/507 = 87.6% | 507 box máy |
| edited | 26 | 26/507 = 5.1% | 507 box máy |
| deleted | 25 | 25/507 = 4.9% | 507 box máy |
| added | 45 | 45/517 = 8.7% | 517 đối tượng sau duyệt được đưa vào profile |

- Nguồn: `qc/review_stats.json` và `qc/review_stats.md`, chạy bằng `scripts/review_stats.py` trên bản export mới nhất `review55.zip`. Nhãn được lưu ở `labels/batch_reviewed.json`.
- Bản export có 24 ảnh và 550 đối tượng (kể cả `ignore_region`). Script loại các đối tượng quanh vùng bẫy khỏi AI error profile: tổng cộng 22 đối tượng ở hai phía pre/final. Các mẫu số 507 và 517 là mẫu số script báo, không phải tổng số đối tượng của file export. Các box chưa ghép nằm trong vùng ignore cũng không tính deleted/added, nên ba tỉ lệ accepted/edited/deleted không cộng thành 100%.
- Có 4 trường hợp đổi lớp được tính là deleted + added. Chỉ số added trừ đổi lớp do script báo là 45 - 4 = 41; đây là số xấp xỉ theo phép ghép box, không phải kiểm đếm chân lý độc lập.
- Theo profile, three_wheeler có 8 accepted, 6 edited, 10 deleted và 5 added: 8/24 = 33.3% box máy của lớp này được giữ ở ngưỡng accepted, thấp trong các lớp có dữ liệu. Có 10/24 = 41.7% bị xóa. Đây là chỉ báo cần ưu tiên review, không phải precision lớp. Ví dụ img_003 có box xe ba bánh trùng; các ảnh thử 004/013 cũng cho thấy giới hạn nhận diện trước khi tạo mask. Nguyên nhân cụ thể từng box vẫn phải đối chiếu ảnh.
- So với gold: precision của **máy** = 35/(35+38) = 35/73 = 47.9%; recall = 35/(35+15) = 35/50 = 70.0%. Gold có 4 ảnh, 50 đối tượng, TP = 35, FP = 38, FN = 15. Khoảng tin cậy 95% theo script: precision 37–59%, recall 56–81%.
- Đây là số đo máy so với gold, không phải chất lượng nhãn batch sau review. Ngưỡng đã được quét trên chính gold nên kết quả có giới hạn in-sample và mẫu nhỏ; gold còn các cảnh báo box cao dưới 20 px cần rà lại. Không có bản gold sau duyệt nên không tính residual/new error.

## 3. Thiên kiến tự động hóa và ảnh bẫy

| Vòng | Bắt bẫy | Ghi chú |
|---|---|---|
| review.zip | 4/12 = 33.3% | Vòng đầu, 40 phút review do người làm xác nhận |
| review3.zip | 8/12 = 66.7% | Sau sửa bổ sung có phản hồi |
| review44.zip | 10/12 = 83.3% | Đạt ngưỡng 80% sau sửa |
| review55.zip | 11/12 = 91.7% | Bản cuối dùng cho release candidate và QC; KTC 95%: 65–99% |

| Loại bẫy | Sửa đạt theo script trên review55 | Còn lọt |
|---|---|---|
| wrong_class | 3/3 | Không |
| missing | 3/3 | Không |
| loose | 2/3 | img_034.jpg |
| extra | 3/3 | Không |

- Sau vòng đầu, đã xem kết quả chấm và được chatbot chỉ ra các ảnh/loại bẫy còn lọt để rà lại. Kết quả 11/12 là sau sửa có phản hồi, không phải phép đo duyệt mù độc lập mới. Không thay số 4/12 của vòng đầu bằng số cuối.
- Bẫy cuối còn lọt ở img_034 thuộc loose (lệch/lỏng); cần rà lại bốn cạnh box ở bước kiểm hình học. Chưa có xác nhận người làm đã bỏ bước nào, nên không quy kết do mệt hay do bấm chấp nhận theo điểm số.
- Accepted bản cuối là 444/507 = 87.6%, bắt bẫy sau phản hồi là 11/12 = 91.7%; vòng đầu chỉ bắt 4/12. Accepted cao không tự chứng minh người duyệt phát hiện lỗi: phép ghép chỉ đo giống nhau giữa hai bộ nhãn, còn bẫy đo khả năng sửa lỗi cài sẵn. Không coi sự tăng điểm sau khi biết ảnh bẫy là bằng chứng duyệt mù đã cải thiện tương ứng.

## 4. Máy có đáng dùng không?

- Thời gian (từ `time_log.csv`): gán tay gold 10 phút/4 ảnh = 2.50 phút/ảnh; review vòng đầu, có sửa box, 40 phút/24 ảnh = 1.67 phút/ảnh. Thời gian sửa bổ sung qua các vòng review3/review44/review55 chưa được cung cấp, nên chưa có tổng thời gian review cuối cùng.
- Không ghép tốc độ vòng đầu với số đo sau sửa để báo là tốc độ bản cuối. Hai tập gold/batch khác ảnh và độ khó; việc gán gold trước có thể tạo hiệu ứng quen cảnh nhưng chưa đo được mức ảnh hưởng. Chưa có lời xác nhận riêng về trải nghiệm này.
- Kết luận theo bằng chứng hiện có: pre-label tạo sẵn ứng viên để review, nhưng chưa chứng minh tiết kiệm tổng thời gian ở chất lượng yêu cầu. So sánh sơ bộ vòng đầu là 1.67 so với 2.50 phút/ảnh (thấp hơn khoảng 33.3%), chưa tính các vòng sửa và chưa kiểm soát độ khó. Kết quả QC cuối là Rework; không dùng tốc độ vòng đầu hoặc accepted cao để kết luận lô đã đủ chất lượng.
- **Ba quy tắc đề xuất cho vòng làm tiếp** (chưa có log chứng minh đã thực hiện ở vòng trước):
  1. Ẩn nhãn và quét toàn bộ ảnh trước khi hiện pre-label ở 100% ảnh; đánh dấu việc đã quét trong log theo tên ảnh.
  2. Kiểm từng lớp theo thứ tự bỏ sót → thừa/trùng → sai lớp → bốn cạnh box; không bỏ qua box điểm cao. Ghi ít nhất tên ảnh và ID cho mỗi lỗi phát hiện.
  3. Sau mỗi 10 ảnh nghỉ 3–5 phút, ghi thời gian riêng cho review đầu và vòng sửa; không mở đáp án bẫy trước export đầu tiên.

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hoá? | Lỗi quan sát | File trong `masks/` |
|---|---|---|---|---|---|
| img_004.jpg | SAM local trong CVAT; chưa xác nhận phiên bản | 6 dương + 7 âm = 13; chưa xác nhận tính theo một xe hay cả ảnh | Người dùng xác nhận mỗi xe là một mask riêng; người đứng bên phải phần lớn nằm ngoài vùng tô | Cần người làm đối chiếu các vùng rỗng và phần bị che theo guideline; dùng ảnh 004 thay thế gửi sau cùng | [sam_local_img004.png](masks/sam_local_img004.png) |
| img_013.jpg | SAM local trong CVAT; chưa xác nhận phiên bản | 3 dương + 0 âm = 3 | Phần lớn người lái không bị tô trong ảnh kết quả; chưa có xác nhận riêng về hàng hoá | Cần người làm kiểm tra vùng rỗng trong khung xe có còn nền bị tô hay không | [sam_local_img013.png](masks/sam_local_img013.png) |
| img_017.jpg | SAM local trong CVAT; chưa xác nhận phiên bản | 8 dương + 7 âm = 15; chưa xác nhận tính theo một xe hay cả ảnh | Hai người phía trước phần lớn nằm ngoài vùng tô; chưa có xác nhận riêng về hàng hoá | Cần người làm kiểm tra vùng rỗng trong khung xe và biên tiếp xúc với vật phía sau | [sam_local_img017.png](masks/sam_local_img017.png) |

- Bằng chứng là ba ảnh chụp kết quả SAM local trong CVAT, đã lưu nguyên tệp PNG trong `submission/masks/` từ thư mục Screenshots của người dùng. Với `img_004`, dùng bản thay thế gửi sau cùng (Screenshot 2026-11-18 135729.png); img_013 và img_017 lần lượt từ Screenshot 2026-11-18 134801.png và Screenshot 2026-11-18 135318.png. Đây là ảnh chụp giao diện, không phải đầu ra của sam2_points.py.
- Cách thực hiện đã xác nhận: dùng SAM local trong CVAT với click dương/âm, không phải MobileSAM và không chạy bằng `sam2_points.py`. Tên model/phiên bản chính xác vẫn cần đối chiếu giao diện.

- Mục 4.2 yêu cầu biên theo phần nhìn thấy và tách vật chạm nhau; cùng định nghĩa người là vật riêng giúp xác định vùng người lái không thuộc mask xe. Mục 4.2.2 cho phép bỏ qua lỗ nhỏ khoảng dưới 5% diện tích vật. Điểm cần làm rõ là kính xe so với khoảng hở không kính, đã được chốt ở CAL-02. Chưa khẳng định mọi mask ảnh chụp đã đạt sai số biên 3 px.
- Interactor SAM trong CVAT: người dùng đã thao tác click và gửi kết quả trên ba ảnh 004, 013, 017. Chưa có lượt chạy offline tương ứng để so sánh trực tiếp.

## 6. Calibration (Part 6)

- Đã chốt 3 quyết định trong `qc/calibration_decisions.csv` ngày 2026-11-18: CAL-01 (loi_may, img_013), CAL-02 (cau_hoi_mo, img_013), CAL-03 (loi_may, img_004 và img_017). Người làm đã đọc, kiểm tra và đồng ý nội dung đề xuất trong hội thoại.
- CAL-02 bổ sung guideline v1.1: kính chắn gió/cửa sổ thuộc mask xe; khoảng hở không kính nhìn ra nền cần loại nếu lớn hơn khoảng 5% diện tích vật; lỗ nhỏ có thể bỏ qua theo v1.0.
- CAL-01 và CAL-03 áp dụng lại quy tắc đã có về lớp three_wheeler và tách người khỏi xe, không phải hai thay đổi định nghĩa mới. Đã ghi trong bảng ca khó v1.1.
- Phân loại vấn đề: CAL-02 làm rõ câu hỏi mở về guideline; CAL-01/CAL-03 xử lý nguy cơ sai/sót lớp của detector và mask lấy lẫn đối tượng. Chưa xác nhận đã sửa mọi nhãn theo các quyết định này; cần đối chiếu trong QC, export qc_fixed.json nếu thay đổi nhãn detection.
- Các câu hỏi mở khác của v1.0 chưa chốt ở vòng này. Trong ba quyết định đã được người làm kiểm tra và chấp nhận, CAL-02 trực tiếp làm rõ ngữ nghĩa mới về kính và khoảng hở; CAL-01/CAL-03 là áp dụng lại quy tắc có sẵn. Không mô tả cả ba là ba quy tắc mới.

## 7. QC và quyết định (Part 6)

- Chốt vòng QC ngày 2026-11-18 trên `review55.zip`, đã chuyển XML sang `labels/batch_reviewed.json` và chạy lại `build_release.py`: 24 ảnh, 550 đối tượng gồm 22 ignore_region. Đây là release candidate, không phải dữ liệu được duyệt Release.
- Mẫu `qc_sample.py --n 5 --seed 42`: 7 ảnh (2 ca khó can_review + 5 ngẫu nhiên), 145 đối tượng không tính ignore. Các ảnh: img_003, img_006, img_013, img_014, img_022, img_033, img_035; ca khó là img_022 và img_035. Nội dung nhãn của 7 ảnh này không đổi giữa bản đã được người dùng kiểm và review55; đã đối chiếu trước khi dùng lại nhận xét.
- Người kiểm: Đinh Tuấn Thành đã xem 7 ảnh và báo lỗi trong hội thoại; Codex đối chiếu ảnh và COCO, bổ sung lỗi hình học box 129 motorbike ở img_013. Nguồn: `qc/qc_review.csv`, `qc/qc_report_auto.md`, `qc/QC_LIMITATIONS.md`. Đây không phải đánh giá độc lập giữa hai người gán nhãn.
- Lỗi theo ảnh: **6/7 = 85.7%**, vượt ngưỡng 2%. Ảnh 035 là ảnh người kiểm chưa thấy lỗi, không phải chứng nhận ảnh hoàn toàn sạch.
- Lỗi theo đối tượng: **ít nhất 8/145 = 5.5%** theo mẫu số đối tượng đã gán của bài. Đây là cận dưới, không phải tỷ lệ chính xác: các nhóm xe bị sót chưa đếm đủ chỉ ghi 1 ca tối thiểu mỗi nhóm; các vật bị sót không nằm trong mẫu số 145. Không diễn giải nhóm nhiều xe thành đúng 1 xe.

| Loại lỗi | Số ca tối thiểu đã ghi | Ví dụ |
|---|---|---|
| Bỏ sót | 5 | Xe đạp ở img_003; xe máy cạnh person 135 ở img_014; xe dưới person 266 ở img_022; nhóm xe máy và xe đạp ở img_033 |
| Thừa/trùng | 1 | img_003: box 11 trùng xe đã có box 10 |
| Sai lớp/vùng | 1 | img_006: người kiểm yêu cầu đổi vùng box 67 motorbike sang ignore_region |
| Sai vị trí/kích thước | 1 | img_013: box 129 motorbike không bao hết phần bánh xe nhìn thấy |

- Box 11 chỉ tính một đối tượng lỗi, không cộng cả thừa và hình học thành hai vật lỗi. Box 12 ở img_003, box 71 ở img_006 và phản hồi sai lớp của box 119 ở img_013 được giữ trong `qc/qc_unresolved.csv`: loại lỗi/ID chưa được xác nhận; riêng 119 hiện đã là three_wheeler nên không ghi bừa lỗi car. Các lỗi chưa rõ không bị chuyển thành khong.
- Theo phiếu, script nhận diện bỏ sót là lỗi lặp (5/8 ca lỗi). Vì số vật trong nhóm chưa đủ, cần hiểu đây là cơ cấu ca lỗi tối thiểu trong phiếu, không phải thống kê đầy đủ mọi vật lỗi.
- Bắt bẫy cuối **11/12 = 91.7%**, đạt 80%, nhưng không thay thế QC và được đo sau các vòng sửa có phản hồi.
- Cận trên 95% Wilson cho 6/7 ảnh lỗi là **97%** theo `qc_report_auto.md`. Mẫu nhỏ và có ca khó được chọn có chủ đích; không dùng để khẳng định tỷ lệ lỗi chính xác của cả lô hoặc chứng minh dưới 2%.
- Script kiểm cấu trúc, lớp, tên ảnh và tính số đo từ phiếu. Độ sát vật, lớp đúng thực tế, số xe bị cả máy và người bỏ sót và việc áp dụng guideline vẫn cần đối chiếu ảnh. Còn 15 cảnh báo box dưới 20 px trong bản review55.

**Quyết định vòng QC: Rework**, ngày **2026-11-18**, chốt theo yêu cầu người làm dựa trên lỗi đã báo và kiểm tra bổ sung. Tỷ lệ ảnh lỗi 6/7 vượt 2% và có lỗi bỏ sót lặp; đạt ngưỡng bẫy không đủ để Release. Chưa xác nhận có ca guideline chưa chốt nào đang trực tiếp ngăn xử lý các lỗi này; nếu gặp ca như vậy thì Hold để làm rõ trước khi sửa.

Việc tiếp theo nếu tiếp tục cải thiện dữ liệu: sửa các ảnh lỗi trên CVAT, đếm riêng từng vật sót, làm rõ các ID chưa khớp và rà vật dưới 20 px; export `qc_fixed.json` hoặc bản batch mới, tạo lại release và QC. Chưa có bằng chứng các lỗi QC đã được sửa nên không ghi chúng là đã khắc phục. Có thể giữ kết quả Rework trung thực cho vòng hiện tại.

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài:** ChatGPT/Codex hướng dẫn thao tác CVAT, đọc file export, chuyển box và thuộc tính từ CVAT XML 1.1 trong các bản review3/review44/review55 sang COCO, chạy `validate_labels.py` và `review_stats.py`, chép số đo vào báo cáo và log thời gian theo người dùng xác nhận. Đã đối chiếu đúng 24 tên ảnh batch, bản cuối 550 đối tượng và chạy validator; có cảnh báo box nhỏ. Đã dùng phản hồi về bẫy lọt để sửa vòng sau. Codex ghi lại ba quyết định calibration đã được người làm chấp nhận và chốt vòng QC Rework theo yêu cầu; phần diễn giải và quy trình cải thiện được Codex soạn theo yêu cầu điền nốt, dựa trên số đo và các quyết định đã chấp nhận; các thông tin cá nhân chưa xác nhận được ghi rõ. Người làm cần đọc và kiểm tra bản hoàn chỉnh trước khi nộp.
- Tình trạng gold: time_log hiện ghi gán tay không AI; file export có 4 ảnh, 50 đối tượng. Hội thoại có hướng dẫn quy tắc gán nhãn chung và ảnh test AI trên các ảnh khác gold. Chưa có xác nhận riêng đầy đủ về việc người làm có xem pre-label của gold trước khi export hay không; vì vậy không chứng nhận gold độc lập tuyệt đối. Cần người làm tự chịu trách nhiệm xác nhận khi nộp.
- Môi trường theo thông tin đã ghi: Windows + Ubuntu/WSL, CPU only. Python xử lý nhãn trong `/home/asus/day08/.venv/`. CVAT Docker local tại localhost:8080; API `/api/server/about` đã xác nhận phiên bản 2.81.0. Lệnh Python Windows không có đủ thư viện nên các script được chạy bằng môi trường WSL đã cài. Part 5 người làm phản ánh SAM local nặng; không có thời gian inference đo được.
- Có dùng cache: hai file trong `work/prelabel/y26_all/` và `work/prelabel/yoloe_all/` trùng nội dung cache YOLO26/YOLOE. Đây là nguồn dự đoán dùng cho quét ngưỡng và gộp nhãn; không coi là hai lượt chạy local của người làm. Người làm dùng CPU và đã phản ánh SAM chạy nặng, nhưng chưa xác nhận lý do riêng cho quyết định dùng cache; không suy lý do này thành một sự kiện đã xác nhận.
- Vướng mắc đã ghi nhận: file review2 trùng nội dung export đầu; từ review3 các ZIP là CVAT XML thay vì COCO nên đã chuyển box và thuộc tính sang COCO để chạy script. ID/lớp box 119 ở img_013 không khớp mô tả sai lớp của người kiểm; đã đối chiếu và giữ trong qc_unresolved.csv, không sửa bừa. Chưa ghi nhận bug repo cần sửa; script chuẩn dùng nguyên bản.
- Đề xuất cải thiện lần sau: ghi đủ thời gian từng vòng và số lượng vật sót ngay khi kiểm, rồi đối chiếu đúng ID trên bản export đang dùng trước khi chốt phiếu. Đây là đề xuất từ số liệu và vướng mắc của bài, chưa phải việc đã thực hiện.
