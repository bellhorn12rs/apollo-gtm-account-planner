# Executive 1-Pager: Account Expansion & Buying Committee Agent Skill
**Author:** Eric Paul  
**Target Role:** Senior Business Systems Analyst, GTM (Apollo.io)  

---

### 1. The Problem
Sales reps waste 30 to 45 minutes per target account manually toggling between Salesforce, LinkedIn, and Apollo to figure out who to contact. This manual friction leads to single-threading (contacting only one person) and rep collision (outreach to contacts already active in another campaign). This skill automates account mapping in under 10 seconds while enforcing CRM deduplication.

### 2. Key Assumptions & Data Grounding Transparency
* **Firmographics (Live API Grounded):** Organization metadata (employee count, industry, HQ location) is queried live from Apollo's official REST API (`/v1/organizations/enrich`).
* **Contact Data (Free-Tier API Restriction & Fallback):** Due to API permission scope restrictions (`403 Forbidden` on free-tier bulk people search), contacts are supplied via a domain-specific structured fallback database (`DOMAIN_MOCK_DATABASE`). In a production environment with an upgraded API key, the script hits `/v1/mixed_people/search` directly to pull live employees.
* **CRM Source of Truth:** A local CRM file (`sfdc_existing_contacts.json`) simulates Salesforce state to validate exact-match email deduplication logic before staging write-backs.

### 3. Design Decisions & Tradeoffs
* **Why a SKILL (`SKILL.md`) + Script (`apollo_enrichment.py`):** 
  * Python executes deterministic data processing (API fetching, exact-string email matching, and structural JSON formatting).
  * The Agent Skill (`SKILL.md`) provides the strategic reasoning layer—reading the JSON payload to perform dynamic MEDDPICC gap analysis, assess single-threading risks, and draft rep-facing next-best-action briefs.
* **Deterministic Deduplication vs. Raw LLM Guesswork:** Rather than letting an LLM guess if a record exists in Salesforce (which risks hallucinations and duplicate outreach), email deduplication is enforced deterministically in Python prior to LLM evaluation.
* **Staged Payloads vs. Direct CRM Writes:** Direct automated write-backs into production Salesforce were replaced with a staged JSON file (`crm_writeback_staging.json`) to enforce human-in-the-loop verification before CRM ingestion.

### 4. Production Hardening Roadmap
To scale this proof-of-concept for enterprise production:
1. **OAuth 2.0 & Webhooks:** Transition from static API keys to OAuth 2.0 with event-driven webhooks triggered directly on Salesforce Account creation.
2. **Asynchronous Batch Processing:** Implement Celery/Redis queueing for asynchronous bulk processing across large target account lists with rate-limit retries.
3. **Bi-Directional CRM Ingestion:** Connect `crm_writeback_staging.json` to an integration tool (e.g., Workato, n8n, Salesforce REST API) to populate Opportunity Contact Roles (OCR) and trigger Apollo sequence enrollments automatically.