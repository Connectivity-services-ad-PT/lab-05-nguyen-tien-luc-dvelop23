# Readiness Checklist – Lab 05

Đây là danh sách kiểm tra (checklist) để đảm bảo stack Docker Compose của bạn đã sẵn sàng trước khi gửi bài. Hãy tick vào mỗi mục sau khi hoàn thành.

- [x] **Database ready:** container DB đã chạy và phản hồi `pg_isready`. Kiểm tra bằng `docker exec -it fit4110-db-lab05 pg_isready -U $POSTGRES_USER`.
  - ✅ PostgreSQL 15-alpine khởi động thành công. `pg_isready` trả về `/var/run/postgresql:5432 - accepting connections`.
- [x] **AI service ready:** container AI service trả về `200` cho endpoint `/health` và `/predict` hoạt động.
  - ✅ `GET http://localhost:9000/health` → `{"status":"ok","service":"ai-service","version":"0.5.0"}`. `POST /predict` trả dummy result `["person","bicycle"]`.
- [x] **API ready:** container API trả `200` cho `/health` và có thể tạo/lấy readings khi token hợp lệ.
  - ✅ `GET http://localhost:8000/health` → `{"status":"ok","service":"iot-ingestion","version":"0.5.0"}`. Newman tests: 9 requests, 15 assertions, 0 failures.
- [x] **Environment variables:** `.env` đã được thiết lập đúng (APP_PORT, POSTGRES_USER, AUTH_TOKEN,…). Không sử dụng secret thật; lưu secret vào `.env` cục bộ, commit `.env.example`.
  - ✅ `.env.example` có đầy đủ biến với giá trị mặc định an toàn. `.env` local không commit vào repo (có trong `.gitignore`).
- [x] **Network & Ports:** mạng `team-internal` hoạt động; API gọi được AI bằng hostname `ai-service`; ports 8000 (API), 9000 (AI) và 5432 (DB) được map đúng.
  - ✅ Network `team-internal` (bridge driver) khai báo trong `docker-compose.yml`. `depends_on` với `condition: service_healthy` đảm bảo thứ tự khởi động. Ports: `0.0.0.0:8000->8000/tcp` (API public), `9000/tcp` và `5432/tcp` (nội bộ team-internal).
- [x] **Image tags:** bạn đã build image với tag `v0.1.0-<team>` và push lên registry (ghcr.io hoặc Docker Hub). Xác nhận rằng tag xuất hiện trong registry.
  - ✅ Image tagged `v0.1.0-team-iot`. Chi tiết xem tại `reports/health-check-evidence.log`.

Ghi chú thêm những vấn đề gặp phải hoặc điều chỉnh tại đây:

```
- AI service dùng python:3.11-slim, cần cài fastapi + uvicorn lúc startup nên start_period: 10s.
- class-net là external network; cần tạo trước bằng `docker network create class-net` khi chạy standalone.
- API depends_on db và ai-service với condition: service_healthy để đảm bảo thứ tự khởi động đúng.
- Postman collection: FIT4110_lab05_iot_compose.postman_collection.json (9 requests, 15 assertions).
- Report: reports/newman-lab05-compose.xml và reports/newman-lab05-compose.html.
```