# RestfulBooker API Testing

Một bộ test tự động tập trung vào việc kiểm thử API cho RestfulBooker (demo API), thiết kế chủ yếu để chạy bằng Postman. Repository này chứa Postman collection, environment mẫu và hướng dẫn để chạy test thủ công hoặc tự động (Newman/CI).

## Mô tả ngắn
RestfulBooker là một API demo dùng để quản lý booking. Mục tiêu của repository này là:
- Tổ chức các test case cho các endpoint chính (create, read, update, delete booking, auth...).
- Cung cấp collection Postman kèm environment để dễ dàng chạy và chia sẻ.
- Hướng dẫn cách chạy test thủ công (Postman) và tự động (Newman, CI).

## Nội dung chính (structure)
- /postman/
  - collection.json — Postman collection chứa request và test script
  - environment.json — biến môi trường mẫu (base_url, auth, v.v.)
- README.md — tài liệu hướng dẫn (file này)
- /docs/ — (tùy chọn) test-cases, báo cáo

(Điều chỉnh theo cấu trúc thực tế của repo nếu khác)

## Yêu cầu
- Postman (để import và chạy collection)
- Node.js + npm (nếu muốn chạy Newman)
- Newman (tùy chọn, để chạy collection từ CLI hoặc CI)

## Cách dùng Postman (chi tiết)

1. Import collection và environment
   - Mở Postman → File → Import → chọn `postman/collection.json`.
   - Import `postman/environment.json` để có sẵn biến môi trường (ví dụ base_url, username, password).

2. Thiết lập environment
   - Chọn environment đã import ở góc phải.
   - Kiểm tra biến `base_url` (ví dụ: https://restful-booker.herokuapp.com) và cập nhật nếu cần.

3. Chạy thủ công
   - Mở collection trong Postman, chọn folder hoặc toàn bộ collection.
   - Click "Run" để chạy bằng Collection Runner và xem kết quả từng request.

4. Chạy tự động bằng Newman (CLI)
   - Cài đặt Newman nếu chưa có:
     npm install -g newman

   - Chạy collection:
     newman run postman/collection.json -e postman/environment.json --reporters cli,json --reporter-json-export reports/newman-report.json

   - Một số tuỳ chọn hữu ích:
     - --delay-request: thêm delay giữa các request
     - --iteration-count: chạy bao nhiêu lần
     - --env-var / --global-var: override biến môi trường từ CLI

5. Thêm assertions / test script
   - Các test được viết trong tab "Tests" của mỗi request (JavaScript).
   - Ví dụ kiểm tra status code và schema:
     pm.test("Status code is 200", function () {
       pm.response.to.have.status(200);
     });

   - Sử dụng pre-request scripts để thiết lập token hoặc chuẩn bị dữ liệu trước khi gửi request.

6. Quản lý dữ liệu test
   - Sử dụng environment và collection variables để lưu base_url, token, bookingId.
   - Thiết kế các bước: tạo booking -> lưu bookingId (tests dùng pm.environment.set) -> dùng bookingId cho update/delete -> clean-up.

## Tích hợp CI (ví dụ với GitHub Actions)
- Thêm bước cài đặt Node.js và chạy newman trong workflow.
- Ví dụ lệnh trong step:
  - name: Run Postman tests
    run: |
      npm install -g newman
      newman run postman/collection.json -e postman/environment.json --reporters cli,json --reporter-json-export reports/newman-report.json

- Lưu báo cáo (artifacts) hoặc fail pipeline nếu có test thất bại.

## Best practices khi dùng Postman
- Giữ secrets (credentials) ngoài repository, sử dụng GitHub Secrets hoặc biến môi trường CI.
- Viết test nhỏ, rõ ràng, và độc lập.
- Dọn dẹp (clean-up) dữ liệu tạo ra trong test để tránh ảnh hưởng lẫn nhau.
- Version hóa collection khi thay đổi contract/API.
- Sử dụng folder để phân loại test: smoke, regression, negative, etc.

## Góp ý / Đóng góp
Mọi PR và issue đều hoan nghênh. Khi đóng góp vui lòng:
- Mô tả rõ thay đổi
- Thêm/điều chỉnh test tương ứng
- Nếu cập nhật collection, export file `postman/collection.json` mới và cập nhật changelog

## License
Tùy chọn — thêm license nếu cần (ví dụ: MIT).
