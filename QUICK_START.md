# ⚡ دليل البدء السريع

## 🚀 تشغيل الموقع محلياً

### 1. تثبيت Node.js
تأكد من تثبيت Node.js 18+ من [nodejs.org](https://nodejs.org)

### 2. فتح Terminal/Command Prompt
اذهب إلى مجلد المشروع:
```bash
cd path/to/project
```

### 3. تثبيت التبعيات
```bash
npm install
```

### 4. تشغيل الخادم
```bash
npm run dev
```

### 5. فتح الموقع
افتح المتصفح واذهب إلى: `http://localhost:3000`

## 📱 الصفحات المتاحة

- `/` - الصفحة الرئيسية
- `/login` - تسجيل الدخول  
- `/register` - إنشاء حساب

## 🔧 حل المشاكل الشائعة

### المشكلة: `'next' is not recognized`
**الحل:**
```bash
npm install next react react-dom
npm run dev
```

### المشكلة: `Port 3000 is already in use`
**الحل:**
```bash
npm run dev -- -p 3001
```

### المشكلة: أخطاء في التبعيات
**الحل:**
```bash
rm -rf node_modules
rm package-lock.json
npm install
```

## 🌐 النشر السريع

### Vercel (الأسرع):
1. اذهب إلى [vercel.com](https://vercel.com)
2. اسحب مجلد المشروع
3. انتظر النشر

### Netlify:
1. اذهب إلى [netlify.com](https://netlify.com)
2. اضغط "Deploy manually"
3. اسحب مجلد المشروع

---

🎉 **الموقع جاهز للاستخدام!**
