# Poly G7500 Integration Starter

Dự án khởi tạo để điều khiển/tích hợp thiết bị **Poly G7500** bằng Python.

## Bao gồm
- `FastAPI` service (health + test kết nối + system status placeholder)
- `httpx` client cho gọi HTTPS API tới thiết bị
- `Typer` CLI để test nhanh kết nối và lấy trạng thái
- Quản lý config qua biến môi trường (`.env`)
- Test scaffold với `pytest`

## 1) Chuẩn bị
1. Tạo virtual environment
2. Cài dependencies:
   - Runtime: `pip install -e .`
   - Dev/test: `pip install -e .[dev]`
3. Copy `.env.example` thành `.env` và điền IP/tài khoản thật của thiết bị.

## 2) Chạy API server
`uvicorn poly_g7500_integration.main:app --reload --host 0.0.0.0 --port 8000`

Các endpoint:
- `GET /health`
- `GET /poly/connect-test`
- `GET /poly/system-status`

## 3) Dùng CLI
- `poly-g7500-cli health`
- `poly-g7500-cli connect-test`
- `poly-g7500-cli system-status`

## 4) Đóng gói để mang sang máy khác
### Build gói phát hành
Từ thư mục dự án, chạy:

`python -m build`

Sau khi build xong sẽ có thư mục `dist/` chứa:
- file `.whl` để cài nhanh bằng `pip`
- file `.tar.gz` để giữ mã nguồn phát hành

### Cài trên máy Windows khác
1. Cài Python 3.11 trở lên.
2. Copy các mục sau sang máy mới:
   - thư mục `dist/`
   - file `.env.example`
   - script `deploy/install_on_windows.ps1` nếu muốn cài tự động
3. Tạo virtual environment:
   - `py -3.11 -m venv .venv`
   - `.venv\Scripts\activate`
4. Cài ứng dụng:
   - `pip install dist/poly_g7500_integration-0.1.0-py3-none-any.whl`
5. Tạo file `.env` từ `.env.example` rồi điền thông tin thiết bị.
6. Có thể chạy script tự động: `powershell -ExecutionPolicy Bypass -File deploy/install_on_windows.ps1`

### Chạy sau khi cài
- GUI: `poly-g7500-gui`
- CLI: `poly-g7500-cli health`
- API: `uvicorn poly_g7500_integration.main:app --host 0.0.0.0 --port 8000`

## 5) Đóng gói thành file `.exe` cho Windows
Nếu muốn mang sang máy khác mà **không cần cài Python**, có thể build GUI thành một file `.exe`.

### Build
1. Cài công cụ build:
   - `pip install pyinstaller`
2. Chạy script:
   - `powershell -ExecutionPolicy Bypass -File deploy/build_gui_exe.ps1`

### Kết quả
- File chạy trực tiếp: `dist-exe/PolyG7500Control.exe`
- Bộ phát hành đã zip sẵn: `release/poly-g7500-exe.zip`

### Mang sang máy khác
1. Copy file `release/poly-g7500-exe.zip` sang máy đích.
2. Giải nén.
3. Copy `.env.example` thành `.env` và điền thông tin Poly G7500.
4. Chạy `PolyG7500Control.exe`.

Ứng dụng sẽ ưu tiên đọc `.env` ngay cạnh file `.exe` và ghi log vào thư mục `logs/` cùng vị trí đó.

## Lưu ý tích hợp Poly G7500
- Tuỳ firmware, endpoint API có thể khác nhau. Dự án này để sẵn pattern gọi API; bạn cần map endpoint thật theo tài liệu firmware đang dùng.
- Nếu thiết bị dùng cert tự ký, giữ `POLY_VERIFY_SSL=false` trong môi trường lab.
- Trước production, bật xác thực TLS đầy đủ.
