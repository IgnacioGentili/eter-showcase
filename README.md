# ÉTER

**Multi-tenant AI platform for small businesses.** Automate customer conversations, generate websites, capture leads, and manage everything from a single dashboard.

🌐 **Live:** [eter.network](https://eter.network)

---

## Demo

<div align="center">

📹 **[Marketing Site & Product Overview](https://www.loom.com/share/54ef1ec5cf594726a8a6e38f9bee4009)**

📹 **[AI Widget Editor & Customization](https://www.loom.com/share/2a2b4bb1ac29437c8cb6376380398700)**

📹 **[AI Assistant Setup & Client Experience](https://www.loom.com/share/ada4874619634426b05bb1c2be595abf)**

</div>

---

## What is ÉTER

ÉTER is a SaaS platform that gives small businesses an AI-powered assistant connected to their messaging channels. Business owners sign up, configure their AI assistant with their own FAQs and brand voice, and deploy it across web, WhatsApp, Instagram, Messenger, and Telegram — all from one dashboard.

The platform handles the full lifecycle: onboarding, site generation, AI chat configuration, lead capture, analytics, and payments.

**Built solo from scratch** — no templates, no boilerplate, no team. Every line of code is mine.

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                     FRONTEND (Next.js 14)                │
│  Marketing · Auth · Dashboard · Widget · Site Generator  │
├─────────────────────────────────────────────────────────┤
│                     BACKEND (FastAPI)                     │
│  Multi-Agent AI · Smart Router · Webhooks · Payments     │
├──────────┬──────────┬──────────┬────────────────────────┤
│ Supabase │ Stripe & │ Meta API │  LLM Providers         │
│ Postgres │ MercPago │ WhatsApp │  OpenAI · Claude ·     │
│   Auth   │          │ IG · MSG │  Gemini · Grok · Llama │
└──────────┴──────────┴──────────┴────────────────────────┘
```

**Backend:** Python 3.12, FastAPI, async — 196 files, 30 directories  
**Frontend:** TypeScript, Next.js 14, Tailwind CSS, shadcn/ui — 192 files, 84 directories  
**Database:** Supabase (PostgreSQL) with Row Level Security  
**Deployment:** Vercel (frontend) + Railway (backend), 148 deployments to date

---

## Core Features

### Multi-Agent AI System

Seven specialized agents coordinated by a central router:

| Agent | Purpose |
|-------|---------|
| FAQ | Matches known questions using keyword patterns (zero tokens) |
| Sales | Handles pricing, purchases, upselling |
| Support | Technical issues, troubleshooting |
| General | Broad topics, general knowledge |
| Smalltalk | Casual conversation, greetings |
| Internal | Admin queries, system commands |
| Coordinator | Routes messages to the right agent using rules, not AI |

**Key design decision:** The coordinator uses deterministic keyword rules instead of AI for routing. This means zero tokens spent on classification and sub-millisecond routing decisions.

### Smart LLM Router

Routes queries to the optimal model based on complexity:

| Complexity | Routed To | Cost |
|------------|-----------|------|
| Low (greetings, simple FAQs) | GPT-4o-mini | $0.00015/1K tokens |
| Medium (product questions) | GPT-4o | $0.005/1K tokens |
| High (complex reasoning) | GPT-4o / Claude | Premium pricing |

Three routing strategies: `cost_optimized` · `balanced` · `quality_optimized`

**Result:** 50%+ cost reduction compared to routing everything through a single premium model.

See the routing implementation: [multi-llm-router](https://github.com/IgnacioGentili/multi-llm-router)

### Cost Tracking

Every API request is logged with provider, model, input/output tokens, and calculated cost. Per-tenant analytics show exactly where money is being spent.

### Messaging Channels

- **Web Widget** — Embeddable chat, fully brandable (colors, logo, position, welcome message)
- **WhatsApp** — Via Twilio/Meta Cloud API, with automatic human handoff detection
- **Instagram DM** — Meta Graph API integration
- **Facebook Messenger** — Meta Graph API integration
- **Telegram** — Bot API integration

Each channel has its own adapter that normalizes messages into a common format before hitting the AI pipeline.

### AI Website Generator

Step-by-step wizard that creates a full business website:

1. Choose a vertical (restaurant, services, retail, etc.)
2. Enter business info
3. AI generates content (headlines, descriptions, CTAs)
4. Customize colors, fonts, layout
5. Preview and publish to `yourbusiness.eter.network`

Built with a template engine that supports multiple verticals with preset palettes, typography, and layouts.

### Tenant Dashboard

Full-featured dashboard for business owners:

- **Home** — Overview with 4-step onboarding tour + Socio IA (AI assistant for the dashboard itself)
- **AI Widget Editor** — 6 tabs: button style, window design, messages, FAQs, contact options, embed code. Live preview and testing.
- **Site Generator** — 6-step wizard with AI content generation, style customization, and one-click publish
- **Analytics** — Message stats, response times, agent performance, missing Q&A detection
- **Contacts/CRM** — Lead management, conversation history, status tracking, CSV export
- **Integrations** — Connect WhatsApp, Instagram, Messenger, Telegram
- **Settings** — Branding, configuration, API keys
- **Plan Management** — Upgrade flow with Stripe/MercadoPago checkout

### Lead Capture & CRM

- Automatic lead extraction from conversations
- Contact management with status tracking
- Conversation history per contact
- Export to CSV (Starter+ plans)

### Payments

- **Stripe** — International payments
- **MercadoPago** — Latin American payments
- Webhook handling for both providers
- Three-tier pricing: Free · Starter · Growth

### Plan System

| Feature | Free | Starter | Growth |
|---------|------|---------|--------|
| AI Messages | 100/mo | 1,000/mo | 10,000/mo |
| FAQs | 10 | 50 | Unlimited |
| Site Generator | Basic | Full | Full + AI regen |
| Analytics | 7 days | 30 days | 90 days |
| Channels | Web only | Web + 1 | All channels |
| Support | Community | Email | Priority |

---

## Tech Stack

### Backend

| Component | Technology |
|-----------|------------|
| Framework | FastAPI (Python 3.12) |
| AI Providers | OpenAI, Anthropic, Google Gemini, xAI Grok, Meta Llama |
| Database | Supabase (PostgreSQL) |
| Auth | Supabase Auth + JWT |
| Messaging | Twilio, Meta Graph API, Telegram Bot API |
| Payments | Stripe, MercadoPago |
| Email | SMTP integration |
| File Storage | Supabase Storage |
| Task Queue | Background jobs with schedulers |
| Testing | pytest (17 test modules) |

### Frontend

| Component | Technology |
|-----------|------------|
| Framework | Next.js 14 (App Router) |
| Language | TypeScript |
| Styling | Tailwind CSS + shadcn/ui |
| Auth | Supabase Auth (SSR) |
| State | React hooks |
| i18n | Custom EN/ES system |
| Deployment | Vercel |

---

## Project Structure

```
backend/                          (196 files, 30 directories)
├── ai/
│   ├── adapter/                  # Channel adapters (WA, IG, Telegram, Web, Messenger)
│   ├── agents/                   # 7 specialized agents + per-tenant configs
│   ├── auto_optimizer/           # Self-improving response quality
│   └── orchestrator/             # Central AI pipeline
├── automation/                   # DSL-based automation engine
├── clients/                      # Tenant configurations + knowledge bases
├── core/                         # Auth, config, middleware, rate limiting
├── engine/                       # LLM factory, smart router, circuit breaker, cost tracking
├── integrations/                 # WhatsApp, Instagram, Messenger, Telegram, email, payments
├── models/                       # 18 data models
├── policy_engine/                # Compliance, permissions, audit trail, data retention
├── providers/                    # LLM provider implementations
├── routes/                       # API endpoints + webhook handlers
├── schemas/                      # Request/response validation
├── services/                     # 18 business logic modules
├── tasks/                        # Background jobs
└── tests/                        # 17 test modules

frontend/                         (192 files, 84 directories)
├── app/
│   ├── (auth)/                   # Login, signup, onboarding, password reset
│   ├── (marketing)/              # Landing, pricing, features, legal pages
│   ├── api/                      # Next.js API routes
│   ├── dashboard/[tenant]/       # Full tenant dashboard (10 sections)
│   ├── embed/                    # Embeddable chat widget
│   └── preview/                  # Site preview
├── components/
│   ├── dashboard/                # Dashboard shell, sidebar, onboarding tour
│   ├── generator/                # Site generator wizard (6 steps + editor)
│   ├── marketing/                # Landing page components
│   ├── site/                     # Generated site renderer + blocks
│   └── ui/                       # shadcn/ui components
├── lib/                          # Utilities, API clients, Supabase, i18n
└── verticals/                    # Industry-specific templates and presets
```

---

## Design Decisions

**Why multi-agent instead of one big prompt?**  
Each agent has a focused system prompt optimized for its task. The sales agent is persuasive, the support agent is patient. This produces better responses than a single generic prompt trying to do everything.

**Why rules-based routing instead of AI classification?**  
Speed and cost. An LLM call to classify intent takes ~500ms and costs tokens. Keyword rules take <1ms and cost nothing. At scale, this saves thousands of dollars monthly.

**Why multiple LLM providers?**  
No single provider is best at everything. GPT-4o-mini is great for simple queries at low cost. Claude handles nuanced conversations better. Having options lets us optimize per use case.

**Why Supabase instead of a custom backend DB?**  
Auth, storage, real-time subscriptions, and Row Level Security out of the box. For a solo developer, this saves months of work while providing enterprise-grade security.

---

## Related Repositories

- **[multi-llm-router](https://github.com/IgnacioGentili/multi-llm-router)** — The LLM orchestration and smart routing engine extracted from ÉTER

---

## Status

ÉTER is a live product with active deployments. The source code is in a private repository.

---

**Built by [Ignacio Gentili](https://github.com/IgnacioGentili)** — Full Stack & AI Engineer
