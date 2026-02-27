# BizPulse — AI Business Diagnostic Tool

## Vision
A standalone web app that grades small businesses (A+ to F) using a combination of website scraping + short questionnaire. Fast, impressive, "click and boom you're graded." This is the top of a funnel: free diagnostic → upsell consulting/services → acquire + improve businesses.

## Stack
- **Backend:** Python (FastAPI) — serves API + static files
- **Frontend:** Single HTML file (vanilla JS, no framework) — clean, modern UI
- **AI:** Claude CLI subprocess (`claude -p`) for analysis
- **Storage:** SQLite — log every submission (business data goldmine)
- **Port:** 8090

## User Flow
1. User lands on page → enters business website URL
2. We scrape the website (company name, description, industry, social links, tech stack hints)
3. Short questionnaire appears (5-6 questions, pre-filled where we can from scrape):
   - How many employees? (1-5, 6-20, 21-100, 100+)
   - Annual revenue range? (<$250k, $250k-$1M, $1M-$5M, $5M+)
   - How do you currently get new customers? (referrals, ads, SEO, social, cold outreach)
   - Do you use any automation or AI tools? (yes/no + text)
   - Biggest challenge right now? (getting customers, keeping customers, operations, cash flow, hiring)
4. Hit "Grade My Business" → loading animation → report appears instantly

## Grading (5 Dimensions, 0-100 each → weighted average → letter grade)
1. **Digital Presence** (website quality, SEO signals, social media) — weight 20%
2. **Customer Acquisition** (how they get leads, diversity of channels) — weight 25%
3. **Operations & Efficiency** (automation, systems, tools) — weight 20%
4. **AI Readiness** (current AI use, opportunity for AI) — weight 20%
5. **Growth Trajectory** (revenue range vs company size, ambition signals) — weight 15%

Letter grades: A+ (95-100), A (90-94), A- (85-89), B+ (80-84), B (75-79), B- (70-74), C+ (65-69), C (60-64), C- (55-59), D (50-54), F (<50)

## Claude Prompt
Send Claude the scraped website data + questionnaire answers. Ask for JSON output:
```json
{
  "scores": {
    "digital_presence": 72,
    "customer_acquisition": 58,
    "operations": 45,
    "ai_readiness": 30,
    "growth_trajectory": 65
  },
  "findings": {
    "digital_presence": "Website loads slowly and lacks clear calls-to-action...",
    "customer_acquisition": "Heavily reliant on referrals with no systematic lead gen...",
    "operations": "No visible automation or CRM usage detected...",
    "ai_readiness": "No AI tools mentioned. Significant opportunity in customer service and marketing...",
    "growth_trajectory": "Revenue range suggests early-stage growth with room to scale..."
  },
  "top_fixes": [
    "Implement a CRM and basic email automation sequence",
    "Add Google Ads or Meta retargeting to diversify lead sources",
    "Use AI tools for content creation and customer follow-up"
  ],
  "verdict": "This business has solid fundamentals but is leaving significant revenue on the table due to manual processes and single-channel customer acquisition.",
  "ai_opportunity_score": 78
}
```

## Report UI
- Big letter grade center-top (bold, colored: green A, yellow B/C, red D/F)
- Overall score (e.g. "67/100")
- 5 category bars with scores and short finding text
- "Top 3 Things To Fix" section (numbered list, actionable)
- Verdict paragraph
- AI Opportunity callout (separate box: "AI could improve this business X%")
- CTA button: "Want help implementing these fixes? →" (links to #general for now, we'll update later)

## Data Storage (SQLite)
Table: `submissions`
- id, created_at, url, company_name, industry, employees, revenue, acquisition_channels, uses_ai, challenge, scores_json, overall_score, letter_grade, top_fixes_json, verdict

## File Structure
```
bizpulse/
├── main.py          # FastAPI app
├── scraper.py       # Website scraper (requests + BeautifulSoup)
├── analyzer.py      # Claude CLI analysis
├── database.py      # SQLite logging
├── pyproject.toml
└── static/
    └── index.html   # Full frontend (form + report in one page)
```

## When Done
Run: `openclaw system event --text "Done: BizPulse v1 built" --mode now`
