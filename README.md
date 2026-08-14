# Chill Workspace Management

Dự án Chill Workspace là hệ thống quản lý web cho quán cà phê / phòng chờ, bao gồm:
- Frontend React + Vite cho giao diện Admin và Nhân viên.
- Backend Express + PostgreSQL + Redis cho API, đăng ký, đăng nhập, chấm công, đặt bàn/phòng và tính doanh thu.

## Tổng quan

Hai vai trò chính:
- **Admin**: quản lý dashboard, duyệt tài khoản nhân viên, xem báo cáo doanh thu, quản lý trạng thái hoạt động.
- **Nhân viên (Staff)**: đăng ký tài khoản, đăng nhập, tạo đơn hàng bàn/phòng, order thêm món, checkout bàn/phòng và theo dõi ca làm.

## Cấu trúc dự án

- `cwd-backend/` - thư mục backend Node.js.
- `cwd-frontend/` - thư mục frontend React + Vite.
- `docker-compose.yml` - chạy nhanh toàn bộ hệ thống với PostgreSQL, Redis, backend và frontend.
- `cwd-backend/init.sql` - seed dữ liệu ban đầu cho PostgreSQL (menu, admin mặc định, cấu trúc bảng).

## Cây thư mục dự án

Bạn có thể dán trực tiếp phần này vào `README.md` trên GitHub để mô tả cấu trúc:

```text
D:\CWD
├─ docker-compose.yml
├─ docker-compose.override.yml
├─ cwd-backend
│  ├─ .dockerignore
│  ├─ .env
│  ├─ Dockerfile
│  ├─ init.sql
│  ├─ package.json
│  ├─ package-lock.json
│  ├─ scripts
│  ├─ src
│  │  ├─ app.js
│  │  ├─ config
│  │  ├─ middleware
│  │  ├─ routes
│  │  └─ services
│  ├─ testdb.js
│  └─ tests
├─ cwd-frontend
│  ├─ .dockerignore
│  ├─ .env.development
│  ├─ Dockerfile
│  ├─ eslint.config.js
│  ├─ index.html
│  ├─ nginx.conf
│  ├─ package.json
│  ├─ package-lock.json
│  ├─ postcss.config.js
│  ├─ public
│  ├─ README.md
│  ├─ src
│  │  ├─ App.css
│  │  ├─ App.jsx
│  │  ├─ assets
│  │  ├─ context
│  │  ├─ index.css
│  │  ├─ main.jsx
│  │  └─ pages
│  ├─ tailwind.config.js
│  └─ vite.config.js
└─ README.md
```

## Cài đặt dự án từ cấu trúc

1. Mở terminal tại thư mục gốc dự án `D:\CWD`.
2. Kiểm tra cấu trúc thư mục chính:

```powershell
dir
```

Bạn sẽ thấy các thư mục và file chính:
- `cwd-backend/`
- `cwd-frontend/`
- `docker-compose.yml`
- `cwd-backend/init.sql`

3. Nếu chạy bằng Docker Compose, mọi thứ sẽ tự động cài đặt và khởi tạo theo file `docker-compose.yml`.
4. Nếu muốn chạy thủ công, bạn cần cài đặt dependencies riêng cho từng phần:
   - Backend: vào `cwd-backend/`, chạy `npm install`.
   - Frontend: vào `cwd-frontend/`, chạy `npm install`.
5. Khởi tạo cơ sở dữ liệu PostgreSQL và Redis bằng Docker Compose hoặc cài đặt thủ công ngoài project.

## Yêu cầu

- Node.js 20.x
- npm
- Docker và Docker Compose (nếu muốn chạy bằng container)
- PostgreSQL 15 (khi chạy thủ công)
- Redis (khi chạy thủ công)

## Chạy nhanh bằng Docker Compose

1. Mở terminal tại thư mục gốc dự án `D:\CWD`.
2. Khởi động toàn bộ hệ thống (gồm frontend, backend, PostgreSQL, Redis):

```powershell
docker compose --profile prod up --build
```

3. Sau khi Docker khởi động xong:
- Frontend truy cập: `http://localhost:3000`
- Backend API: `http://localhost:5000`
- Endpoint health: `http://localhost:5000/health`

4. Dừng dịch vụ:

```powershell
docker compose down
```

## Chạy thủ công (local development)

### Backend

1. Vào thư mục backend:

```powershell
cd cwd-backend
```

2. Cài dependencies:

```powershell
npm install
```

3. Tạo hoặc kiểm tra file môi trường `cwd-backend\.env` có nội dung tương tự:

```env
PORT=5000
DB_HOST=localhost
DB_PORT=5433
DB_USER=postgres
DB_PASSWORD=cwd_password
DB_NAME=cwd_db
REDIS_URL=redis://localhost:6379
JWT_SECRET=cwd_super_secret_key_2026
```

4. Khởi động backend:

```powershell
npm run dev
```

> Backend sẽ lắng nghe mặc định tại `http://localhost:5000`.

### Frontend

1. Vào thư mục frontend:

```powershell
cd cwd-frontend
```

2. Cài dependencies:

```powershell
npm install
```

3. Chỉnh file `cwd-frontend\.env.development` nếu cần.
- Mặc định file hiện tại đang cấu hình `VITE_BACKEND_URL=http://backend:5000` cho môi trường Docker.
- Khi chạy local, nếu backend chạy tại `http://localhost:5000`, sửa thành:

```env
VITE_BACKEND_URL=http://localhost:5000
```

4. Chạy frontend:

```powershell
npm run dev
```

5. Mở trình duyệt vào URL hiển thị trong terminal (mặc định: `http://localhost:5173`).

### Build frontend production

Trong thư mục `cwd-frontend`:

```powershell
npm run build
```

Nếu muốn xem trước build production:

```powershell
npm run preview
```

## Quy trình nghiệp vụ

### 1. Đăng ký và duyệt tài khoản

- Nhân viên mở trang đăng ký và tạo tài khoản tại form `Register`.
- Sau khi đăng ký, tài khoản nhân viên ở trạng thái chờ duyệt.
- Admin đăng nhập bằng tài khoản mặc định hoặc tài khoản admin đã tạo sẵn.
- Admin duyệt hoặc từ chối tài khoản nhân viên trong dashboard.
- Nhân viên chỉ có thể đăng nhập sau khi admin duyệt.

### 2. Đăng nhập và phân quyền

- Admin đăng nhập sẽ vào giao diện Admin Dashboard.
- Nhân viên đăng nhập sẽ vào giao diện Staff Workspace.
- Khi session còn hợp lệ, hệ thống tự động gọi API `/api/auth/me` để tải lại thông tin người dùng.

### 3. Nhân viên vận hành bán hàng

- Nhân viên có thể tạo đơn hàng cho bàn/phòng hoặc chọn combo/gói dịch vụ.
- Nhân viên dùng biểu mẫu trong `StaffWorkspace` để chọn loại phòng, số bàn/phòng, thời gian vào, và phương thức thanh toán.
- Khi khách yêu cầu gọi thêm món, nhân viên dùng chức năng `order thêm` với danh sách món hiện có.
- Nhân viên `checkout` bàn/phòng khi khách đã thanh toán.
- Danh sách bàn/phòng đang phục vụ được cập nhật ở mục `Active Tables`.

### 4. Admin quản lý và báo cáo

- Admin theo dõi doanh thu tổng, doanh thu theo ngày, xu hướng doanh thu và tỷ lệ thanh toán.
- Admin xem danh sách nhân viên đã duyệt và tổng số giờ làm, lương ước tính, doanh số của từng nhân viên.
- Admin xem danh sách tài khoản nhân viên mới đang chờ duyệt.
- Admin có thể xuất báo cáo đơn hàng ngày ra file Excel trực tiếp từ dashboard.

## Các API chính

Backend cung cấp các route chính sau:
- `POST /api/auth/register` - đăng ký nhân viên mới.
- `POST /api/auth/login` - đăng nhập.
- `GET /api/auth/me` - lấy thông tin người dùng hiện tại từ JWT.
- `GET /api/bookings/menu` - lấy danh sách menu công khai.
- `POST /api/bookings/orders` - tạo đơn hàng mới.
- `GET /api/bookings/orders/active` - lấy các đơn hàng/bàn đang phục vụ.
- `POST /api/bookings/orders/:id/add-items` - order thêm món cho đơn hàng.
- `POST /api/bookings/orders/:id/checkout` - checkout bàn/phòng.
- `GET /api/staff/active-shift` - lấy ca làm hiện tại của nhân viên.
- `POST /api/staff/clock-in` - bắt đầu ca làm.
- `POST /api/staff/clock-out` - kết thúc ca làm.
- `GET /api/staff/admin/dashboard` - lấy dữ liệu dashboard cho admin.
- `POST /api/staff/admin/users/:userId/approve` - duyệt tài khoản nhân viên.
- `POST /api/staff/admin/users/:userId/reject` - từ chối tài khoản nhân viên.

## Tài khoản mặc định

- Username: `admin`
- Password: `Admin@123`

> Tài khoản admin mặc định được seed từ `cwd-backend/init.sql`.

## Ghi chú

- Frontend sử dụng proxy `/api` trong `cwd-frontend/vite.config.js` để gửi yêu cầu đến backend.
- Backend lưu token JWT trong `localStorage` và gửi `Authorization` khi gọi các API bảo mật.
- Cơ sở dữ liệu PostgreSQL và Redis được cấu hình sẵn khi chạy qua Docker Compose.
- Nếu dùng Docker Compose, cổng PostgreSQL là `5433` và Redis là `6379` trên máy host.
- Frontend Docker service chỉ chạy dưới profile `prod`, nên cần thêm `--profile prod` khi gọi `docker compose up`.
