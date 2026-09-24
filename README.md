# 🎯 Apollo GTM Account Expansion Skill

An AI-native Agent Skill built for GTM Systems teams. It accepts a target company domain, queries live organization data via the official **Apollo.io API**, performs deduplication against existing Salesforce records, maps the **Buying Committee**, and generates an actionable MEDDPICC next-best-action brief.

---

## 🏗️ Architecture & Data Flow

```text
[User / Rep Input] ──> python apollo_enrichment.py --domain stripe.com
                                   │
                                   ▼
                   [Apollo.io Organization API]
                                   │
                                   ▼
                 [CRM Deduplication vs SFDC Records]
                                   │
                                   ▼
                     [output/account_data.json]
                                   │
                                   ▼
                     [SKILL.md AI Agent Prompt]
                                   │
                                   ▼
             [Structured Brief + crm_writeback_staging.json]

             
             
             🚀 Quick Start Setup
1. Clone the Repository
Bash
git clone [https://github.com/YOUR_GITHUB_USERNAME/apollo-gtm-account-planner.git](https://github.com/YOUR_GITHUB_USERNAME/apollo-gtm-account-planner.git)
cd apollo-gtm-account-planner
2. Export Free Apollo API Key
Bash
export APOLLO_API_KEY="your_apollo_api_key_here"
3. Run Live API Enrichment & CRM Deduplication
Bash
python3 skills/account-expansion-planner/scripts/apollo_enrichment.py --domain stripe.com
4. Run the Agent Skill
Open output/account_data.json alongside skills/account-expansion-planner/SKILL.md in Cursor, Claude Code, or VS Code AI Chat to generate your executive brief.

📁 Repository Structure
Plaintext
apollo-gtm-account-planner/
├── README.md                          <-- Project overview & setup instructions
├── writeup_and_design.md              <-- Executive 1-Pager covering Discussion Questions
├── .gitignore                         <-- Prevents committing API keys & output
├── data/
│   └── sfdc_existing_contacts.json   <-- Mock CRM database for deduplication testing
├── output/                            <-- Auto-generated staging payloads
└── skills/
    └── account-expansion-planner/
        ├── SKILL.md                   <-- Agent prompt & execution instructions
        ├── references/
        │   └── buying_committee_schema.json <-- ICF rules & persona definitions
        └── scripts/
            └── apollo_enrichment.py   <-- Python script for Apollo API & CRM check

5. Save the file (`Cmd + S`).

---

### Step 2: Create `writeup_and_design.md`

1. Click on the **`apollo-gtm-account-planner`** folder at the top left to highlight it.
2. Click the **New File** icon (`+` page icon).
3. Name the file `writeup_and_design.md` and press **Enter**.
4. Paste this executive 1-pager into the file:

```markdown
# Executive 1-Pager: Account Expansion & Buying Committee Agent Skill
**Author:** Eric Paul  
**Target Role:** Senior Business Systems Analyst, GTM (Apollo.io)  

---

### 1. The Problem
Sales reps waste 30 to 45 minutes per target account manually toggling between Salesforce, LinkedIn, and Apollo to figure out who to contact. This manual friction leads to single-threading (contacting only one person) and rep collision (outreach to contacts already active in another campaign). This skill automates account mapping in under 10 seconds while enforcing CRM deduplication.

### 2. Key Assumptions
* **GTM Strategy:** Winning Enterprise/Mid-Market deals requires multi-threading across an Identified Contact Framework (Economic Buyer, Champion, Technical Evaluator).
* **Data Sources:** Live account firmographics are pulled directly from Apollo's official REST API (`/v1/organizations/enrich`).
* **CRM Source of Truth:** A local CRM file (`sfdc_existing_contacts.json`) represents Salesforce state to validate deduplication logic before staging write-backs.

### 3. Design Decisions
* **Why an Agent Skill vs. Rigid Script:** A pure script can pull data, but an Agent Skill (`SKILL.md` + Python) allows the LLM to dynamically interpret messy job titles, perform MEDDPICC gap reasoning, and tailor multi-threading angles based on company size.
* **Separation of Concerns:** Python handles API HTTP calls and strict email matching logic; the LLM handles persona classification and strategic next-best-actions.
* **What Was Deliberately Omitted:** Direct automated write-backs into production Salesforce were replaced with a staged JSON file (`crm_writeback_staging.json`) to give reps human-in-the-loop validation before CRM ingestion.

### 4. Where AI Helped vs. Where It Was Overridden
* **Where AI Helped:** Accelerated Python boilerplate generation for Apollo REST API requests and structured output parsing in VS Code/Cursor.
* **Where AI Was Overridden:** AI LLMs naturally hallucinate job seniority and duplicate existing CRM records. I overrode raw LLM behavior by enforcing deterministic, hardcoded email-matching logic in Python prior to passing the payload to the LLM.

### 5. Production Hardening Roadmap
To scale this proof-of-concept for enterprise production:
1. **OAuth & Webhooks:** Transition from static API keys to OAuth 2.0 with event-driven webhooks triggered directly on Salesforce Account creation.
2. **Asynchronous Batching:** Implement Celery/Redis queueing for asynchronous processing across large target account lists.
3. **Bi-Directional CRM Sync:** Replace JSON file staging with direct Salesforce REST API calls to populate Opportunity Contact Roles (OCR) and trigger Apollo sequence enrollments automatically.# apollo-gtm-account-planner
