# Báo cáo bài lab CI/CD cho AI Systems

## Thông tin sinh viên
- Họ tên / MSSV: 2A202600029 - Đào Phước Thịnh
- Repository: Điền link GitHub public của repo tại đây

## Mục tiêu thực hiện
Bài làm xây dựng pipeline CI/CD cho một hệ thống AI gồm các bước kiểm thử, huấn luyện, đánh giá và triển khai mô hình. Hệ thống sử dụng MLflow để theo dõi thí nghiệm, DVC để quản lý phiên bản dữ liệu và FastAPI để phục vụ mô hình trên máy ảo.

## Những nội dung đã hoàn thành
- Xây dựng workflow GitHub Actions gồm 4 job: `Unit Test`, `Train`, `Eval`, `Deploy`.
- Tự động huấn luyện khi code hoặc dữ liệu liên quan thay đổi.
- Lưu model và artifact phục vụ theo dõi kết quả.
- Triển khai API suy luận lên VM và kiểm tra bằng endpoint `/health`.
- Hỗ trợ continuous training khi dữ liệu được cập nhật.
- Bổ sung quality gate để chỉ deploy khi mô hình đạt yêu cầu.
- Hỗ trợ nhiều mô hình có thể chọn qua `params.yaml`.

## Mô hình và cấu hình sử dụng
Trong quá trình thử nghiệm, em benchmark nhiều mô hình gồm `RandomForest`, `GradientBoosting`, `ExtraTrees` và `LogisticRegression`. Cấu hình mặc định cuối cùng được chọn trong `params.yaml` là `extra_trees` với `n_estimators=500` do cho kết quả tốt và ổn định hơn trên dữ liệu huấn luyện đã ghép.

## Quy trình pipeline
- `Unit Test`: chạy kiểm thử cho logic huấn luyện.
- `Train`: train model, log thông số và metric, sinh artifact báo cáo.
- `Eval`: kiểm tra chất lượng mô hình và áp dụng ngưỡng chất lượng trước khi deploy.
- `Deploy`: SSH vào VM, restart service `mlops-serve`, kiểm tra `/health`, đồng thời in log `systemctl` và `journalctl` khi có lỗi để dễ debug.

## Khó khăn và cách xử lý
Khó khăn lớn nhất là lỗi ở bước deploy. Ban đầu workflow không kích hoạt đúng do lệch nhánh giữa `main` và `master`. Ngoài ra, bước SSH deploy từng gặp lỗi xác thực khóa và lỗi service không vượt qua health check sau khi restart. Em đã xử lý bằng cách kiểm tra lại SSH key trong GitHub Secrets, đồng bộ branch trigger của workflow, thêm retry cho health check và bổ sung logging chi tiết bằng `systemctl status` và `journalctl` để xác định nguyên nhân trên VM.

## Kết luận
Bài làm đã hoàn thiện quy trình CI/CD cơ bản cho hệ thống AI, bao gồm kiểm thử, huấn luyện, đánh giá, triển khai và hỗ trợ huấn luyện lại khi dữ liệu thay đổi. Hệ thống đáp ứng mục tiêu tự động hóa và giúp việc theo dõi, kiểm soát chất lượng mô hình trở nên rõ ràng hơn.
