# Claude.md - CompeteIQ

## What This Is
AI-powered competitive intelligence web app that auto-discovers competitors, scrapes their websites, and generates strategic insights using real-time analysis. No manual data entry required.

**Key Differentiator:** Goes beyond alerts to answer "what does this competitive move mean for our strategy?"

## Tech Stack
- **Backend:** Python/FastAPI + PostgreSQL + Redis + Celery
- **Frontend:** React/Next.js
- **AI:** Google Gemini (primary), Claude/OpenAI (fallbacks)
- **Deployment:** Podman containers
- **Data:** Web scraping + RSS feeds + review platforms

## Core Architecture
```
Web Scraper → Content Analysis (AI) → Strategic Insights → User Dashboard
```

**AI Router Pattern:** All AI calls go through abstraction layer for easy model switching
**Background Processing:** Celery handles scraping, analysis, alerts
**Real Data:** Live competitor websites, news, reviews (no mocks)

## Critical Design Decisions

### AI Integration
- **Gemini as default** (generous free tier for POC)
- **Pluggable models** via standardized interface
- **Token optimization** to control costs
- **Fallback chains** when primary AI fails

### Data Collection
- **Intelligent scraping** with JavaScript rendering (Playwright)
- **RSS feed monitoring** for news (no API keys needed)
- **Respect robots.txt** and implement rate limiting
- **Multi-source validation** prevents single points of failure
- **Change significance scoring** (not all updates are strategic)

### User Experience
- **Zero manual setup** via automated competitor discovery
- **Action-oriented insights** with specific recommendations
- **Signal vs noise** filtering (target: 70% noise reduction)

## Implementation Notes

### Competitor Discovery Algorithm
1. Analyze user's website for product/market context
2. Search multiple sources: Google, RSS feeds, G2, tech stack data
3. Score relevance based on market overlap, features, audience
4. Present top 10 suggestions for user approval

### Content Processing Pipeline
```python
Raw HTML → Clean/Extract → AI Analysis → Structured Data → Insights
```

### Database Schema Essentials
- `users` (auth + company context)
- `competitors` (discovered companies)
- `features` (product capabilities tracked)
- `intelligence_updates` (AI-analyzed changes)
- `user_roadmap` (for competitive impact scoring)

## Development Setup
```bash
# Get API keys (Gemini free tier sufficient for POC)
export GOOGLE_GEMINI_API_KEY=your_key

# Start infrastructure
podman-compose up

# Run services
python -m uvicorn app.main:app --reload  # Backend
npm run dev                              # Frontend
```

## Common Pitfalls

### AI-Related
- **Over-prompting:** Keep prompts focused, avoid wall-of-text
- **No validation:** Always validate AI JSON responses
- **Cost spirals:** Monitor token usage, especially in loops

### Scraping-Related
- **JS-heavy sites:** Use Playwright for SPAs, not just requests
- **Getting blocked:** Implement delays, rotate user agents
- **Stale data:** Track last-update timestamps, alert on staleness

### Product-Related
- **Alert fatigue:** Not every competitor change is strategic
- **Generic insights:** Focus on impact to user's specific roadmap
- **No context:** Users need "why this matters" not just "what happened"

## Success Metrics for POC
- **Competitor discovery accuracy:** >80% relevant suggestions
- **Scraping coverage:** >90% of significant updates detected
- **Insight quality:** >4.0/5.0 user rating
- **Setup time:** <5 minutes to first monitoring

## Key Files Structure
```
backend/
├── core/ai_router.py          # Model abstraction
├── core/scraping_engine.py    # Web scraping
├── core/competitor_discovery.py
├── services/analysis_pipeline.py
└── tasks/background_jobs.py   # Celery tasks

frontend/
├── components/dashboard/
├── components/setup/
└── hooks/useCompetitorData.ts
```

## Environment Variables
```bash
GOOGLE_GEMINI_API_KEY=...     # Primary AI model
DATABASE_URL=postgresql://...
REDIS_URL=redis://...

# Optional for later scaling:
# GOOGLE_NEWS_API_KEY=...     # Can use RSS feeds instead
# CRUNCHBASE_API_KEY=...      # Can use web scraping instead
```

## What Makes This Hard
1. **Content quality varies** across competitor sites
2. **AI hallucination** on strategic analysis
3. **Rate limiting** from aggressive scraping
4. **Feature name normalization** ("SSO" = "Single Sign-On")
5. **Significance scoring** (what changes actually matter)

Remember: This is intelligence software where accuracy matters more than speed. Focus on insight quality over feature quantity.
