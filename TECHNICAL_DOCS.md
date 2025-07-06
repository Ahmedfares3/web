# الوثائق الفنية - أفلييت سوق مصر

## 📋 نظرة عامة

أفلييت سوق مصر هو منصة تسويق بالعمولة شاملة مبنية باستخدام Next.js 15 مع TypeScript، مصممة خصيصاً للسوق المصري مع دعم كامل للغة العربية وتخطيط RTL.

## 🏗️ معمارية النظام

### Frontend Architecture
```
src/
├── app/                    # Next.js App Router
│   ├── (auth)/            # Authentication routes
│   ├── admin/             # Admin panel
│   ├── dashboard/         # User dashboard
│   └── api/               # API routes
├── components/            # Reusable components
│   ├── ui/               # Base UI components
│   └── features/         # Feature-specific components
├── lib/                   # Utilities and configurations
└── hooks/                # Custom React hooks
```

### Backend Architecture
- **API Routes**: Next.js API routes for server-side logic
- **Database**: PostgreSQL with Prisma ORM
- **Authentication**: JWT-based with NextAuth.js
- **Caching**: Redis for session and data caching
- **File Storage**: Local storage with future cloud integration

## 🗄️ قاعدة البيانات

### ERD (Entity Relationship Diagram)
```
Users (1) ──── (1) Affiliates
  │                 │
  │                 │ (1)
  │                 │
  │                 ▼ (N)
  │              Clicks ──── (N) Campaigns (1)
  │                 │              │
  │                 │ (1)          │
  │                 │              │
  │                 ▼ (1)          │
  │            Conversions ────────┘
  │
  │ (1)
  │
  ▼ (N)
Notifications
```

### الجداول الرئيسية

#### Users Table
```sql
CREATE TABLE users (
  id VARCHAR PRIMARY KEY,
  email VARCHAR UNIQUE NOT NULL,
  firstName VARCHAR NOT NULL,
  lastName VARCHAR NOT NULL,
  password VARCHAR NOT NULL,
  role UserRole DEFAULT 'AFFILIATE',
  status UserStatus DEFAULT 'ACTIVE',
  emailVerified BOOLEAN DEFAULT false,
  createdAt TIMESTAMP DEFAULT NOW(),
  updatedAt TIMESTAMP DEFAULT NOW()
);
```

#### Affiliates Table
```sql
CREATE TABLE affiliates (
  id VARCHAR PRIMARY KEY,
  userId VARCHAR UNIQUE REFERENCES users(id),
  affiliateCode VARCHAR UNIQUE NOT NULL,
  referralCode VARCHAR UNIQUE NOT NULL,
  commissionRate DECIMAL DEFAULT 0.05,
  totalEarnings DECIMAL DEFAULT 0,
  pendingEarnings DECIMAL DEFAULT 0,
  paidEarnings DECIMAL DEFAULT 0,
  isVerified BOOLEAN DEFAULT false,
  createdAt TIMESTAMP DEFAULT NOW()
);
```

## 🔐 نظام الأمان

### Authentication Flow
```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant API
    participant Database
    
    User->>Frontend: Login Request
    Frontend->>API: POST /api/auth/login
    API->>Database: Validate Credentials
    Database-->>API: User Data
    API->>API: Generate JWT Token
    API-->>Frontend: Token + User Data
    Frontend->>Frontend: Store Token
    Frontend-->>User: Redirect to Dashboard
```

### Security Measures
1. **Password Hashing**: bcrypt with 12 rounds
2. **JWT Tokens**: Secure token generation with expiration
3. **Rate Limiting**: API endpoint protection
4. **CSRF Protection**: Cross-site request forgery prevention
5. **XSS Protection**: Input sanitization and output encoding
6. **SQL Injection Prevention**: Prisma ORM parameterized queries

### Security Headers
```javascript
const securityHeaders = {
  'X-Content-Type-Options': 'nosniff',
  'X-Frame-Options': 'DENY',
  'X-XSS-Protection': '1; mode=block',
  'Referrer-Policy': 'strict-origin-when-cross-origin',
  'Content-Security-Policy': "default-src 'self'...",
  'Strict-Transport-Security': 'max-age=31536000'
}
```

## 📊 تتبع الأداء

### Performance Monitoring
```typescript
// Web Vitals tracking
performanceMonitor.trackWebVitals()

// Custom metrics
performanceMonitor.recordMetric('api.response.time', responseTime)
performanceMonitor.recordMetric('database.query.time', queryTime)
```

### Caching Strategy
1. **Browser Caching**: Static assets with long-term caching
2. **API Caching**: GET requests cached for 5 minutes
3. **Database Caching**: Frequently accessed data cached in Redis
4. **CDN Caching**: Static content delivery optimization

## 🔄 API Documentation

### Authentication Endpoints

#### POST /api/auth/login
```typescript
// Request
{
  email: string
  password: string
}

// Response
{
  user: {
    id: string
    email: string
    firstName: string
    lastName: string
    role: string
  }
  token: string
}
```

#### POST /api/auth/register
```typescript
// Request
{
  firstName: string
  lastName: string
  email: string
  phone?: string
  password: string
}

// Response
{
  user: UserData
  token: string
}
```

### Dashboard Endpoints

#### GET /api/dashboard/stats
```typescript
// Response
{
  totalEarnings: number
  pendingEarnings: number
  totalClicks: number
  totalConversions: number
  conversionRate: number
  activeCampaigns: number
}
```

#### GET /api/campaigns
```typescript
// Response
{
  campaigns: Array<{
    id: string
    name: string
    description: string
    commissionType: 'PERCENTAGE' | 'FIXED'
    commissionValue: number
    status: 'ACTIVE' | 'INACTIVE'
  }>
}
```

## 🧪 استراتيجية الاختبار

### Test Pyramid
```
    /\
   /  \     E2E Tests (Playwright)
  /____\    
 /      \   Integration Tests (Jest + Testing Library)
/________\  Unit Tests (Jest)
```

### Test Coverage Goals
- **Unit Tests**: 80% coverage minimum
- **Integration Tests**: Critical user flows
- **E2E Tests**: Main user journeys

### Running Tests
```bash
# Unit tests
npm run test

# Integration tests
npm run test:integration

# E2E tests
npm run test:e2e

# Coverage report
npm run test:coverage
```

## 🚀 النشر والتشغيل

### Environment Variables
```env
# Database
DATABASE_URL="postgresql://user:pass@host:5432/db"

# Authentication
NEXTAUTH_URL="https://your-domain.com"
NEXTAUTH_SECRET="your-secret-key"
JWT_SECRET="your-jwt-secret"

# Email
SMTP_HOST="smtp.gmail.com"
SMTP_USER="your-email@gmail.com"
SMTP_PASS="your-app-password"

# Redis
REDIS_URL="redis://localhost:6379"
```

### Docker Deployment
```bash
# Build and run with Docker Compose
docker-compose up -d

# Scale services
docker-compose up -d --scale app=3

# View logs
docker-compose logs -f app
```

### Production Checklist
- [ ] Environment variables configured
- [ ] SSL certificates installed
- [ ] Database migrations applied
- [ ] Redis cache configured
- [ ] Monitoring tools setup
- [ ] Backup strategy implemented
- [ ] CDN configured
- [ ] Security headers enabled

## 📈 مراقبة النظام

### Health Checks
```typescript
// Health check endpoint
GET /api/health

// Response
{
  status: 'healthy',
  database: 'connected',
  redis: 'connected',
  uptime: 3600,
  memory: {
    used: '150MB',
    total: '512MB'
  }
}
```

### Logging Strategy
```typescript
// Structured logging
logger.info('User login', {
  userId: user.id,
  ip: request.ip,
  userAgent: request.headers['user-agent']
})

logger.error('Database error', {
  error: error.message,
  stack: error.stack,
  query: sanitizedQuery
})
```

### Metrics Collection
- **Response Times**: API endpoint performance
- **Error Rates**: Application error tracking
- **User Activity**: Login, registration, conversions
- **Business Metrics**: Revenue, commissions, user growth

## 🔧 التطوير والصيانة

### Code Style
- **ESLint**: Code quality enforcement
- **Prettier**: Code formatting
- **TypeScript**: Type safety
- **Husky**: Git hooks for quality gates

### Git Workflow
```
main (production)
├── develop (staging)
│   ├── feature/user-dashboard
│   ├── feature/payment-system
│   └── hotfix/security-patch
```

### Release Process
1. Feature development in feature branches
2. Merge to develop for staging deployment
3. Testing and QA on staging
4. Merge to main for production deployment
5. Tag release with semantic versioning

## 🛠️ استكشاف الأخطاء

### Common Issues

#### Database Connection Issues
```bash
# Check database connectivity
npx prisma db push

# Reset database
npx prisma migrate reset

# View database
npx prisma studio
```

#### Performance Issues
```typescript
// Enable query logging
const prisma = new PrismaClient({
  log: ['query', 'info', 'warn', 'error'],
})

// Monitor slow queries
performanceMonitor.recordMetric('db.query.slow', queryTime)
```

#### Memory Leaks
```bash
# Monitor memory usage
node --inspect server.js

# Heap dump analysis
npm install -g clinic
clinic doctor -- node server.js
```

## 📚 المراجع والموارد

### Documentation Links
- [Next.js Documentation](https://nextjs.org/docs)
- [Prisma Documentation](https://www.prisma.io/docs)
- [TypeScript Handbook](https://www.typescriptlang.org/docs)
- [Tailwind CSS](https://tailwindcss.com/docs)

### Best Practices
- [React Best Practices](https://react.dev/learn)
- [Node.js Security](https://nodejs.org/en/docs/guides/security)
- [Database Design](https://www.postgresql.org/docs/current/ddl.html)

---

**آخر تحديث**: ديسمبر 2024  
**الإصدار**: 1.0.0
