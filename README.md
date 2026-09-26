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


