# 🚀 Hướng dẫn Deploy BookNow trên Render

## ✅ Các Sửa Chữa Đã Hoàn Thành

1. ✅ **Sửa typo** `sspring` → `spring` trong database config
2. ✅ **Port động** `${PORT:8080}` - Render sẽ tự gán port
3. ✅ **Environment Variables** cho tất cả secrets/credentials
4. ✅ **Hardcoded URLs** trong JavaScripts đã fix thành dynamic URLs
5. ✅ **WebSocket URLs** sử dụng protocol/host động từ browser

---

## 🔧 Cấu Hình Render Environment Variables

Trên **Render Dashboard**, thêm các environment variables sau:

```env
# Database
DATABASE_URL=jdbc:postgresql://aws-1-ap-south-1.pooler.supabase.com:6543/postgres
DATABASE_USER=postgres.ghfaxrtcnpmilgqljzwi
DATABASE_PASSWORD=@Khangdinh123

# Google OAuth2
GOOGLE_CLIENT_ID=19768273685-6g9i93fh2ke0sjsilq4n3jcbp0e4cdq3.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=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

# Momo Payment
MOMO_PARTNER_CODE=MOMOLRJZ20181206
MOMO_ACCESS_KEY=mTCKt9W3eU1m39TW
MOMO_SECRET_KEY=SetA5RDnLHvt51AULf51DyauxUo3kDU6
APP_URL=https://your-app-name.onrender.com

# Gmail SMTP
MAIL_USERNAME=vod019608@gmail.com
MAIL_PASSWORD=hvyvhhsxmmjypqak

# Cloudinary
CLOUDINARY_CLOUD_NAME=dzlfgmtbc
CLOUDINARY_API_KEY=144287537763843
CLOUDINARY_API_SECRET=7kJ1TfEb7kEAVArwaY5jq0ctvlA
```

### ⚠️ **Important**: Thay đổi CONFIG trong render.yaml hoặc thông qua UI:

1. Vào **Render Dashboard** → Chọn service
2. → **Settings** → **Environment**
3. → Thêm tất cả biến trên

---

## 📝 Bước Deploy Trên Render

### 1️⃣ Kết nối GitHub
```bash
# Render sẽ tự pull code từ GitHub
# Cấu hình Auto Deploy khi push code mới
```

### 2️⃣ Build Command
```bash
./gradlew build  # hoặc
mvn clean package -DskipTests
```

### 3️⃣ Start Command
```bash
java -jar target/book-now-1.0-SNAPSHOT.jar
```

---

## 🐳 Dockerfile (Đã Tối Ưu)

File `Dockerfile` hiện tại đã có tối ưu:
- Multi-stage build giảm kích thước image
- Sử dụng `eclipse-temurin:21-jdk` chính thức
- Tự động detect `*.jar` từ target

---

## ✨ Chưa Sửa - Nên Làm Thêm:

### 1. **Prod Profile** (Nếu cần)
Tạo `application-prod.properties` cho cấu hình production riêng:

```properties
logging.level.root=INFO
logging.level.org.springframework.web=INFO
spring.jpa.show-sql=false
```

### 2. **Security Config**
- Google OAuth2 redirect URI phải cấu hình: 
  - Local: `http://localhost:8080/book-now/login/oauth2/code/google`
  - Render: `https://your-app-name.onrender.com/book-now/login/oauth2/code/google`

### 3. **Momo Callback URLs**
- Render Dashboard → Application Settings
- Thêm domain: `https://your-app-name.onrender.com`

---

## ⚠️ Lỗi Có Thể Xảy Ra & Cách Fix

| Lỗi | Nguyên Nhân | Cách Fix |
|-----|-----------|---------|
| Database connection failed | Env vars chưa set | Check Render Env tab |
| OAuth2 redirect mismatch | URL sai trong Google Console | Update redirect URI |
| Port already in use | Render không nhận `${PORT}` | Xóa cache, rebuild |
| WebSocket connection refused | Localhost URL cũ | Đã fix - check templates |
| File upload fail | Cloudinary key sai | Kiểm tra API key/secret |

---

## 🔍 Kiểm Tra Sau Deploy

1. **Test Database Connection**
   ```bash
   curl https://your-app-name.onrender.com/book-now/admin/dashboard
   ```

2. **Check Logs**
   ```bash
   # Render Dashboard → Logs
   # Tìm: "Hibernate DDL" hoặc lỗi connection
   ```

3. **Test OAuth2**
   - Click "Login with Google"
   - Kiểm tra callback URL

4. **Test WebSocket**
   - Vào check-in page
   - Console should show: "Connected"

---

## 📞 Cần Giúp?

Chạy lệnh kiểm tra local trước:
```bash
mvn clean package -DskipTests
java -jar target/book-now-1.0-SNAPSHOT.jar
```

Nếu chạy OK trên local → Render cũng OK ✅
