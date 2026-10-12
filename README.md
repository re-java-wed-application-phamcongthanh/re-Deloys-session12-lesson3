# Bài 3: Tách Biệt Luồng CI/CD Đa Môi Trường (Staging & Production) Với GitHub Environments

## 🎯 1. Mục Tiêu
- Quản lý hạ tầng triển khai đa môi trường (**Staging** vs **Production**) thông qua tính năng **GitHub Environments**.
- Thiết lập quy tắc bảo vệ môi trường (**Environment Protection Rules**) với tính năng **Required Reviewers**: Yêu cầu phê duyệt thủ công (Manual Gate / Approval) từ Tech Lead / DevOps trước khi mã nguồn được deploy lên Production.
- Tự động điều hướng triển khai theo nhánh: Nhánh `develop` triển khai lên môi trường `staging`, nhánh `main` triển khai lên môi trường `production`.

---

## 📁 2. Cấu Trúc Thư Mục
```text
bai3/
├── .github/
│   └── workflows/
│       └── cd-multi-env.yml     # Kịch bản CD phân luồng đa môi trường
└── README.md                    # Hướng dẫn cấu hình Protection Rules & kiểm tra
```

---

## ⚙️ 3. Kịch Bản Workflow Đa Môi Trường (`.github/workflows/cd-multi-env.yml`)

```yaml
name: Multi-Environment CD Pipeline (Staging & Production)

on:
  push:
    branches:
      - main
      - develop

jobs:
  deploy-staging:
    name: Deploy to Staging Environment
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    environment:
      name: staging
      url: https://staging.example.com
    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v5

      - name: Triển khai lên môi trường Staging
        run: |
          echo "Triển khai phiên bản thử nghiệm lên môi trường Staging thành công!"
          echo "URL: https://staging.example.com"

  deploy-prod:
    name: Deploy to Production Environment
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://api.prod.example.com
    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v5

      - name: Triển khai lên môi trường Production
        run: |
          echo "Đã phê duyệt thủ công! Triển khai ứng dụng lên môi trường Production thành công!"
          echo "URL: https://api.prod.example.com"
```

---

## 🔐 4. Hướng Dẫn Cấu Hình GitHub Environments Trên Settings

### Bước 1: Khởi tạo Environment `staging`
1. Truy cập Repository trên GitHub: [re-Deloys-session12-lesson3](https://github.com/re-java-wed-application-phamcongthanh/re-Deloys-session12-lesson3).
2. Vào **Settings** -> Danh mục bên trái chọn mục **Environments**.
3. Nhấp nút **New environment**, nhập tên: `staging` và bấm **Configure environment**.

### Bước 2: Khởi tạo Environment `production` & Bật Protection Rules
1. Nhấp nút **New environment**, nhập tên: `production` và bấm **Configure environment**.
2. Tại mục **Environment protection rules**:
   - Tích chọn ô **Required reviewers**.
   - Tìm kiếm và chọn tài khoản GitHub của bạn làm người phê duyệt (Reviewer).
   - *(Tùy chọn) Bật Wait timer hoặc Deployment branches chỉ cho phép nhánh `main`*.
3. Nhấp **Save protection rules** để hoàn tất cấu hình.

---

## 🔍 5. Kiểm Tra Luồng Hoạt Động & Phê Duyệt

1. **Đẩy mã nguồn lên nhánh `main`**:
   ```bash
   git add .
   git commit -m "feat: complete session 12 exercise 3 with cd-multi-env.yml workflow"
   git push origin main
   ```
2. **Quan sát trạng thái luồng chạy trên tab Actions**:
   - Mở tab **Actions** trên repository.
   - Nhấp vào workflow run mới nhất. Bạn sẽ thấy Job `deploy-prod` dừng lại với trạng thái màu vàng: **Waiting for review** *(1 environment waiting for review: production)*.
3. **Phê duyệt triển khai (Approve and Deploy)**:
   - Nhấp vào nút **Review deployments**.
   - Tích chọn môi trường `production`.
   - Nhấp nút **Approve and deploy**.
   - Job `deploy-prod` sẽ ngay lập tức được giải phóng, chuyển sang trạng thái thực thi và tích xanh (Success ✅).
4. Chụp ảnh màn hình cửa sổ phê duyệt deployment để nộp bài.
