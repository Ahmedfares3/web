# 🚀 دليل نشر موقع أفلييت سوق مصر

## 📋 المتطلبات الأساسية

- Node.js 18+ 
- npm أو yarn
- Git

## 🌐 خيارات النشر

### 1. النشر على Vercel (الأسهل والأسرع)

#### الطريقة الأولى: من خلال GitHub
1. ارفع المشروع على GitHub
2. اذهب إلى [vercel.com](https://vercel.com)
3. سجل دخول بحساب GitHub
4. اضغط "New Project"
5. اختر المستودع
6. اضغط "Deploy"

#### الطريقة الثانية: رفع مباشر
1. ثبت Vercel CLI:
```bash
npm i -g vercel
```

2. في مجلد المشروع:
```bash
vercel
```

3. اتبع التعليمات

### 2. النشر على Netlify

#### من خلال الموقع:
1. اذهب إلى [netlify.com](https://netlify.com)
2. اضغط "Add new site" > "Deploy manually"
3. اسحب مجلد المشروع كاملاً
4. انتظر حتى ينتهي النشر

#### من خلال GitHub:
1. ارفع المشروع على GitHub
2. في Netlify اضغط "New site from Git"
3. اختر GitHub واختر المستودع
4. Build command: `npm run build`
5. Publish directory: `out`

### 3. النشر على GitHub Pages

1. في `next.config.js` تأكد من:
```javascript
const nextConfig = {
  output: 'export',
  trailingSlash: true,
  images: {
    unoptimized: true
  }
}
```

2. ابني المشروع:
```bash
npm run build
```

3. ارفع محتويات مجلد `out` على GitHub Pages

## 🔧 إعداد المشروع للنشر

### 1. تثبيت التبعيات:
```bash
npm install
```

### 2. بناء المشروع:
```bash
npm run build
```

### 3. اختبار البناء محلياً:
```bash
npm start
```

## 📁 ملفات مهمة للنشر

- `vercel.json` - إعدادات Vercel
- `netlify.toml` - إعدادات Netlify  
- `next.config.js` - إعدادات Next.js
- `.gitignore` - ملفات مستبعدة من Git

## 🌍 روابط مفيدة

- [Vercel Docs](https://vercel.com/docs)
- [Netlify Docs](https://docs.netlify.com)
- [Next.js Deployment](https://nextjs.org/docs/deployment)

## 🔍 استكشاف الأخطاء

### خطأ في البناء:
```bash
npm run build
```
تحقق من الأخطاء في الكونسول

### مشاكل التبعيات:
```bash
rm -rf node_modules package-lock.json
npm install
```

### مشاكل TypeScript:
في `next.config.js`:
```javascript
typescript: {
  ignoreBuildErrors: true,
}
```

## 📞 الدعم

إذا واجهت أي مشاكل، تواصل معنا:
- البريد: info@affiliatesouk.eg
- الهاتف: +20 100 123 4567

---

✅ **المشروع جاهز للنشر!**
