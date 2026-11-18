# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 628; đối tượng sau duyệt: 628; ngưỡng accepted: IoU >= 0.9
- Đã **loại 19 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 628 | 628/628 = 100.0% | 628 box máy |
| edited (sửa) | 0 | 0/628 = 0.0% | 628 box máy |
| deleted (xoá) | 0 | 0/628 = 0.0% | 628 box máy |
| added (vẽ thêm) | 0 | 0/628 = 0.0% | 628 đối tượng sau duyệt |

Trong số deleted + added, **0 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 0.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 380 | 0 | 0 | 0 |
| bicycle | 8 | 0 | 0 | 0 |
| motorbike | 135 | 0 | 0 | 0 |
| car | 54 | 0 | 0 | 0 |
| bus | 3 | 0 | 0 | 0 |
| truck | 6 | 0 | 0 | 0 |
| three_wheeler | 42 | 0 | 0 | 0 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 56 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 47 | 35 | 9 | 57.3% (KTC 95%: 47–67%) | 83.9% (KTC 95%: 72–91%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 8/12 = 66.7% (KTC 95%: 39–86%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - extra: 3/3
  - wrong_class: 3/3
  - loose: 2/3
  - missing: 0/3
  - lọt: img_024.jpg (loose), img_034.jpg (missing), img_004.jpg (missing), img_033.jpg (missing)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
