# 🤖 AI Agency — Complete Outreach Pipeline
 
> Built in 48 hours as a technical assignment for **Califnco** (Prompt Engineering & n8n Internship).  
> What started as an assignment became a fully working outreach system that runs itself.
 
---
 
## 🎯 What This Does
 
Every morning at 9AM, this pipeline wakes up and:
 
1. **Scrapes** Instagram profiles using targeted hashtags (`#AIagency`, `#businessautomation`, etc.)
2. **Scores** every lead by follower count, engagement rate & bio keywords — filters out the noise
3. **Writes** a fully personalized DM for each lead using Groq's LLaMA 3.1 — not a template, an actual message
4. **Waits** randomly between sends (anti-spam) so the account stays safe
5. **Logs** everything to Google Sheets in real-time
6. **Follows up** automatically after 3 days if there's no reply
7. **Emails** a full daily report every night at 8PM
 
**Zero manual work. Zero human intervention. Just results.**
 
---
 
## 📊 Test Results
 
| Metric | Result |
|---|---|
| Posts Scraped | 165 |
| Qualified Leads | 134 |
| DMs Generated | 134 (100% personalized) |
| Follow-up Sequence | ✅ Working |
| Daily Report | ✅ Delivered to Gmail |
| Manual Work Required | 0 hours |
 
---
 
## 🏗️ Pipeline Architecture
 
```
┌─────────────────────────────────────────────────────────────┐
│                    DAILY 9AM TRIGGER                        │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│  PHASE 1 — LEAD DISCOVERY                                   │
│  Apify → Trigger Scraper → Wait 60s → Fetch Results         │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│  PHASE 2 — LEAD SCORING & FILTERING                         │
│  Score Leads → Filter Quality → Save to Google Sheets       │
│  Filter Max 100 New Leads (daily cap)                       │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│  PHASE 3 — AI DM GENERATION                                 │
│  Groq (LLaMA 3.1) → Merge DM + Lead Data                   │
│  Random Delay 2–5min (Anti-Spam) → Log DM to Sheets         │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│  PHASE 4 — FOLLOW-UP SEQUENCE                               │
│  Read DM Log → Filter Leads (no reply in 3 days)            │
│  Groq → Generate Follow-up → Delay 3–7min → Log to Sheets   │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│  PHASE 5 — DAILY REPORTING                                  │
│  Read All DM Data → Calculate Stats                         │
│  Save Report to Sheets → Send via Gmail at 8PM              │
└─────────────────────────────────────────────────────────────┘
```
 
---
 
## 🛠️ Tech Stack
 
| Tool | Role |
|---|---|
| **n8n** | Workflow automation backbone — 21 nodes, fully connected |
| **Apify** | Instagram hashtag scraping & profile extraction |
| **Groq (LLaMA 3.1 8B Instant)** | AI-powered personalized DM generation |
| **Google Sheets** | Lead tracking, DM logs, follow-up management, reports |
| **Gmail** | Automated daily report delivery |
 
---
 
## ⚙️ Setup Instructions
 
### Step 1 — Get Your API Keys
 
| Service | Where to Get It |
|---|---|
| Apify | [apify.com](https://apify.com) → Settings → Integrations |
| Groq | [console.groq.com](https://console.groq.com) → API Keys |
| Google Sheets | Google Cloud Console → Enable Sheets API → OAuth2 |
| Gmail | Same Google Cloud project as Sheets |
 
---
 
### Step 2 — Create Your Google Sheet
 
Create a new Google Sheet with exactly **3 tabs** named:
 
```
Leads       ← scraped & scored leads land here
DM_Log      ← every DM sent gets logged here
Report      ← daily stats get written here
```
 
Note your Sheet ID from the URL:
```
https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID_HERE/edit
```
 
---
 
### Step 3 — Import the Workflow into n8n
 
1. Open n8n → **Workflows** → **Import from File**
2. Upload `AI_AGNECY_-_COMPLETE_OUTREACH_PIPELINE.json`
3. Find and replace all instances of `REPLACE_YOUR_GOOGLE_SHEET_ID` with your actual Sheet ID
4. Find and replace `REPLACE_YOUR_EMAIL@gmail.com` with your Gmail address
5. Connect credentials for: **Google Sheets**, **Gmail**, **Groq (HTTP Header Auth)**, **Apify (HTTP Header Auth)**
6. Click **Publish**
 
---
 
### Step 4 — Connect Credentials
 
**Groq API** (HTTP Header Auth):
- Header Name: `Authorization`
- Header Value: `Bearer YOUR_GROQ_API_KEY`
 
**Apify API** (HTTP Header Auth):
- Header Name: `Authorization`
- Header Value: `Bearer YOUR_APIFY_TOKEN`
 
**Google Sheets & Gmail** — use n8n's built-in OAuth2 connector.
 
---
 
## 📁 Project Structure
 
```
AI-AGENCY---COMPLETE-OUTREACH-BOT/
│
├── AI_AGNECY_-_COMPLETE_OUTREACH_PIPELINE.json   ← Import this into n8n
├── README.md                                      ← You're reading this
└── LICENSE                                        ← MIT
```
 
---
 
## 🧠 How the Lead Scorer Works
 
Each scraped profile gets scored out of 100:
 
```
Followers score    → 0–40 pts  (based on count range)
Engagement rate    → 0–40 pts  (higher engagement = better lead)
Bio keywords       → 0–20 pts  (detects: agency, AI, marketing, automation, etc.)
```
 
Only profiles scoring **above threshold** make it to the DM phase.
 
---
 
## 🤖 How the AI DM Works
 
The Groq node reads each lead's:
- `full_name`
- `bio`
- `profile niche` (inferred from hashtags + bio)
 
And writes a **unique, personalized outreach DM** for each one using LLaMA 3.1 8B Instant — fast, free, and surprisingly good at sounding human.
 
---
 
## 💡 What I'd Add With More Time
 
- [ ] Proxy rotation for safer high-volume scraping
- [ ] Live real-time dashboard (not just Sheets)
- [ ] LinkedIn as a second outreach platform
- [ ] Dedicated warmed-up Instagram accounts for scale
- [ ] Reply detection to pause follow-ups automatically
- [ ] A/B testing for DM copy performance
 
---
 
## ⚠️ Disclaimer
 
> Built and tested on **dedicated test accounts only**.  
> No personal profiles were used.  
> Use responsibly and in compliance with Instagram's Terms of Service.
 
---
 
## 👨‍💻 Built By
 
**Tufaque Sayyed**  
Prompt Engineering & n8n Intern — Assignment 01 @ Califnco  
 
[![GitHub](https://img.shields.io/badge/GitHub-TUFAQUE-181717?style=flat&logo=github)](https://github.com/TUFAQUE)
 
---
 
## 📄 License
 
MIT — free to use, modify, and distribute.
 
---
 
<p align="center">
  <i>Built in 48 hours. Runs forever.</i>
</p>
 
