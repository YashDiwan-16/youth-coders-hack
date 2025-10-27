# Avichal Mind - Features Documentation

## Table of Contents

1. [Core Features](#core-features)
2. [AI & Intelligence](#ai--intelligence)
3. [Communication Features](#communication-features)
4. [Session Management](#session-management)
5. [Privacy & Security](#privacy--security)
6. [Gamification & Engagement](#gamification--engagement)
7. [Crisis Support System](#crisis-support-system)
8. [Analytics & Insights](#analytics--insights)
9. [Upcoming Features](#upcoming-features)

---

## Core Features

### 1. Bilingual Support

**Languages Supported**: Hindi, English

#### Auto Language Detection
- Automatic detection of user's language preference
- Seamless switching between Hindi and English mid-conversation
- Context preservation across language switches
- Romanized Hindi (Hinglish) support

**Example:**
```
User: "Mujhe bahut stress ho raha hai"
AI: Detected language: Hindi
Response: "मैं समझता हूं। आप अपनी भावनाओं को और विस्तार से बता सकते हैं?"

User: "I'm feeling anxious about work"
AI: Detected language: English
Response: "I understand work anxiety can be challenging. Tell me more about what's happening at work."
```

#### Cultural Context Awareness
- Understands Indian family dynamics
- Respects religious and cultural values
- Aware of Indian workplace culture
- Considers social stigma around mental health

### 2. Multi-Modal Communication

#### Text Chat Interface
- Real-time messaging with AI therapist
- Typing indicators and read receipts
- Rich text formatting support
- Emoji support for expression
- Message history and scrollback

#### Voice Conversations
**Powered by Twilio Programmable Voice**

Features:
- **Phone Call Integration**: Call a dedicated number to start therapy
- **Speech-to-Text**: Converts your voice to text in real-time
- **Text-to-Speech**: AI responses spoken back naturally
- **Voice Activity Detection**: Knows when you're done speaking
- **Background Noise Cancellation**: Clear audio even in noisy environments

**How It Works:**
1. User calls Twilio number
2. System creates new session or continues existing one
3. User speaks their thoughts
4. AI processes and responds via voice
5. Full transcript saved for later review

### 3. AI-Powered Therapy Assistant

**Models Used:**
- **OpenAI GPT-4**: Primary LLM for conversation
- **Google Gemini**: Alternative/fallback model

#### Capabilities

**1. Active Listening**
- Empathetic response generation
- Validation of feelings
- Non-judgmental conversation
- Reflective listening techniques

**2. Therapeutic Techniques**
- Cognitive Behavioral Therapy (CBT) principles
- Mindfulness guidance
- Stress management techniques
- Breathing exercises and grounding

**3. Personalization**
- Remembers previous conversations (with consent)
- Adapts tone based on user preference
- Context-aware responses
- Progress tracking across sessions

**4. Sentiment Analysis**
- Detects emotional state from text
- Tracks mood changes during session
- Identifies crisis indicators
- Measures sentiment trends over time

---

## Communication Features

### Real-Time Chat

**Features:**
- Instant message delivery
- Typing indicators
- Message timestamps
- Read/unread status
- Conversation threading

**Technical:**
- Built with React hooks for real-time updates
- Optimistic UI updates
- Error handling and retry logic
- Auto-scroll to latest message

### Voice Integration

**Twilio Voice Features:**
- **Inbound Calls**: Users can call in for therapy
- **Call Recording**: Optional session recording (with consent)
- **Call Transfer**: Transfer to crisis hotline if needed
- **DTMF Input**: Menu navigation via phone keypad
- **Call Quality Monitoring**: Automatic quality checks

**Voice Processing:**
- **STT (Speech-to-Text)**: Deepgram or Google Cloud Speech
- **TTS (Text-to-Speech)**: ElevenLabs or Azure Cognitive Services
- **Language Detection**: Auto-detect Hindi/English from speech
- **Accent Handling**: Trained on Indian accents

---

## Session Management

### Creating Sessions

**Session Types:**
1. **Quick Chat**: Immediate text-based session
2. **Voice Call**: Phone-based therapy
3. **Scheduled Session**: Plan ahead (coming soon)

**Process:**
```
User clicks "Start Session"
→ System creates new session in database
→ Assigns unique session ID
→ Loads user context and history
→ Initializes AI with system prompt
→ Ready to chat!
```

### Session States

1. **Active**: Currently in progress
2. **Completed**: User ended session
3. **Abandoned**: Inactive for 30+ minutes
4. **Crisis**: Flagged for urgent attention

### Session History

**Features:**
- View all past sessions
- Filter by date, mood, or topic
- Search within conversations
- Export session transcripts
- Delete sessions (data privacy)

**Storage:**
- MongoDB for structured data
- Encrypted message storage
- Indexed for fast retrieval
- Automatic backup

### Continue Previous Sessions

**Smart Resume:**
- "Continue where you left off" option
- Context preservation from last session
- Seamless conversation flow
- Progress reminder

---

## Privacy & Security

### DPDP Act Compliance

**Indian Data Protection Standards:**

1. **Data Minimization**: Only collect necessary data
2. **Purpose Limitation**: Use data only for therapy
3. **Storage Limitation**: Delete after retention period
4. **Consent Management**: Explicit user consent required
5. **Right to Access**: Users can download their data
6. **Right to Deletion**: Users can delete all data
7. **Data Portability**: Export in standard formats
8. **Breach Notification**: Immediate notification if breach occurs

### Encryption

**Data at Rest:**
- MongoDB encrypted storage
- AES-256 encryption for sensitive fields
- Encrypted backups

**Data in Transit:**
- HTTPS/TLS 1.3 for all connections
- Secure WebSocket connections
- End-to-end encryption for voice calls

### Privacy Features

**PII Masking in Summaries:**
```
Original: "My name is Rajesh and I live in Mumbai at 123 Main St"
Masked: "My name is [NAME] and I live in [CITY] at [ADDRESS]"
```

**No Third-Party Tracking:**
- No Google Analytics
- No Facebook Pixel
- No advertising cookies
- Privacy-first analytics only

**Anonymous Mode (Coming Soon):**
- Use without creating account
- Temporary sessions
- Auto-delete after 24 hours

---

## Gamification & Engagement

### Points System

**Earning Points:**
- 10 points per completed session
- 50 bonus points for crisis help usage
- 20 points for daily streak maintenance
- 100 points for weekly check-ins

**Point Tiers:**
- 0-100: Beginner
- 101-500: Regular
- 501-1000: Dedicated
- 1000+: Champion

### Badges & Achievements

**Badge Categories:**

1. **Engagement Badges**
   - 🌟 First Session: Complete your first therapy session
   - 🔥 On Fire: 7-day streak
   - 💪 Dedicated: 30-day streak
   - 👑 Champion: 100 total sessions

2. **Progress Badges**
   - 📈 Growth Minded: Track mood for 7 days
   - 🎯 Goal Setter: Set and achieve wellness goal
   - 🧘 Mindful: Complete 10 mindfulness exercises

3. **Community Badges**
   - 🤝 Helper: Share resource with friend
   - 💬 Engaged: Leave feedback
   - 🌟 Advocate: Share mental health awareness

### Streak System

**Daily Streaks:**
- Track consecutive days of engagement
- Visual streak calendar
- Streak freeze feature (1 per month)
- Milestone celebrations

**Benefits:**
- Motivation to maintain consistency
- Visual progress tracking
- Habit formation
- Community leaderboard (anonymous)

### Levels & Progression

**User Levels:**
```
Level 1: Beginner (0-100 points)
Level 2: Explorer (101-250 points)
Level 3: Practitioner (251-500 points)
Level 4: Dedicated (501-1000 points)
Level 5: Expert (1001-2500 points)
Level 6: Master (2501-5000 points)
Level 7: Champion (5000+ points)
```

**Level Benefits:**
- Unlock new features at higher levels
- Priority support access
- Custom avatars and themes
- Early access to new features

---

## Crisis Support System

### Crisis Detection

**Detection Methods:**

1. **Keyword Matching**
   - Suicide-related terms
   - Self-harm indicators
   - Severe distress language
   - Emergency keywords

2. **Sentiment Analysis**
   - Extreme negative sentiment
   - Hopelessness indicators
   - Sudden sentiment drops
   - Desperation patterns

3. **Contextual Analysis**
   - Conversation flow analysis
   - Historical pattern matching
   - Escalation detection

**Example:**
```
User: "I don't want to live anymore"
System: 🚨 CRISIS DETECTED
Action:
1. Show crisis resources immediately
2. Offer crisis helpline numbers
3. Display emergency contacts
4. Show calming resources
5. Suggest video support
```

### Crisis Resources

**Integrated Resources:**

1. **National Crisis Helplines**
   - KIRAN Mental Health Helpline: 1800-599-0019
   - Vandrevala Foundation: 9999 666 555
   - iCall: 9152987821
   - AASRA: 91-22-27546669

2. **State-Specific Helplines**
   - Organized by state/region
   - Language-specific support
   - 24/7 availability info

3. **Emergency Services**
   - Police: 100
   - Ambulance: 102
   - Women Helpline: 181

### Crisis Video Support

**YouTube Integration:**
- Curated crisis intervention videos
- Categorized by crisis type
- Professional guidance videos
- Calming meditation content
- Grounding technique videos

**Categories:**
- Suicide prevention
- Self-harm alternatives
- Anxiety attacks
- Panic disorder
- Severe depression

**Features:**
- Embedded player
- No YouTube tracking
- Offline download option (coming soon)
- Verified mental health professionals only

### Safety Features

**Automatic Actions on Crisis:**
1. Display crisis resources overlay
2. Disable regular chat temporarily
3. Show calming breathing exercise
4. Offer to call helpline
5. Log for safety monitoring (encrypted)

---

## Analytics & Insights

### Personal Dashboard

**Widgets:**

1. **Session Overview**
   - Total sessions
   - This week's sessions
   - Average session duration
   - Completion rate

2. **Mood Tracker**
   - Mood graph over time
   - Most common moods
   - Mood patterns and trends
   - Correlation insights

3. **Progress Indicators**
   - Current streak
   - Total points
   - Level and rank
   - Badges earned

4. **Topic Analysis**
   - Most discussed topics
   - Topic frequency
   - Topic sentiment
   - Growth areas

### Session Summaries

**AI-Generated Summaries:**

Each session automatically generates:
- **Key Discussion Points**: Main topics covered
- **Insights**: Important takeaways
- **Mood Assessment**: Overall emotional state
- **Progress Notes**: Growth indicators
- **Next Steps**: Suggested actions

**Example Summary:**
```
Session Summary - Jan 15, 2025

🎯 Key Topics:
- Work-related stress
- Family relationships
- Sleep issues

💡 Insights:
- Improved boundary setting at work
- Better communication with family
- Identified sleep routine needs

😊 Mood: Slightly Anxious → Calm
Progress: Positive shift

📝 Next Steps:
- Practice boundary-setting exercise
- Try suggested sleep routine
- Continue mindfulness practice
```

### Export & Download

**Available Formats:**
- PDF (formatted, printable)
- JSON (machine-readable)
- TXT (plain text)
- CSV (for spreadsheets)

**Export Options:**
- Single session
- Date range
- All sessions
- Summaries only
- Full transcripts

---

## Upcoming Features

### Phase 2 (Q2 2025)

🌐 **More Indian Languages**
- Tamil, Telugu, Bengali, Marathi
- Kannada, Malayalam, Gujarati, Punjabi
- Regional dialect support

📱 **Mobile Apps**
- Native iOS app (Swift/SwiftUI)
- Native Android app (Kotlin)
- React Native unified app
- Offline mode support

🎥 **Video Therapy**
- Live video sessions with AI avatar
- Screen sharing for exercises
- Interactive mood boards
- Group therapy sessions (anonymous)

### Phase 3 (Q3 2025)

🤝 **Community Features**
- Anonymous support groups
- Peer-to-peer sharing (moderated)
- Mental health forums
- Expert Q&A sessions

📊 **Advanced Analytics**
- ML-based mood prediction
- Personalized intervention timing
- Progress forecasting
- Wellness score calculation

🧘 **Wellness Programs**
- Guided meditation library
- CBT exercise programs
- Journaling prompts
- Habit tracking integration

### Long-Term Vision

🌍 **Global Expansion**
- Support for other countries
- Cultural adaptation toolkit
- Multi-timezone support
- Local crisis resources

🏥 **Professional Integration**
- Connect with real therapists
- Insurance integration (India)
- Prescription reminders
- Treatment plan tracking

🔬 **Research & Development**
- Contribute to mental health research (anonymized)
- Clinical trial participation (opt-in)
- Open dataset for researchers (de-identified)

---

## Feature Comparison

| Feature | Free Version | Premium (Coming Soon) |
|---------|-------------|---------------------|
| Text Chat | ✅ Unlimited | ✅ Unlimited |
| Voice Calls | ✅ 30 min/month | ✅ Unlimited |
| AI Models | ✅ GPT-4 | ✅ GPT-4 + Claude |
| Session History | ✅ Last 30 days | ✅ Unlimited |
| Summaries | ✅ Basic | ✅ Detailed |
| Export Data | ✅ Limited | ✅ All formats |
| Crisis Support | ✅ Full Access | ✅ Full Access |
| Priority Support | ❌ | ✅ 24/7 Support |
| Video Therapy | ❌ | ✅ Coming Soon |
| Family Accounts | ❌ | ✅ Up to 5 |

---

## Accessibility Features

### Visual
- High contrast mode
- Screen reader support
- Keyboard navigation
- Adjustable font sizes
- Color blind friendly palette

### Audio
- Closed captions for voice
- Visual indicators for audio cues
- Volume control
- Audio descriptions

### Cognitive
- Simple, clear language
- Consistent navigation
- Progress indicators
- Session save/resume
- Distraction-free mode

---

**Last Updated**: January 2025
**Version**: 1.0.0

For technical implementation details, see [ARCHITECTURE.md](ARCHITECTURE.md)
