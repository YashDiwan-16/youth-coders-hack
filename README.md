# Avichal Mind - AI Mental Wellness for India

<div align="center">

**Making mental wellness accessible, private, and culturally-aware for every Indian**

[![Next.js](https://img.shields.io/badge/Next.js-14-black)](https://nextjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue)](https://www.typescriptlang.org/)
[![MongoDB](https://img.shields.io/badge/MongoDB-Atlas-green)](https://www.mongodb.com/atlas)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

</div>

## 📖 About

Avichal Mind is an AI-assisted mental wellness platform designed specifically for the Indian audience. With support for Hindi and English, culturally-aware AI guidance, and complete privacy protection, we're breaking down barriers to mental health support.

**The Problem**: In India, 1 in 7 people face mental health challenges, but only 1 in 10 seek help due to stigma, cost, and limited accessibility.

**Our Solution**: A free, private, AI-powered platform that provides compassionate mental wellness support in your language, available 24/7.

## ✨ Key Features

### 🗣️ Bilingual Voice & Text Support
- Speak naturally or type your thoughts in **Hindi or English**
- Advanced language detection automatically switches context
- Voice calls powered by Twilio for hands-free therapy sessions
- Real-time transcription and response generation

### 🤖 AI-Powered Compassionate Guidance
- Powered by **OpenAI GPT-4** and **Google Gemini**
- Trained on psychiatry and therapy best practices
- Culturally-aware responses respecting Indian values and traditions
- Context-aware conversations that remember your journey
- Sentiment analysis to understand your emotional state

### 🔒 Complete Privacy & Security
- **DPDP Act compliant** with Indian data protection standards
- End-to-end encryption for all communications
- No third-party analytics or tracking
- PII masking in session summaries
- Right to data deletion anytime

### 🆘 Crisis Detection & Support
- Real-time keyword and sentiment-based crisis detection
- Instant access to **verified crisis helplines** across India
- Curated YouTube crisis support videos
- Emergency resources categorized by situation

### 📊 Session History & Insights
- Track your wellness journey over time
- AI-generated session summaries with key insights
- Mood tracking and progress visualization
- Private journal with full transcripts

### 🎮 Gamification & Engagement
- **Streaks System**: Build daily wellness habits
- **Points & Rewards**: 10 points per session, bonus for engagement
- **Achievement Badges**: Starter, Regular, Dedicated, Champion
- **Progress Levels**: Beginner → Intermediate → Advanced
- Visual dashboard with stats and milestones

### 🌐 24/7 Availability
- No appointments needed - start a session anytime
- Global accessibility via web platform
- Mobile-responsive design
- Works on any device with internet connection

## 🛠 Tech Stack

### Frontend
- **Framework**: Next.js 14 (App Router, Server Components)
- **UI Library**: React 18 with TypeScript
- **Styling**: Tailwind CSS + Custom UI Components
- **State Management**: React Context + TanStack Query

### Backend
- **API**: Next.js API Routes (Serverless)
- **Authentication**: Clerk (Google OAuth, Email/Password, Phone)
- **Database**: MongoDB Atlas with Mongoose ODM
- **Voice**: Twilio Programmable Voice

### AI & ML
- **LLM**: OpenAI GPT-4 / Google Gemini
- **Speech-to-Text**: Deepgram / Google Cloud Speech
- **Text-to-Speech**: ElevenLabs / Azure Cognitive Services
- **Training Data**: Custom psychiatry and therapy datasets

### Infrastructure
- **Hosting**: Vercel (Edge Network)
- **Database**: MongoDB Atlas (Cloud)
- **File Storage**: Cloudflare R2 / AWS S3
- **Monitoring**: Built-in health checks

## 📋 Prerequisites

Before you begin, ensure you have the following:

### Required
- **Node.js**: Version 18.0 or higher ([Download](https://nodejs.org/))
- **npm/yarn**: Package manager (comes with Node.js)
- **Git**: Version control ([Download](https://git-scm.com/))

### API Keys & Accounts
- **MongoDB Atlas**: Free tier account ([Sign up](https://www.mongodb.com/cloud/atlas/register))
- **Clerk**: Authentication service ([Sign up](https://clerk.com/))
- **OpenAI**: API access ([Get API key](https://platform.openai.com/api-keys))
- **Google Cloud**: For Gemini API ([Console](https://console.cloud.google.com/))
- **Twilio**: For voice features (optional) ([Sign up](https://www.twilio.com/))
- **YouTube Data API**: For crisis videos ([Setup](https://developers.google.com/youtube/v3))

### Knowledge Requirements
- Basic understanding of React and Next.js
- Familiarity with TypeScript
- Understanding of RESTful APIs
- Basic MongoDB/NoSQL knowledge

## 🚀 Quick Start

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/avichal-mind.git
cd avichal-mind
```

### 2. Install Dependencies
```bash
npm install
# or
yarn install
# or
pnpm install
```

### 3. Environment Setup

Create a `.env.local` file in the root directory:

```bash
cp env.example .env.local
```

Fill in your environment variables (see [ENV_SECURITY_GUIDE.md](ENV_SECURITY_GUIDE.md) for detailed setup):

```env
# Database (Required)
MONGODB_URI=mongodb+srv://<username>:<password>@cluster.mongodb.net/avichal-mind

# Clerk Authentication (Required)
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_xxxxx
CLERK_SECRET_KEY=sk_test_xxxxx
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up

# AI Services (Choose one or both)
OPENAI_API_KEY=sk-xxxxx                    # For GPT-4
GEMINI_API_KEY=xxxxx                        # For Gemini

# Voice Services (Optional)
TWILIO_ACCOUNT_SID=ACxxxxx
TWILIO_AUTH_TOKEN=xxxxx
TWILIO_PHONE_NUMBER=+1234567890

# YouTube API (For crisis videos)
YOUTUBE_API_KEY=xxxxx

# Audio Services (Optional)
ELEVENLABS_API_KEY=xxxxx                   # Text-to-Speech
DEEPGRAM_API_KEY=xxxxx                     # Speech-to-Text

# App Configuration
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

**Important**: See setup guides for detailed configuration:
- [GEMINI_SETUP.md](GEMINI_SETUP.md) - Google Gemini API setup
- [TWILIO_SETUP.md](TWILIO_SETUP.md) - Voice call configuration
- [YOUTUBE_SETUP.md](YOUTUBE_SETUP.md) - Crisis video integration

### 4. Database Setup

The application will automatically create collections on first run, but you can optionally seed data:

```bash
# Test database connection
npm run test:db

# (Optional) Seed sample data
npm run seed
```

### 5. Run Development Server

```bash
npm run dev
```

The application will be available at [http://localhost:3000](http://localhost:3000)

### 6. Create Your First Account

1. Navigate to http://localhost:3000
2. Click "Get Started"
3. Sign up using Google OAuth or email
4. Complete your profile
5. Start your first wellness session!

## 🔧 Available Scripts

```bash
npm run dev          # Start development server
npm run build        # Build for production
npm start            # Start production server
npm run lint         # Run ESLint
npm run type-check   # Run TypeScript type checking
npm run test:db      # Test database connection
npm run test:ai      # Test AI integration
```

## 📁 Project Structure

```
avichal-mind/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── api/               # API routes
│   │   │   ├── session/       # Session management
│   │   │   └── webhook/       # Clerk webhooks
│   │   ├── dashboard/         # User dashboard
│   │   ├── session/           # Session pages
│   │   ├── globals.css        # Global styles
│   │   ├── layout.tsx         # Root layout
│   │   └── page.tsx           # Landing page
│   ├── components/            # React components
│   │   ├── ui/               # Reusable UI components
│   │   ├── providers/        # Context providers
│   │   └── session/          # Session-specific components
│   ├── lib/                  # Utility functions
│   ├── models/               # MongoDB models
│   ├── services/             # External service integrations
│   ├── types/                # TypeScript type definitions
│   └── utils/                # Helper functions
├── public/                   # Static assets
├── scripts/                  # Database scripts
└── docs/                     # Documentation
```

## 🔧 Configuration

### Database Setup

1. Create a MongoDB Atlas cluster
2. Set up database indexes for optimal performance
3. Configure connection string in environment variables

### Authentication Setup

1. Create a Clerk application
2. Configure Google OAuth and email/password authentication
3. Set up webhook endpoints for user provisioning

### AI Services Setup

1. **OpenAI**: Set up API key for LLM responses
2. **ElevenLabs**: Configure for text-to-speech
3. **Deepgram**: Set up for speech-to-text

## 🧪 Testing

```bash
# Run unit tests
npm test

# Run integration tests
npm run test:integration

# Run E2E tests
npm run test:e2e
```

## 📦 Deployment

### Frontend (Vercel)

1. Connect your GitHub repository to Vercel
2. Configure environment variables
3. Deploy automatically on push to main branch

### Backend (Railway/Render)

1. Connect your repository
2. Set environment variables
3. Deploy the application

## 🔒 Security & Compliance

- **DPDP Act Compliance**: Indian data protection standards
- **HTTPS Everywhere**: Secure communication
- **Rate Limiting**: API protection
- **Crisis Detection**: Automatic safety measures
- **PII Masking**: Privacy protection in summaries

## 📊 Monitoring & Analytics

- **Health Checks**: Application monitoring
- **Error Tracking**: Sentry integration
- **Performance**: Lighthouse optimization
- **Analytics**: Privacy-focused user insights

## 📚 Documentation

- **[ARCHITECTURE.md](ARCHITECTURE.md)** - Complete system architecture and design
- **[FEATURES.md](FEATURES.md)** - Detailed feature documentation
- **[API_DOCUMENTATION.md](API_DOCUMENTATION.md)** - API endpoints and usage
- **[CONTRIBUTING.md](CONTRIBUTING.md)** - Contribution guidelines
- **[GEMINI_SETUP.md](GEMINI_SETUP.md)** - Google Gemini API configuration
- **[TWILIO_SETUP.md](TWILIO_SETUP.md)** - Voice integration setup
- **[YOUTUBE_SETUP.md](YOUTUBE_SETUP.md)** - Crisis video system setup
- **[ENV_SECURITY_GUIDE.md](ENV_SECURITY_GUIDE.md)** - Environment security best practices
- **[TROUBLESHOOTING.md](TROUBLESHOOTING.md)** - Common issues and solutions

## 🤝 Contributing

We welcome contributions from the community! Here's how you can help:

1. **Fork the repository**
2. **Create a feature branch** (`git checkout -b feature/AmazingFeature`)
3. **Make your changes** with clear commit messages
4. **Add tests** for new functionality
5. **Run linting and tests** (`npm run lint && npm test`)
6. **Submit a pull request**

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

### Areas We Need Help With
- 🌐 Regional Indian language support (Tamil, Telugu, Bengali, etc.)
- 🎨 UI/UX improvements and accessibility
- 📱 Mobile app development (React Native)
- 🧪 Testing and quality assurance
- 📖 Documentation and tutorials
- 🌍 Internationalization (i18n)

## 🔒 Security

Security is our top priority. We follow industry best practices:

- **HTTPS Only**: All communications encrypted
- **DPDP Act Compliant**: Indian data protection standards
- **Regular Audits**: Code and dependency security checks
- **No Data Selling**: Your data is never shared or sold
- **Transparent Privacy**: Clear privacy policy and data usage

**Report Security Issues**: Please email security@avichalmind.com (do not use public issues)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🆘 Support & Contact

- **GitHub Issues**: [Report bugs or request features](https://github.com/yourusername/avichal-mind/issues)
- **Discussions**: [Join community discussions](https://github.com/yourusername/avichal-mind/discussions)
- **Email**: support@avichalmind.com
- **Twitter**: [@AvichalMind](https://twitter.com/avichalmind)

## 🌟 Show Your Support

If this project helps you or someone you know, please consider:
- ⭐ Starring the repository
- 🐦 Sharing on social media
- 💬 Spreading awareness about mental health
- 🤝 Contributing to the project

## 🙏 Acknowledgments

- Built with ❤️ for the Indian community
- Inspired by Talkspace, BetterHelp, and Wysa
- Special thanks to mental health professionals who provided guidance
- Thanks to all our contributors and supporters

## 📊 Project Stats

- **Sessions Created**: Helping users daily
- **Languages Supported**: Hindi, English (more coming soon)
- **Privacy First**: Zero data breaches since inception
- **Open Source**: Free forever

---

<div align="center">

**Made with ❤️ for India**

**"Your mental wellness matters. We're here to help."**

[Get Started](http://localhost:3000) • [Documentation](ARCHITECTURE.md) • [Report Issue](https://github.com/yourusername/avichal-mind/issues)

</div>
