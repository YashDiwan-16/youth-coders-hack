# Avichal Mind - System Architecture

## Overview

Avichal Mind is a full-stack mental wellness application built with modern web technologies, designed for scalability, security, and user privacy. This document outlines the system architecture, data flow, and key design decisions.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                          │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │  Next.js 14 │  │   React 18   │  │  TypeScript  │       │
│  │  App Router │  │  Components  │  │   Tailwind   │       │
│  └─────────────┘  └──────────────┘  └──────────────┘       │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                   Authentication Layer                       │
│  ┌──────────────────────────────────────────────────┐       │
│  │  Clerk Auth (OAuth, Email/Password, Phone)       │       │
│  └──────────────────────────────────────────────────┘       │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                     API Layer (Next.js)                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │ Session  │  │ Messages │  │ Chatbot  │  │ Summaries│   │
│  │   APIs   │  │   APIs   │  │   API    │  │   APIs   │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │  Voice   │  │ Gamifi-  │  │  Crisis  │  │  Streak  │   │
│  │ Webhook  │  │  cation  │  │  System  │  │   APIs   │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                     Service Layer                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ AI Service   │  │   Summary    │  │ Crisis Video │      │
│  │ (OpenAI/     │  │   Service    │  │   Service    │      │
│  │  Gemini)     │  │              │  │  (YouTube)   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Gamification │  │   Twilio     │  │   Audio      │      │
│  │   Service    │  │   (Voice)    │  │  Services    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      Data Layer                              │
│  ┌──────────────────────────────────────────────────┐       │
│  │         MongoDB Atlas (Primary Database)         │       │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐       │       │
│  │  │  Users   │  │ Sessions │  │ Messages │       │       │
│  │  └──────────┘  └──────────┘  └──────────┘       │       │
│  │  ┌──────────┐  ┌──────────┐                     │       │
│  │  │Summaries │  │  Streaks │                     │       │
│  │  └──────────┘  └──────────┘                     │       │
│  └──────────────────────────────────────────────────┘       │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    External Services                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │ OpenAI   │  │  Gemini  │  │ YouTube  │  │  Twilio  │   │
│  │   API    │  │   API    │  │   API    │  │   API    │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## Core Components

### 1. Frontend Layer

#### Technology Stack
- **Next.js 14**: App Router for server-side rendering and optimal performance
- **React 18**: Component-based UI with hooks and context
- **TypeScript**: Type safety across the application
- **Tailwind CSS**: Utility-first styling

#### Key Components
- **Chat Interface** (`src/components/chat-interface.tsx`): Real-time messaging UI
- **Session Management**: Create, view, and continue therapy sessions
- **Dashboard**: User analytics, streaks, and session history
- **Gamification**: Points, badges, and progress tracking
- **Crisis Support**: Emergency resources and video support

### 2. Authentication Layer

#### Clerk Integration
- **Multi-provider Auth**: Google OAuth, Email/Password, Phone
- **Webhook System**: Automatic user provisioning in MongoDB
- **Session Management**: Secure JWT tokens
- **Middleware Protection**: Route-level authentication

**Flow:**
```
User Sign-up → Clerk → Webhook → MongoDB User Creation → Dashboard
```

### 3. API Layer

#### Session Management
- `POST /api/session`: Create new therapy session
- `GET /api/session/[id]`: Retrieve session details
- `GET /api/session/count`: Get user's total sessions

#### Messaging System
- `POST /api/session/[id]/message`: Send message in session
- Real-time language detection (Hindi/English)
- Crisis keyword detection

#### AI Integration
- `POST /api/chatbot`: Process user messages with AI
- Sentiment analysis and context awareness
- Culturally-aware responses

#### Summary Generation
- `POST /api/session/[id]/summary`: Generate session summary
- `GET /api/summaries`: Retrieve all summaries
- PII masking for privacy

#### Gamification
- `POST /api/gamification`: Award points and badges
- `GET /api/streak/daily`: Daily streak tracking
- `POST /api/streak/refresh`: Reset streaks

#### Voice Integration
- `POST /api/voice-webhook`: Twilio voice call handling
- `POST /api/call`: Initiate voice session

### 4. Service Layer

#### AI Service (`src/services/ai.ts`)
**Capabilities:**
- Multi-model support (OpenAI GPT-4, Gemini)
- Psychiatry training data integration
- Crisis detection algorithms
- Sentiment analysis
- Language detection (Hindi/English)

**AI Prompt Engineering:**
```typescript
System Prompt:
- Act as compassionate mental health companion
- Understand Indian cultural context
- Respect religious and family values
- Detect crisis situations
- Provide evidence-based guidance
```

#### Summary Service (`src/services/summary.ts`)
**Features:**
- Session transcript generation
- Key insights extraction
- Mood tracking
- PII masking
- Progress indicators

#### Crisis Video Service (`src/lib/crisis-video-service.ts`)
**Integration:**
- YouTube API for crisis support videos
- Categorized by mental health topics
- Fallback mechanisms
- Privacy-focused (no tracking)

#### Gamification Service (`src/lib/gamification.ts`)
**System:**
- Points: 10 per session, 50 per crisis help
- Badges: Starter, Regular, Dedicated, Champion
- Streaks: Daily engagement tracking
- Level system: Beginner → Advanced

### 5. Data Layer

#### MongoDB Collections

**Users Collection** (`src/models/user.ts`)
```typescript
{
  clerkId: String (unique),
  email: String,
  firstName: String,
  lastName: String,
  phoneNumber: String,
  createdAt: Date,
  updatedAt: Date
}
```

**Sessions Collection** (`src/models/session.ts`)
```typescript
{
  userId: ObjectId,
  startTime: Date,
  endTime: Date,
  status: 'active' | 'completed',
  language: 'en' | 'hi',
  mood: String,
  crisis: Boolean,
  gamificationPoints: Number,
  createdAt: Date
}
```

**Messages Collection** (`src/models/message.ts`)
```typescript
{
  sessionId: ObjectId,
  userId: ObjectId,
  role: 'user' | 'assistant',
  content: String,
  timestamp: Date,
  language: 'en' | 'hi',
  sentiment: String
}
```

**Summaries Collection** (`src/models/summary.ts`)
```typescript
{
  userId: ObjectId,
  sessionId: ObjectId,
  summary: String,
  keyInsights: [String],
  mood: String,
  topics: [String],
  generatedAt: Date
}
```

## Data Flow

### 1. Chat Session Flow

```
User Input → API → Language Detection → Crisis Check → AI Processing
                                                              │
                                                              ▼
User Display ← Format ← Store in DB ← Generate Response ← Context Loading
```

### 2. Voice Session Flow

```
Phone Call → Twilio → Webhook → Session Creation → Voice Response
                                                          │
                                                          ▼
Call End ← Twilio ← Voice TTS ← AI Response ← STT ← Audio Input
```

### 3. Summary Generation Flow

```
Session End → Retrieve Messages → AI Summarization → PII Masking → Store Summary
```

## Security Architecture

### 1. Authentication
- JWT tokens via Clerk
- HTTP-only cookies
- Session expiration
- Refresh token rotation

### 2. Data Protection
- HTTPS everywhere
- MongoDB encryption at rest
- PII masking in summaries
- No third-party analytics

### 3. API Security
- Rate limiting
- CORS configuration
- Request validation
- Error sanitization

### 4. DPDP Act Compliance
- User consent tracking
- Data minimization
- Right to deletion
- Breach notification procedures

## Scalability Considerations

### 1. Database Optimization
- Indexed queries on userId, sessionId
- Connection pooling
- Query optimization
- Aggregation pipelines

### 2. Caching Strategy
- Session data caching
- AI response caching (similar queries)
- Static asset CDN (Vercel)

### 3. Load Handling
- Serverless functions (auto-scaling)
- Database replica sets
- API rate limiting
- Async processing for summaries

## Monitoring & Observability

### 1. Health Checks
- Database connectivity
- API endpoint health
- External service status

### 2. Error Tracking
- Centralized error logging
- User error reporting
- AI failure monitoring

### 3. Performance Metrics
- API response times
- Database query performance
- AI generation latency
- User session duration

## Deployment Architecture

### Production Setup
```
GitHub → Vercel (Frontend/API) → MongoDB Atlas
                │
                └─→ Edge Functions → Global CDN
```

### Environment Variables
- Development: `.env.local`
- Production: Vercel environment config
- Secrets management: Encrypted at rest

## Future Enhancements

1. **Microservices Migration**: Separate AI, voice, and summary services
2. **Real-time WebSockets**: Live chat updates
3. **Mobile Apps**: React Native implementation
4. **Advanced Analytics**: ML-based mood prediction
5. **Multi-language Support**: Regional Indian languages
6. **Offline Mode**: PWA with service workers

---

**Last Updated**: January 2025
**Version**: 1.0.0
