# Thiết kế Canteen API

## 1. Tài nguyên

Tài nguyên chính là **món trong thực đơn** (MenuItem).
URI dùng danh từ số nhiều, nối bằng gạch nối: `/menu-items`.

## 2. Đặc tả tài nguyên MenuItem

| Trường     | Kiểu     | Ràng buộc                                  | Ví dụ               |
|------------|----------|--------------------------------------------|---------------------|
| id         | int      | Hệ thống tự sinh                           | 1                   |
| name       | str      | 2–100 ký tự, duy nhất, không phân biệt hoa thường (BR1) | Bún cá Hải Phòng |
| category   | enum     | com, bun_pho, do_uong, an_vat (BR3)        | bun_pho             |
| price      | int      | 0 < giá ≤ 200000, bội số của 1000 (BR2)    | 30000               |
| available  | bool     | Mặc định true; món hết chỉ đổi thành false (BR4) | true          |
| updated_at | datetime | Hệ thống tự cập nhật, chỉ đọc              | 2026-10-20T11:30:00 |

## 3. Đặc tả endpoint

| Phương thức | URI              | User story     | Thành công      | Lỗi           |
|-------------|------------------|----------------|-----------------|---------------|
| GET         | /menu-items      | US1 (có query) | 200             | 422           |
| GET         | /menu-items/{id} | US2            | 200             | 404, 422      |
| POST        | /menu-items      | US3            | 201 + Location  | 409, 422      |
| PUT         | /menu-items/{id} | US4 (toàn bộ)  | 200             | 404, 409, 422 |
| PATCH       | /menu-items/{id} | US4 (một phần) | 200             | 404, 409, 422 |
| DELETE      | /menu-items/{id} | US5            | 204             | 404           |

Query params của `GET /menu-items`:
`category`, `available`, `max_price`, `sort` (id | price | name), `order` (asc | desc).

Ví dụ: `/menu-items?category=do_uong&available=true&sort=price&order=desc`

## 4. Mã trạng thái và quy tắc nghiệp vụ

| Mã  | Ý nghĩa              | Khi nào                                            |
|-----|----------------------|----------------------------------------------------|
| 200 | OK                   | Đọc hoặc sửa thành công                            |
| 201 | Created              | Thêm món thành công, kèm header Location           |
| 204 | No Content           | Xóa thành công, không trả nội dung                 |
| 404 | Not Found            | Không có món với id đó                             |
| 409 | Conflict             | Tên món đã tồn tại (BR1)                           |
| 422 | Unprocessable Entity | Dữ liệu sai: giá (BR2), loại món (BR3), id ≤ 0, thiếu trường |
