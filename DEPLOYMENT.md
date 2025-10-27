# Deployment Guide - Avichal Mind

Complete guide for deploying Avichal Mind to production.

## Table of Contents

1. [Pre-Deployment Checklist](#pre-deployment-checklist)
2. [Environment Setup](#environment-setup)
3. [Database Deployment](#database-deployment)
4. [Vercel Deployment](#vercel-deployment)
5. [Domain Configuration](#domain-configuration)
6. [Environment Variables](#environment-variables)
7. [Post-Deployment Steps](#post-deployment-steps)
8. [Monitoring & Maintenance](#monitoring--maintenance)
9. [Rollback Procedures](#rollback-procedures)
10. [Troubleshooting](#troubleshooting)

---

## Pre-Deployment Checklist

Before deploying to production, ensure:

### Code Quality
- [ ] All tests passing (`npm test`)
- [ ] No TypeScript errors (`npm run type-check`)
- [ ] Linting passes (`npm run lint`)
- [ ] Build succeeds (`npm run build`)
- [ ] No console errors or warnings in production build

### Security
- [ ] All API keys are in environment variables (not hardcoded)
- [ ] `.env.local` is in `.gitignore`
- [ ] Clerk webhook secrets configured
- [ ] CORS settings are production-ready
- [ ] Rate limiting enabled
- [ ] SQL injection protection (using Mongoose)
- [ ] XSS protection enabled

### Performance
- [ ] Images optimized
- [ ] Code splitting configured
- [ ] Bundle size acceptable (<500KB initial load)
- [ ] Lighthouse score > 90
- [ ] Critical CSS inlined

### Compliance
- [ ] Privacy policy updated
- [ ] Terms of service ready
- [ ] DPDP Act compliance verified
- [ ] Cookie consent implemented
- [ ] Data retention policy configured

### Documentation
- [ ] README.md up to date
- [ ] API documentation complete
- [ ] Environment variables documented
- [ ] Deployment guide reviewed

---

## Environment Setup

### 1. Production Environment Variables

Create a `.env.production` file (DO NOT commit):

```bash
# Application
NODE_ENV=production
NEXT_PUBLIC_APP_URL=https://yourdomain.com

# Database
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/avichal-mind-prod?retryWrites=true&w=majority

# Clerk Authentication
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_live_xxxxx
CLERK_SECRET_KEY=sk_live_xxxxx
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
CLERK_WEBHOOK_SECRET=whsec_xxxxx

# AI Services
OPENAI_API_KEY=sk-xxxxx
GEMINI_API_KEY=xxxxx

# Voice Services (Twilio)
TWILIO_ACCOUNT_SID=ACxxxxx
TWILIO_AUTH_TOKEN=xxxxx
TWILIO_PHONE_NUMBER=+1234567890

# YouTube API
YOUTUBE_API_KEY=xxxxx

# Audio Services
ELEVENLABS_API_KEY=xxxxx
DEEPGRAM_API_KEY=xxxxx

# Monitoring (Optional)
SENTRY_DSN=https://xxxxx@sentry.io/xxxxx
VERCEL_ANALYTICS_ID=xxxxx
```

### 2. Secrets Management

**Using Vercel:**
```bash
# Install Vercel CLI
npm i -g vercel

# Login to Vercel
vercel login

# Add environment variables
vercel env add MONGODB_URI
vercel env add CLERK_SECRET_KEY
vercel env add OPENAI_API_KEY
# ... add all secrets
```

**Best Practices:**
- Never commit `.env.production`
- Use different API keys for production vs development
- Rotate secrets regularly (every 90 days)
- Use strong, unique passwords
- Enable 2FA on all service accounts

---

## Database Deployment

### MongoDB Atlas Setup

#### 1. Create Production Cluster

```bash
# Login to MongoDB Atlas
# https://cloud.mongodb.com/

# Create new cluster:
- Cloud Provider: AWS
- Region: Mumbai (ap-south-1) or closest to users
- Tier: M10 or higher (for production)
- Cluster Name: avichal-mind-prod
```

#### 2. Configure Security

**IP Whitelist:**
```
# Add Vercel IP ranges
0.0.0.0/0  # For Vercel (they use dynamic IPs)

# Or use Vercel's static outbound IPs:
76.76.21.21
76.76.21.142
# ... (check Vercel docs for full list)
```

**Database User:**
```
Username: avichal-prod-user
Password: <strong-generated-password>
Role: readWrite on avichal-mind-prod database
```

#### 3. Database Indexes

Create indexes for performance:

```javascript
// Connect to MongoDB
mongosh "mongodb+srv://cluster.mongodb.net/avichal-mind-prod"

// Sessions collection
db.sessions.createIndex({ userId: 1, createdAt: -1 });
db.sessions.createIndex({ status: 1 });
db.sessions.createIndex({ startTime: -1 });

// Messages collection
db.messages.createIndex({ sessionId: 1, timestamp: 1 });
db.messages.createIndex({ userId: 1, timestamp: -1 });

// Users collection
db.users.createIndex({ clerkId: 1 }, { unique: true });
db.users.createIndex({ email: 1 });

// Summaries collection
db.summaries.createIndex({ userId: 1, generatedAt: -1 });
db.summaries.createIndex({ sessionId: 1 }, { unique: true });
```

#### 4. Backup Configuration

```
# In MongoDB Atlas Console:
- Navigate to Backup tab
- Enable Continuous Backup
- Set retention period: 30 days
- Configure backup frequency: Every 6 hours
- Set up backup alerts
```

---

## Vercel Deployment

### 1. Connect Repository

```bash
# Option 1: Using Vercel Dashboard
1. Go to https://vercel.com/new
2. Import your Git repository
3. Select the main branch
4. Configure project settings

# Option 2: Using Vercel CLI
vercel login
vercel --prod
```

### 2. Project Configuration

**vercel.json:**
```json
{
  "buildCommand": "npm run build",
  "devCommand": "npm run dev",
  "installCommand": "npm install",
  "framework": "nextjs",
  "regions": ["bom1"],
  "functions": {
    "api/**/*.ts": {
      "maxDuration": 60
    }
  },
  "headers": [
    {
      "source": "/api/(.*)",
      "headers": [
        {
          "key": "Access-Control-Allow-Origin",
          "value": "https://yourdomain.com"
        },
        {
          "key": "Access-Control-Allow-Methods",
          "value": "GET, POST, PUT, DELETE, OPTIONS"
        },
        {
          "key": "Access-Control-Allow-Headers",
          "value": "Content-Type, Authorization"
        }
      ]
    }
  ]
}
```

### 3. Build Settings

In Vercel Dashboard:
```
Framework Preset: Next.js
Build Command: npm run build
Output Directory: .next
Install Command: npm install
Development Command: npm run dev

Node.js Version: 18.x
```

### 4. Environment Variables

Add all production environment variables in Vercel Dashboard:

```
Settings → Environment Variables

Add each variable:
- Variable name: MONGODB_URI
- Value: <your-value>
- Environment: Production
```

### 5. Deploy

```bash
# Deploy to production
vercel --prod

# Or push to main branch (auto-deploy)
git push origin main
```

**Deployment URL:**
```
Production: https://avichalmind.com
Preview: https://avichal-mind-git-branch-name.vercel.app
```

---

## Domain Configuration

### 1. Add Custom Domain

**In Vercel Dashboard:**
```
Settings → Domains → Add Domain

Domain: avichalmind.com
```

### 2. DNS Configuration

**At your domain registrar (e.g., GoDaddy, Namecheap):**

```
# A Record
Type: A
Name: @
Value: 76.76.21.21 (Vercel's IP)
TTL: 3600

# CNAME for www
Type: CNAME
Name: www
Value: cname.vercel-dns.com
TTL: 3600
```

### 3. SSL Certificate

Vercel automatically provisions SSL certificates via Let's Encrypt.

**Verify:**
```bash
curl -I https://avichalmind.com
# Should show: HTTP/2 200
```

---

## Environment Variables

### Complete Production List

```bash
# ============================================
# APPLICATION
# ============================================
NODE_ENV=production
NEXT_PUBLIC_APP_URL=https://avichalmind.com

# ============================================
# DATABASE
# ============================================
MONGODB_URI=mongodb+srv://user:pass@cluster.mongodb.net/avichal-mind-prod

# ============================================
# AUTHENTICATION (Clerk)
# ============================================
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_live_xxxxx
CLERK_SECRET_KEY=sk_live_xxxxx
CLERK_WEBHOOK_SECRET=whsec_xxxxx
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/dashboard
NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/dashboard

# ============================================
# AI SERVICES
# ============================================
OPENAI_API_KEY=sk-xxxxx
GEMINI_API_KEY=xxxxx
AI_MODEL=gpt-4  # or gemini-pro
AI_TEMPERATURE=0.7
AI_MAX_TOKENS=500

# ============================================
# VOICE (Twilio)
# ============================================
TWILIO_ACCOUNT_SID=ACxxxxx
TWILIO_AUTH_TOKEN=xxxxx
TWILIO_PHONE_NUMBER=+919876543210
TWILIO_WEBHOOK_URL=https://avichalmind.com/api/voice-webhook

# ============================================
# YOUTUBE API
# ============================================
YOUTUBE_API_KEY=xxxxx

# ============================================
# AUDIO SERVICES
# ============================================
ELEVENLABS_API_KEY=xxxxx
DEEPGRAM_API_KEY=xxxxx

# ============================================
# MONITORING & ANALYTICS
# ============================================
SENTRY_DSN=https://xxxxx@sentry.io/xxxxx
VERCEL_ANALYTICS_ID=xxxxx

# ============================================
# FEATURE FLAGS
# ============================================
ENABLE_VOICE_CALLS=true
ENABLE_GAMIFICATION=true
ENABLE_CRISIS_VIDEOS=true

# ============================================
# RATE LIMITING
# ============================================
RATE_LIMIT_CHATBOT=30  # per minute
RATE_LIMIT_SESSION_CREATE=10  # per hour
RATE_LIMIT_GLOBAL=100  # per minute
```

---

## Post-Deployment Steps

### 1. Verify Deployment

```bash
# Check homepage
curl https://avichalmind.com
# Should return 200 OK

# Check API health
curl https://avichalmind.com/api/health
# Should return: {"status": "ok"}

# Test authentication
# Visit https://avichalmind.com/sign-in
# Should redirect to Clerk login
```

### 2. Configure Clerk Webhooks

**In Clerk Dashboard:**
```
Settings → Webhooks → Add Endpoint

URL: https://avichalmind.com/api/webhook/clerk
Events:
  ✓ user.created
  ✓ user.updated
  ✓ user.deleted

Webhook Secret: <copy-this>
```

Add webhook secret to Vercel environment:
```bash
vercel env add CLERK_WEBHOOK_SECRET
```

### 3. Configure Twilio Webhooks

**In Twilio Console:**
```
Phone Numbers → Your Number → Voice & Fax

When a call comes in:
  Webhook: https://avichalmind.com/api/voice-webhook
  HTTP Method: POST
```

### 4. Test Critical Flows

**Manual Testing:**
- [ ] User registration (Google OAuth)
- [ ] User registration (Email/Password)
- [ ] Create new session
- [ ] Send message in session
- [ ] AI response generation
- [ ] Crisis detection
- [ ] Session summary generation
- [ ] Voice call (if enabled)
- [ ] Gamification points
- [ ] Streak tracking

**Automated Testing:**
```bash
# Run E2E tests against production
NEXT_PUBLIC_APP_URL=https://avichalmind.com npm run test:e2e
```

### 5. Set Up Monitoring

**Vercel Analytics:**
```javascript
// pages/_app.tsx
import { Analytics } from '@vercel/analytics/react';

export default function App({ Component, pageProps }) {
  return (
    <>
      <Component {...pageProps} />
      <Analytics />
    </>
  );
}
```

**Sentry Integration:**
```javascript
// sentry.config.js
import * as Sentry from "@sentry/nextjs";

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 1.0,
});
```

---

## Monitoring & Maintenance

### 1. Uptime Monitoring

**Tools:**
- Vercel built-in monitoring
- UptimeRobot (https://uptimerobot.com)
- Pingdom
- StatusCake

**Setup:**
```
URL to monitor: https://avichalmind.com/api/health
Check interval: 5 minutes
Alert on: Down, Slow response (>2s)
Notification: Email, SMS
```

### 2. Error Tracking

**Sentry Dashboard:**
```
Monitor:
- Error rate
- Response time
- User impact
- Release health

Alerts:
- New error types
- Error spike (>10 in 5 min)
- Performance degradation
```

### 3. Database Monitoring

**MongoDB Atlas Alerts:**
```
Alerts → Add Alert

Metrics to monitor:
- Connections > 80% of max
- CPU > 75%
- Disk space < 10% free
- Slow queries > 100ms

Notification: Email, SMS, Slack
```

### 4. Performance Monitoring

**Lighthouse CI:**
```yaml
# .github/workflows/lighthouse.yml
name: Lighthouse CI
on: [push]
jobs:
  lighthouse:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: treosh/lighthouse-ci-action@v9
        with:
          urls: |
            https://avichalmind.com
            https://avichalmind.com/dashboard
          uploadArtifacts: true
```

### 5. Log Management

**Vercel Logs:**
```bash
# View real-time logs
vercel logs --follow

# Filter by function
vercel logs --filter=/api/chatbot

# Last 100 entries
vercel logs --limit=100
```

---

## Rollback Procedures

### Quick Rollback

**Method 1: Vercel Dashboard**
```
1. Go to Deployments
2. Find previous stable deployment
3. Click "Promote to Production"
```

**Method 2: Git Revert**
```bash
# Revert to previous commit
git revert HEAD
git push origin main

# Vercel auto-deploys reverted version
```

### Database Rollback

**MongoDB Atlas:**
```
1. Go to Clusters → Backup
2. Select restore point
3. Choose "Restore to new cluster"
4. Update MONGODB_URI to new cluster
5. Redeploy application
```

---

## Troubleshooting

### Common Issues

#### 1. "Internal Server Error" on API Routes

**Cause:** Missing environment variables

**Solution:**
```bash
# Check Vercel env vars
vercel env ls

# Ensure all required vars are set
vercel env add MONGODB_URI
vercel env add OPENAI_API_KEY
```

#### 2. Database Connection Timeout

**Cause:** MongoDB IP whitelist

**Solution:**
```
1. MongoDB Atlas → Network Access
2. Add Vercel IPs: 0.0.0.0/0
3. Or use Vercel's static IPs
```

#### 3. Clerk Webhook Failures

**Cause:** Wrong webhook secret

**Solution:**
```
1. Clerk Dashboard → Webhooks
2. Copy webhook secret
3. Update CLERK_WEBHOOK_SECRET in Vercel
4. Redeploy
```

#### 4. Slow API Responses

**Cause:** Cold starts, inefficient queries

**Solution:**
```javascript
// Optimize database queries
// Add indexes
db.sessions.createIndex({ userId: 1, createdAt: -1 });

// Use projection to limit fields
const sessions = await Session.find({ userId })
  .select('_id startTime mood')
  .limit(10);
```

#### 5. High Memory Usage

**Cause:** Memory leaks, large data processing

**Solution:**
```javascript
// Close database connections
mongoose.connection.close();

// Stream large responses
res.setHeader('Content-Type', 'application/json');
res.write('[');
for await (const item of largeDataset) {
  res.write(JSON.stringify(item));
}
res.write(']');
res.end();
```

---

## Performance Optimization

### 1. Caching Strategy

```javascript
// API route with caching
export async function GET(req: NextRequest) {
  const { searchParams } = new URL(req.url);
  const userId = searchParams.get('userId');

  // Check cache
  const cached = await redis.get(`sessions:${userId}`);
  if (cached) {
    return NextResponse.json(JSON.parse(cached), {
      headers: {
        'Cache-Control': 'public, s-maxage=60, stale-while-revalidate'
      }
    });
  }

  // Fetch from DB
  const sessions = await Session.find({ userId });

  // Cache result
  await redis.set(`sessions:${userId}`, JSON.stringify(sessions), 'EX', 60);

  return NextResponse.json(sessions);
}
```

### 2. Image Optimization

```jsx
import Image from 'next/image';

<Image
  src="/hero.jpg"
  alt="Mental wellness"
  width={1200}
  height={600}
  priority
  placeholder="blur"
/>
```

### 3. Code Splitting

```javascript
// Dynamic imports
const CrisisSupport = dynamic(() => import('@/components/CrisisSupport'), {
  loading: () => <Spinner />,
  ssr: false
});
```

---

## Security Checklist

- [ ] HTTPS enabled with valid SSL certificate
- [ ] Environment variables secured
- [ ] API rate limiting enabled
- [ ] CORS configured properly
- [ ] SQL injection prevention (using ORM)
- [ ] XSS protection enabled
- [ ] CSRF tokens implemented
- [ ] Security headers configured
- [ ] Dependencies updated regularly
- [ ] Secrets rotation scheduled
- [ ] Webhook signatures verified
- [ ] Input validation on all endpoints

---

## Support

**Deployment Issues:**
- Vercel Support: https://vercel.com/support
- GitHub Discussions: [Link to repo]
- Email: devops@avichalmind.com

**Emergency Contact:**
- On-call engineer: +91-XXXX-XXXXXX
- Email: emergency@avichalmind.com

---

**Last Updated**: January 2025
**Deployment Version**: 1.0.0
