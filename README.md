# AI Agent Platform for GoHighLevel

## 🎯 Product Vision

A multi-agent AI business operating system that integrates with GoHighLevel (GHL), providing small-to-medium businesses with an AI-powered workforce including an AI Receptionist, Finance Agent, Personal Assistant, and Social Media Manager. Built as a PWA with native app deployment capability, designed for both internal use (thinkBOLD clients) and white-label resale to other GHL agencies.

---

## 📋 Table of Contents

1. [Executive Summary](#executive-summary)
2. [System Architecture](#system-architecture)
3. [Agent Specifications](#agent-specifications)
4. [Core Platform Features](#core-platform-features)
5. [Security & Compliance](#security--compliance)
6. [Multi-Tenant Architecture](#multi-tenant-architecture)
7. [Database Schema](#database-schema)
8. [API Integration Map](#api-integration-map)
9. [User Interface Specifications](#user-interface-specifications)
10. [Onboarding Flow](#onboarding-flow)
11. [Notification System](#notification-system)
12. [Analytics & Reporting](#analytics--reporting)
13. [Billing & Usage Metering](#billing--usage-metering)
14. [Deployment Architecture](#deployment-architecture)
15. [Development Phases](#development-phases)

---

## Executive Summary

### Product Name
**AgentForce AI** (working title - customizable for white-label)

### Target Users
- **Primary:** thinkBOLD Solutions clients (20-30 initial users)
- **Secondary:** GHL agencies seeking AI agent solutions for their clients
- **Tertiary:** Direct SMB customers

### Core Value Proposition
Replace expensive human receptionists, assistants, and social media managers with AI agents that work 24/7, learn from every interaction, and integrate seamlessly with GoHighLevel CRM.

### Tech Stack
- **Frontend:** React + Tailwind CSS (Emergent-generated)
- **Backend:** Python FastAPI (Emergent-generated)
- **Database:** MongoDB Atlas
- **Hosting:** Netlify (frontend) + serverless functions
- **Mobile:** Capacitor wrapper for iOS/Android
- **Voice AI:** Native GHL Voice AI API
- **Automations:** GHL Agent AI Studio + self-hosted n8n

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                        CLIENT APPLICATIONS                          │
├─────────────────┬─────────────────┬─────────────────────────────────┤
│   PWA (Web)     │   iOS App       │   Android App                   │
│   React/Tailwind│   Capacitor     │   Capacitor                     │
└────────┬────────┴────────┬────────┴────────┬────────────────────────┘
         │                 │                 │
         └─────────────────┼─────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      API GATEWAY (Netlify Functions)                │
├─────────────────────────────────────────────────────────────────────┤
│  Authentication │ Rate Limiting │ Request Routing │ Usage Metering  │
└────────┬────────────────────────────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        BACKEND SERVICES                             │
├──────────────┬──────────────┬──────────────┬───────────────────────┤
│ Agent Engine │ Notification │ Analytics    │ Billing Service       │
│ Service      │ Service      │ Service      │                       │
├──────────────┼──────────────┼──────────────┼───────────────────────┤
│ Knowledge    │ Audit Log    │ Report       │ Webhook               │
│ Base Service │ Service      │ Generator    │ Service               │
└──────────────┴──────────────┴──────────────┴───────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        DATA LAYER                                   │
├──────────────┬──────────────┬──────────────┬───────────────────────┤
│ MongoDB      │ Redis        │ Vector DB    │ File Storage          │
│ (Primary)    │ (Cache)      │ (Embeddings) │ (S3/Cloudflare R2)    │
└──────────────┴──────────────┴──────────────┴───────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     EXTERNAL INTEGRATIONS                           │
├──────────────┬──────────────┬──────────────┬───────────────────────┤
│ GHL APIs     │ OpenAI/      │ Apify        │ Google APIs           │
│ (Voice, CRM, │ Anthropic    │ (Scraping)   │ (Calendar, Trends)    │
│ Social)      │ (LLM)        │              │                       │
├──────────────┼──────────────┼──────────────┼───────────────────────┤
│ n8n          │ Stripe       │ Twilio       │ Email Services        │
│ (Automations)│ (Payments)   │ (SMS Alerts) │ (SendGrid/Postmark)   │
└──────────────┴──────────────┴──────────────┴───────────────────────┘
```

---

## Agent Specifications

### Agent 1: AI Receptionist 📞

#### Purpose
Handle inbound calls and text messages 24/7, qualify leads, answer FAQs, schedule appointments, and route complex inquiries to humans.

#### Operating Modes
1. **Autopilot Mode:** Fully autonomous responses within configured parameters
2. **Suggestive Mode:** Drafts responses for human approval before sending
3. **Hybrid Mode:** Autopilot for high-confidence (>90%), suggestive for lower

#### Core Capabilities
| Feature | Description | GHL API Used |
|---------|-------------|--------------|
| Call Answering | Answer inbound calls with custom greeting | Voice AI API |
| Lead Qualification | Ask qualifying questions, assign lead scores | Custom + CRM API |
| FAQ Handling | Answer common questions from knowledge base | Custom + KB |
| Appointment Booking | Check availability, book directly on calendar | Calendar API |
| Call Transfer | Warm transfer to human with context | Voice AI Custom Actions |
| SMS Response | Reply to text messages contextually | Conversations API |
| Voicemail Handling | Transcribe and summarize voicemails | Voice AI + Custom |
| Spam Blocking | Filter robocalls and telemarketers | Custom screening layer |

#### Lead Scoring System
```
Score Range | Classification | Action
90-100      | Hot Lead       | Immediate notification, priority queue
70-89       | Warm Lead      | Same-day follow-up queue
50-69       | Cool Lead      | Nurture sequence
0-49        | Cold/Unqualified | Archive or disqualify
```

#### Scoring Criteria (Configurable)
- Budget mentioned: +20 points
- Timeline urgency: +15 points
- Decision maker: +15 points
- Specific service requested: +10 points
- Repeat caller: +10 points
- Referred by existing customer: +15 points
- After-hours inquiry: +5 points

#### Escalation Rules
| Trigger | Action |
|---------|--------|
| "Speak to manager/human" | Immediate transfer or callback scheduling |
| Negative sentiment detected | Flag for human review, soften tone |
| 3+ failed intent recognitions | Transfer to human |
| Emergency keywords | Immediate alert + transfer |
| VIP caller (tagged in CRM) | Priority routing |
| Complaint keywords | Escalate + log for review |

#### Voice AI Prompt Template
```markdown
# IDENTITY
You are [Business Name]'s virtual receptionist. Your name is [Agent Name].
You sound friendly, professional, and helpful—like talking to a real team member.

# BUSINESS CONTEXT
- Business: [Business Name]
- Industry: [Industry]
- Location: [Address]
- Hours: [Operating Hours]
- Services: [Service List]
- Unique Value: [Value Proposition]

# YOUR OBJECTIVES (in priority order)
1. Greet caller warmly and identify their need
2. Qualify the lead using discovery questions
3. Answer questions from knowledge base
4. Book appointment if appropriate
5. Capture contact information for follow-up
6. Handle objections professionally

# QUALIFYING QUESTIONS
- "What brings you in today?" (need identification)
- "Have you worked with a [service type] before?" (experience)
- "What's your timeline for getting started?" (urgency)
- "What area are you located in?" (service area fit)

# BEHAVIOR RULES
## ALWAYS:
- Use caller's name after they provide it
- Confirm understanding before taking action
- Offer next steps at end of call
- Thank them for calling

## NEVER:
- Confirm appointments as "booked" (say "scheduled pending confirmation")
- Promise specific callback times you can't guarantee
- Discuss pricing unless specifically authorized
- Share other customer information
- Admit you're AI unless directly asked

# SPECIAL SCENARIOS
- If asked "Are you a robot/AI?": "I'm [Agent Name], a virtual assistant. I can help you just like any team member, or I can connect you with a human if you prefer."
- If caller is upset: "I understand this is frustrating. Let me make sure the right person helps you with this."
- If outside service area: "We primarily serve [area]. Let me see if we can still help or recommend someone closer to you."

# KNOWLEDGE BASE
[Dynamically injected from KB system]
```

---

### Agent 2: Finance Agent 💰

#### Purpose
Provide natural language interface to business analytics, interpret financial data, identify trends, and deliver actionable insights.

#### Core Capabilities
| Feature | Description | Data Source |
|---------|-------------|-------------|
| Revenue Analytics | MRR, ARR, revenue trends | Webhook aggregation |
| Lead Metrics | Conversion rates, pipeline value | GHL CRM API |
| Response Time Analysis | Average response times by channel | Webhook tracking |
| Email Performance | Open rates, click rates, best send times | GHL Email API |
| Campaign ROI | Ad spend vs revenue attribution | Custom tracking |
| Churn Analysis | Customer retention trends | CRM + Custom |
| Forecasting | Predictive revenue modeling | ML on historical data |

#### Chat Interface Queries (Examples)
```
User: "What's my revenue this month compared to last month?"
Agent: "Your revenue this month is $47,250, up 12% from $42,150 last month. 
        The increase is primarily driven by 3 new clients in your [service] 
        category. Would you like a breakdown by service type?"

User: "When do my customers usually open emails?"
Agent: "Based on your last 90 days of email data, your highest open rates 
        occur Tuesday-Thursday between 9-11 AM. Your worst performing time 
        is Friday afternoon. I'd recommend scheduling important campaigns 
        for Tuesday at 10 AM."

User: "Show me my top performing lead sources"
Agent: "Your top 5 lead sources this quarter:
        1. Google Ads - 45 leads (32% conversion)
        2. Referrals - 28 leads (54% conversion)
        3. Facebook - 22 leads (18% conversion)
        4. Website organic - 19 leads (26% conversion)
        5. Direct calls - 15 leads (47% conversion)
        
        While Google Ads brings volume, Referrals have your best ROI. 
        Want me to suggest ways to increase referral traffic?"
```

#### Dashboard Widgets
1. **Revenue Overview** - MRR/ARR with trend arrows
2. **Lead Pipeline** - Funnel visualization
3. **Response Time Gauge** - Current avg vs target
4. **Conversion Rate** - By source and time period
5. **Email Performance** - Open/click rates over time
6. **Top Performers** - Best converting campaigns/sources
7. **Alerts Panel** - Anomalies and action items

#### Metric Definitions
```yaml
mrr:
  formula: sum(active_subscriptions.monthly_value)
  update_frequency: daily

lead_conversion_rate:
  formula: (converted_leads / total_leads) * 100
  lookback_period: 30_days

avg_response_time:
  formula: avg(first_response_timestamp - lead_created_timestamp)
  channels: [sms, email, call]

customer_lifetime_value:
  formula: avg_revenue_per_customer * avg_customer_lifespan_months
```

---

### Agent 3: Personal Assistant 🤖

#### Purpose
Monitor assigned phone line for the user, handle voicemails, missed calls, and text messages with intelligent response drafting and lightweight automation suggestions.

#### Core Capabilities
| Feature | Description | Integration |
|---------|-------------|-------------|
| Voicemail Monitoring | Transcribe, summarize, prioritize | GHL Voice AI |
| Missed Call Alerts | Notify with context and suggested response | Webhooks + Push |
| SMS Draft Responses | Context-aware reply suggestions | Conversations API |
| Email Chain Context | Read full threads before responding | GHL Email Sync |
| Task Creation | Create follow-up tasks from conversations | GHL Tasks API |
| Quick Automations | Suggest and deploy simple follow-up sequences | GHL Workflows |

#### Operating Modes
1. **Autopilot:** Auto-respond to routine messages (confirmations, simple questions)
2. **Suggestive:** Draft responses for all messages, user approves
3. **Notify Only:** Alert user to messages, no drafts

#### Suggested Automation Templates
```yaml
missed_call_followup:
  name: "Missed Call Follow-up"
  trigger: missed_call
  delay: 5_minutes
  action: 
    type: sms
    template: "Hi {first_name}, sorry I missed your call! I'm available now 
               or can schedule a time that works better. What works for you?"

voicemail_acknowledgment:
  name: "Voicemail Receipt"
  trigger: new_voicemail
  delay: immediate
  action:
    type: sms
    template: "Got your voicemail, {first_name}! I'll get back to you within 
               {response_sla}. Is there anything urgent I should know?"

appointment_reminder:
  name: "Appointment Reminder"
  trigger: 24_hours_before_appointment
  action:
    type: sms
    template: "Reminder: You have an appointment tomorrow at {time}. 
               Reply CONFIRM to confirm or RESCHEDULE if you need to change."
```

#### Priority Scoring for Messages
```
Factor                          | Points
--------------------------------|--------
VIP contact tag                 | +30
Contains "urgent" or "ASAP"     | +25
Unanswered for >2 hours         | +20
From active deal contact        | +15
Multiple messages in thread     | +10
Contains question mark          | +5
After business hours            | +5
```

---

### Agent 4: Social Media Manager 📱

#### Purpose
Monitor social trends, generate content recommendations based on business profile and customer persona, schedule posts, and track performance.

#### Core Capabilities
| Feature | Description | Data Source |
|---------|-------------|-------------|
| Trend Monitoring | Track relevant industry trends | Google Trends + Apify |
| Content Suggestions | AI-generated post ideas | LLM + Business Profile |
| Competitor Tracking | Monitor competitor social activity | Apify scraping |
| Seasonal Alerts | Notify about relevant dates/events | Calendar + Trends |
| Post Scheduling | Schedule across platforms | GHL Social Planner API |
| Performance Analytics | Track engagement metrics | GHL Social API |
| Audience Insights | Analyze best posting times | Historical data |

#### Content Recommendation Engine
```yaml
input_sources:
  - business_profile (industry, services, value_prop)
  - customer_persona (demographics, interests, pain_points)
  - trending_topics (Google Trends, industry news)
  - seasonal_calendar (holidays, industry events)
  - competitor_activity (what's working for them)
  - historical_performance (what worked for this business)

output:
  - content_ideas (5-10 per week)
  - optimal_posting_times
  - hashtag_suggestions
  - content_type_mix (image, video, carousel, story)
  - caption_drafts
```

#### Supported Platforms (via GHL)
- Facebook (Pages)
- Instagram (Business)
- LinkedIn (Company)
- Google Business Profile
- TikTok (limited)
- Twitter/X (limited)

#### Trend Alert Types
```
Alert Type           | Example
---------------------|--------------------------------------------------
Seasonal Opportunity | "Valentine's Day is in 2 weeks. Based on your 
                     | pet grooming business, consider a 'Puppy Love' 
                     | photo contest promotion."
                     |
Industry Trend       | "Posts about 'sustainable pet products' are up 
                     | 340% this month. Here are 3 content ideas..."
                     |
Competitor Activity  | "[Competitor] just launched a promotion getting 
                     | high engagement. Consider a response campaign."
                     |
Viral Opportunity    | "A trending sound on TikTok aligns with your 
                     | industry. Here's how you could use it..."
```

---

## Core Platform Features

### 1. Authentication & User Management

#### Authentication Methods
- **Email/Password** with bcrypt hashing
- **Google OAuth** (primary recommendation)
- **GHL OAuth** for CRM connection
- **Magic Links** for passwordless login

#### User Roles & Permissions
```yaml
roles:
  super_admin:
    description: "Platform owner (thinkBOLD)"
    permissions: [all]
    
  agency_admin:
    description: "Agency owner/reseller"
    permissions:
      - manage_agency_settings
      - manage_sub_accounts
      - view_all_agency_data
      - manage_billing
      - white_label_customization
      
  account_admin:
    description: "Business owner"
    permissions:
      - manage_account_settings
      - manage_agents
      - manage_team_members
      - view_all_account_data
      - manage_integrations
      
  manager:
    description: "Team manager"
    permissions:
      - view_team_data
      - manage_agents
      - approve_agent_actions
      - view_reports
      
  agent_user:
    description: "Standard user"
    permissions:
      - view_own_data
      - interact_with_agents
      - approve_own_drafts
      
  viewer:
    description: "Read-only access"
    permissions:
      - view_assigned_data
```

### 2. Knowledge Base System

#### Structure
```
Knowledge Base
├── FAQ Items
│   ├── Question
│   ├── Answer
│   ├── Keywords (for matching)
│   ├── Category
│   └── Priority (for conflict resolution)
├── Business Information
│   ├── Hours
│   ├── Location(s)
│   ├── Services
│   ├── Pricing (if public)
│   └── Policies
├── Training Documents
│   ├── Uploaded PDFs
│   ├── Website crawl data
│   └── Custom training text
└── Correction Log
    ├── Original AI response
    ├── Corrected response
    └── Applied to training (Y/N)
```

#### Import Methods
1. **Manual Entry** - Add Q&A pairs through UI
2. **Website Crawl** - Scrape and ingest website content
3. **Document Upload** - PDF, DOCX, TXT parsing
4. **GHL Import** - Pull from GHL custom values
5. **CSV Bulk Upload** - Import from spreadsheet

#### Version Control
- All KB changes tracked with timestamps
- Rollback to previous versions
- Diff view for changes
- Approval workflow for changes (optional)

### 3. Conversation Management

#### Unified Inbox View
- All channels in one view (calls, SMS, email, social)
- Filter by: channel, status, assigned user, date range
- Search across all conversations
- Bulk actions (archive, assign, tag)

#### Conversation States
```
State        | Description                    | Actions Available
-------------|--------------------------------|------------------
new          | Just received, unread          | Read, Assign, Reply
in_progress  | Being handled                  | Reply, Transfer, Close
waiting      | Awaiting customer response     | Follow-up, Close
escalated    | Transferred to human           | Reply, De-escalate
resolved     | Completed successfully         | Reopen, Archive
archived     | Closed and stored              | Reopen, Delete
```

#### Tagging System
- Custom tags per account
- Auto-tagging based on keywords
- Tag-based reporting
- Tag-triggered automations

### 4. Business Hours & Scheduling

#### Configuration
```yaml
business_hours:
  timezone: "America/New_York"
  regular_hours:
    monday: {open: "09:00", close: "17:00"}
    tuesday: {open: "09:00", close: "17:00"}
    wednesday: {open: "09:00", close: "17:00"}
    thursday: {open: "09:00", close: "17:00"}
    friday: {open: "09:00", close: "17:00"}
    saturday: {open: "10:00", close: "14:00"}
    sunday: closed
    
  holidays:
    - date: "2025-12-25"
      name: "Christmas Day"
      hours: closed
    - date: "2025-12-24"
      name: "Christmas Eve"
      hours: {open: "09:00", close: "12:00"}
      
  after_hours_behavior:
    calls: voicemail_with_callback_offer
    sms: auto_reply_with_next_available
    email: auto_acknowledge
```

### 5. Team Collaboration

#### Features
- **Shared Dashboard** - Team-wide metrics view
- **Conversation Comments** - Internal notes on threads
- **@Mentions** - Tag team members for attention
- **Activity Feed** - Real-time team action log
- **Assignment Queue** - Fair distribution of work

#### Activity Feed Events
```
- New lead received
- Agent response sent (auto/manual)
- Conversation escalated
- Appointment booked
- Lead score changed
- Team member mentioned
- Settings changed
- Agent configuration updated
```

---

## Security & Compliance

### Encryption Standards
| Data Type | At Rest | In Transit |
|-----------|---------|------------|
| User credentials | bcrypt hash | TLS 1.3 |
| API keys | AES-256-GCM | TLS 1.3 |
| Conversations | AES-256 | TLS 1.3 |
| Call recordings | AES-256 | TLS 1.3 |
| PII fields | Field-level encryption | TLS 1.3 |

### Audit Logging

#### Events to Log
```yaml
authentication:
  - login_success
  - login_failure
  - logout
  - password_change
  - mfa_enabled
  - mfa_disabled

agent_activity:
  - agent_response_sent
  - agent_response_approved
  - agent_response_rejected
  - agent_escalation_triggered
  - agent_tool_called (CRM update, calendar book, etc.)

data_access:
  - conversation_viewed
  - report_generated
  - data_exported
  - recording_played

configuration:
  - agent_settings_changed
  - knowledge_base_updated
  - user_permissions_changed
  - integration_connected
  - integration_disconnected

billing:
  - subscription_created
  - subscription_changed
  - payment_processed
  - payment_failed
```

#### Log Entry Schema
```json
{
  "id": "uuid",
  "timestamp": "2025-01-15T10:30:00Z",
  "tenant_id": "tenant_123",
  "user_id": "user_456",
  "event_type": "agent_response_sent",
  "resource_type": "conversation",
  "resource_id": "conv_789",
  "action": "create",
  "details": {
    "agent_type": "receptionist",
    "mode": "autopilot",
    "confidence_score": 0.94,
    "response_length": 145
  },
  "ip_address": "192.168.1.1",
  "user_agent": "Mozilla/5.0...",
  "session_id": "sess_abc"
}
```

#### Retention Policy
| Data Type | Active | Archive | Delete |
|-----------|--------|---------|--------|
| Audit logs | 90 days | 2 years | After archive |
| Conversations | 1 year | 3 years | After archive |
| Call recordings | 90 days | 1 year | After archive |
| Analytics data | 2 years | 5 years | After archive |
| User data | Active | On deletion request | 30 days after request |

### Compliance Considerations

#### HIPAA (Healthcare Clients)
- [ ] Business Associate Agreement (BAA) template
- [ ] PHI field identification and encryption
- [ ] Access logging for PHI
- [ ] Minimum necessary access principle
- [ ] Breach notification procedures

#### SOC 2 Preparation
- [ ] Access control documentation
- [ ] Change management procedures
- [ ] Incident response plan
- [ ] Vendor management policy
- [ ] Data backup procedures

#### GDPR/CCPA
- [ ] Data export functionality
- [ ] Right to deletion workflow
- [ ] Consent management
- [ ] Privacy policy generator
- [ ] Cookie consent (web)

---

## Multi-Tenant Architecture

### Hierarchy
```
Platform (thinkBOLD)
└── Agency Tier (Resellers)
    ├── Agency Settings
    │   ├── White-label branding
    │   ├── Custom domain
    │   ├── Billing configuration
    │   └── Default agent templates
    └── Sub-Accounts (Agency's Clients)
        ├── Account Settings
        ├── Agent Configurations
        ├── Team Members
        ├── Conversations
        └── Analytics
```

### Data Isolation
```python
# Every database query MUST include tenant filter
# Middleware automatically injects tenant_id

# CORRECT
def get_conversations(tenant_id: str, filters: dict):
    return db.conversations.find({
        "tenant_id": tenant_id,  # Always required
        **filters
    })

# INCORRECT - Never do this
def get_conversations(filters: dict):
    return db.conversations.find(filters)  # Missing tenant isolation!
```

### White-Label Configuration
```yaml
agency_branding:
  agency_id: "agency_123"
  
  # Visual Branding
  logo_url: "https://..."
  favicon_url: "https://..."
  primary_color: "#FF5733"
  secondary_color: "#333333"
  font_family: "Inter"
  
  # Custom Domain
  custom_domain: "ai.agencyname.com"
  ssl_certificate: "auto"  # or custom
  
  # Email Branding
  email_from_name: "Agency Name AI"
  email_from_address: "ai@agencyname.com"
  email_footer_text: "Powered by Agency Name"
  
  # App Branding
  app_name: "Agency AI Assistant"
  app_description: "Your AI-powered business assistant"
  
  # Support
  support_email: "support@agencyname.com"
  support_url: "https://help.agencyname.com"
```

### Pricing Tiers
```yaml
tiers:
  starter:
    name: "Starter"
    price_monthly: 49
    price_annual: 470  # 2 months free
    features:
      agents: [receptionist]  # 1 agent only
      conversations_monthly: 500
      voice_minutes_monthly: 100
      team_members: 2
      knowledge_base_items: 100
      support: "email"
    api_access: false
    white_label: false
    
  professional:
    name: "Professional"
    price_monthly: 149
    price_annual: 1430
    features:
      agents: [receptionist, personal_assistant]
      conversations_monthly: 2000
      voice_minutes_monthly: 500
      team_members: 5
      knowledge_base_items: 500
      support: "priority_email"
    api_access: true
    white_label: false
    
  business:
    name: "Business"
    price_monthly: 299
    price_annual: 2870
    features:
      agents: [receptionist, personal_assistant, finance, social_media]
      conversations_monthly: 10000
      voice_minutes_monthly: 2000
      team_members: 15
      knowledge_base_items: 2000
      support: "chat"
    api_access: true
    white_label: false
    
  agency:
    name: "Agency"
    price_monthly: 599
    price_annual: 5750
    features:
      agents: [all]
      conversations_monthly: unlimited
      voice_minutes_monthly: 5000
      team_members: unlimited
      knowledge_base_items: unlimited
      support: "dedicated"
      sub_accounts: 10  # included
      additional_sub_account: 29  # per account
    api_access: true
    white_label: true

  enterprise:
    name: "Enterprise"
    price: "custom"
    features:
      agents: [all]
      everything: unlimited
      support: "dedicated_csm"
      sla: "99.9%"
      custom_development: true
      on_premise_option: true
    api_access: true
    white_label: true
```

### BYOK (Bring Your Own Keys)
```yaml
byok_configuration:
  enabled: true
  supported_providers:
    openai:
      key_field: "api_key"
      endpoint: "https://api.openai.com/v1"
    anthropic:
      key_field: "api_key"
      endpoint: "https://api.anthropic.com"
    
  storage:
    method: "encrypted"
    encryption: "AES-256-GCM"
    key_storage: "environment_variable"  # or "aws_kms" or "hashicorp_vault"
    
  fallback:
    enabled: true
    provider: "platform_pool"
    rate_limit: "100_requests_per_day"
```

---

## Database Schema

### Collections

#### tenants
```javascript
{
  _id: ObjectId,
  tenant_id: String,  // unique identifier
  type: "agency" | "account",
  parent_tenant_id: String | null,  // for sub-accounts
  
  // Basic Info
  name: String,
  slug: String,
  industry: String,
  
  // GHL Connection
  ghl_location_id: String,
  ghl_access_token: String,  // encrypted
  ghl_refresh_token: String,  // encrypted
  ghl_connected_at: Date,
  
  // Subscription
  subscription: {
    tier: String,
    status: "active" | "past_due" | "canceled",
    stripe_customer_id: String,
    stripe_subscription_id: String,
    current_period_end: Date
  },
  
  // White-label (agencies only)
  branding: {
    logo_url: String,
    primary_color: String,
    custom_domain: String,
    // ... other branding fields
  },
  
  // Settings
  settings: {
    timezone: String,
    business_hours: Object,
    default_agent_mode: "autopilot" | "suggestive",
    notification_preferences: Object
  },
  
  // Usage Tracking
  usage: {
    current_period_start: Date,
    conversations_count: Number,
    voice_minutes_used: Number,
    tokens_used: Number
  },
  
  // Metadata
  created_at: Date,
  updated_at: Date,
  created_by: ObjectId
}
```

#### users
```javascript
{
  _id: ObjectId,
  tenant_id: String,
  
  // Auth
  email: String,
  password_hash: String | null,
  google_id: String | null,
  
  // Profile
  first_name: String,
  last_name: String,
  avatar_url: String,
  phone: String,
  
  // Role
  role: "super_admin" | "agency_admin" | "account_admin" | "manager" | "agent_user" | "viewer",
  permissions: [String],  // custom overrides
  
  // Preferences
  preferences: {
    notification_channels: ["email", "push", "sms"],
    dashboard_layout: Object,
    timezone: String  // override tenant timezone
  },
  
  // Security
  mfa_enabled: Boolean,
  mfa_secret: String,  // encrypted
  last_login: Date,
  failed_login_attempts: Number,
  locked_until: Date | null,
  
  // Metadata
  created_at: Date,
  updated_at: Date,
  last_active_at: Date
}
```

#### agents
```javascript
{
  _id: ObjectId,
  tenant_id: String,
  
  // Identity
  agent_type: "receptionist" | "finance" | "personal_assistant" | "social_media",
  name: String,
  avatar_url: String,
  
  // Configuration
  status: "active" | "paused" | "configuring",
  mode: "autopilot" | "suggestive" | "hybrid",
  
  // For Voice Agents
  voice_config: {
    ghl_agent_id: String,
    voice_id: String,
    greeting: String,
    language: String,
    transfer_number: String
  },
  
  // Behavior
  system_prompt: String,
  temperature: Number,
  max_tokens: Number,
  
  // Escalation Rules
  escalation_rules: [{
    trigger_type: "keyword" | "sentiment" | "intent_failure" | "manual",
    trigger_value: String,
    action: "transfer" | "notify" | "flag",
    priority: Number
  }],
  
  // Lead Scoring (receptionist)
  lead_scoring: {
    enabled: Boolean,
    criteria: [{
      field: String,
      condition: String,
      value: Any,
      points: Number
    }]
  },
  
  // Version Control
  version: Number,
  version_history: [{
    version: Number,
    changed_at: Date,
    changed_by: ObjectId,
    changes: Object
  }],
  
  // Metadata
  created_at: Date,
  updated_at: Date,
  created_by: ObjectId
}
```

#### conversations
```javascript
{
  _id: ObjectId,
  tenant_id: String,
  conversation_id: String,  // external ID from GHL
  
  // Contact
  contact_id: String,
  contact_name: String,
  contact_phone: String,
  contact_email: String,
  
  // Conversation Details
  channel: "voice" | "sms" | "email" | "facebook" | "instagram" | "webchat",
  status: "new" | "in_progress" | "waiting" | "escalated" | "resolved" | "archived",
  
  // Assignment
  assigned_agent_id: ObjectId,  // AI agent
  assigned_user_id: ObjectId | null,  // Human user
  
  // Lead Scoring
  lead_score: Number,
  lead_score_factors: [{
    factor: String,
    points: Number,
    timestamp: Date
  }],
  
  // Messages
  messages: [{
    message_id: String,
    direction: "inbound" | "outbound",
    content: String,
    content_type: "text" | "audio" | "image",
    sender_type: "contact" | "agent" | "user",
    sender_id: String,
    timestamp: Date,
    
    // AI-specific
    ai_generated: Boolean,
    ai_confidence: Number,
    ai_mode: "autopilot" | "approved" | null,
    
    // Sentiment
    sentiment: "positive" | "neutral" | "negative",
    sentiment_score: Number
  }],
  
  // Call-specific (if voice)
  call_data: {
    call_id: String,
    duration_seconds: Number,
    recording_url: String,
    transcript: String,
    voicemail: Boolean
  },
  
  // Tags
  tags: [String],
  
  // Internal Notes
  notes: [{
    user_id: ObjectId,
    content: String,
    created_at: Date
  }],
  
  // Timestamps
  started_at: Date,
  last_message_at: Date,
  resolved_at: Date | null,
  
  // Metadata
  created_at: Date,
  updated_at: Date
}
```

#### knowledge_base
```javascript
{
  _id: ObjectId,
  tenant_id: String,
  
  // Content
  type: "faq" | "document" | "business_info" | "training",
  title: String,
  content: String,
  
  // For FAQ type
  question: String,
  answer: String,
  keywords: [String],
  
  // Categorization
  category: String,
  subcategory: String,
  
  // Matching
  priority: Number,  // higher = prefer this answer
  embedding: [Number],  // vector for semantic search
  
  // Source
  source: "manual" | "import" | "website_crawl" | "document_upload",
  source_url: String | null,
  
  // Version Control
  version: Number,
  previous_versions: [{
    version: Number,
    content: String,
    changed_at: Date,
    changed_by: ObjectId
  }],
  
  // Status
  status: "active" | "draft" | "archived",
  
  // Metadata
  created_at: Date,
  updated_at: Date,
  created_by: ObjectId
}
```

#### audit_logs
```javascript
{
  _id: ObjectId,
  tenant_id: String,
  
  // Event Info
  event_type: String,
  event_category: "auth" | "agent" | "data" | "config" | "billing",
  
  // Actor
  user_id: ObjectId | null,
  agent_id: ObjectId | null,
  
  // Target
  resource_type: String,
  resource_id: String,
  
  // Action
  action: "create" | "read" | "update" | "delete" | "execute",
  
  // Details
  details: Object,
  
  // Request Context
  ip_address: String,
  user_agent: String,
  session_id: String,
  
  // Timestamp
  timestamp: Date,
  
  // For compliance queries
  contains_pii: Boolean,
  data_categories: [String]
}
```

#### notifications
```javascript
{
  _id: ObjectId,
  tenant_id: String,
  user_id: ObjectId,
  
  // Content
  type: "lead_alert" | "escalation" | "system" | "team" | "billing",
  title: String,
  body: String,
  
  // Delivery
  channels: ["in_app", "push", "email", "sms"],
  delivered: {
    in_app: Boolean,
    push: Boolean,
    email: Boolean,
    sms: Boolean
  },
  
  // Status
  read: Boolean,
  read_at: Date | null,
  
  // Action
  action_url: String | null,
  action_label: String | null,
  
  // Priority
  priority: "low" | "medium" | "high" | "urgent",
  
  // Related Resource
  resource_type: String | null,
  resource_id: String | null,
  
  // Metadata
  created_at: Date,
  expires_at: Date | null
}
```

#### usage_records
```javascript
{
  _id: ObjectId,
  tenant_id: String,
  
  // Period
  period_start: Date,
  period_end: Date,
  
  // Metrics
  conversations: {
    total: Number,
    by_channel: {
      voice: Number,
      sms: Number,
      email: Number,
      social: Number
    }
  },
  
  voice_minutes: {
    total: Number,
    by_agent: Object
  },
  
  tokens: {
    total: Number,
    by_model: {
      "gpt-4o": Number,
      "gpt-4o-mini": Number,
      "claude-3-5-sonnet": Number
    },
    by_agent: Object
  },
  
  api_calls: {
    total: Number,
    by_endpoint: Object
  },
  
  // Costs (for BYOK billing)
  estimated_cost: Number,
  
  // Metadata
  created_at: Date
}
```

### Indexes
```javascript
// tenants
db.tenants.createIndex({ tenant_id: 1 }, { unique: true })
db.tenants.createIndex({ parent_tenant_id: 1 })
db.tenants.createIndex({ "subscription.status": 1 })

// users
db.users.createIndex({ tenant_id: 1, email: 1 }, { unique: true })
db.users.createIndex({ google_id: 1 })

// conversations
db.conversations.createIndex({ tenant_id: 1, status: 1 })
db.conversations.createIndex({ tenant_id: 1, contact_id: 1 })
db.conversations.createIndex({ tenant_id: 1, last_message_at: -1 })
db.conversations.createIndex({ tenant_id: 1, lead_score: -1 })
db.conversations.createIndex({ tenant_id: 1, "messages.timestamp": -1 })

// knowledge_base
db.knowledge_base.createIndex({ tenant_id: 1, type: 1, status: 1 })
db.knowledge_base.createIndex({ tenant_id: 1, keywords: 1 })

// audit_logs
db.audit_logs.createIndex({ tenant_id: 1, timestamp: -1 })
db.audit_logs.createIndex({ tenant_id: 1, event_type: 1, timestamp: -1 })
db.audit_logs.createIndex({ tenant_id: 1, user_id: 1, timestamp: -1 })
db.audit_logs.createIndex({ timestamp: 1 }, { expireAfterSeconds: 7776000 })  // 90 days TTL

// notifications
db.notifications.createIndex({ tenant_id: 1, user_id: 1, read: 1 })
db.notifications.createIndex({ tenant_id: 1, user_id: 1, created_at: -1 })

// usage_records
db.usage_records.createIndex({ tenant_id: 1, period_start: -1 })
```

---

## API Integration Map

### GoHighLevel APIs

#### Voice AI
```yaml
endpoints:
  create_agent:
    method: POST
    path: /voice-ai/agents
    purpose: Create new voice AI agent
    
  update_agent:
    method: PUT
    path: /voice-ai/agents/{agentId}
    purpose: Update agent configuration
    
  get_call_logs:
    method: GET
    path: /voice-ai/calls
    purpose: Retrieve call history with transcripts
    
  create_custom_action:
    method: POST
    path: /voice-ai/agents/{agentId}/actions
    purpose: Add webhook actions during calls

webhooks:
  - call.started
  - call.ended
  - call.transferred
  - voicemail.received
```

#### CRM
```yaml
endpoints:
  contacts:
    - GET /contacts
    - GET /contacts/{contactId}
    - POST /contacts
    - PUT /contacts/{contactId}
    - GET /contacts/{contactId}/tasks
    
  opportunities:
    - GET /opportunities
    - GET /opportunities/{opportunityId}
    - POST /opportunities
    - PUT /opportunities/{opportunityId}
    
  custom_objects:
    - GET /custom-objects
    - GET /custom-objects/{objectId}/records
```

#### Conversations
```yaml
endpoints:
  - GET /conversations
  - GET /conversations/{conversationId}
  - GET /conversations/{conversationId}/messages
  - POST /conversations/{conversationId}/messages
  
webhooks:
  - conversation.created
  - conversation.updated
  - message.received
  - message.sent
```

#### Social Planner
```yaml
endpoints:
  - GET /social-media-posting/oauth
  - GET /social-media-posting/posts
  - POST /social-media-posting/posts
  - PUT /social-media-posting/posts/{postId}
  - DELETE /social-media-posting/posts/{postId}
  - GET /social-media-posting/posts/{postId}/stats
```

### LLM Providers

#### OpenAI
```yaml
models:
  - gpt-4o (primary for complex tasks)
  - gpt-4o-mini (for simple tasks - cost optimization)
  
endpoints:
  - POST /v1/chat/completions
  - POST /v1/embeddings
```

#### Anthropic
```yaml
models:
  - claude-3-5-sonnet (primary)
  - claude-3-5-haiku (cost optimization)
  
endpoints:
  - POST /v1/messages
```

### Model Routing Logic
```python
def select_model(task_type: str, complexity: str) -> str:
    """
    Route to appropriate model based on task and complexity.
    Target: 60-70% of requests to smaller/cheaper models.
    """
    routing_rules = {
        # Simple tasks -> cheap model
        ("faq_lookup", "low"): "gpt-4o-mini",
        ("sentiment_analysis", "low"): "gpt-4o-mini",
        ("intent_classification", "low"): "gpt-4o-mini",
        ("simple_response", "low"): "gpt-4o-mini",
        
        # Medium complexity -> depends
        ("lead_qualification", "medium"): "gpt-4o-mini",
        ("email_draft", "medium"): "gpt-4o",
        ("conversation_summary", "medium"): "gpt-4o-mini",
        
        # Complex tasks -> premium model
        ("multi_turn_conversation", "high"): "gpt-4o",
        ("financial_analysis", "high"): "gpt-4o",
        ("content_generation", "high"): "claude-3-5-sonnet",
        ("complex_reasoning", "high"): "gpt-4o",
    }
    
    return routing_rules.get(
        (task_type, complexity), 
        "gpt-4o-mini"  # default to cheaper
    )
```

### External Services

#### Apify (Web Scraping)
```yaml
actors:
  - google-trends-scraper
  - instagram-scraper
  - facebook-pages-scraper
  
usage:
  monthly_budget: $50
  rate_limit: 1000_calls_per_day
```

#### Google APIs
```yaml
services:
  - Google Calendar API (appointment booking)
  - Google Trends API (trend data)
  - Google OAuth (authentication)
```

#### Stripe (Billing)
```yaml
features:
  - Subscription management
  - Usage-based billing
  - Invoice generation
  - Payment method management
  - Webhook events
```

---

## User Interface Specifications

### Navigation Structure
```
┌─────────────────────────────────────────────────────────────────┐
│  [Logo]  Dashboard | Conversations | Agents | Analytics | Settings│
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                    MAIN CONTENT AREA                       │  │
│  │                                                           │  │
│  │                                                           │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│  [Receptionist] [Finance] [Assistant] [Social] ← Agent Tabs     │
└─────────────────────────────────────────────────────────────────┘
```

### Screen Inventory

#### 1. Dashboard
- **Purpose:** Overview of all activity and key metrics
- **Components:**
  - Quick stats cards (calls today, leads, response time)
  - Activity feed (real-time updates)
  - Pending actions (drafts awaiting approval)
  - Agent status indicators
  - Recent conversations preview

#### 2. Conversations
- **Purpose:** Unified inbox for all channels
- **Components:**
  - Conversation list with filters
  - Conversation detail panel
  - Message composer
  - Contact sidebar
  - Internal notes section
  - Lead score badge

#### 3. Agent Configuration (per agent type)
- **Purpose:** Configure agent behavior
- **Components:**
  - Mode toggle (Autopilot/Suggestive)
  - System prompt editor
  - Knowledge base manager
  - Escalation rules builder
  - Test conversation simulator
  - Version history

#### 4. Analytics Dashboard
- **Purpose:** Performance insights
- **Components:**
  - Time period selector
  - KPI cards
  - Charts (line, bar, pie)
  - Comparison views
  - Export buttons

#### 5. Settings
- **Sub-pages:**
  - Account settings
  - Team members
  - Integrations
  - Billing
  - Notifications
  - Security
  - White-label (agencies)

### Mobile-Specific Considerations
```yaml
mobile_ui:
  navigation: bottom_tab_bar
  
  priority_features:
    - Push notification handling
    - Quick response approval
    - Voice-to-text input
    - One-tap call back
    
  simplified_views:
    - Dashboard: Cards only, no charts
    - Conversations: List view, swipe actions
    - Agents: Status and mode toggle only
    
  offline_capability:
    - Cache recent conversations
    - Queue responses for sync
    - Show offline indicator
```

### Voice Input (Mobile)
```yaml
voice_commands:
  activation: "Hey [Agent Name]" or tap microphone
  
  supported_commands:
    - "Show me today's leads"
    - "What's my response time?"
    - "Call back [contact name]"
    - "Approve the last draft"
    - "Show unread messages"
    
  implementation:
    platform: capacitor_speech_recognition
    fallback: web_speech_api (limited on iOS)
```

---

## Onboarding Flow

### Step 1: Account Creation (< 1 minute)
```yaml
fields:
  - email (required)
  - password (required, or Google OAuth)
  - business_name (required)
  
actions:
  - Create tenant record
  - Send verification email
  - Redirect to Step 2
```

### Step 2: Business Profile (2-3 minutes)
```yaml
fields:
  - industry (dropdown, 15 options)
  - business_type (service, retail, healthcare, etc.)
  - team_size (1-5, 6-20, 21-50, 50+)
  - primary_goal (dropdown):
    - "Answer more calls"
    - "Qualify leads faster"  
    - "Reduce response time"
    - "Automate follow-ups"
    - "Manage social media"
    
optional_fields:
  - website_url (for KB crawling)
  - phone_number
  - address
  
actions:
  - Update tenant record
  - If website provided, queue for crawling
```

### Step 3: GHL Connection (1-2 minutes)
```yaml
flow:
  1. Explain what we'll access
  2. "Connect GoHighLevel" button
  3. OAuth redirect to GHL
  4. User authorizes
  5. Callback with tokens
  6. Verify connection with test API call
  7. Import basic data (contacts count, etc.)
  
error_handling:
  - Connection failed: Retry button + manual setup option
  - Invalid permissions: Show required scopes
```

### Step 4: First Agent Setup (3-5 minutes)
```yaml
flow:
  1. "Let's set up your AI Receptionist"
  2. Choose voice (play samples)
  3. Set greeting message
  4. Add 5 common FAQs (or import from website)
  5. Set business hours
  6. Choose mode (Autopilot recommended for simple, Suggestive for cautious)
  
test_call:
  - "Try calling your AI Receptionist!"
  - Provide test number
  - Celebrate successful test
```

### Step 5: Team Invites (Optional, < 1 minute)
```yaml
fields:
  - Invite team members by email
  - Assign roles
  
skip_option: "I'll do this later"
```

### Post-Onboarding
```yaml
actions:
  - Show dashboard with guided tour
  - Send welcome email series (Days 1, 3, 7)
  - Schedule check-in (Day 7)
  
progressive_profiling:
  triggers:
    - After 10 conversations: "Add more FAQs?"
    - After 7 days: "Set up second agent?"
    - After first month: "Upgrade prompt"
```

---

## Notification System

### Notification Types
```yaml
notifications:
  lead_alert:
    title: "Hot Lead Alert 🔥"
    body: "{contact_name} scored {score}/100"
    channels: [push, sms, in_app]
    priority: high
    
  escalation:
    title: "Escalation Required"
    body: "Conversation with {contact_name} needs attention"
    channels: [push, email, in_app]
    priority: urgent
    
  draft_pending:
    title: "Response Ready for Review"
    body: "{count} drafts awaiting approval"
    channels: [push, in_app]
    priority: medium
    
  daily_summary:
    title: "Daily Summary"
    body: "{calls} calls, {leads} leads, {conversion}% conversion"
    channels: [email]
    priority: low
    schedule: "08:00 local"
    
  usage_warning:
    title: "Usage Alert"
    body: "You've used {percent}% of your monthly quota"
    channels: [email, in_app]
    priority: medium
    thresholds: [75, 90, 100]
    
  system_alert:
    title: "System Notification"
    body: "{message}"
    channels: [in_app]
    priority: varies
```

### Channel Configuration
```yaml
channels:
  in_app:
    implementation: websocket_realtime
    storage: mongodb_notifications
    badge_count: true
    
  push:
    implementation: firebase_cloud_messaging
    platforms: [ios, android, web]
    sound: true
    
  email:
    provider: sendgrid
    templates: true
    unsubscribe: true
    
  sms:
    provider: twilio
    opt_in_required: true
    cost_per_message: $0.0075
```

### User Preferences
```yaml
notification_preferences:
  per_type:
    lead_alert: [push, in_app]
    escalation: [push, email, sms, in_app]
    draft_pending: [in_app]
    daily_summary: [email]
    usage_warning: [email]
    
  quiet_hours:
    enabled: true
    start: "22:00"
    end: "07:00"
    exceptions: [escalation]  # These still come through
    
  digest_mode:
    enabled: false
    frequency: hourly
```

---

## Analytics & Reporting

### Real-Time Dashboard Metrics
```yaml
metrics:
  - conversations_today
  - calls_answered_vs_missed
  - average_response_time
  - lead_conversion_rate
  - agent_activity_status
  - pending_approvals_count
```

### Standard Reports
```yaml
reports:
  conversation_summary:
    frequency: daily, weekly, monthly
    metrics:
      - total_conversations
      - by_channel_breakdown
      - by_status_breakdown
      - average_handle_time
      
  lead_performance:
    frequency: weekly, monthly
    metrics:
      - leads_generated
      - lead_score_distribution
      - conversion_rate
      - source_attribution
      
  agent_performance:
    frequency: weekly, monthly
    metrics:
      - calls_handled
      - average_call_duration
      - escalation_rate
      - customer_satisfaction
      
  response_time:
    frequency: daily, weekly
    metrics:
      - first_response_time
      - average_response_time
      - response_time_by_channel
      - sla_compliance
```

### Custom Report Builder
```yaml
builder_options:
  metrics: [all_available_metrics]
  dimensions: [date, channel, agent, tag, lead_score_range]
  filters: [date_range, channel, status, assigned_user]
  visualization: [table, line_chart, bar_chart, pie_chart]
  
export_formats: [csv, xlsx, pdf]
scheduling: [once, daily, weekly, monthly]
delivery: [email, download]
```

### A/B Testing Framework
```yaml
experiments:
  - name: "Greeting Test"
    agent: receptionist
    variable: greeting_message
    variants:
      - control: "Hi, thanks for calling {business}. How can I help?"
      - variant_a: "Good {time_of_day}! You've reached {business}. What can I do for you today?"
    metrics: [call_duration, lead_score, conversion]
    sample_size: 100_calls_per_variant
    
  - name: "Response Mode Test"
    agent: personal_assistant
    variable: response_mode
    variants:
      - control: suggestive
      - variant_a: autopilot
    metrics: [response_time, customer_satisfaction, escalation_rate]
```

---

## Billing & Usage Metering

### Metering Implementation
```python
class UsageMeter:
    """
    Track usage across all metered dimensions.
    Called from middleware on every API request.
    """
    
    async def record_usage(
        self,
        tenant_id: str,
        usage_type: str,
        quantity: float,
        metadata: dict = None
    ):
        """
        Usage types:
        - conversation: 1 per conversation
        - voice_minute: minutes of voice AI
        - token: LLM tokens used
        - api_call: external API call
        """
        await self.db.usage_records.update_one(
            {
                "tenant_id": tenant_id,
                "period_start": self.current_period_start()
            },
            {
                "$inc": {
                    f"{usage_type}.total": quantity,
                    f"{usage_type}.by_type.{metadata.get('subtype', 'default')}": quantity
                }
            },
            upsert=True
        )
        
        # Check thresholds and alert
        await self.check_thresholds(tenant_id, usage_type)
```

### Billing Integration (Stripe)
```yaml
stripe_integration:
  subscription_management:
    - Create subscription on signup
    - Handle plan upgrades/downgrades
    - Prorate mid-cycle changes
    
  usage_billing:
    - Report usage at end of billing cycle
    - Calculate overages
    - Generate itemized invoice
    
  webhook_events:
    - customer.subscription.created
    - customer.subscription.updated
    - customer.subscription.deleted
    - invoice.payment_succeeded
    - invoice.payment_failed
    
  dunning:
    - Retry failed payments (3 attempts)
    - Send payment failure notifications
    - Grace period before suspension
```

### Usage Dashboard (Customer-Facing)
```yaml
display:
  current_period:
    - Conversations: {used} / {limit}
    - Voice Minutes: {used} / {limit}
    - Tokens: {used} / {limit}
    
  progress_bars: true
  
  projected_usage:
    - Based on current rate
    - Warning if exceeding limit
    
  historical:
    - Last 6 months trend
    - Month-over-month comparison
```

---

## Deployment Architecture

### Infrastructure
```yaml
frontend:
  hosting: netlify
  cdn: netlify_edge
  domains:
    - app.agentforce.ai (primary)
    - *.agentforce.ai (white-label subdomains)
    - Custom domains (CNAME)
    
backend:
  compute: netlify_functions (serverless)
  alternatives:
    - AWS Lambda (if scale requires)
    - Render.com (for persistent services)
    
database:
  primary: mongodb_atlas
  region: us-east-1 (primary), eu-west-1 (secondary)
  tier: M10 (start), M30+ (scale)
  
cache:
  provider: upstash_redis
  purpose: session, rate_limiting, semantic_cache
  
file_storage:
  provider: cloudflare_r2
  purpose: recordings, documents, exports
  
monitoring:
  apm: sentry
  logging: betterstack
  uptime: betterstack
```

### CI/CD Pipeline
```yaml
pipeline:
  trigger: push_to_main
  
  steps:
    - lint_and_format
    - run_tests
    - build_frontend
    - build_backend
    - deploy_to_staging
    - run_e2e_tests
    - deploy_to_production (manual approval)
    
  environments:
    development:
      url: dev.agentforce.ai
      auto_deploy: true
      
    staging:
      url: staging.agentforce.ai
      auto_deploy: true
      
    production:
      url: app.agentforce.ai
      auto_deploy: false
      requires_approval: true
```

### Mobile Deployment
```yaml
capacitor:
  platforms: [ios, android]
  
  ios:
    bundle_id: ai.agentforce.app
    app_store_connect: true
    testflight: true
    
  android:
    package_name: ai.agentforce.app
    play_store: true
    
  plugins:
    - @capacitor/push-notifications
    - @capacitor-community/speech-recognition
    - @capacitor/camera
    - @capacitor/filesystem
    
  build_process:
    1. Export PWA from Emergent
    2. Add Capacitor to project
    3. Configure native plugins
    4. Build iOS/Android
    5. Submit to app stores
```

---

## Development Phases

### Phase 1: Foundation (Weeks 1-4)
```yaml
week_1_2:
  focus: "Project Setup & Authentication"
  deliverables:
    - [ ] Emergent project initialization
    - [ ] GitHub repository setup
    - [ ] MongoDB Atlas configuration
    - [ ] Authentication system (Email + Google OAuth)
    - [ ] User management (CRUD)
    - [ ] Role-based access control
    - [ ] Basic navigation shell
    
week_3_4:
  focus: "GHL Integration & Data Layer"
  deliverables:
    - [ ] GHL OAuth flow
    - [ ] GHL API wrapper service
    - [ ] Webhook receiver setup
    - [ ] Tenant/account data model
    - [ ] Database schema implementation
    - [ ] Audit logging infrastructure
```

### Phase 2: AI Receptionist (Weeks 5-8)
```yaml
week_5_6:
  focus: "Voice AI & Conversation Core"
  deliverables:
    - [ ] GHL Voice AI integration
    - [ ] Agent configuration UI
    - [ ] System prompt editor
    - [ ] Conversation inbox (basic)
    - [ ] Real-time webhook processing
    - [ ] Call log display
    
week_7_8:
  focus: "Intelligence Layer"
  deliverables:
    - [ ] Knowledge base system
    - [ ] KB import (manual, website crawl)
    - [ ] Lead scoring engine
    - [ ] Escalation rules engine
    - [ ] Autopilot vs Suggestive modes
    - [ ] Human approval workflow
    - [ ] Test call simulator
```

### Phase 3: Personal Assistant & Notifications (Weeks 9-12)
```yaml
week_9_10:
  focus: "Personal Assistant Agent"
  deliverables:
    - [ ] Phone line monitoring
    - [ ] Voicemail transcription display
    - [ ] Missed call alerts
    - [ ] SMS draft responses
    - [ ] Response approval flow
    - [ ] Quick automation templates
    
week_11_12:
  focus: "Notification System & Team"
  deliverables:
    - [ ] Notification service
    - [ ] Push notification setup (Firebase)
    - [ ] Email notifications (SendGrid)
    - [ ] SMS alerts (Twilio)
    - [ ] User notification preferences
    - [ ] Team collaboration features
    - [ ] Activity feed
```

### Phase 4: Finance Agent & Analytics (Weeks 13-16)
```yaml
week_13_14:
  focus: "Finance Agent Core"
  deliverables:
    - [ ] Analytics data aggregation
    - [ ] Webhook-based metrics collection
    - [ ] Chat interface for queries
    - [ ] Natural language to data queries
    - [ ] Dashboard widgets
    - [ ] KPI cards
    
week_15_16:
  focus: "Reporting & Insights"
  deliverables:
    - [ ] Standard reports
    - [ ] Custom report builder
    - [ ] Scheduled reports
    - [ ] Export functionality (CSV, PDF)
    - [ ] Trend analysis
    - [ ] Comparative analytics
```

### Phase 5: Social Media Manager (Weeks 17-20)
```yaml
week_17_18:
  focus: "Social Media Core"
  deliverables:
    - [ ] GHL Social Planner integration
    - [ ] Post scheduling interface
    - [ ] Content calendar view
    - [ ] Multi-platform posting
    - [ ] Post performance tracking
    
week_19_20:
  focus: "Intelligence & Trends"
  deliverables:
    - [ ] Trend monitoring (Google Trends)
    - [ ] Apify integration
    - [ ] Content recommendation engine
    - [ ] Seasonal/behavioral alerts
    - [ ] Competitor tracking (basic)
    - [ ] AI content suggestions
```

### Phase 6: Mobile & Polish (Weeks 21-24)
```yaml
week_21_22:
  focus: "Mobile Application"
  deliverables:
    - [ ] Capacitor integration
    - [ ] Native speech recognition
    - [ ] Push notification handling
    - [ ] Mobile-optimized UI
    - [ ] Offline mode (basic)
    - [ ] App store preparation
    
week_23_24:
  focus: "Multi-Tenant & Launch Prep"
  deliverables:
    - [ ] White-label customization
    - [ ] Agency admin panel
    - [ ] Sub-account management
    - [ ] Billing integration (Stripe)
    - [ ] Usage metering
    - [ ] Documentation
    - [ ] Beta testing with thinkBOLD clients
```

### Phase 7: Optimization & Scale (Weeks 25-28)
```yaml
week_25_26:
  focus: "Performance & Cost Optimization"
  deliverables:
    - [ ] Semantic caching implementation
    - [ ] Model routing logic
    - [ ] Token usage optimization
    - [ ] Database query optimization
    - [ ] Response time improvements
    
week_27_28:
  focus: "Enterprise Features"
  deliverables:
    - [ ] SSO/SAML support
    - [ ] Advanced audit logging
    - [ ] Custom API access
    - [ ] Webhook system for customers
    - [ ] Advanced security features
    - [ ] SOC 2 preparation documentation
```

---

## Success Metrics

### Launch Criteria (MVP)
```yaml
functional:
  - [ ] AI Receptionist handles 90% of calls without escalation
  - [ ] Lead scoring accuracy > 80%
  - [ ] Response drafts approved > 70% without modification
  - [ ] Voice recognition accuracy > 95%
  
performance:
  - [ ] API response time < 500ms (p95)
  - [ ] Voice latency < 1 second
  - [ ] 99.5% uptime
  
business:
  - [ ] 10 beta users active
  - [ ] < 5% churn in first month
  - [ ] NPS > 30
```

### Growth Targets
```yaml
month_3:
  - 30 paying customers
  - $5,000 MRR
  - 2 agency resellers
  
month_6:
  - 100 paying customers
  - $20,000 MRR
  - 5 agency resellers
  
month_12:
  - 300 paying customers
  - $75,000 MRR
  - 15 agency resellers
  - App store ratings > 4.5
```

---

## Appendix

### A. Glossary
| Term | Definition |
|------|------------|
| GHL | GoHighLevel - CRM platform |
| Tenant | Top-level account (agency or business) |
| Sub-account | Account under an agency tenant |
| Agent | AI agent (Receptionist, Finance, etc.) |
| BYOK | Bring Your Own Keys |
| KB | Knowledge Base |

### B. Reference Links
- [GHL API Documentation](https://highlevel.stoplight.io/)
- [GHL Voice AI Guide](https://help.gohighlevel.com/support/solutions/articles/155000003911-ai-voice-agents-overview)
- [Emergent Documentation](https://help.emergent.sh/)
- [Capacitor Documentation](https://capacitorjs.com/docs)

### C. Contact
- Technical Lead: [Your Name]
- Project Owner: Corey (thinkBOLD)
- Repository: [GitHub URL]

---

*Last Updated: December 2024*
*Version: 1.0*
