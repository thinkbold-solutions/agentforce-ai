# Quick Reference Card

## Project Overview

| Item | Value |
|------|-------|
| **Project Name** | AgentForce AI (working title) |
| **Duration** | 28 weeks (7 phases) |
| **Tech Stack** | React + Tailwind, FastAPI, MongoDB, Netlify |
| **Primary Integration** | GoHighLevel (GHL) |
| **Target Launch** | Beta with 10-30 thinkBOLD clients |

---

## The 4 AI Agents

| Agent | Purpose | Key Features | Phase |
|-------|---------|--------------|-------|
| 🤖 **AI Receptionist** | Handle calls/texts 24/7 | Voice AI, Lead Scoring, FAQ, Scheduling | Phase 2 (Weeks 5-8) |
| 💰 **Finance Agent** | Business analytics chat | Natural language queries, Dashboards, Reports | Phase 4 (Weeks 13-16) |
| 📱 **Personal Assistant** | Monitor your phone line | Voicemail summary, Draft responses, Automations | Phase 3 (Weeks 9-12) |
| 📣 **Social Media Manager** | Content & trends | Trend monitoring, Post scheduling, AI content | Phase 5 (Weeks 17-20) |

---

## Phase Timeline

```
Phase 1 (Weeks 1-4):   Foundation ████████░░░░░░░░░░░░░░░░
Phase 2 (Weeks 5-8):   AI Receptionist ░░░░████████░░░░░░░░░░
Phase 3 (Weeks 9-12):  Personal Assistant & Notifications ░░░░░░░░████████░░░░
Phase 4 (Weeks 13-16): Finance Agent & Analytics ░░░░░░░░░░░░████████
Phase 5 (Weeks 17-20): Social Media Manager ░░░░░░░░░░░░░░░░████████
Phase 6 (Weeks 21-24): Mobile & Polish ░░░░░░░░░░░░░░░░░░░░████████
Phase 7 (Weeks 25-28): Optimization & Scale ░░░░░░░░░░░░░░░░░░░░░░░░████
```

---

## Key Deliverables by Phase

### Phase 1: Foundation
- ✅ Authentication (Email + Google OAuth)
- ✅ User & Tenant Management
- ✅ GHL OAuth Integration
- ✅ Webhook Infrastructure
- ✅ Audit Logging

### Phase 2: AI Receptionist
- ✅ GHL Voice AI Integration
- ✅ Knowledge Base System
- ✅ Lead Scoring Engine
- ✅ Escalation Rules
- ✅ Human Approval Workflow

### Phase 3: Personal Assistant & Notifications
- ✅ Phone Line Monitoring
- ✅ Voicemail Transcription
- ✅ Response Drafting
- ✅ Push/Email/SMS Notifications
- ✅ Team Collaboration

### Phase 4: Finance Agent
- ✅ Analytics Data Pipeline
- ✅ Natural Language Queries
- ✅ Dashboard Widgets
- ✅ Custom Report Builder
- ✅ Scheduled Reports

### Phase 5: Social Media Manager
- ✅ GHL Social Planner Integration
- ✅ Content Calendar
- ✅ Trend Monitoring
- ✅ AI Content Generation
- ✅ Performance Analytics

### Phase 6: Mobile & Polish
- ✅ Capacitor (iOS/Android)
- ✅ Native Push Notifications
- ✅ Voice Input
- ✅ White-Label Branding
- ✅ Stripe Billing

### Phase 7: Optimization & Scale
- ✅ Semantic Caching (50-70% cost reduction)
- ✅ Model Routing
- ✅ SSO/SAML
- ✅ Customer API Access
- ✅ Security Hardening

---

## Pricing Tiers

| Tier | Price/mo | Agents | Conversations | Voice Min | Team |
|------|----------|--------|---------------|-----------|------|
| **Starter** | $49 | Receptionist only | 500 | 100 | 2 |
| **Professional** | $149 | +Personal Assistant | 2,000 | 500 | 5 |
| **Business** | $299 | All 4 agents | 10,000 | 2,000 | 15 |
| **Agency** | $599 | All + White-label | Unlimited | 5,000 | Unlimited |
| **Enterprise** | Custom | Everything | Unlimited | Unlimited | Unlimited |

---

## Key Integrations

### Required
- **GoHighLevel** - CRM, Voice AI, Conversations, Social Planner
- **MongoDB Atlas** - Primary database
- **OpenAI/Anthropic** - LLM for agents
- **Stripe** - Billing & subscriptions

### Optional/Enhancement
- **Google Calendar** - Direct booking
- **SendGrid** - Email notifications
- **Twilio** - SMS alerts
- **Firebase** - Push notifications
- **Apify** - Web scraping for trends

---

## Database Collections

| Collection | Purpose | Key Fields |
|------------|---------|------------|
| `tenants` | Accounts & agencies | subscription, branding, ghl_tokens |
| `users` | Team members | role, permissions, preferences |
| `agents` | AI agent configs | system_prompt, mode, escalation_rules |
| `conversations` | All interactions | messages, lead_score, call_data |
| `knowledge_base` | FAQ & training | content, embeddings, version |
| `audit_logs` | Compliance logging | event_type, user_id, details |
| `notifications` | User alerts | type, channels, read status |
| `usage_records` | Billing metrics | conversations, tokens, voice_minutes |

---

## User Roles

| Role | Access Level |
|------|--------------|
| **Super Admin** | Platform owner (thinkBOLD) - full access |
| **Agency Admin** | Manage agency + all sub-accounts |
| **Account Admin** | Manage single account + team |
| **Manager** | View team data, manage agents |
| **Agent User** | View own data, use agents |
| **Viewer** | Read-only access |

---

## Success Metrics

### MVP Launch Criteria
- AI Receptionist handles 90% calls without escalation
- Lead scoring accuracy > 80%
- Response drafts approved > 70% unchanged
- API response time < 500ms (p95)
- 99.5% uptime

### Month 3 Targets
- 30 paying customers
- $5,000 MRR
- 2 agency resellers

### Month 12 Targets
- 300 paying customers
- $75,000 MRR
- 15 agency resellers

---

## Cost Optimization Strategies

| Strategy | Savings | Implementation |
|----------|---------|----------------|
| Model Routing | 40-60% | Route simple tasks to GPT-4o-mini |
| Semantic Caching | 25-50% | Cache similar LLM responses |
| Context Summarization | 20-40% | Summarize long conversations |

**Projected costs at 1,000 users:** ~$750/month (with optimization)

---

## Security Checklist

- [x] Encryption at rest (AES-256-GCM)
- [x] Encryption in transit (TLS 1.3)
- [x] Audit logging (90-day retention)
- [x] Role-based access control
- [x] Rate limiting
- [x] CSRF protection
- [ ] SOC 2 Type 1 (Phase 7)
- [ ] HIPAA BAA template (Phase 7)

---

## Emergency Contacts & Resources

| Resource | Link |
|----------|------|
| GHL API Docs | https://highlevel.stoplight.io/ |
| GHL Voice AI | https://help.gohighlevel.com/support/solutions/articles/155000003911 |
| Emergent Docs | https://help.emergent.sh/ |
| MongoDB Atlas | https://cloud.mongodb.com/ |
| Netlify Dashboard | https://app.netlify.com/ |

---

## File Structure

```
/project-root
├── README.md                 ← Primary specification (start here!)
├── /docs
│   ├── implementation-phases.md  ← Week-by-week tasks
│   ├── gap-analysis-summary.md   ← Research findings
│   └── quick-reference.md        ← This file
├── /src
│   ├── /frontend             ← React + Tailwind
│   └── /backend              ← FastAPI
└── /prompts
    └── voice-ai-templates.md ← GHL Voice AI prompts
```

---

## Getting Started Checklist

### Before Week 1
- [ ] Create Emergent account
- [ ] Set up GitHub repository
- [ ] Create MongoDB Atlas account
- [ ] Set up Netlify account
- [ ] Register GHL Marketplace app
- [ ] Get OpenAI API key

### Week 1 Goals
- [ ] Project initialized in Emergent
- [ ] GitHub connected
- [ ] MongoDB cluster running
- [ ] Basic auth working
- [ ] First deployment to Netlify

---

*Keep this card handy during development!*
