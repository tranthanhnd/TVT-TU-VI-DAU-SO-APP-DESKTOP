# Hướng dẫn build file .exe cho T&T Tử Vi Đẩu Số App

Khung dự án này đã được chuẩn bị dựa đúng theo cấu hình đã build thành công cho App Nhân Số Học (cùng kiểu Tauri v2, cùng bộ icon T&T Hoàng Gia Royal, cùng workflow GitHub Actions).

## Điểm khác biệt quan trọng so với App Nhân Số Học

App Tử Vi có gọi Gemini API trực tiếp khi chạy (mục Luận giải), khác với Nhân Số Học vốn hoàn toàn ngoại tuyến. Cấu hình `security.csp: null` trong `tauri.conf.json` (giữ nguyên như bản Nhân Số Học) đảm bảo cửa sổ ứng dụng KHÔNG bị chặn gọi mạng ra ngoài — nhưng đây là điểm CẦN TỰ TAY KIỂM TRA THẬT sau khi cài .exe: mở app, nhập API Key, bấm "Luận giải" thử xem có chạy được không. Nếu gặp lỗi mạng, báo lại ngay để xử lý tiếp, đừng coi là chắc chắn chạy được chỉ vì cấu hình giống bản cũ.

## Repo này ĐANG ĐỂ CÔNG KHAI (public)

Khác với App Nhân Số Học (private), repo này để công khai theo đúng lựa chọn của anh — để build .exe qua GitHub Actions HOÀN TOÀN MIỄN PHÍ, KHÔNG GIỚI HẠN số lần (chính sách của GitHub: Actions miễn phí không giới hạn cho repo public dùng runner tiêu chuẩn). Đánh đổi: mã nguồn app (giao diện, logic tính lá số) sẽ hiển thị công khai trên GitHub — không có API Key hay dữ liệu cá nhân nào trong code nên không có rủi ro lộ thông tin.

## Cấu trúc thư mục

```
TVT-TU-VI-DAU-SO-APP-DESKTOP/
├── dist/
│   └── index.html            <- app Tu Vi (ban day du, dong bo voi ban web)
├── src-tauri/
│   ├── icons/                 <- icon T&T Hoang Gia Royal (dung lai tu Nhan So Hoc)
│   ├── tauri.conf.json         <- cau hinh: ten, phien ban, cua so, csp
│   ├── Cargo.toml
│   └── src/ (main.rs, lib.rs)  <- phan loi Tauri, khong can dung vao
├── .github/workflows/build.yml <- quy trinh tu dong build ra .exe
├── package.json
└── HUONG-DAN-BUILD-EXE.md      <- chinh la file nay
```

## Bước 1 — Chạy build

Vào tab **Actions** trên trang GitHub của repo `TVT-TU-VI-DAU-SO-APP-DESKTOP` → chọn workflow **"build-windows"** ở cột trái → bấm **"Run workflow"** → chọn nhánh `main` → Run workflow.

Chờ khoảng 10–20 phút (GitHub Actions cài Rust + Node + build từ đầu nên hơi lâu ở lần đầu). Vì repo công khai nên KHÔNG tốn phút Actions trong hạn mức, không cần lo về chi phí.

## Bước 2 — Tải file .exe

Khi build xong (dấu tích xanh ✅), vào tab **Releases** của repo → sẽ có 1 bản nháp (Draft) tên "T&T Tu Vi Dau So App v1.0.0" → mở ra, tải file **`...-setup.exe`** về máy, cài thử để kiểm tra.

Nếu chạy tốt (kể cả thử luận giải bằng API Key thật), vào lại bản Release đó bấm "Publish release" để chính thức hoá (bỏ trạng thái Draft).

## Lưu ý quan trọng cho lần build sau (rất hay gặp lỗi nếu quên)

Mỗi lần muốn build lại (kể cả chỉ sửa 1 dòng nội dung app), **phải tăng số phiên bản** ở cả 3 chỗ sau, nếu không GitHub sẽ báo lỗi "already_exists" vì tên bản phát hành bị trùng:

- `src-tauri/tauri.conf.json` → dòng `"version": "1.0.0"`
- `src-tauri/Cargo.toml` → dòng `version = "1.0.0"`
- `package.json` → dòng `"version": "1.0.0"` (không bắt buộc nhưng nên đồng bộ)

Đồng thời cần cập nhật lại `dist/index.html` bằng đúng bản mới nhất từ file `TuViDauSo_App_v1.html` trên ổ E: (giống bước đồng bộ đã làm cho bản web).

## Muốn có cả bản macOS sau này?

File `.github/workflows/build.yml` hiện tại **chỉ build Windows**. Khi nào cần thêm bản Mac, chỉ cần báo lại, sẽ mở rộng thêm cấu hình matrix runner `macos-latest` — không cần làm lại từ đầu.
