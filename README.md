# Changelog Master

**The ultimate changelog tracking and notification system powered by AI.**

Track changelogs from any source, get AI-powered summaries, listen to audio briefings, and receive email notifications when new versions drop.

---

## What It Does

Changelog Master monitors software changelogs (like Claude Code, VS Code, or any project with a markdown changelog) and transforms them into actionable insights:

1. **Fetches** raw changelog markdown from any URL
2. **Parses** version history into structured data
3. **Analyzes** changes using Gemini 3 Flash AI to extract what actually matters
4. **Generates** audio summaries using Gemini TTS so you can listen on the go
5. **Notifies** you via email with audio attachments when new versions are released
6. **Chats** with you about any changelog using an AI-powered assistant

---

## Features

### Multi-Source Monitoring
Track multiple changelog sources simultaneously. Switch between them instantly or monitor all at once.

```
Claude Code ─────┐
VS Code ─────────┼──► Changelog Master ──► Unified Dashboard
Antigravity IDE ─┘
```

### AI-Powered Analysis
Gemini 3 Flash analyzes each changelog and extracts:

- **TL;DR** - Markdown-formatted executive summary
- **Critical Breaking Changes** - What will break your code
- **Removals** - Features being deprecated with severity ratings
- **Major Features** - New capabilities worth knowing
- **Important Fixes** - Bugs that were squashed
- **Action Items** - What you need to do

### Analysis History
Browse previous analyses for any changelog version. Never lose context when new versions overwrite the current view - just select from the history dropdown to revisit past summaries.

### Text-to-Speech Audio
Listen to changelog summaries using Gemini 2.5 Flash TTS with 30+ voice options:

| Voice | Tone |
|-------|------|
| Charon | Informative |
| Puck | Upbeat |
| Kore | Firm |
| Zephyr | Bright |
| Aoede | Breezy |
| *...and 25 more* | |

**Audio Features:**
- **Seekable Progress Bar** - Click anywhere on the progress bar to jump to that position
- **Auto-Restore** - Last played audio is automatically restored on page reload (ready to play)
- **SQLite Caching** - Generated audio is cached in the database, no regeneration needed
- **Adjustable Playback Speed** - 0.5x to 2x speed options

### Email Notifications
Automatic emails when new versions are detected, including:
- HTML-formatted summary
- Categorized changes with severity indicators
- Audio file attachment (WAV format)
- **Scheduled Summaries** - Option to send emails on every check, not just new versions

### Changelog Chat
Ask questions about any changelog versions using the AI assistant:

> "What breaking changes were introduced between 2.0.70 and 2.0.74?"
> "Summarize all the new slash commands added this month"
> "Are there any security-related fixes I should know about?"

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         FRONTEND (React)                         │
├─────────────┬─────────────┬─────────────┬───────────────────────┤
│   Header    │   TabNav    │  AudioPlayer │    ChatPanel         │
│  (Source    │ (Changelog/ │  (Play/Pause │   (AI Assistant)     │
│  Selector)  │  Matters)   │   Download)  │                      │
├─────────────┴─────────────┴─────────────┴───────────────────────┤
│                        useChangelog Hook                         │
│              (Fetches, parses, caches, manages state)           │
└─────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                      BACKEND (Express.js)                        │
├─────────────────────────────────────────────────────────────────┤
│  /api/sources/*     - CRUD for changelog sources                │
│  /api/analysis/*    - Cached AI analysis                        │
│  /api/audio/*       - TTS audio cache                           │
│  /api/chat          - Gemini-powered Q&A                        │
│  /api/conversations - Chat history persistence                  │
│  /api/monitor/*     - Cron job status & manual triggers         │
│  /api/settings/*    - User preferences                          │
│  /api/send-*        - Email notifications                       │
└─────────────────────────────────────────────────────────────────┘
                                  │
              ┌───────────────────┼───────────────────┐
              ▼                   ▼                   ▼
      ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
      │   SQLite     │   │  Gemini API  │   │  Resend API  │
      │  (Caching)   │   │   (AI/TTS)   │   │   (Email)    │
      └──────────────┘   └──────────────┘   └──────────────┘
```

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| Frontend | React 19 + TypeScript + Vite |
| Styling | Tailwind CSS 4 (Anthropic-inspired design) |
| Backend | Express.js + TypeScript |
| Database | SQLite (better-sqlite3) |
| AI Analysis | Gemini 3 Flash (`gemini-3-flash-preview`) |
| Text-to-Speech | Gemini 2.5 Flash TTS (`gemini-2.5-flash-preview-tts`) |
| Email | Resend API |
| Scheduling | node-cron |

---

## Getting Started

### Prerequisites

- Node.js 18+
- npm or yarn
- Gemini API key ([Get one here](https://makersuite.google.com/app/apikey))
- Resend API key ([Get one here](https://resend.com)) - optional, for email notifications

### Installation

```bash
# Clone the repository
git clone https://github.com/earlyaidopters/changelog-master.git
cd changelog-master

# Install dependencies
npm install

# Copy environment variables
cp .env.example .env
```

### Configuration

Edit `.env` with your API keys:

```env
# Required - Gemini API for AI analysis and TTS
VITE_GEMINI_API_KEY=your-gemini-api-key

# Optional - Email notifications
RESEND_API_KEY=re_xxxxxxxxxxxx
NOTIFY_EMAIL=you@example.com

# Optional - Customize defaults
VITE_CHANGELOG_CACHE_DURATION=3600000
VITE_VOICE_PREFERENCE=Charon
```

### Running

```bash
# Start both frontend and backend (RECOMMENDED)
npm run dev:all

# Or run separately:
npm run dev        # Frontend on http://localhost:5173
npm run dev:server # Backend on http://localhost:3001
```

> **Important:** Always use `npm run dev:all` to start both servers. The backend is required for audio caching, analysis storage, and chat persistence to work properly.

---

## Usage Guide

### Managing Changelog Sources

1. Click the **link icon** (🔗) in the header
2. Click **"Add Changelog Source"**
3. Enter a name and the raw markdown URL
4. Click **Test** to validate the URL works
5. Click **Add Source** to save

**Example URLs:**
```
# Claude Code
https://raw.githubusercontent.com/anthropics/claude-code/main/CHANGELOG.md

# VS Code
https://raw.githubusercontent.com/microsoft/vscode/main/CHANGELOG.md

# Any GitHub project
https://raw.githubusercontent.com/{owner}/{repo}/main/CHANGELOG.md
```

### Switching Between Sources

When you have multiple sources:
- Hover over the **dropdown arrow** next to the title
- Click any source to switch
- The app dynamically updates with that source's changelog

### Listening to Summaries

1. Go to the **"What Matters"** tab
2. Click **"Read Latest"** to hear the full summary
3. Or click **"Read TL;DR"** for a quick briefing
4. Use the audio player at the bottom to control playback:
   - **Play/Pause** - Toggle audio playback
   - **Seek** - Click anywhere on the progress bar to jump to that position
   - **Speed** - Adjust playback speed (0.5x to 2x)
   - **Download** - Save audio as WAV file
5. Change voices using the dropdown selector
6. **Auto-Restore**: Reload the page and your last played audio is ready to go

### Setting Up Email Notifications

1. Click the **gear icon** (⚙️) for Settings
2. Toggle **"Notify me when a new version is released"**
3. Optionally enable **"Send email on every check"** for scheduled summaries (even without new versions)
4. Select check frequency (e.g., "Every hour", "Once a week", "Every two weeks")
5. Choose a voice for audio attachments
6. Click **"Send Demo Email"** to test

### Chatting About Changelogs

1. Click the **chat bubble** (💬) in the bottom-right
2. Select which versions to include in context
3. Ask any question about the changes
4. Previous conversations are saved and can be resumed

---

## API Reference

### Sources

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/sources` | List all changelog sources |
| GET | `/api/sources/:id` | Get single source details |
| POST | `/api/sources` | Add new source |
| PATCH | `/api/sources/:id` | Update source (name, URL, active) |
| DELETE | `/api/sources/:id` | Delete source and history |
| GET | `/api/sources/:id/changelog` | Fetch changelog markdown |
| POST | `/api/sources/test` | Test if URL is valid changelog |

### Analysis

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/analysis` | List all cached analyses |
| GET | `/api/analysis/:version` | Get cached analysis |
| POST | `/api/analysis/:version` | Save analysis to cache |

### Audio

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/audio/:hash/:voice` | Get cached audio |
| POST | `/api/audio` | Save audio to cache |

### Chat

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/chat` | Send message, get AI response |
| GET | `/api/conversations` | List all conversations |
| GET | `/api/conversations/:id` | Get conversation with messages |
| POST | `/api/conversations` | Create new conversation |
| DELETE | `/api/conversations/:id` | Delete conversation |

### Monitoring

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/monitor/status` | Get cron job status |
| POST | `/api/monitor/check` | Trigger manual check |
| GET | `/api/monitor/history` | Get version detection history |

---

## Database Schema

```sql
-- Changelog sources to monitor
CREATE TABLE changelog_sources (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  url TEXT NOT NULL UNIQUE,
  is_active BOOLEAN DEFAULT 1,
  last_version TEXT,
  last_checked_at DATETIME,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Version detection history
CREATE TABLE changelog_history (
  version TEXT PRIMARY KEY,
  source_id TEXT,
  detected_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  notified BOOLEAN DEFAULT 0
);

-- Cached AI analyses
CREATE TABLE analysis_cache (
  version TEXT PRIMARY KEY,
  analysis_json TEXT NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Cached TTS audio
CREATE TABLE audio_cache (
  id TEXT PRIMARY KEY,
  text_hash TEXT NOT NULL,
  voice TEXT NOT NULL,
  audio_data BLOB NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Chat conversations
CREATE TABLE chat_conversations (
  id TEXT PRIMARY KEY,
  title TEXT,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Chat messages
CREATE TABLE chat_messages (
  id TEXT PRIMARY KEY,
  conversation_id TEXT NOT NULL,
  role TEXT NOT NULL,
  content TEXT NOT NULL,
  selected_versions TEXT,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- User settings
CREATE TABLE settings (
  key TEXT PRIMARY KEY,
  value TEXT NOT NULL
);
```

---

## How the Cron Scheduler Works

The monitoring system uses `node-cron` to check for new versions:

| Setting | Cron Expression | Schedule |
|---------|----------------|----------|
| Every 5 minutes | `*/5 * * * *` | `:00, :05, :10...` |
| Every 15 minutes | `*/15 * * * *` | `:00, :15, :30, :45` |
| Every hour | `0 * * * *` | `:00` each hour |
| Every 6 hours | `0 */6 * * *` | `00:00, 06:00, 12:00, 18:00` |
| Once a day | `0 0 * * *` | Midnight |
| Once a week | `0 0 * * 0` | Sunday at midnight |
| Every two weeks | `0 0 1,15 * *` | 1st and 15th of month |

When a new version is detected:

1. **Save** version to history
2. **Analyze** changelog with Gemini 3 Flash
3. **Generate** audio summary with Gemini TTS
4. **Send** email with HTML summary + audio attachment
5. **Mark** version as notified

---

## Project Structure

```
changelog-master/
├── server/
│   └── index.ts          # Express backend with all API routes
├── src/
│   ├── components/
│   │   ├── Header.tsx        # Title, source selector, toolbar
│   │   ├── TabNav.tsx        # Changelog/Matters tabs
│   │   ├── ChangelogView.tsx # Raw markdown display
│   │   ├── MattersView.tsx   # AI analysis display
│   │   ├── AudioPlayer.tsx   # TTS playback controls
│   │   ├── ChatPanel.tsx     # AI chat interface
│   │   ├── SettingsPanel.tsx # Notification settings
│   │   ├── SourcesPanel.tsx  # Manage changelog URLs
│   │   └── Toast.tsx         # Notifications
│   ├── hooks/
│   │   ├── useChangelog.ts   # Changelog data + source selection
│   │   ├── useAudio.ts       # TTS generation + playback
│   │   └── useTheme.ts       # Dark/light mode
│   ├── services/
│   │   ├── changelogService.ts # Fetch & parse changelogs
│   │   ├── geminiService.ts    # AI analysis
│   │   ├── ttsService.ts       # Text-to-speech
│   │   ├── emailService.ts     # Send notifications
│   │   └── cacheService.ts     # SQLite caching
│   ├── types/
│   │   └── index.ts          # TypeScript interfaces
│   ├── App.tsx               # Main application
│   └── main.tsx              # Entry point
├── docs/
│   ├── claude-changelog-app.md  # Original spec
│   ├── gemini-3.md              # Gemini API reference
│   └── audio_understanding.md   # TTS documentation
├── data/
│   └── audio.db              # SQLite database (gitignored)
└── .env                      # API keys (gitignored)
```

---

## Customization

### Default Theme
Set dark mode as the default in Settings > Appearance. The app remembers your preference across sessions.

### Adding a Custom Voice

Voices are defined in `src/types/index.ts`:

```typescript
export const VOICE_OPTIONS: VoiceOption[] = [
  { name: 'Charon', tone: 'Informative' },
  { name: 'Puck', tone: 'Upbeat' },
  // Add more from Gemini's voice library
];
```

### Modifying the Analysis Prompt

Edit the prompt in `server/index.ts`:

```typescript
const prompt = `Analyze this changelog and return JSON:
{
  "tldr": "...",
  "categories": { ... },
  "action_items": [...],
  "sentiment": "positive|neutral|critical"
}`;
```

### Custom Email Template

The HTML email is generated in `generateEmailHtml()` in `server/index.ts`.

---

## Troubleshooting

### "Failed to fetch changelog"
- Check if the URL returns raw markdown (not HTML)
- Use the **Test** button in Sources panel to validate

### "Analysis failed"
- Verify your Gemini API key is correct
- Check the browser console for error details

### "Audio not playing"
- Audio is generated as PCM and converted to WAV
- Ensure browser supports WAV playback

### "Audio regenerates every time"
- Make sure the backend server is running (`npm run dev:all`)
- Audio caching requires the `/api/audio` endpoints on port 3001
- Check browser console for "Using cached audio from SQLite" message

### "Email not sending"
- Verify Resend API key and NOTIFY_EMAIL in `.env`
- Check server logs for error messages
- Use "Send Demo Email" to test

---

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing`)
5. Open a Pull Request

---

## License

MIT License - see [LICENSE](LICENSE) for details.

---

## Acknowledgments

- **Anthropic** - Claude Code changelog inspiration
- **Google** - Gemini 3 Flash & TTS APIs
- **Resend** - Email delivery
- **Vite** - Lightning-fast builds

---

<p align="center">
  <b>Built with AI, for developers who value their time.</b>
</p>
