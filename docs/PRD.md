# Fear Forward — Product Requirements Document
**Version 5.0** · SheBuilds x Lovable · International Women's Day · March 8, 2025

> *我们的恐惧和梦想，其实是同一枚硬币的两面。*
> *Our fears and dreams are two sides of the same coin.*

**A safe space to face what scares you — and claim what you want.**

`Next.js 15` · `Clerk` · `Supabase` · `Claude API` · `Canvas API`

---

## 01 Vision & Core Insight

Fear Forward is a **ritual app with two doors**. One for your fears. One for your wishes. Both lead to the same place: clarity about what matters most, one doable action, and a wallpaper that carries the truth with you every day.

### The Two Doors

| 🌑 The Fear Door | 🌕 The Wish Door |
|---|---|
| Start from pain, resistance, what's stopping you | Start from hope, longing, what's pulling you |
| Drill: CBT → Stoic → Narrative Therapy | Drill: Positive Psychology → Neuroscience → Behavioral Science |
| Finds the dream hiding inside the fear | Anchors the wish in real intention |
| *"I am afraid of failing publicly."* | *"I want to build something that matters."* |
| → Ends at: what you're protecting | → Ends at: what you're moving toward |

### 殊途同归 — Both paths arrive at the same place

> 💬 A Voice · ✨ Your Truth · 🪜 One Step Forward · 🎨 Your Wallpaper

Fear reveals the dream. Wish reveals the intention. The output is always the same: **clarity + action + a beautiful reminder**.

### Design Principle

> Minimum input. Maximum depth. Two outputs, two frequencies:
> - 🪜 **One Step Forward** — practical, grounded, today's action
> - 🎨 **Wallpaper** — poetic, philosophical, built to last 100 views a day
>
> These two things must never be conflated. One is a to-do. The other is a truth.

---

## 02 The Dual Drill Framework

Same 3-round structure. Mirror-image questions.
**Fear asks what's blocking you. Wish asks what's driving you.**

| Round | FEAR PATH 🌑 | Framework | WISH PATH 🌕 | Framework |
|---|---|---|---|---|
| **1** | *"What's the worst specific thing you imagine happening?"* | CBT — Surface the distortion | *"If this wish came true, what would be concretely different about your life?"* | Positive Psychology — Concretise the vision |
| **2** | *"In this fear — what is within your power, and what is not?"* | Stoic — Restore agency | *"When have you already felt a glimpse of this — even briefly?"* | Neuroscience — Activate existing circuits |
| **3** | *"If this fear is protecting something you deeply want — what is that?"* | Narrative Therapy — Fear → Wish | *"If tomorrow you could only do one thing toward this wish — what would it be?"* | Behavioral Science — Wish → Action |

### AI Rules
- **Stop-early:** After each answer, Claude evaluates whether the core truth is already clear. If yes, skip remaining rounds. **Max 3 questions total.**
- **Multi-input handling:** If the user writes more than one fear/wish, show: *"今天，哪一个最沉？"* with tappable chips. User selects one. The selection itself is a moment of self-awareness.
- **Input guidance on the text field:** *"今天，有什么让你最放不下？写下一个就好。"*

---

## 03 The Reveal — Unified Output (Both Paths)

One screen. Three outputs. Identical structure whether the user came through Fear or Wish.

### 💬 The Voice
One sentence spoken by the Fear or the Wish — whichever door they entered.
- ≤ 15 words. Warm. First person.
- Fear: *"I am here because you care about being seen for who you really are."*
- Wish: *"I have always been in you — you are finally ready to listen."*

### ✨ Your Truth
One sentence. The core of what was revealed in Round 3. Poetic, specific.
- ≤ 15 words.
- Fear path: *"You want to build something that outlasts you."*
- Wish path: *"You want to be the kind of person your younger self needed."*
- This feeds the wallpaper quote generation.

### 🪜 One Step Forward
One concrete action. Achievable within 24 hours.
- **Lives on the Reveal screen only — never on the wallpaper.**
- e.g. *"Write down one sentence about what you would do if you knew you couldn't fail."*
- e.g. *"Send a voice note to one person who already believes in this version of you."*

### UI Principle
Three items. Large font. Generous white space. No borders between them — they arrive together, not as a checklist.

Single button at the bottom:

> **「 Generate My Wallpaper 」**

---

## 04 Wallpaper Builder

A separate page. A different emotional register. The user is now building something to carry.

### What it contains
- **Wallpaper Quote** — AI-generated from Your Truth. Poetic reframe. ≤ 12 words. *Not* the same sentence as Your Truth.
- **Hand-drawn SVG illustration** — selected by AI from 8 keys, matched to emotional tone
- **Background colour** — user picks from curated palette of 8–12 warm options
- **Fear Forward wordmark** — small, bottom corner, subtle

### What it does NOT contain
❌ One Step Forward. No checklists. No actions. **The wallpaper is for truth, not tasks.**

### Output sizes
- Mobile: 1170 × 2532 px (iPhone 14)
- Desktop: 2560 × 1440 px

### On save
PNG uploaded to Supabase Storage → URL saved to coin record → downloadable from Coin Collection forever.

### Your Truth vs Wallpaper Quote

| Your Truth (Reveal screen) | Wallpaper Quote (Wallpaper) |
|---|---|
| Plain, direct, personal | Poetic, philosophical, built to last |
| *"You want to build something that outlasts you."* | *"The things built with love outlive the hands that made them."* |
| Tells you what the drill revealed | Resonates every time, stands alone |

### 8 Illustration Keys
| Key | Meaning |
|---|---|
| `coin` | Uncertainty — both sides are real |
| `seedling` | Something small beginning |
| `door` | A choice, a threshold |
| `mountain` | The long path, one step |
| `candle` | Small light in the dark |
| `thread` | Things slowly connecting |
| `window` | Dreaming, looking out |
| `tide` | Coming and going — not the end |

---

## 05 AI Prompt Architecture (Claude API)

Five API calls. Each focused. Each with a strict JSON output schema.

| Call | Input | Output | Notes |
|---|---|---|---|
| Drill ×1–3 | mode (fear/wish) + text + prev answers | `{ question }` or `{ done: true }` | mode switches framework. Max 3 calls. Stop-early logic. |
| Reveal | mode + all drill answers | `{ voice, truth, action }` | voice ≤15w. truth ≤15w. action = 24h specific task. |
| Wallpaper Quote | truth + mode + tone | `{ quote, illustration_key }` | quote ≤12w, poetic. 8 illustration keys. |
| Coin Title | mode + original text + truth | `{ title }` (3–5 words) | e.g. *"The fear of disappearing"* |
| Multi-input detect | raw user input | `{ multiple: bool, items: [] }` | If multiple detected, return list for user to select one. |

### Tone Contract (all calls)
- Warm, direct, non-judgmental — always
- Never bullet-point responses — flowing human prose only
- Never gives advice unprompted — only reflects and reveals
- Never more than 3 drill questions — restraint is kindness
- The wallpaper quote must feel written for this person alone

---

## 06 Database Schema (Supabase / PostgreSQL)

### profiles
```sql
id           UUID  PRIMARY KEY  -- = Clerk user_id
email        TEXT
display_name TEXT
created_at   TIMESTAMPTZ  DEFAULT now()
```

### coins
```sql
id                UUID  PRIMARY KEY  DEFAULT gen_random_uuid()
user_id           UUID  REFERENCES profiles(id)  NOT NULL
mode              TEXT  NOT NULL  CHECK (mode IN ('fear', 'wish'))
title             TEXT
input_text        TEXT  NOT NULL        -- original fear or wish text
voice             TEXT  NOT NULL        -- ≤15 words, first person
truth             TEXT  NOT NULL        -- ≤15 words, the core reveal
action            TEXT  NOT NULL        -- One Step Forward
wallpaper_quote   TEXT  NOT NULL        -- poetic, ≤12 words
illustration_key  TEXT  NOT NULL
bg_color          TEXT  NOT NULL
wallpaper_url     TEXT                  -- Supabase Storage public URL
created_at        TIMESTAMPTZ  DEFAULT now()
```

### drill_rounds
```sql
id            UUID  PRIMARY KEY  DEFAULT gen_random_uuid()
coin_id       UUID  REFERENCES coins(id)  ON DELETE CASCADE
round_number  INT   CHECK (round_number BETWEEN 1 AND 3)
framework     TEXT  -- 'CBT' | 'Stoic' | 'Narrative' | 'PositivePsych' | 'Neuroscience' | 'BehavioralScience'
question      TEXT  NOT NULL
answer        TEXT  NOT NULL
created_at    TIMESTAMPTZ  DEFAULT now()
```

### RLS Policy (all tables)
```sql
-- Users can only access their own data
USING (user_id = auth.uid())
```

### Future: Coin Pairing (v2)
When a fear coin and wish coin share a common truth, they get linked:
```sql
paired_coin_id  UUID  REFERENCES coins(id)  -- add in v2
```

---

## 07 Technical Stack

| Layer | Tool | Why |
|---|---|---|
| Framework | Next.js 15 (App Router) | Full-stack in one repo; Server Actions keep API keys safe |
| Auth | Clerk | Native Lovable integration; Google/Apple/Email; zero JWT config |
| Database | Supabase (PostgreSQL) | RLS out of the box; real SQL; pairs well with Clerk |
| Storage | Supabase Storage | Wallpaper PNGs; public URLs; same dashboard |
| AI | Claude API (claude-sonnet-4-5) | 5-call architecture; structured JSON; tone contract |
| Wallpaper | HTML5 Canvas API | Client-side render; no cost; PNG export |
| Illustrations | 8 SVG presets | Hand-drawn style; AI matches by emotional tone; MVP-safe |
| AI Image (v2) | Gemini 2.0 Flash Imagen | 2–4s; hand-drawn style prompt; cheap — post-MVP |
| Hosting | Vercel | One-click deploy from GitHub; free tier |

---

## 08 User Journey (Full Flow)

```
Sign In (Clerk)
    ↓
Home Screen — Two doors
    ┌─────────────────────────────────┐
    │  🌑 Face a Fear                 │
    │  🌕 Claim a Wish                │
    └─────────────────────────────────┘
    ↓
Input Screen
    "今天，有什么让你最放不下？写下一个就好。"
    [open text field]
    ↓
    (if multiple detected)
    "今天，哪一个最沉？"  [chip selection]
    ↓
The Drill — 1 to 3 rounds
    (mode-aware framework, stop-early)
    ↓
The Reveal
    💬 The Voice
    ✨ Your Truth
    🪜 One Step Forward
    [Generate My Wallpaper →]
    ↓
Wallpaper Builder
    Pick background colour
    Preview: quote + illustration
    Download (mobile / desktop)
    ↓
Coin Minted 🪙
    Saved to Coin Collection
    Fear/Wish faced. Truth named.
```

---

## 09 MVP Scope (2-Hour Demo Build)

### ✅ In Scope
- Clerk auth (Google / Email)
- Dual entry: Fear door + Wish door
- Input guidance + multi-input detection
- 3-round drill (mode-aware)
- Reveal: Voice + Truth + One Step Forward
- Wallpaper builder: colour picker + SVG + Canvas
- Download (mobile + desktop)
- Supabase: coins (with mode) + drill_rounds + profiles
- Coin Collection page
- RLS on all tables

### ❌ Out of Scope (v1)
- AI image generation (Gemini — v2)
- Coin pairing (fear ↔ wish linking — v2)
- Fear/wish pattern analysis
- Letters to future self
- Community coins
- Social sharing
- Notifications / streaks
- Subscriptions

---

## 10 Build Order (2-Hour Session)

| Time | Task | Done when... |
|---|---|---|
| 0:00–0:15 | Supabase: create tables + RLS | Tables visible in dashboard |
| 0:15–0:25 | GitHub push + Lovable connect | Lovable shows repo synced |
| 0:25–0:45 | Dual entry home screen (Fear / Wish UI) | Two doors visible, input works |
| 0:45–1:15 | Claude API: Drill (mode-aware, 3 rounds) | 3 questions flow, stop-early works |
| 1:15–1:35 | Claude API: Reveal + Wallpaper Quote | Voice + Truth + Action + Quote appear |
| 1:35–1:50 | Canvas wallpaper: colour + SVG + quote | Download button works |
| 1:50–2:00 | Save coin to Supabase + Collection page | Coin shows in history |

---

## 11 Success Metrics (Demo Day)

| Metric | Target | Why it matters |
|---|---|---|
| Rituals completed | 20+ at event | The flow is frictionless |
| Wallpapers downloaded | >60% of completions | Product has real-world life |
| Coins saved | >50% of users | Emotional connection formed |
| Session completion rate | >70% | No drop-off in the drill |
| Organic sharing observed | Yes | Product has soul |

---

## 12 Future Vision (Post-MVP)

- **Coin Pairing** — AI detects when a fear coin and wish coin are two sides of the same coin, links them
- **Pattern Analysis** — "You've faced the fear of not being enough 7 times"
- **Gemini Image Generation** — unique hand-drawn style illustration per coin
- **Letters to Future Self** — schedule your truth to arrive in 3 months
- **Community Coins** — anonymised, opt-in: see what others face today
- **Seasonal Wallpaper Packs** — new illustration sets each season
- **Subscription tier** — unlimited coins, custom palettes

---

*Two doors. One truth. A wallpaper to carry it home.*
*Built with courage. Designed with love.*

**Fear Forward — StillKind AI Lab · fearforward.app**