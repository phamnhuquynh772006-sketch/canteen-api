
# THIẾT KẾ API QUẢN LÝ THỰC ĐƠN CĂNG TIN

## 1. Giới thiệu

Tên hệ thống: Canteen API

Mục đích: Xây dựng API quản lý thực đơn căng tin trường học, cho phép sinh viên xem thông tin món ăn và nhân viên thực hiện thêm, sửa, xóa món ăn.

Công nghệ sử dụng: Python, FastAPI, Pydantic, Postman.

## 2. Đặc tả tài nguyên MenuItem

| Trường | Kiểu dữ liệu | Ràng buộc | Ví dụ |
|---|---|---|---|
| id | int | Hệ thống tự sinh | 1 |
| name | str | 2–100 ký tự, duy nhất | Bún cá Hải Phòng |
| category | enum | com, bun_pho, do_uong, an_vat | bun_pho |
| price | int | > 0, <= 200000, bội số 1000 | 30000 |
| available | bool | Mặc định true | true |
| updated_at | datetime | Tự cập nhật, chỉ đọc | 2026-10-20T11:30:00 |

## 3. Đặc tả các Endpoint

| Phương thức | URI | Chức năng | Thành công | Lỗi |
|---|---|---|---|---|
| GET | /menu-items | Xem thực đơn | 200 | 422 |
| GET | /menu-items/{id} | Xem chi tiết món | 200 | 404, 422 |
| POST | /menu-items | Thêm món | 201 + Location | 409, 422 |
| PUT | /menu-items/{id} | Sửa toàn bộ món | 200 | 404, 409, 422 |
| PATCH | /menu-items/{id} | Sửa một phần món | 200 | 404, 409, 422 |
| DELETE | /menu-items/{id} | Xóa món | 204 | 404 |

## 4. Query Parameters

Endpoint: GET /menu-items

- category: Lọc theo loại món.
- available: Lọc món còn bán hoặc đã hết.
- max_price: Lọc theo mức giá tối đa.
- sort: Sắp xếp theo id, price hoặc name.
- order: Sắp xếp tăng dần (asc) hoặc giảm dần (desc).

Ví dụ:

GET /menu-items?category=do_uong&available=true&sort=price&order=desc

## 5. Quy tắc nghiệp vụ

BR1: Tên món phải duy nhất, không phân biệt chữ hoa và chữ thường. Nếu trùng tên, trả về 409 Conflict.

BR2: Giá món ăn phải lớn hơn 0, tối đa 200000 đồng và là bội số của 1000. Nếu không hợp lệ, trả về 422.

BR3: Loại món chỉ được thuộc một trong bốn giá trị: com, bun_pho, do_uong, an_vat. Nếu không hợp lệ, trả về 422.

BR4: Món tạm hết không bị xóa khỏi thực đơn mà được cập nhật available = false.

## 6. Mã trạng thái HTTP

- 200 OK: Yêu cầu thành công.
- 201 Created: Tạo món ăn thành công.
- 204 No Content: Xóa thành công, không trả về nội dung.
- 404 Not Found: Không tìm thấy món ăn.
- 409 Conflict: Tên món ăn bị trùng.
- 422 Unprocessable Entity: Dữ liệu không hợp lệ.
