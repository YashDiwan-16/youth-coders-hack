# Contributing to Avichal Mind

First off, thank you for considering contributing to Avichal Mind! 🙏

It's people like you who make Avichal Mind a reality and help us provide mental wellness support to millions across India.

## Table of Contents

1. [Code of Conduct](#code-of-conduct)
2. [How Can I Contribute?](#how-can-i-contribute)
3. [Development Setup](#development-setup)
4. [Coding Standards](#coding-standards)
5. [Commit Guidelines](#commit-guidelines)
6. [Pull Request Process](#pull-request-process)
7. [Project Structure](#project-structure)
8. [Testing Guidelines](#testing-guidelines)
9. [Documentation](#documentation)
10. [Community](#community)

---

## Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inspiring community for all. This project deals with sensitive mental health topics, so we hold ourselves and contributors to high standards:

- **Be Respectful**: Treat everyone with respect and kindness
- **Be Empathetic**: Remember this project helps real people with real struggles
- **Be Professional**: Maintain professional conduct in all interactions
- **Be Inclusive**: Welcome people of all backgrounds and identities
- **Be Collaborative**: Help each other succeed

### Unacceptable Behavior

- Harassment, discrimination, or hate speech
- Dismissive or stigmatizing language about mental health
- Sharing others' private information without consent
- Trolling, insulting comments, or personal attacks
- Any behavior that could harm users' wellbeing

**Reporting**: If you experience or witness unacceptable behavior, please email conduct@avichalmind.com

---

## How Can I Contribute?

### 🐛 Reporting Bugs

**Before Submitting:**
1. Check existing [GitHub Issues](https://github.com/yourusername/avichal-mind/issues)
2. Test with the latest version
3. Collect relevant information

**Bug Report Template:**
```markdown
## Description
Clear description of the bug

## Steps to Reproduce
1. Go to '...'
2. Click on '....'
3. See error

## Expected Behavior
What should happen

## Actual Behavior
What actually happens

## Environment
- OS: [e.g., macOS 13.0]
- Browser: [e.g., Chrome 120]
- Node.js: [e.g., 18.17.0]
- Version: [e.g., 1.0.0]

## Screenshots
If applicable

## Additional Context
Any other relevant information
```

### 💡 Suggesting Enhancements

We love new ideas! Before suggesting:

1. Check if it's already been suggested
2. Consider if it aligns with project goals
3. Think about implementation complexity

**Feature Request Template:**
```markdown
## Feature Description
Clear description of the feature

## Problem It Solves
What user problem does this address?

## Proposed Solution
How would it work?

## Alternatives Considered
Other approaches you've thought about

## Additional Context
Mockups, examples, or references
```

### 🌐 Translation & Localization

We need help supporting more Indian languages!

**Current Languages**: Hindi, English

**Needed**: Tamil, Telugu, Bengali, Marathi, Kannada, Malayalam, Gujarati, Punjabi

**How to Help:**
1. Check `src/locales/` directory
2. Copy `en.json` to your language code (e.g., `ta.json`)
3. Translate all strings
4. Test with language switcher
5. Submit PR

**Translation Guidelines:**
- Keep cultural context
- Maintain empathetic tone
- Preserve formatting placeholders
- Test with native speakers

### 📝 Documentation

Documentation is crucial! Ways to help:

- Fix typos and grammar
- Improve clarity of explanations
- Add code examples
- Create tutorials or guides
- Translate documentation
- Add missing API documentation

### 🎨 Design Contributions

- UI/UX improvements
- Accessibility enhancements
- Mobile responsiveness fixes
- Dark mode refinements
- Icon and graphic design

### 🧪 Testing

- Write unit tests
- Add integration tests
- Perform manual testing
- Test accessibility
- Cross-browser testing
- Mobile device testing

---

## Development Setup

### Prerequisites

- Node.js 18+
- Git
- MongoDB (local or Atlas)
- Code editor (VS Code recommended)

### Fork & Clone

```bash
# Fork the repository on GitHub
# Then clone your fork
git clone https://github.com/YOUR_USERNAME/avichal-mind.git
cd avichal-mind

# Add upstream remote
git remote add upstream https://github.com/original/avichal-mind.git
```

### Install Dependencies

```bash
npm install
```

### Environment Setup

```bash
cp env.example .env.local
```

Fill in required environment variables. See [ENV_SECURITY_GUIDE.md](ENV_SECURITY_GUIDE.md) for details.

**Minimum Required for Development:**
```env
MONGODB_URI=mongodb://localhost:27017/avichal-mind
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=your_key
CLERK_SECRET_KEY=your_key
OPENAI_API_KEY=your_key
```

### Run Development Server

```bash
npm run dev
```

Visit [http://localhost:3000](http://localhost:3000)

### Database Setup

```bash
# Test database connection
npm run test:db

# (Optional) Seed sample data
npm run seed
```

---

## Coding Standards

### General Principles

- **Write Clear Code**: Code should be self-documenting
- **Keep It Simple**: Avoid over-engineering
- **Be Consistent**: Follow existing patterns
- **Think Privacy**: Always consider user data protection
- **Be Accessible**: Follow WCAG 2.1 AA standards

### TypeScript

```typescript
// ✅ Good
interface UserSession {
  id: string;
  userId: string;
  startTime: Date;
  language: 'en' | 'hi';
}

function createSession(userId: string): Promise<UserSession> {
  // Implementation
}

// ❌ Bad
function createSession(userId: any): any {
  // No type safety
}
```

### React Components

```tsx
// ✅ Good - Functional component with TypeScript
interface ChatMessageProps {
  content: string;
  role: 'user' | 'assistant';
  timestamp: Date;
}

export function ChatMessage({ content, role, timestamp }: ChatMessageProps) {
  return (
    <div className={`message ${role}`}>
      <p>{content}</p>
      <time>{timestamp.toLocaleTimeString()}</time>
    </div>
  );
}

// ❌ Bad - No types, inline styles
export function ChatMessage(props) {
  return <div style={{ color: 'blue' }}>{props.content}</div>;
}
```

### Styling with Tailwind

```tsx
// ✅ Good - Semantic class grouping
<button className="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 focus:ring-2 focus:ring-blue-500">
  Start Session
</button>

// ❌ Bad - Inline styles
<button style={{ padding: '8px 16px', backgroundColor: '#2563eb' }}>
  Start Session
</button>
```

### API Routes

```typescript
// ✅ Good - Proper error handling and types
import { NextRequest, NextResponse } from 'next/server';

export async function POST(req: NextRequest) {
  try {
    const body = await req.json();

    // Validation
    if (!body.userId) {
      return NextResponse.json(
        { error: 'userId is required' },
        { status: 400 }
      );
    }

    // Business logic
    const result = await createSession(body.userId);

    return NextResponse.json(result, { status: 201 });
  } catch (error) {
    console.error('Session creation error:', error);
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}

// ❌ Bad - No error handling, no validation
export async function POST(req) {
  const body = await req.json();
  const result = await createSession(body.userId);
  return NextResponse.json(result);
}
```

### Naming Conventions

```typescript
// Components - PascalCase
ChatInterface, SessionList, CrisisSupport

// Functions - camelCase
createSession, generateSummary, detectCrisis

// Constants - SCREAMING_SNAKE_CASE
API_BASE_URL, MAX_SESSION_DURATION, CRISIS_KEYWORDS

// Files - kebab-case
chat-interface.tsx, session-service.ts, crisis-detection.ts

// Types/Interfaces - PascalCase
UserSession, MessageContent, CrisisAlert
```

### Code Organization

```typescript
// ✅ Good - Organized imports
// 1. External libraries
import React from 'react';
import { useRouter } from 'next/navigation';

// 2. Internal components
import { ChatMessage } from '@/components/ChatMessage';
import { SessionHeader } from '@/components/SessionHeader';

// 3. Utilities and types
import { formatDate } from '@/lib/utils';
import type { Session } from '@/types';

// 4. Styles
import './styles.css';

// ❌ Bad - Random order
import './styles.css';
import type { Session } from '@/types';
import React from 'react';
```

---

## Commit Guidelines

We follow [Conventional Commits](https://www.conventionalcommits.org/) specification.

### Commit Message Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- **feat**: New feature
- **fix**: Bug fix
- **docs**: Documentation changes
- **style**: Code style changes (formatting, etc.)
- **refactor**: Code refactoring
- **perf**: Performance improvements
- **test**: Adding or updating tests
- **chore**: Maintenance tasks
- **ci**: CI/CD changes

### Examples

```bash
# Feature
git commit -m "feat(chat): add voice message support"

# Bug fix
git commit -m "fix(session): resolve session timeout issue"

# Documentation
git commit -m "docs(readme): update installation instructions"

# Refactoring
git commit -m "refactor(api): optimize database queries"

# Breaking change
git commit -m "feat(auth)!: migrate to new auth provider

BREAKING CHANGE: Users need to re-authenticate after update"
```

### Scope Examples

- `chat` - Chat interface
- `session` - Session management
- `auth` - Authentication
- `api` - API routes
- `db` - Database
- `ui` - UI components
- `crisis` - Crisis detection
- `voice` - Voice features
- `gamification` - Gamification system

---

## Pull Request Process

### Before Submitting

1. ✅ Sync with upstream main branch
2. ✅ Run linting: `npm run lint`
3. ✅ Run type check: `npm run type-check`
4. ✅ Test your changes thoroughly
5. ✅ Update documentation if needed
6. ✅ Add tests for new features
7. ✅ Ensure build passes: `npm run build`

### Creating a PR

```bash
# Create a new branch
git checkout -b feature/amazing-feature

# Make your changes
git add .
git commit -m "feat: add amazing feature"

# Push to your fork
git push origin feature/amazing-feature

# Create PR on GitHub
```

### PR Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix (non-breaking change fixing an issue)
- [ ] New feature (non-breaking change adding functionality)
- [ ] Breaking change (fix or feature causing existing functionality to change)
- [ ] Documentation update

## How Has This Been Tested?
Describe the tests you ran

## Checklist
- [ ] My code follows the project's style guidelines
- [ ] I have performed a self-review of my code
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix is effective or that my feature works
- [ ] New and existing unit tests pass locally with my changes
- [ ] Any dependent changes have been merged and published

## Screenshots (if applicable)
Add screenshots to help explain your changes

## Related Issues
Fixes #(issue number)
```

### Review Process

1. Maintainer reviews code
2. Automated tests run
3. Feedback provided (if needed)
4. Approve and merge

**Expected Timeline**: 1-3 days for initial review

---

## Project Structure

```
avichal-mind/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── api/               # API routes
│   │   ├── dashboard/         # Dashboard pages
│   │   ├── session/           # Session pages
│   │   └── page.tsx           # Landing page
│   ├── components/            # React components
│   │   ├── ui/               # Reusable UI components
│   │   └── providers/        # Context providers
│   ├── lib/                  # Utility libraries
│   │   ├── db.ts            # Database connection
│   │   ├── gemini.ts        # Gemini AI integration
│   │   └── youtube.ts       # YouTube API
│   ├── models/               # MongoDB models
│   ├── services/             # Business logic
│   ├── types/                # TypeScript definitions
│   └── middleware.ts         # Route middleware
├── public/                   # Static assets
└── docs/                     # Documentation
```

**Key Files to Know:**

- `src/app/api/chatbot/route.ts` - Main AI chat endpoint
- `src/services/ai.ts` - AI service logic
- `src/lib/db.ts` - Database connection
- `src/models/session.ts` - Session data model
- `src/components/chat-interface.tsx` - Chat UI

---

## Testing Guidelines

### Running Tests

```bash
# Unit tests
npm test

# Watch mode
npm test -- --watch

# Coverage
npm test -- --coverage

# Specific file
npm test ChatInterface.test.tsx
```

### Writing Tests

```typescript
// ChatMessage.test.tsx
import { render, screen } from '@testing-library/react';
import { ChatMessage } from './ChatMessage';

describe('ChatMessage', () => {
  it('renders user message correctly', () => {
    render(
      <ChatMessage
        content="Hello!"
        role="user"
        timestamp={new Date('2025-01-15')}
      />
    );

    expect(screen.getByText('Hello!')).toBeInTheDocument();
    expect(screen.getByRole('time')).toBeInTheDocument();
  });

  it('applies correct styling for assistant role', () => {
    const { container } = render(
      <ChatMessage
        content="Hi there!"
        role="assistant"
        timestamp={new Date()}
      />
    );

    expect(container.firstChild).toHaveClass('message assistant');
  });
});
```

### Test Coverage Requirements

- **Minimum**: 60% overall coverage
- **Target**: 80% for critical paths
- **Required**: 100% for crisis detection logic

---

## Documentation

### Code Comments

```typescript
// ✅ Good - Explains WHY, not WHAT
// Use Promise.all to parallelize API calls and reduce latency
const [sessions, summaries] = await Promise.all([
  fetchSessions(userId),
  fetchSummaries(userId)
]);

// ❌ Bad - Obvious from the code
// Loop through the array
for (let i = 0; i < arr.length; i++) {
  // ...
}
```

### JSDoc for Public APIs

```typescript
/**
 * Creates a new therapy session for the user
 *
 * @param userId - The unique identifier of the user
 * @param language - Preferred language ('en' or 'hi')
 * @returns Promise resolving to the created session
 * @throws {Error} If user is not found or database error occurs
 *
 * @example
 * ```typescript
 * const session = await createSession('user123', 'en');
 * console.log(session.id); // 'session456'
 * ```
 */
export async function createSession(
  userId: string,
  language: 'en' | 'hi'
): Promise<Session> {
  // Implementation
}
```

---

## Community

### Getting Help

- **Discord**: [Join our server](https://discord.gg/avichalmind)
- **GitHub Discussions**: [Ask questions](https://github.com/yourusername/avichal-mind/discussions)
- **Email**: contributors@avichalmind.com

### Recognition

We value all contributions! Contributors will be:

- Listed in [CONTRIBUTORS.md](CONTRIBUTORS.md)
- Mentioned in release notes
- Featured on our website (with permission)
- Invited to contributor-only channels

### Maintainers

- **Project Lead**: [Your Name] - @yourusername
- **Core Team**: See [MAINTAINERS.md](MAINTAINERS.md)

---

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

## Questions?

Don't hesitate to reach out! We're here to help:

- Open an issue with the `question` label
- Join our [Discord server](https://discord.gg/avichalmind)
- Email: contributors@avichalmind.com

---

**Thank you for making Avichal Mind better! 🙏**

**Together, we're making mental wellness accessible to millions.**
