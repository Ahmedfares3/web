# دليل النشر - أفلييت سوق مصر

## 🚀 نشر المشروع على الخادم

### المتطلبات الأساسية
- **خادم**: Ubuntu 20.04+ أو CentOS 8+
- **Node.js**: الإصدار 18 أو أحدث
- **PostgreSQL**: الإصدار 14 أو أحدث
- **Redis**: الإصدار 6 أو أحدث
- **Nginx**: لخدمة الملفات الثابتة والـ Reverse Proxy
- **SSL Certificate**: للأمان (Let's Encrypt مجاني)

## 📋 خطوات النشر

### 1. إعداد الخادم

```bash
# تحديث النظام
sudo apt update && sudo apt upgrade -y

# تثبيت Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# تثبيت PostgreSQL
sudo apt install postgresql postgresql-contrib -y

# تثبيت Redis
sudo apt install redis-server -y

# تثبيت Nginx
sudo apt install nginx -y

# تثبيت PM2 لإدارة العمليات
sudo npm install -g pm2
```

### 2. إعداد قاعدة البيانات

```bash
# الدخول إلى PostgreSQL
sudo -u postgres psql

# إنشاء قاعدة البيانات والمستخدم
CREATE DATABASE affiliate_souk_egypt;
CREATE USER affiliate_user WITH ENCRYPTED PASSWORD 'secure_password_here';
GRANT ALL PRIVILEGES ON DATABASE affiliate_souk_egypt TO affiliate_user;
\q
```

### 3. استنساخ المشروع

```bash
# إنشاء مجلد المشروع
sudo mkdir -p /var/www/affiliate-souk-egypt
cd /var/www/affiliate-souk-egypt

# استنساخ المشروع
git clone https://github.com/your-username/affiliate-souk-egypt.git .

# تغيير الملكية
sudo chown -R $USER:$USER /var/www/affiliate-souk-egypt
```

### 4. إعداد متغيرات البيئة

```bash
# نسخ ملف البيئة
cp .env.example .env

# تحرير الملف
nano .env
```

```env
# Production Environment Variables
NODE_ENV=production
PORT=3000

# Database
DATABASE_URL="postgresql://affiliate_user:secure_password_here@localhost:5432/affiliate_souk_egypt"

# Authentication
NEXTAUTH_URL="https://affiliatesouk.eg"
NEXTAUTH_SECRET="your-super-secure-nextauth-secret-here"
JWT_SECRET="your-super-secure-jwt-secret-here"

# Email Configuration
SMTP_HOST="smtp.gmail.com"
SMTP_PORT="587"
SMTP_USER="noreply@affiliatesouk.eg"
SMTP_PASS="your-app-password"
FROM_EMAIL="noreply@affiliatesouk.eg"

# Redis
REDIS_URL="redis://localhost:6379"

# File Upload
UPLOAD_DIR="/var/www/affiliate-souk-egypt/public/uploads"
MAX_FILE_SIZE="5242880"

# Security
BCRYPT_ROUNDS="12"
SESSION_TIMEOUT="86400000"

# Application
APP_NAME="أفلييت سوق مصر"
APP_URL="https://affiliatesouk.eg"
```

### 5. تثبيت التبعيات وبناء المشروع

```bash
# تثبيت التبعيات
npm ci --only=production

# إنشاء Prisma Client
npx prisma generate

# تطبيق migrations
npx prisma migrate deploy

# إدخال البيانات الأولية
npx prisma db seed

# بناء المشروع
npm run build
```

### 6. إعداد PM2

```bash
# إنشاء ملف تكوين PM2
cat > ecosystem.config.js << EOF
module.exports = {
  apps: [{
    name: 'affiliate-souk-egypt',
    script: 'npm',
    args: 'start',
    cwd: '/var/www/affiliate-souk-egypt',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production',
      PORT: 3000
    },
    error_file: '/var/log/pm2/affiliate-souk-egypt-error.log',
    out_file: '/var/log/pm2/affiliate-souk-egypt-out.log',
    log_file: '/var/log/pm2/affiliate-souk-egypt.log',
    time: true
  }]
}
EOF

# إنشاء مجلد اللوجز
sudo mkdir -p /var/log/pm2
sudo chown -R $USER:$USER /var/log/pm2

# تشغيل التطبيق
pm2 start ecosystem.config.js

# حفظ التكوين
pm2 save

# إعداد التشغيل التلقائي
pm2 startup
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u $USER --hp $HOME
```

### 7. إعداد Nginx

```bash
# إنشاء ملف تكوين الموقع
sudo cat > /etc/nginx/sites-available/affiliate-souk-egypt << EOF
server {
    listen 80;
    server_name affiliatesouk.eg www.affiliatesouk.eg;
    return 301 https://\$server_name\$request_uri;
}

server {
    listen 443 ssl http2;
    server_name affiliatesouk.eg www.affiliatesouk.eg;

    ssl_certificate /etc/letsencrypt/live/affiliatesouk.eg/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/affiliatesouk.eg/privkey.pem;
    
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # Rate limiting
    limit_req_zone \$binary_remote_addr zone=api:10m rate=10r/s;
    limit_req_zone \$binary_remote_addr zone=auth:10m rate=5r/m;

    client_max_body_size 10M;

    location /api/ {
        limit_req zone=api burst=20 nodelay;
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade \$http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
        proxy_cache_bypass \$http_upgrade;
    }

    location /api/auth/ {
        limit_req zone=auth burst=5 nodelay;
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }

    location /_next/static/ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        proxy_pass http://localhost:3000;
    }

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade \$http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
        proxy_cache_bypass \$http_upgrade;
    }
}
EOF

# تفعيل الموقع
sudo ln -s /etc/nginx/sites-available/affiliate-souk-egypt /etc/nginx/sites-enabled/

# اختبار التكوين
sudo nginx -t

# إعادة تشغيل Nginx
sudo systemctl restart nginx
```

### 8. إعداد SSL Certificate

```bash
# تثبيت Certbot
sudo apt install certbot python3-certbot-nginx -y

# الحصول على شهادة SSL
sudo certbot --nginx -d affiliatesouk.eg -d www.affiliatesouk.eg

# إعداد التجديد التلقائي
sudo crontab -e
# إضافة السطر التالي:
# 0 12 * * * /usr/bin/certbot renew --quiet
```

### 9. إعداد النسخ الاحتياطي

```bash
# إنشاء سكريبت النسخ الاحتياطي
sudo cat > /usr/local/bin/backup-affiliate-db.sh << EOF
#!/bin/bash
BACKUP_DIR="/var/backups/affiliate-souk"
DATE=\$(date +%Y%m%d_%H%M%S)
DB_NAME="affiliate_souk_egypt"
DB_USER="affiliate_user"

mkdir -p \$BACKUP_DIR

# نسخ احتياطي لقاعدة البيانات
PGPASSWORD="secure_password_here" pg_dump -h localhost -U \$DB_USER \$DB_NAME > \$BACKUP_DIR/db_backup_\$DATE.sql

# نسخ احتياطي للملفات المرفوعة
tar -czf \$BACKUP_DIR/uploads_backup_\$DATE.tar.gz /var/www/affiliate-souk-egypt/public/uploads/

# حذف النسخ القديمة (أكثر من 30 يوم)
find \$BACKUP_DIR -name "*.sql" -mtime +30 -delete
find \$BACKUP_DIR -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: \$DATE"
EOF

# إعطاء صلاحيات التنفيذ
sudo chmod +x /usr/local/bin/backup-affiliate-db.sh

# إعداد النسخ الاحتياطي اليومي
sudo crontab -e
# إضافة السطر التالي:
# 0 2 * * * /usr/local/bin/backup-affiliate-db.sh
```

### 10. مراقبة النظام

```bash
# تثبيت أدوات المراقبة
sudo apt install htop iotop nethogs -y

# مراقبة PM2
pm2 monit

# مراقبة اللوجز
pm2 logs affiliate-souk-egypt

# مراقبة Nginx
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

## 🔧 الصيانة والتحديث

### تحديث التطبيق

```bash
# الانتقال إلى مجلد المشروع
cd /var/www/affiliate-souk-egypt

# سحب آخر التحديثات
git pull origin main

# تثبيت التبعيات الجديدة
npm ci --only=production

# تطبيق migrations الجديدة
npx prisma migrate deploy

# إعادة بناء المشروع
npm run build

# إعادة تشغيل التطبيق
pm2 restart affiliate-souk-egypt
```

### مراقبة الأداء

```bash
# مراقبة استخدام الموارد
htop

# مراقبة قاعدة البيانات
sudo -u postgres psql -c "SELECT * FROM pg_stat_activity;"

# مراقبة Redis
redis-cli info memory

# فحص مساحة القرص
df -h
```

## 🚨 استكشاف الأخطاء

### مشاكل شائعة

#### التطبيق لا يعمل
```bash
# فحص حالة PM2
pm2 status

# فحص اللوجز
pm2 logs affiliate-souk-egypt --lines 50

# إعادة تشغيل
pm2 restart affiliate-souk-egypt
```

#### مشاكل قاعدة البيانات
```bash
# فحص اتصال قاعدة البيانات
npx prisma db push

# فحص حالة PostgreSQL
sudo systemctl status postgresql
```

#### مشاكل Nginx
```bash
# فحص تكوين Nginx
sudo nginx -t

# فحص حالة Nginx
sudo systemctl status nginx

# إعادة تشغيل Nginx
sudo systemctl restart nginx
```

## 📞 الدعم الفني

للحصول على الدعم الفني:
- **البريد الإلكتروني**: tech-support@affiliatesouk.eg
- **الهاتف**: +20 100 123 4567
- **التوثيق**: راجع ملف TECHNICAL_DOCS.md

---

**تم إنشاء هذا الدليل بواسطة فريق أفلييت سوق مصر**  
**آخر تحديث**: ديسمبر 2024
