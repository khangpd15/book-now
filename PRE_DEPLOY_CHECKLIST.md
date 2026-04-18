# 📋 Pre-Deploy Checklist cho Render

## ✅ Tất Cả Đã Sửa (Hoàn Thành)

- [x] **Typo `sspring`** → `spring` ✅
- [x] **Port động** từ environment variables ✅
- [x] **Database credentials** → environment variables ✅
- [x] **OAuth2 secrets** → environment variables ✅
- [x] **Momo payment keys** → environment variables ✅
- [x] **Email credentials** → environment variables ✅
- [x] **Cloudinary API keys** → environment variables ✅
- [x] **Hardcoded localhost URLs** → dynamic URLs ✅
- [x] **WebSocket URLs** → dynamic URLs ✅
- [x] **Render guide document** ✅
- [x] **render.yaml config file** ✅

---

## 🔧 Bước Chuẩn Bị Final

### 1. Test Trên Local Trước
```bash
cd book-now
mvn clean package -DskipTests
java -jar target/book-now-1.0-SNAPSHOT.jar
```
✅ Nếu chạy OK → Render sẽ OK

### 2. Kiểm Tra Git Config
```bash
git add .
git commit -m "Prepare for Render deployment"
git push origin main
```

### 3. Cấu Hình trên Render Dashboard

**Step 1:** Connect Repository
- GitHub → Authorize Render
- Select: `d:\project-booknow\book-now`

**Step 2:** Create Web Service
- Service name: `book-now`
- Runtime: Java
- Build command: `./mvnw clean package -DskipTests`
- Start command: `java -jar target/book-now-1.0-SNAPSHOT.jar`

**Step 3:** Set Environment Variables (IMPORTANT!)
Copy từ section dưới

---

## 🔑 Environment Variables Cần Set Trên Render

```
# Database (REQUIRED)
DATABASE_URL=jdbc:postgresql://aws-1-ap-south-1.pooler.supabase.com:6543/postgres
DATABASE_USER=postgres.ghfaxrtcnpmilgqljzwi
DATABASE_PASSWORD=@Khangdinh123

# Google OAuth (REQUIRED)
GOOGLE_CLIENT_ID=19768273685-6g9i93fh2ke0sjsilq4n3jcbp0e4cdq3.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=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

# Momo Payment
MOMO_PARTNER_CODE=MOMOLRJZ20181206
MOMO_ACCESS_KEY=mTCKt9W3eU1m39TW
MOMO_SECRET_KEY=SetA5RDnLHvt51AULf51DyauxUo3kDU6
APP_URL=https://your-app-name.onrender.com  # ⚠️ Thay bằng URL thực tế

# Gmail SMTP
MAIL_USERNAME=vod019608@gmail.com
MAIL_PASSWORD=hvyvhhsxmmjypqak

# Cloudinary
CLOUDINARY_CLOUD_NAME=dzlfgmtbc
CLOUDINARY_API_KEY=144287537763843
CLOUDINARY_API_SECRET=7kJ1TfEb7kEAVArwaY5jq0ctvlA
```

---

## 🧪 Kiểm Tra Sau Deploy

### ✅ Health Check
```
GET https://your-app-name.onrender.com/book-now/
→ Response 200 = OK ✅
```

### ✅ Database Connection
```
GET https://your-app-name.onrender.com/book-now/admin/dashboard
→ Nếu load OK = Database connected ✅
```

### ✅ OAuth Login
```
Visit: https://your-app-name.onrender.com/book-now/
Click "Login with Google"
→ Nếu redirect OK = OAuth2 configured ✅
```

### ✅ WebSocket Connection
```
1. Go to "Check-in" page
2. Open Browser DevTools (F12)
3. Console tab
4. Search for: "Connected"
→ If found = WebSocket OK ✅
```

---

## ⚠️ Lỗi Phổ Biến & Fix

| Lỗi | Nguyên nhân | Fix |
|-----|-----------|-----|
| 502 Bad Gateway | App crash | Check Render logs |
| Connection refused | Port không đúng | `${PORT}` environment variable |
| Database error | Credentials sai | Check DATABASE_* env vars |
| OAuth redirect mismatch | URL sai | Add to Google Console redirect URIs |
| WebSocket fail | Localhost URL | Đã fix - cache clear browser |
| File upload fail | Cloudinary sai | Kiểm tra CLOUDINARY_* env vars |

---

## 📲 Nếu Gặp Lỗi

### 1. Check Render Logs
- Render Dashboard → Select Service → Logs

### 2. Common Logs
```
ERROR: Could not resolve placeholder 'DATABASE_URL'
→ FIX: Set DATABASE_URL environment variable

ERROR: Connection refused localhost:6543
→ FIX: DATABASE_URL không được pass, check env vars
```

### 3. Rebuild từ Scratch
```bash
# On Render Dashboard
Settings → Manual Deploy → Deploy Latest Commit
# Hoặc: Clear Build Cache + Redeploy
```

---

## 🎉 Xong!

Nếu tất cả test pass ✅ → Application sẵn sàng production! 🚀

Mọi lỗi đều có nguyên nhân → Check logs + environment variables trước!
