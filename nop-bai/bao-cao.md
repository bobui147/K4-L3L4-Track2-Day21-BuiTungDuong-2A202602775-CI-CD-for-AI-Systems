# Báo Cáo Lab Day 21 - CI/CD cho AI Systems

| | |
|---|---|
| Họ và tên | Bùi Tùng Dương |
| MSSV | 2A202602775 |
| Lớp / Khóa | K4 |
| Repo GitHub | https://github.com/bobui147/K4-L3L4-Track2-Day21-BuiTungDuong-2A202602775-CI-CD-for-AI-Systems |
| Ngày nộp | 07/10/2026 |

---

## 1. Bộ Siêu Tham Số Đã Chọn và Lý Do

| Lần chạy | n_estimators | learning_rate | max_depth | f1_score | accuracy |
|---|---|---|---|---|---|
| 1 | 100 | 0.1 | 3 | 0.7109 | 0.8780 |
| 2 | 50 | 0.05 | 2 | 0.6051 | 0.8460 |
| 3 | 200 | 0.1 | 5 | 0.7149 | 0.8740 |

**Bộ siêu tham số đã chọn:** `n_estimators=200`, `learning_rate=0.1`, `max_depth=5`.

**Lý do:** Lần chạy 3 đạt F1 cao nhất (0.7149) và vượt ngưỡng 0.65 nên được chọn. Lần chạy 1 có accuracy cao nhất (0.8780) nhưng F1 thấp hơn, cho thấy accuracy không phản ánh đầy đủ khả năng nhận diện lớp thu nhập cao. Cấu hình 2 giảm learning rate, số cây và độ sâu nên F1 giảm còn 0.6051. Khi giữ `learning_rate=0.1`, tăng số cây từ 100 lên 200 và độ sâu từ 3 lên 5 giúp F1 tăng nhẹ dù accuracy giảm 0.004. Vì quality gate dựa trên F1 của lớp dương, cấu hình 3 phù hợp nhất.

---

## 2. Vì Sao Ngưỡng Chất Lượng Đặt Trên F1 Chứ Không Phải Accuracy

Chỉ 24,8% mẫu thuộc lớp thu nhập trên 50K nên dữ liệu mất cân bằng. Mô hình luôn dự đoán “thu nhập thấp” vẫn đạt accuracy 0,752 nhưng F1 bằng 0 vì không phát hiện trường hợp thu nhập cao. F1 của lớp dương kết hợp precision và recall, phản ánh cả độ chính xác của dự đoán dương lẫn khả năng tìm đủ lớp này. Lab dùng `f1_score(y_eval, preds)` trực tiếp. `average="weighted"` sẽ bị lớp đa số chi phối, còn `average="macro"` không đo riêng mục tiêu dương. Vì vậy quality gate dùng ngưỡng F1 0.65 thay cho accuracy.

---

## 3. Khó Khăn Gặp Phải và Cách Giải Quyết

| Khó khăn | Nguyên nhân | Cách giải quyết |
|---|---|---|
| Không cài được scikit-learn 1.4.2 | Môi trường ban đầu dùng Python 3.13, không có wheel tương thích | Cài Python 3.10 và tạo lại `.venv` |
| Pytest không tạo được thư mục tạm | Windows chặn quyền tại thư mục temp dùng chung | Chạy test với `--basetemp` trong workspace |
| DVC không ghi được cấu hình hệ thống | CLI mặc định truy cập `C:\ProgramData\iterative` | Chuyển các thư mục cấu hình/cache DVC vào workspace |

---

## 4. So Sánh Bước 2 và Bước 3 (bắt buộc, 2 - 3 câu)

| | f1_score | accuracy |
|---|---|---|
| Bước 2 (chỉ `train_batch1`) | 0.7149 | 0.8740 |
| Bước 3 (thêm `train_batch2`) | 0.7354 | 0.8820 |

**Nhận xét:** Sau khi thêm dữ liệu cùng phân phối, F1 tăng 0,0205 và accuracy tăng 0,0080. Mức tăng nhỏ cho thấy thêm dữ liệu có ích trong lần chạy này nhưng không bảo đảm mọi mô hình luôn tốt hơn. Giá trị chính của Bước 3 là dữ liệu mới tự động đi qua huấn luyện, quality gate và triển khai.
