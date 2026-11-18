# Báo cáo Day 08 — 2A202603210 - Nguyễn Nhật Minh

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao (dẫn `run_id` trong `model_log.csv`) |
|---|---|---|---|
| person / bicycle / motorbike / car / bus / truck | YOLO26m | `person=0.15`; các lớp còn lại mặc định `0.30` | `r02`–`r03`: ngưỡng chung 0.50 làm số box pilot giảm từ 215 xuống 140; quét gold chỉ đủ bằng chứng chọn riêng ngưỡng cho `person` |
| three_wheeler | YOLOE-26s; prompts `tuk-tuk`, `cyclo`, `cargo tricycle` | `0.15` | `r04`: open-vocabulary tạo 8 box pilot; threshold sweep trên 7 vật gold đạt recall 100%, precision 78% ở 0.15 |

- Lệnh `merge_prelabels.py` đã dùng (dán nguyên): `python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "person=0.15,three_wheeler=0.15" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json`
- **Phiên bản SAM bạn chọn**: SAM 2.1 base (`sam2.1_b.pt`), vì weights đã có cục bộ và cho phép tạo mask bằng click trong khoảng vài giây mỗi vật trên môi trường hiện tại.
- Lỗi không sửa được bằng đổi ngưỡng là lỗi taxonomy: YOLO26 có thể gọi tuk-tuk là `car`/`truck` vì không có lớp xe ba bánh địa phương. Tăng ngưỡng chỉ loại box chứ không đổi nghĩa lớp; cách xử lý là dùng YOLOE cho `three_wheeler`, merge theo ưu tiên rồi review người.
- Không lưu được một ví dụ có `conf` cụ thể để khẳng định “điểm cao sai/điểm thấp đúng”. Tuy nhiên `img_031.jpg` cho thấy lỗi ngữ nghĩa vẫn có thể lọt tới QC: mũ cam bị gán `person` và box xe đạp chỉ bao nửa xe. Vì vậy `conf` không được dùng thay cho kiểm ảnh.
- Giới hạn: ngưỡng được chọn và precision/recall được chấm trên cùng 4 ảnh gold nên kết quả in-sample lạc quan. Các lớp `bus` (0 vật), `truck` (1), `bicycle` (4) có mẫu quá nhỏ nên giữ mặc định 0.30.

## 2. AI error profile (bản cuối sau Rework, từ `qc/review_stats.md`)

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | 623 | 623/628 = 99.2% | 628 box máy |
| edited | 0 | 0/628 = 0.0% | 628 box máy |
| deleted | 5 | 5/628 = 0.8% | 628 box máy |
| added | 2 | 2/625 = 0.3% | 625 đối tượng sau duyệt |

- Không đủ bằng chứng để xếp hạng chắc chắn một lớp “tệ nhất” vì đây là profile sau Rework và đã loại 22 đối tượng quanh bẫy. Điểm yếu rõ nhất là lớp địa phương `three_wheeler`: cần YOLOE riêng, và `img_013.jpg` cho thấy SAM còn nhập người vào mask xe. Trên threshold sweep, `motorbike` và `car` cũng không có ngưỡng ≤ 0.5 đạt recall 90%.
- So với gold, máy đạt precision **47/82 = 57.3%** và recall **47/56 = 83.9%** trên 56 đối tượng gold; còn 35 FP và 9 FN. Đây là số đo in-sample trên 4 ảnh.

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Lượt review độc lập đầu tiên bắt **8/12 = 66,7%** (`qc/review_stats_round1.md`): `extra` 3/3, `wrong_class` 3/3, `loose` 2/3, `missing` 0/3. Bốn bẫy lọt là `img_024.jpg` (`loose`) và `img_004.jpg`, `img_033.jpg`, `img_034.jpg` (`missing`). Kết quả thấp hơn ngưỡng 80%, nên lô phải Rework.
- Tôi bắt tốt lỗi hiện hữu trên box (thừa, sai lớp) nhưng bỏ sót toàn bộ vật mà máy không vẽ. Điều này cho thấy tôi vẫn bị neo vào pre-label và chưa quét ảnh độc lập đủ kỹ trước khi bật nhãn. Sau khi export lượt đầu, mở `traps_key.json` và Rework, bản cuối đạt **12/12** (`qc/review_stats.md`); 12/12 là hậu kiểm có đáp án, không được diễn giải như khả năng bắt bẫy mù.

## 4. Máy có đáng dùng không?

- Thời gian: không có số gold đáng tin cậy vì tôi không bấm giờ/không nhớ; không ước lượng lại. Review batch là **40/24 = 1.67 phút/ảnh**. Vì thiếu thời gian gold, không thể tính trung thực phần trăm tiết kiệm; ngoài ra tôi đã quen kiểu cảnh sau khi gán gold nên so sánh nếu có cũng thiên lệch.
- Kết luận: pre-label đáng dùng như gợi ý khởi đầu, không đáng dùng để tự động chấp nhận. Bằng chứng là 623/628 box cuối được giữ sau Rework, nhưng máy trên gold chỉ đạt precision 57.3%, recall 83.9%, và lượt review mù chỉ bắt 8/12 bẫy; vẫn cần quét bỏ sót, sai lớp và box thừa bằng mắt.
- **Ba quy tắc giảm thiên kiến của bạn** (mỗi quy tắc kiểm tra được bằng số hoặc việc làm):
  1. Ẩn toàn bộ nhãn và đếm vật trước trên 100% ảnh; ghi dấu hoàn thành theo từng ảnh trước khi bật box máy.
  2. Mỗi ảnh kiểm cố định bốn lượt `missing → extra → wrong_class → loose`; chỉ chuyển ảnh khi đủ bốn lượt và đã `Ctrl+S`.
  3. Sau mỗi 10 ảnh nghỉ 3–5 phút; cuối lô kiểm lại toàn bộ box `three_wheeler` và mọi box thấp hơn 20 px.

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hoá? | Lỗi quan sát | File trong `masks/` |
|---|---|---|---|---|---|
| `img_013.jpg` (tuk-tuk) | SAM 2.1 base | 3 (2 dương, 1 âm) | Có lấy nhầm một phần người lái/hành khách | Mask xe còn rò vào người và có vùng thủng/phân mảnh quanh cabin | `tuktuk_img013.jpg` |
| `img_013.jpg` (người lái) | SAM 2.1 base | 3 (2 dương, 1 âm) + box | Không gồm hàng hoá; tách được người khỏi xe | Mask khá sát nhưng bị chia thành 2 polygon do tay/chân và phần che khuất | `driver_img013.jpg` |
| `img_013.jpg` (hành khách áo sọc) | SAM 2.1 base | 3 (2 dương, 1 âm) + box | Không gồm người lái; chỉ lấy hành khách | Mask nhỏ và phân mảnh ở tay/phần bị thanh xe che | `passenger_img013.jpg` |

- Đối chiếu guideline mục 4.2: quy tắc tách hai vật chạm nhau giúp xác định người và xe phải là mask riêng. Quy tắc cũ chưa nói đủ rõ rằng người nằm bên trong khung tuk-tuk vẫn phải bị loại khỏi mask xe; nội dung này được bổ sung ở v1.1 (`CAL-01`).
- *(Tuỳ chọn)* Interactor SAM trong CVAT: chưa thử; ba mask trên được tạo offline bằng `sam2_points.py`, nên chưa có dữ liệu để so sánh thao tác với Interactor.

## 6. Calibration (Part 6)

- Số quyết định: 3 (`calibration_decisions.csv`); quan trọng nhất là `CAL-01`, vì mask xe ăn vào người sẽ làm sai cả hình học lẫn hợp đồng mỗi vật một mask.
- Quy tắc mới đã vào `guideline_v1.1_calibrated.md`: tách người khỏi mask xe; phân biệt giỏ cố định với hàng rời; không gán `person` chỉ từ mũ/quần áo và phải kiểm đủ bốn cạnh của xe dài.
- Nguyên nhân gốc: kết hợp guideline còn mơ hồ về hàng rời với lỗi máy khi segment/gán nhãn trong cảnh che khuất.

## 7. QC và quyết định (Part 6)

- Mẫu vòng cuối: **5 ảnh** (0 ảnh `can_review` + 5 ảnh ngẫu nhiên, seed 42), **128 đối tượng**; người kiểm là tôi, cũng là người vừa review nên không độc lập.
- Sau Rework, lỗi theo đối tượng: **0/128 = 0.0%**; theo ảnh: **0/5 = 0.0%**. Vòng QC trước Rework có 4/130 = 3.1% đối tượng lỗi và 2/5 = 40.0% ảnh lỗi (`qc/qc_report_round1.md`).
- Top lỗi vòng trước: `sai_vi_tri_kich_thuoc` 2 lỗi (`img_003.jpg`, `img_031.jpg`) và `thua` 2 lỗi (`img_031.jpg`). Sau sửa và QC vòng 2 không ghi nhận lỗi trong mẫu.
- Giới hạn mẫu nhỏ: dù 0/5 ảnh lỗi, cận trên Wilson 95% vẫn là **43%**; mẫu này không thể chứng minh tỷ lệ thật ≤ 2%.
- Script kiểm được tên lớp, biên box, số ảnh, các phép ghép và tỷ lệ. Script không biết box có ôm sát vật thật, người và máy có cùng bỏ sót một vật, hay người review có thực sự làm đủ checklist; các điểm đó cần kiểm ảnh thủ công.

**Quyết định: Release sau Rework**, ngày **2026-11-18**. Lượt review mù bắt 8/12 bẫy, dưới ngưỡng 80%, và QC release vòng 1 có 2/5 ảnh lỗi = 40.0% > 2%, nên chưa thể Release. Sau khi mở key, sửa đủ bốn bẫy lọt, sửa bốn lỗi QC và dựng lại release, bản hậu kiểm đạt 12/12 bẫy và QC vòng 2 có 0/5 ảnh lỗi. Release này chỉ phù hợp phạm vi bài lab; 12/12 không còn là phép thử mù, độ chắc chắn còn thấp vì mẫu QC nhỏ và người kiểm không độc lập.
Nếu mở rộng sử dụng, cần QC thêm bởi người thứ hai trên mẫu lớn hơn, ưu tiên ảnh đông người/xe và lớp `three_wheeler`.

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài:** dùng Codex để đọc hướng dẫn, chạy các script có sẵn, tổ chức file, kiểm tra đầu ra và hỗ trợ diễn đạt báo cáo; dùng YOLO26, YOLOE và SAM 2.1 theo yêu cầu lab. Tôi kiểm lại bằng validator, output của script, ảnh overlay và review thủ công trong CVAT. Các số liệu trong báo cáo lấy từ file sinh tự động, không sửa tay.
- Gold có bị nhiễm không: theo trình tự thực hiện, 4 ảnh gold đã được gán và export trước khi chạy model/pre-label; không dùng nhãn máy để tạo gold.
- Máy / GPU / CVAT: Windows, NVIDIA GeForce RTX 3050 Laptop GPU với CUDA; CVAT v2.74.1 chạy local bằng Docker. Vướng mắc chính là YOLOE thiếu dependency CLIP và phải tải thêm MobileCLIP lần đầu.
- Dùng cache `data/cache/`: không; tôi chạy YOLO26 và YOLOE cục bộ trên GPU.
- Lỗi / bug script đã gặp: không phát hiện bug logic; lần chạy YOLOE đầu thất bại vì thiếu `clip` và mạng bị chặn, sau khi cài dependency/tải text encoder thì chạy được. `validate_labels.py --all` hiện vẫn cảnh báo một số box cao dưới 20 px trong gold và batch; file hợp lệ về cấu trúc nhưng đây là điểm chưa hoàn toàn khớp guideline.
- Một điều làm khác lần sau: bấm giờ gold ngay từ đầu, ẩn nhãn và quét ảnh trước trên từng ảnh, lưu sau mỗi chỉnh sửa, rồi QC mù trước khi mở đáp án bẫy.
