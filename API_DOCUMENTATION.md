# Avichal Mind - API Documentation

## Table of Contents

1. [Overview](#overview)
2. [Authentication](#authentication)
3. [Session APIs](#session-apis)
4. [Message APIs](#message-apis)
5. [Chatbot API](#chatbot-api)
6. [Summary APIs](#summary-apis)
7. [Gamification APIs](#gamification-apis)
8. [Streak APIs](#streak-apis)
9. [Voice APIs](#voice-apis)
10. [User APIs](#user-apis)
11. [Error Handling](#error-handling)
12. [Rate Limiting](#rate-limiting)

---

## Overview

### Base URL

```
Development: http://localhost:3000/api
Production: https://avichalmind.com/api
```

### Response Format

All API responses follow this structure:

```typescript
// Success Response
{
  "success": true,
  "data": { ... },
  "message": "Optional success message"
}

// Error Response
{
  "success": false,
  "error": "Error message",
  "code": "ERROR_CODE"
}
```

### Common Headers

```http
Content-Type: application/json
Authorization: Bearer <clerk_token>
```

---

## Authentication

All API endpoints (except webhooks) require authentication via Clerk.

### Getting Auth Token

```typescript
// In React components
import { useAuth } from '@clerk/nextjs';

const { getToken } = useAuth();
const token = await getToken();

// Make authenticated request
const response = await fetch('/api/session', {
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  }
});
```

### Middleware Protection

Routes are protected via Next.js middleware (`src/middleware.ts`).

**Protected Routes:**
- `/api/session/*`
- `/api/chatbot`
- `/api/summaries/*`
- `/api/gamification`
- `/api/streak/*`

**Public Routes:**
- `/api/webhook/*`
- `/api/voice-webhook`
- `/api/test-*` (development only)

---

## Session APIs

### Create Session

Create a new therapy session for the authenticated user.

**Endpoint:** `POST /api/session`

**Request Body:**
```typescript
{
  "language"?: "en" | "hi",  // Optional, defaults to "en"
  "mood"?: string             // Optional initial mood
}
```

**Response:**
```typescript
{
  "success": true,
  "data": {
    "session": {
      "_id": "507f1f77bcf86cd799439011",
      "userId": "user_2a1b3c4d",
      "startTime": "2025-01-15T10:30:00Z",
      "endTime": null,
      "status": "active",
      "language": "en",
      "mood": "neutral",
      "crisis": false,
      "gamificationPoints": 0,
      "createdAt": "2025-01-15T10:30:00Z",
      "updatedAt": "2025-01-15T10:30:00Z"
    }
  }
}
```

**Example:**
```typescript
const response = await fetch('/api/session', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    language: 'hi',
    mood: 'anxious'
  })
});

const { data } = await response.json();
console.log('Session ID:', data.session._id);
```

---

### Get Session

Retrieve details of a specific session.

**Endpoint:** `GET /api/session/[id]`

**Parameters:**
- `id` (path): Session ID

**Response:**
```typescript
{
  "success": true,
  "data": {
    "session": {
      "_id": "507f1f77bcf86cd799439011",
      "userId": "user_2a1b3c4d",
      "startTime": "2025-01-15T10:30:00Z",
      "endTime": "2025-01-15T11:00:00Z",
      "status": "completed",
      "language": "en",
      "mood": "calm",
      "crisis": false,
      "gamificationPoints": 10,
      "messages": [
        {
          "_id": "msg_123",
          "role": "user",
          "content": "I'm feeling anxious",
          "timestamp": "2025-01-15T10:31:00Z"
        },
        {
          "_id": "msg_124",
          "role": "assistant",
          "content": "I understand...",
          "timestamp": "2025-01-15T10:31:05Z"
        }
      ]
    }
  }
}
```

**Example:**
```typescript
const response = await fetch(`/api/session/${sessionId}`, {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});

const { data } = await response.json();
console.log('Messages:', data.session.messages);
```

---

### Update Session

Update session status or properties.

**Endpoint:** `PATCH /api/session/[id]`

**Request Body:**
```typescript
{
  "status"?: "active" | "completed",
  "mood"?: string,
  "crisis"?: boolean,
  "endTime"?: string  // ISO 8601 format
}
```

**Response:**
```typescript
{
  "success": true,
  "data": {
    "session": { /* updated session */ }
  }
}
```

**Example:**
```typescript
// End a session
const response = await fetch(`/api/session/${sessionId}`, {
  method: 'PATCH',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    status: 'completed',
    endTime: new Date().toISOString(),
    mood: 'calm'
  })
});
```

---

### Get Session Count

Get total number of sessions for the user.

**Endpoint:** `GET /api/session/count`

**Response:**
```typescript
{
  "success": true,
  "data": {
    "count": 15,
    "thisWeek": 3,
    "thisMonth": 8
  }
}
```

**Example:**
```typescript
const response = await fetch('/api/session/count', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});

const { data } = await response.json();
console.log(`Total sessions: ${data.count}`);
```

---

## Message APIs

### Send Message

Send a message in a session.

**Endpoint:** `POST /api/session/[id]/message`

**Request Body:**
```typescript
{
  "content": string,           // Message content
  "role": "user" | "assistant",
  "language"?: "en" | "hi"     // Auto-detected if not provided
}
```

**Response:**
```typescript
{
  "success": true,
  "data": {
    "message": {
      "_id": "msg_123",
      "sessionId": "507f1f77bcf86cd799439011",
      "userId": "user_2a1b3c4d",
      "role": "user",
      "content": "I need help with anxiety",
      "timestamp": "2025-01-15T10:31:00Z",
      "language": "en",
      "sentiment": "negative"
    }
  }
}
```

**Example:**
```typescript
const response = await fetch(`/api/session/${sessionId}/message`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    content: 'I am feeling very stressed',
    role: 'user'
  })
});
```

---

## Chatbot API

### Process Chat Message

Send user message and get AI response.

**Endpoint:** `POST /api/chatbot`

**Request Body:**
```typescript
{
  "message": string,              // User's message
  "sessionId": string,            // Current session ID
  "language"?: "en" | "hi",       // Optional
  "context"?: {                   // Optional context
    "previousMessages": Message[],
    "mood": string,
    "crisis": boolean
  }
}
```

**Response:**
```typescript
{
  "success": true,
  "data": {
    "response": string,           // AI's response
    "language": "en" | "hi",      // Detected language
    "sentiment": string,          // Detected sentiment
    "crisis": boolean,            // Crisis detected
    "suggestions": string[]       // Follow-up suggestions
  }
}
```

**Example:**
```typescript
const response = await fetch('/api/chatbot', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    message: 'I am feeling overwhelmed by work',
    sessionId: sessionId,
    language: 'en'
  })
});

const { data } = await response.json();

if (data.crisis) {
  // Show crisis support
  showCrisisSupport();
} else {
  // Display AI response
  displayMessage(data.response);
}
```

**Crisis Detection:**

If crisis keywords are detected, the response includes:
```typescript
{
  "success": true,
  "data": {
    "response": "I'm very concerned about what you're sharing...",
    "crisis": true,
    "crisisResources": {
      "helplines": [
        {
          "name": "KIRAN Mental Health Helpline",
          "number": "1800-599-0019",
          "available": "24/7"
        }
      ],
      "videos": [
        {
          "title": "Immediate Crisis Support",
          "url": "https://youtube.com/..."
        }
      ]
    }
  }
}
```

---

## Summary APIs

### Generate Summary

Generate AI summary for a completed session.

**Endpoint:** `POST /api/session/[id]/summary`

**Request Body:**
```typescript
{
  "includePII"?: boolean  // Default: false (masks PII)
}
```

**Response:**
```typescript
{
  "success": true,
  "data": {
    "summary": {
      "_id": "summary_123",
      "userId": "user_2a1b3c4d",
      "sessionId": "507f1f77bcf86cd799439011",
      "summary": "User discussed work-related stress and family concerns...",
      "keyInsights": [
        "Increased work pressure affecting sleep",
        "Positive progress in boundary setting",
        "Considering therapy for deeper issues"
      ],
      "mood": "anxious → calm",
      "topics": ["work stress", "family", "sleep"],
      "generatedAt": "2025-01-15T11:05:00Z"
    }
  }
}
```

**Example:**
```typescript
const response = await fetch(`/api/session/${sessionId}/summary`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    includePII: false  // Mask personal information
  })
});
```

---

### Get All Summaries

Retrieve all summaries for the authenticated user.

**Endpoint:** `GET /api/summaries`

**Query Parameters:**
- `limit` (optional): Number of summaries (default: 10)
- `offset` (optional): Pagination offset (default: 0)
- `sort` (optional): Sort order - `newest` or `oldest` (default: newest)

**Response:**
```typescript
{
  "success": true,
  "data": {
    "summaries": [
      {
        "_id": "summary_123",
        "sessionId": "session_456",
        "summary": "...",
        "keyInsights": [...],
        "mood": "anxious → calm",
        "topics": [...],
        "generatedAt": "2025-01-15T11:05:00Z"
      }
    ],
    "total": 25,
    "hasMore": true
  }
}
```

**Example:**
```typescript
const response = await fetch('/api/summaries?limit=20&offset=0', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});

const { data } = await response.json();
data.summaries.forEach(summary => {
  console.log(`Session: ${summary.sessionId}`);
  console.log(`Mood: ${summary.mood}`);
  console.log(`Topics: ${summary.topics.join(', ')}`);
});
```

---

### Get Single Summary

Retrieve a specific summary.

**Endpoint:** `GET /api/summaries/[id]`

**Parameters:**
- `id` (path): Summary ID

**Response:**
```typescript
{
  "success": true,
  "data": {
    "summary": { /* summary object */ }
  }
}
```

---

## Gamification APIs

### Award Points

Award gamification points to user.

**Endpoint:** `POST /api/gamification`

**Request Body:**
```typescript
{
  "action": "session_complete" | "crisis_help" | "daily_streak" | "milestone",
  "sessionId"?: string,
  "metadata"?: any
}
```

**Response:**
```typescript
{
  "success": true,
  "data": {
    "pointsAwarded": 10,
    "totalPoints": 150,
    "newBadges": ["regular_user"],  // Newly unlocked badges
    "level": {
      "current": 3,
      "name": "Practitioner",
      "progress": 50  // Percentage to next level
    }
  }
}
```

**Point Values:**
- Session complete: 10 points
- Crisis help accessed: 50 points
- Daily streak: 20 points
- Weekly milestone: 100 points

**Example:**
```typescript
const response = await fetch('/api/gamification', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    action: 'session_complete',
    sessionId: sessionId
  })
});

const { data } = await response.json();

if (data.newBadges.length > 0) {
  showBadgeUnlocked(data.newBadges[0]);
}
```

---

## Streak APIs

### Get Daily Streak

Get user's current streak information.

**Endpoint:** `GET /api/streak/daily`

**Response:**
```typescript
{
  "success": true,
  "data": {
    "currentStreak": 7,
    "longestStreak": 15,
    "lastActivityDate": "2025-01-15",
    "freezesAvailable": 1,
    "streakStatus": "active" | "at_risk" | "broken",
    "calendar": {
      "2025-01-01": true,
      "2025-01-02": true,
      // ...
    }
  }
}
```

**Example:**
```typescript
const response = await fetch('/api/streak/daily', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});

const { data } = await response.json();
console.log(`Current streak: ${data.currentStreak} days`);
```

---

### Refresh Streak

Update streak based on new activity.

**Endpoint:** `POST /api/streak/refresh`

**Request Body:**
```typescript
{
  "useFreeze"?: boolean  // Use a streak freeze if at risk
}
```

**Response:**
```typescript
{
  "success": true,
  "data": {
    "currentStreak": 8,
    "streakIncreased": true,
    "freezeUsed": false,
    "pointsAwarded": 20
  }
}
```

---

## Voice APIs

### Voice Webhook

Twilio webhook for handling voice calls.

**Endpoint:** `POST /api/voice-webhook`

**Headers:**
```
Content-Type: application/x-www-form-urlencoded
X-Twilio-Signature: <signature>
```

**Request Body (URL-encoded):**
```
CallSid=CAxxxx
From=+919876543210
To=+911234567890
CallStatus=initiated
```

**Response (TwiML):**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Say voice="alice" language="en-IN">
    Welcome to Avichal Mind. How can I help you today?
  </Say>
  <Gather input="speech" action="/api/voice-webhook/process">
    <Say>Please tell me how you're feeling.</Say>
  </Gather>
</Response>
```

---

### Initiate Call

Start an outbound call (admin only).

**Endpoint:** `POST /api/call`

**Request Body:**
```typescript
{
  "phoneNumber": string,  // E.164 format: +919876543210
  "sessionId"?: string    // Optional existing session
}
```

**Response:**
```typescript
{
  "success": true,
  "data": {
    "callSid": "CAxxxx",
    "status": "queued",
    "sessionId": "session_123"
  }
}
```

---

## User APIs

### Sync User

Webhook endpoint to sync user from Clerk to MongoDB.

**Endpoint:** `POST /api/webhook/clerk`

**Headers:**
```
svix-id: msg_xxx
svix-timestamp: 1234567890
svix-signature: v1,xxx
```

**Request Body:**
```typescript
{
  "type": "user.created" | "user.updated",
  "data": {
    "id": "user_xxx",
    "email_addresses": [...],
    "first_name": "John",
    "last_name": "Doe",
    "phone_numbers": [...]
  }
}
```

**Response:**
```typescript
{
  "success": true,
  "message": "User synced successfully"
}
```

---

## Error Handling

### Error Response Format

```typescript
{
  "success": false,
  "error": "User-friendly error message",
  "code": "ERROR_CODE",
  "details"?: any  // Additional error details (dev mode only)
}
```

### Common Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `UNAUTHORIZED` | 401 | Missing or invalid authentication |
| `FORBIDDEN` | 403 | Insufficient permissions |
| `NOT_FOUND` | 404 | Resource not found |
| `VALIDATION_ERROR` | 400 | Invalid request data |
| `SESSION_NOT_FOUND` | 404 | Session ID doesn't exist |
| `SESSION_INACTIVE` | 400 | Session is not active |
| `AI_SERVICE_ERROR` | 500 | AI service unavailable |
| `DATABASE_ERROR` | 500 | Database operation failed |
| `RATE_LIMIT_EXCEEDED` | 429 | Too many requests |

### Error Examples

**Validation Error:**
```typescript
{
  "success": false,
  "error": "Invalid request data",
  "code": "VALIDATION_ERROR",
  "details": {
    "field": "sessionId",
    "message": "sessionId is required"
  }
}
```

**Unauthorized:**
```typescript
{
  "success": false,
  "error": "Authentication required",
  "code": "UNAUTHORIZED"
}
```

**Rate Limit:**
```typescript
{
  "success": false,
  "error": "Too many requests. Please try again later.",
  "code": "RATE_LIMIT_EXCEEDED",
  "retryAfter": 60  // seconds
}
```

---

## Rate Limiting

### Limits

| Endpoint | Limit | Window |
|----------|-------|--------|
| `/api/chatbot` | 30 requests | 1 minute |
| `/api/session` | 10 creates | 1 hour |
| `/api/session/[id]/message` | 100 requests | 1 minute |
| `/api/summaries` | 20 requests | 1 minute |
| All others | 100 requests | 1 minute |

### Rate Limit Headers

```http
X-RateLimit-Limit: 30
X-RateLimit-Remaining: 25
X-RateLimit-Reset: 1705320000
```

### Handling Rate Limits

```typescript
async function makeRequest(url: string, options: RequestInit) {
  const response = await fetch(url, options);

  if (response.status === 429) {
    const retryAfter = response.headers.get('Retry-After');
    console.warn(`Rate limited. Retry after ${retryAfter}s`);

    // Wait and retry
    await new Promise(resolve =>
      setTimeout(resolve, parseInt(retryAfter!) * 1000)
    );

    return makeRequest(url, options);
  }

  return response;
}
```

---

## Best Practices

### 1. Always Handle Errors

```typescript
try {
  const response = await fetch('/api/session', {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${token}` }
  });

  if (!response.ok) {
    const error = await response.json();
    console.error('API Error:', error.code, error.error);
    // Handle specific error codes
    if (error.code === 'UNAUTHORIZED') {
      // Redirect to login
    }
  }

  const data = await response.json();
  // Success handling
} catch (error) {
  console.error('Network error:', error);
  // Handle network failures
}
```

### 2. Use TypeScript Types

```typescript
import type { Session, Message, Summary } from '@/types';

async function createSession(): Promise<Session> {
  const response = await fetch('/api/session', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    }
  });

  const { data } = await response.json();
  return data.session as Session;
}
```

### 3. Implement Retry Logic

```typescript
async function fetchWithRetry(
  url: string,
  options: RequestInit,
  maxRetries = 3
) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      const response = await fetch(url, options);
      if (response.ok) return response;

      if (response.status >= 500) {
        // Server error, retry
        await new Promise(r => setTimeout(r, 1000 * Math.pow(2, i)));
        continue;
      }

      // Client error, don't retry
      return response;
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      await new Promise(r => setTimeout(r, 1000 * Math.pow(2, i)));
    }
  }

  throw new Error('Max retries exceeded');
}
```

### 4. Cache Responses When Appropriate

```typescript
const sessionCache = new Map<string, Session>();

async function getSession(id: string): Promise<Session> {
  // Check cache first
  if (sessionCache.has(id)) {
    return sessionCache.get(id)!;
  }

  // Fetch from API
  const response = await fetch(`/api/session/${id}`, {
    headers: { 'Authorization': `Bearer ${token}` }
  });

  const { data } = await response.json();

  // Cache result
  sessionCache.set(id, data.session);

  return data.session;
}
```

---

**Last Updated**: January 2025
**API Version**: v1.0.0

For implementation details, see [ARCHITECTURE.md](ARCHITECTURE.md)
