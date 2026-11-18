# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603210 - Nguyễn Nhật Minh

Chỉ các quyết định mới so với v1.0 được ghi dưới đây. Mỗi quyết định khớp một dòng trong `qc/calibration_decisions.csv`.

## 1. Câu hỏi mở đã chốt (từ mục 6 của v1.0)

| # | Câu hỏi | Quyết định | Ảnh ví dụ | `case_id` |
|---|---|---|---|---|
| 3 | Giỏ và hàng hóa nhô xa trên xe đạp chở hàng có thuộc box/mask `bicycle` không? | Giá, khung chở hàng và giỏ gắn cố định thuộc xe. Rau quả, bao hàng và vật rời không thuộc mask xe; không nới box chỉ để ôm hàng rời nhô quá 20%. | `img_031.jpg` | `CAL-02` |

## 2. Ca khó mới (từ lỗi của máy, bẫy lọt, box `can_review`)

| Ảnh | Tình huống | Máy làm gì (model nào) | Quyết định của bạn | Quy tắc mới (vào mục nào của v1.0) |
|---|---|---|---|---|
| `img_013.jpg` | Tuk-tuk có người lái và hành khách nằm trong khung xe | SAM 2.1 lấy cả một phần người vào mask tuk-tuk | Người luôn là vật riêng; thêm negative click lên người và loại vùng người khỏi mask xe | Mục 4.2: mask xe không gồm người, kể cả khi người nằm trong khung xe (`CAL-01`) |
| `img_031.jpg` | Quầy quần áo có mũ cam và xe đạp chở hàng bị che bởi giỏ | Detector gán mũ thành `person` và box `bicycle` chỉ bao nửa trước xe | Xóa box người không có dấu hiệu cơ thể; kéo box xe ôm toàn bộ phần xe nhìn thấy, không tạo box trùng cho từng nửa | Mục 4.1: cần dấu hiệu cơ thể để gán `person`; kiểm đủ bốn cạnh của vật dài (`CAL-03`) |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do (ca nào, `case_id`) |
|---|---|---|
| v1.0 | Bản giảng viên | — |
| v1.1 | Làm rõ hàng rời trên xe chở hàng, tách người khỏi mask xe và cách loại box người giả trong cảnh cửa hàng | `CAL-01`–`CAL-03`, `img_013.jpg`, `img_031.jpg` |
