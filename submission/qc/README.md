# Dấu vết các vòng review và QC

Các file không có hậu tố là kết quả cuối sau Rework. Các file có hậu tố `round1` giữ lại bằng chứng trước khi sửa để tránh diễn giải nhầm kết quả hậu kiểm thành kết quả độc lập.

| Giai đoạn | Bằng chứng | Kết quả chính |
|---|---|---|
| Review độc lập lượt đầu | `review_stats_round1.{json,md}` | Bắt 8/12 bẫy: extra 3/3, wrong_class 3/3, loose 2/3, missing 0/3 |
| Review sau khi mở key và Rework | `review_stats.{json,md}` | Bắt 12/12; đây là hậu kiểm có đáp án, không phải lượt mù độc lập |
| QC release vòng 1 | `qc_sample_round1.csv`, `qc_review_round1.csv`, `qc_report_round1.md` | 4/130 lỗi theo đối tượng; 2/5 ảnh có lỗi; quyết định Rework |
| QC release vòng 2 | `qc_sample.csv`, `qc_review.csv`, `qc_report_auto.md` | 0/128 lỗi theo đối tượng; 0/5 ảnh có lỗi; Release sau Rework, với giới hạn mẫu nhỏ |

`review_stats_round1.*` và `qc_report_round1.md` là output nguyên bản do script sinh, được lưu lại để có thể kiểm tra lịch sử; không sửa tay các số đo trong các file này.
