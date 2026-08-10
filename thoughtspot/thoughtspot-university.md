# ThoughtSpot Spotter — Setup & Coaching Reference

> Personal knowledge base for getting Spotter (ThoughtSpot's AI analyst) accurate, coached, and production-ready. All source links point back to the ThoughtSpot https://training.thoughtspot.com/ and docs site.

## Table of Contents
- [Quick Reference](#quick-reference)
- [1. Six Guidelines for Setting Up Models](#1-six-guidelines-for-setting-up-models)
- [2. Column Naming Problems and Solutions](#2-column-naming-problems-and-solutions)
- [3. How to Frame Questions to Spotter](#3-how-to-frame-questions-to-spotter)
- [4. Spotter Memory](#4-spotter-memory)
- [5. Phases of Making Spotter Ready](#5-phases-of-making-spotter-ready)
- [6. Coaching Methods](#6-coaching-methods)
- [7. Data Model Instructions vs Spotter Instructions](#7-data-model-instructions-vs-spotter-instructions)
- [8. Recommended Coaching Sequence](#8-recommended-coaching-sequence)
- [9. Troubleshooting Playbook](#9-troubleshooting-playbook)
- [10. Spotter Architecture and Query Lifecycle](#10-spotter-architecture-and-query-lifecycle)
- [11. Monitoring and Engagement Dashboards](#11-monitoring-and-engagement-dashboards)
- [12. Further Reading](#12-further-reading)

---

## Quick Reference

*Source: [TML docs](https://docs.thoughtspot.com/cloud/26.8.0.cl/tml)*

- **Fix the model before you coach anything.** Column names, synonyms, AI Context, formulas > memory > instructions > reference questions > business terms — in that priority order.
- **Ask Spotter what confuses it.** `"Show me the data model columns you're confused about and what specifically is causing the confusion."`
- **Two memory types:** *Rules* (business definitions/constraints) and *Recipes* (trusted query patterns). Recipes only come from Liveboards; conversation learning only produces Rules.
- **Coaching only fires on explicit intent** — phrases like *"Remember this,"* *"Always use this filter,"* *"Correct, save this."*
- **Data Model Instructions** = stable, permanent rules baked into the model (max reliability, hardest to override).
- **Reference Questions + NL Context** = lock in one specific question's exact logic.
- **Business Terms** = last resort, for simple synonym/value mappings only.
- **Spotter Instructions** (org-level, behavioral) are different from **Data Model Instructions** (model-level, semantic) — see the comparison table in [§7](#7-data-model-instructions-vs-spotter-instructions).

**TML vs. YAML** — when to use which when editing ThoughtSpot metadata:

| Format | Use for |
|---|---|
| **TML** | Basic connection migration, re-creation, or editing properties in ThoughtSpot |
| **YAML** | Detailed remapping, bulk changes, or metadata synchronization |

---

## 1. Six Guidelines for Setting Up Models

*Source: [TML docs](https://docs.thoughtspot.com/cloud/26.8.0.cl/tml) · referenced further in the "Optimizing Data Models for Spotter" lesson on the [AI Learning Path](https://training.thoughtspot.com/path/ai-learning-path)*

These guidelines make Spotter more accurate and cut down on future coaching/feedback effort. Run through all of them on a model *before* rolling it out.

1. **Use unique and readable column names.**
2. **Write detailed column descriptions.**
3. **Add AI Context to columns** — write it as a *command*, not a description. Keep it under 400 characters. Focus on columns that are ambiguous, frequently used, or have non-standard values.

   | Use Case | AI Context (example) |
   |---|---|
   | Disambiguation between similar columns | "Prefer this column for all revenue queries. This is the primary date for when a sale occurred." |
   | Boolean / indicator columns | "true = valid transaction, false = invalid transaction" |
   | Internal codes or shortforms | "Contains medicine shortforms. 'MP' = Metoprolol" |
   | Deprecated columns | "Do not use this column. Replaced by Order Date v2." |

4. **Add synonyms for column names.**
5. **Avoid multiple date columns** — keywords like "growth" often rely on date columns; multiple date columns can make Spotter miscalculate growth. If you need different aggregation granularity, set it per column via the **Default Date Bucket** property.
6. **Index relevant columns.**
7. **Define formulas** at the model level — including pre-aggregated formulas — whenever a metric has a fixed definition.
   - Example: `Net Revenue = Quantity Purchased * Item Price`, defined once at the model level rather than left for Spotter to infer.
   - **Why:**
     1. **Consistency** — every user, every query, same answer.
     2. **Lower latency** — no need to reconstruct logic per request.
     3. **Fewer errors** — removes the risk of Spotter guessing wrong from ambiguous phrasing.
     4. **Single source of truth** — update the definition in one place.
   - Use the self-diagnose prompt (see [Quick Reference](#quick-reference) above) to find which columns are causing confusion before defining more formulas.

### AI Context vs. Natural Language Context

Two different coaching inputs that are easy to confuse — this comparison is worth keeping close at hand:

| Context Type | Scope | Persistence | Usage |
|---|---|---|---|
| **Natural Language context** | One-off coaching | Not permanent | Improves Spotter's reasoning for specific reference questions |
| **AI context** | Permanent model metadata | Yes | Guides Spotter for all queries on a model |

---

## 2. Column Naming Problems and Solutions

*Source: [Snowflake Semantic Views Integration](https://training.thoughtspot.com/path/data-expert-cloud/models-course/2507618)*

| # | Problem | Solution |
|---|---|---|
| 1 | Two similarly-named columns (e.g. **Product Name** vs **Product Type**) — Spotter may pick the wrong one for "show customers by Product" | Give each a distinct name, e.g. **Product** and **Item Type** |
| 2 | Abbreviations/acronyms (e.g. `SLS_ID`) — Spotter can't discern content | Use full names, e.g. **Sales ID** |
| 3 | Column names starting with a number | Avoid leading numbers (a number mid-name is fine) |
| 4 | A column like **Profit %** — readable, but often misinterpreted | Name it **Profit** and let Spotter perform the % calculation on top |
| 5 | Column name doesn't match its data type (e.g. **Weeks** stored as attribute values "Week 1", "Week 2") — hurts date-query accuracy | Rename to **Week Number** if you want to keep the data as-is |

---

## 3. How to Frame Questions to Spotter

*Source: [Using Spotter, the AI Analyst](https://training.thoughtspot.com/path/ai-learning-path/using-spotter-the-ai-analyst) · [lesson video](https://training.thoughtspot.com/path/ai-learning-path/using-spotter-the-ai-analyst/2252271/scorm/3tbx688ys8ug8)*

Break the question into parts for faster, more accurate answers:

- **Computation** — what should Spotter calculate? Phrase it so any new person would understand.
- **Groupings** — what breakdowns do you want in the results?
- **Filters** — what should be excluded/included?

Spotter is conversational — you can build up a question incrementally rather than trying to nail it in one shot.

---

## 4. Spotter Memory

*Source: [Spotter Administration](https://training.thoughtspot.com/path/ai-learning-path/spotter-administration/2499360) · [lesson video](https://training.thoughtspot.com/path/ai-learning-path/spotter-administration/2381995/scorm/1vhs3tcf6riyd) · [Personal Memory](https://training.thoughtspot.com/path/ai-learning-path/spotter-administration/2534691) · [Create Memory via External Knowledge Connectors](https://training.thoughtspot.com/path/ai-learning-path/spotter-administration/2523955)*

### Two types

| Type | What it stores | Source |
|---|---|---|
| **Rules** | Business definitions, constraints, conventions ("Revenue always excludes returns", "Use Event Name for all error analysis") | Liveboard learning + conversation learning |
| **Recipes** | Trusted query execution patterns — filters, columns, calculations for a correct answer (similar to Reference Questions) | Liveboards only |

### Learning from a Liveboard

1. Navigate to **Data Workspace**
2. Select **Memory Sources**
3. Click **Learn from Liveboards**
4. Select up to 3 Liveboards
5. Click **Generate Memory**

### Triggering coaching in conversation

Learning only happens on **explicit coaching intent**, e.g.:
- "Remember this definition"
- "Always use this filter"
- "No, this metric should be calculated differently"
- "Correct. Save this"

Useful prompt to surface hidden assumptions: `"What are your assumptions about [topic]?"`

Update memory in conversation whenever a metric definition, filter, or business rule changes.

### Limitations

- Works only with **Spotter 3**
- Does **not** auto-sync with data model changes
- Liveboard edits do **not** auto-refresh memory
- Recipes come only from Liveboards
- Conversation learning stores **Rules only** (no Recipes), and only at the data-model level
- No cross-data-model learning

---

## 5. Phases of Making Spotter Ready

*Source: [Spotter Administration — lesson video](https://training.thoughtspot.com/path/ai-learning-path/spotter-administration/2381995/scorm/1vhs3tcf6riyd)*

This is the *process*/rollout view — who does what, in what order. It leans on the tools already defined in [§1](#1-six-guidelines-for-setting-up-models) (Guidelines), [§4](#4-spotter-memory) (Memory), and [§6](#6-coaching-methods) (Coaching Methods) rather than re-describing them.

### Phase 1 — Use Case Discovery
Define what Spotter needs to answer *before* touching the data model.
- Identify target users; customize per team rather than for everyone at once.
- Collect **real** questions from users — don't assume. Group by topic to find where coaching effort should go.
- Validate model coverage against real questions; remove unused columns.

### Phase 2 — Optimize the Data Model
The highest-leverage step, and it comes before any coaching. Apply the **Six Guidelines** ([§1](#1-six-guidelines-for-setting-up-models)) — naming, synonyms, AI Context, formulas — then run the self-diagnose prompt and the **Spotter Optimization** tool to catch indexing, date-format, and type mismatches.

### Phase 3 — Add a Trusted Liveboard as Memory
Follow the memory-generation steps in [§4](#4-spotter-memory). Generation takes **10–20 minutes** depending on Liveboard complexity; afterward, test with questions that mirror the Liveboard's charts and correct anything wrong in conversation.

### Phase 4 — Manage Memory Access
Validate with a small trusted group before wider rollout.
- Pick 2–5 experienced users (model owners, senior analysts, stakeholders).
- Give them editing/coaching access, or have them document findings if not.
- Have them define expected outputs for key business questions and log incorrect answers, missing filters, ambiguous terms.
- Refine via AI Context / Liveboards / training inputs; expand rollout only once power users confirm accuracy.

### Phase 5 — Train in Conversation, Then Escalate
Correct wrong answers in conversation using the trigger phrases from [§4](#4-spotter-memory) — Spotter saves them as memory. If conversation learning can't resolve something, escalate up the coaching hierarchy: Data Model Instructions → Reference Questions + NL Context → Business Terms (definitions in [§6](#6-coaching-methods), full priority order in [§8](#8-recommended-coaching-sequence)).

---

## 6. Coaching Methods

*Source: [Spotter Administration — lesson video](https://training.thoughtspot.com/path/ai-learning-path/spotter-administration/2381995/scorm/1vhs3tcf6riyd), "Traditional Coaching Methods" lesson*

### Data Model Instructions
Permanent, global directives passed with *every* query against that model.
- Requires data model editing access to manage.
- Foundational: do's/don'ts, core concepts, default behaviors, data nuances.
- **Example:** "When calculating revenue, always exclude transactions where Account_Type = 'Internal Test'."

### Reference Questions
Sample questions paired with their correct answer — ensures the same question always gets the same, verified answer.
- **Example:** "Who are our high-value customers?" — without coaching, Spotter might use highest purchase history. With a reference question defining "lifetime spend > $1000 and a purchase in the last 90 days," it locks in your org's actual definition.

### Natural Language (NL) Context
Explains the *why* behind a reference question — the logic/intent behind the answer, reducing ambiguity and helping Spotter generalize to similar questions. See the [AI Context vs. NL Context comparison](#ai-context-vs-natural-language-context) in §1 for how this differs from AI Context on a column.
- Addable from Data Workspace or directly in a Spotter conversation.
- **Example:** "A High Value Customer is someone with a lifetime spend of over $1000, with at least one purchase in the last 90 days."

### Business Terms
Last-resort, most specific layer — bridges everyday company terminology and precise data logic that a generic LLM wouldn't grasp.
- Synonyms/acronyms for values, e.g. "N.Am" → country = "North America".
- Simple, universally-true formulas, e.g. `Adjusted Gross Profit = Gross Profit - Marketing Spend`.
- Managed via the **Business Terms** tab in Data Workspace, or **Add to coaching** from a Spotter conversation.

### Tool Reference Table

| | Data Model Optimization | Memory from Liveboard | Conversational Learning | Data Model Instructions | Reference Questions | Business Terms |
|---|---|---|---|---|---|---|
| **Use when** | Before any coaching — the foundation | Cold start; need broad coverage fast | Ongoing corrections, evolving definitions | A rule is stable and must never be overridden by memory | A specific query needs exact formula/logic locked in | Migrating simple value mappings across orgs/clusters |
| **Example** | Rename `txn_dt` → Transaction Date; define Net Revenue as a formula | Add the Revenue Dashboard — absorbs all revenue definitions at once | Spotter miscalculates ARR — correct it and ask it to remember | Always filter for production + paid clusters unless specified | "% Spotter adoption" needs a specific denominator — lock in with a reference answer | "N.Am." → country = 'North America' |
| **Use as** | Foundation | Primary | Primary | Override | Precision fix | Last resort |

### Access levels (Reference Questions & Business Terms)

| Level | Applies to | Who sets it |
|---|---|---|
| **Global** | All users querying that model/dataset — a verified, org-wide definition | Users with Model editing access / Admins (default) |
| **User** | Only the individual user's own queries | General/View-only users giving feedback (defaults here) — e.g. via **Add to coaching** on the homepage, or Reference Questions in the Data Workspace |

---

## 7. Data Model Instructions vs Spotter Instructions

*Source: [Spotter Administration — lesson video](https://training.thoughtspot.com/path/ai-learning-path/spotter-administration/2381995/scorm/1vhs3tcf6riyd)*

**Spotter Instructions** are a system-level prompt Spotter follows on *every* conversation across the org — sits alongside ThoughtSpot's built-in agent prompt.

Use them to:
- Give the agent a name/persona matching your brand or domain.
- Set default response style (tone, length, structure, comparison framing).
- Require disclaimers or AI safety statements.
- Declare topics the agent should decline or redirect.
- Enforce proactive behavior (e.g. "always flag pipeline coverage below 3x").
- Control which data models/tools the agent uses in Auto mode.

**Important points:**
- Requires the **Can manage Spotter** privilege (typically Org/Analytics admins, but grantable to other roles).
- Scope is **org-level** — one set of instructions per org (each Org has its own in multi-org instances).
- Plain natural language only, up to **5,000 characters**. No code, no schema.
- Cannot override ThoughtSpot's core safety guardrails or manipulate data; row-level security and data permissions still apply.

### Comparison

| | Data Model Instructions | Spotter Instructions |
|---|---|---|
| **Answers the question** | What does the data mean? | How should the agent behave? |
| **Configured by** | Data Model Owner / Data Engineer | Admin / Org Admin |
| **Where it lives** | On the Model, in Data Workspace | Spotter settings, at the Org level |
| **Scope** | Everyone querying that Model | Everyone in the Org, across all Models |
| **Example** | "Default time period is the last 30 days." | "Decline questions about HR data and redirect users to the People workspace." |

---

## 8. Recommended Coaching Sequence

*Source: [Spotter Administration — lesson video](https://training.thoughtspot.com/path/ai-learning-path/spotter-administration/2381995/scorm/1vhs3tcf6riyd)*

1. **Optimize the data model** — column names, synonyms, formulas, AI Context, run Spotter Optimization.
2. **Run Spotter self-diagnosis** — ask what it's confused about; fix those metadata gaps.
3. **Add a Liveboard as a memory source** — fast, broad coverage, no manual writing.
4. **Verify and correct in conversation** — test representative questions; correct and ask Spotter to remember.
5. **Set Spotter Instructions** — shapes org-wide behavior.
6. **Add Data Model Instructions** — only for stable, explicit overrides that must not evolve.
7. **Add Reference Questions + NL Context** — only for specific, complex queries with exact formula requirements.
8. **Add Business Terms** — last resort, simple universal mappings.

> Reference Questions, NL Context, and Business Terms apply only to Spotter and Spotter 2.

---

## 9. Troubleshooting Playbook

*Source: [Spotter docs](https://docs.thoughtspot.com/cloud/26.8.0.cl/spotter) · [video walkthrough](https://drive.google.com/file/d/15Y84VBtimcjG-OPL8DcUSpvlv9Dvzjxd/view)*

| Issue | Diagnose | Fix |
|---|---|---|
| Wrong column picked (e.g. Ship Date instead of Order Date) | Ask: *"Why did you use [column X] for this?"* | Review/clarify AI Context on the correct column; fix synonyms and indexing. Correct in conversation only if it persists after fixing the model. |
| Doesn't know business definitions (e.g. wrong "active customers" definition) | Ask: *"What do you understand by [term]?"* | Broad topic → add a relevant Liveboard to memory. Specific question → correct in conversation and ask Spotter to remember. |
| Formula wrong or calculation fails (e.g. ARR, % contribution) | Is it rigid (always the same definition) or flexible (adapts by context)? | Rigid → define once in the data model as a formula. Flexible → add a Reference Question with the correct pattern + NL Context explaining the logic. |
| Incorrect value selection (wrong status/region/category) | Ask: *"Why didn't you choose [column + value]?"* | Check column indexing (lets Spotter see actual values); review AI Context. Fine-tune in conversation if it persists. Use Business Terms only for cross-org value mapping. |
| Inconsistent results between runs | Ask: *"Why are you confused about [topic]? What context is conflicting or unclear?"* | Review data model semantics for conflicts; fix in metadata/coaching. Correct remaining issues in conversation. If the rule must be stable, add a Data Model Instruction. |

---

## 10. Spotter Architecture and Query Lifecycle

*Source: [Inside Spotter: ThoughtSpot's AI-powered NLQ engine](https://training.thoughtspot.com/inside-spotter-thoughtspots-ai-powered-nlq-engine/2279506) · [MCP tool reference (Spotter 3)](https://developers.thoughtspot.com/docs/mcp-tool-reference-spotter3) · [Integrating Spotter MCP Server in a custom app/chatbot](https://developers.thoughtspot.com/docs/custom-chatbot-integration-mcp)*

### High-level flow, end to end

| Step | What happens |
|---|---|
| User Asks a Question | Natural language query entered into Spotter |
| Data Source Selection | Auto mode or manual selection of the model/dataset |
| Semantic Search | Finds the best-matching data model |
| Business Context Loading | Loads coaching layers, instructions, and memory |
| Query Planning | LLM + semantic mappings build the query plan |
| NLS Query Construction | Iterates until a precise query is formed |
| ReAct Loop | Validates the result and self-corrects if needed |
| Answer Delivered | Final result returned to the user |
| Downstream Action | Optional: sent onward via Slack, Asana, or other connectors |

### Layer-by-layer breakdown

1. **User Input** — the natural-language question entered into Spotter (simple or complex, plain English).
2. **BARQ Layer** *(Business-Augmented Reasoning for Questions)* — adds business context from multiple enrichment sources so the query is interpreted using your org's business logic, not just technically. BARQ itself is made up of:

   | BARQ Component | Role |
   |---|---|
   | Semantic Model | The structured representation of the data model |
   | Data Blueprint | Layout/relationships of the underlying data |
   | UBR | Business rules resolution layer |
   | Knowledge Graph | Connects related business entities and concepts |
   | Spotter Coach | Feeds in coaching/memory signals |

3. **Prompt IQ** — builds the complete LLM prompt from BARQ metadata: schema details, sample values, business terms, few-shot examples, user corrections.
4. **LLM Integration** — sends the prompt to a supported LLM (e.g. GPT via Azure OpenAI, or Google Gemini), which returns structured output — typically search tokens or pseudo-SQL, not raw SQL.
5. **AI Trust Layer** — validates the LLM's response against actual schema, user permissions, and enterprise logic; fixes or discards incomplete/incorrect logic.
6. **Query Execution** — translates validated search tokens into real SQL run against the warehouse, with secure, optimized, permission-aware access.
7. **Dynamic Presentation** — renders final results as interactive charts, summaries, or KPIs.

### MCP terminology (for custom/agentic integrations)

| Term | Definition |
|---|---|
| **MCP** | An open interoperability standard that enables AI models and agents to securely connect to external tools |
| **MCP Host** | The AI application that runs the MCP client — e.g. Claude, Cursor, ChatGPT |
| **MCP Client** | The software component running inside the host that connects to the MCP Server |
| **MCP Server** | The service that implements the Model Context Protocol |

---

## 11. Monitoring and Engagement Dashboards

*Source: built-in "Spotter Conversations" Liveboard, referenced alongside [Personal Memory](https://training.thoughtspot.com/path/ai-learning-path/spotter-administration/2534691) and [External Knowledge Connectors](https://training.thoughtspot.com/path/ai-learning-path/spotter-administration/2523955) in Spotter Administration*

ThoughtSpot ships a **Spotter Conversations** Liveboard for tracking adoption, with three tabs:

| Tab | What it shows |
|---|---|
| **Overview** | Active Users, Conversations, Questions Asked, Conversations with Feedback (all weekly); Most Active Users; Users Providing Feedback; Feedback Response (up/down vote split); Conversations by Origin/Model/Liveboard; Avg Conversation Length; Conversation Length Distribution |
| **Homepage Conversations** | Every user interaction started from the homepage, as one row per event — user, model, conversation ID, timestamp, action type, query, response, and feedback. Downvoted interactions are highlighted. There's up to a 2-hour delay before an event appears. |
| **Liveboard Conversations** | Same structure as Homepage Conversations, scoped to conversations started from a Liveboard |

All tabs are filterable by date, org, model, user name, conversation origin, and Liveboard name.

---

## 12. Further Reading

- [TML docs](https://docs.thoughtspot.com/cloud/26.8.0.cl/tml)
- [Spotter docs](https://docs.thoughtspot.com/cloud/26.8.0.cl/spotter)
- [AI Learning Path (full course)](https://training.thoughtspot.com/path/ai-learning-path)
- [Snowflake Semantic Views Integration (training)](https://training.thoughtspot.com/path/data-expert-cloud/models-course/2507618)
- [Using Spotter, the AI Analyst (training)](https://training.thoughtspot.com/path/ai-learning-path/using-spotter-the-ai-analyst)
- [Spotter Administration (training)](https://training.thoughtspot.com/path/ai-learning-path/spotter-administration/2499360)
- [Inside Spotter: ThoughtSpot's AI-powered NLQ engine (training)](https://training.thoughtspot.com/inside-spotter-thoughtspots-ai-powered-nlq-engine/2279506)
- [MCP tool reference (Spotter 3)](https://developers.thoughtspot.com/docs/mcp-tool-reference-spotter3)
- [Integrating Spotter MCP Server in a custom app/chatbot](https://developers.thoughtspot.com/docs/custom-chatbot-integration-mcp)
- [Related read: "Every AI question should not feel like a..." (LinkedIn)](https://www.linkedin.com/posts/michelinenijmeh_every-ai-question-should-not-feel-like-a-share-7483170029392265216-rnYa/)
