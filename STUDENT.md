# Thông tin học viên

- Họ và tên: Nguyễn Nhật Minh
- MSHV: 2A202603210
- Lớp (L2 / L3 / khác): L2A
- Tài khoản GitHub: nminnnnn
- Repo bài nộp (URL): https://github.com/nminnnnn/K5-L23-DAY08-2A202603210-NguyenNhatMinh-AIPrelabel
- Commit chốt bài (hash): `4425e8dd8452697c929878907bf25d502d77c7b9`
- Máy: Windows, NVIDIA GeForce RTX 3050 Laptop GPU (CUDA)
- CVAT (Docker): v2.74.1 (`cvat/server:v2.74.1`, `cvat/ui:v2.74.1`), chạy local bằng Docker

## Kết quả tóm tắt (điền cuối bài; số lấy từ `submission/qc/*`, kèm mẫu số)

- Máy: accepted / edited / deleted: **623/628 = 99.2% / 0/628 = 0.0% / 5/628 = 0.8%**; added: **2/625 = 0.3%**.
- So với gold: precision / recall của **máy**: **47/82 = 57.3% / 47/56 = 83.9%**.
- Bắt bẫy: lượt độc lập đầu **8/12 = 66,7%** (extra 3/3, wrong_class 3/3, loose 2/3, missing 0/3); sau khi mở key và Rework, bản cuối **12/12 = 100%**. Bản cuối là hậu kiểm có đáp án, không phải lượt mù.
- Thời gian: gán tay gold không nhớ/không có log đáng tin cậy; duyệt có pre-label **40/24 = 1.67 phút/ảnh**.
- Lỗi QC vòng cuối: **0/5 ảnh = 0.0%**, **0/128 đối tượng = 0.0%**; vòng trước Rework là 2/5 ảnh và 4/130 đối tượng.
- **Quyết định:** **Release sau Rework** — vòng 2 sạch trên mẫu QC, nhưng cận trên 95% vẫn 43% vì mẫu nhỏ.
