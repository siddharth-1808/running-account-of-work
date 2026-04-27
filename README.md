# Running Account of Work
**Product Manager: Siddharth Chauhan**

---

## 8th Mar 2026 — ST-ERP Platform Release Notes

### 1. Batch & Expiry Governance

#### 1.1 Safer Batch Creation: Strong Expiry & Manufacturing Date Guardrails
**Jira:** STERP26-154 – Date Validation during Batch creation

**What changed**
- Added calendar-based date selection for batch creation; no more free-form "gibberish" dates.
- Enforced validation that blocks saving if expiry date is earlier than manufacturing date, or dates are otherwise invalid.

**Business impact**
- Prevents invalid expiry/manufacturing dates from entering batch master data.
- Strengthens the foundation for expiry-based reporting and compliance.

**Key people:** PM: Siddharth Chauhan · Dev: Manoj Jadhav

---

### 2. Inventory Integrity & Exposure

#### 2.1 Exclude Rejected Inventory from Sellable Stock
**Jira:** STERP26-110 – Inventory for Rejected Bins is getting synced to Shopify

**What changed**
- Inventory snapshot generation now excludes bins with status = "Rejected".
- Shopify sync uses this cleaned snapshot so QC-failed stock is never exposed as sellable.

**Business impact**
- Reduces cancellations and refunds from customers ordering stock that should not be sold.
- Improves reliability of inventory as seen on the storefront.

**Key people:** PM: Siddharth Chauhan · Dev: Ankit Singh

#### 2.2 Negative Inventory – Detection, Cleanup & Ledger Accuracy
**Jira:** STERP26-79 · STERP26-90

**What changed**
- Identified negative-stock batches in bulk and rectified them.
- Introduced backdated stock entries at the moment the negative balance began, ensuring the stock ledger is chronologically correct.

**Business impact**
- Cleans up legacy negative inventory issues.
- Makes stock history and audits more credible and easier to reconcile.

**Key people:** PM: Siddharth Chauhan · Devs: Ankit Singh, Akash Mahajan

#### 2.3 Promise Engine Sync – No More Negative Quantities Upstream
**Jira:** STERP26-127

**What changed**
- Fixed the ERP → Promise Engine sync so negative quantities are no longer pushed to the Promise Engine.

**Business impact**
- Prevents impossible "negative availability" in the promise layer.
- Improves the accuracy of delivery promises shown to customers.

**Key people:** PM: Siddharth Chauhan · Dev: Ankit Singh

#### 2.4 Inventory Reset on Warehouse Mapping Removal
**Jira:** STERP26-109

**What changed**
- Fixed how inventory is reset when warehouse mappings are removed.

**Business impact**
- Prevents orphan or ghost inventory when a warehouse is de-mapped.
- Keeps channel inventory in sync with configuration changes.

**Key people:** PM & Assignee: Siddharth Chauhan

#### 2.5 HSN Compliance & Data Quality
**Jira:** STERP26-108 · STERP26-113

**What changed**
- HSN code is now mandatory on SKU creation/update.
- HSN must be numeric and 6–8 digits long.

**Business impact**
- Drives GST/HSN compliance by design.
- Reduces risk of incorrect tax computation and reporting.

**Key people:** PM: Siddharth Chauhan · Dev: Ankit Singh

#### 2.6 Mandatory Purpose for Stock Movements (Auditability)
**Jira:** STERP26-118

**What changed**
- Introduced a mandatory "Purpose" dropdown in Stock Entry and Stock Reconciliation.
- "Others" requires mandatory free-text reason.
- Purpose is persisted and surfaced in reports.

**Business impact**
- Greatly improves traceability of why inventory moved.
- Strengthens audit readiness and supports RCA on variances.

**Key people:** PM: Siddharth Chauhan · Dev: Manoj Jadhav

---

### 3. Order / DN / AWB / RFP Reliability

#### 3.1 Reverse Logistics – Correct AWB for RVP
**Jira:** STERP26-151

**What changed**
- For reverse DNs (RVP), ER now receives the reverse AWB, not the forward one.

**Business impact**
- Enables correct tracking of reverse shipments in ER/Navis.
- Reduces reconciliation errors in reverse logistics.

**Key people:** PM: Siddharth Chauhan · Dev: Ankit Singh

#### 3.2 Cancellations Now Propagate Correctly to ER
**Jira:** STERP26-139

**What changed**
- Fixed the flow where DN cancellation in ST was not triggering cancellation in ER, especially at last-unit inventory.

**Business impact**
- Avoids inventory divergence between ST and ER.
- Prevents repeated RFPs and allocation failures on last-unit scenarios.

**Key people:** PM: Siddharth Chauhan · Dev: Ankit Singh

#### 3.3 Robust Handling of Multiple RFP Calls
**Jira:** STERP26-96

**What changed**
- When multiple RFP calls come in concurrently from ER, only the first job is processed; parallel jobs for the same DN are skipped.

**Business impact**
- Prevents duplicate DNs caused by concurrent RFPs.
- Reduces confusion and manual cleanup with 3PL.

**Key people:** PM: Siddharth Chauhan · Dev: Ankit Singh

#### 3.4 DN Deduplication During ER Sync
**Jira:** STERP26-106

**What changed**
- Added deduplication so the DN creation hook and the 24-hour retry job don't create duplicate DNs when they trigger around the same time.
- Introduced a 5-minute buffer before retrying old DNs.

**Business impact**
- Stops double-creation of DNs in ER.
- Cleaner DN lifecycle and less manual remediation.

**Key people:** PM: Siddharth Chauhan · Dev: Akash Mahajan

#### 3.5 Sales Invoice Retry – Fewer Stuck Invoices
**Jira:** STERP26-175

**What changed**
- Implemented a temporary retry mechanism for Sales Invoice posting that does not depend on a fresh RFP from ER.

**Business impact**
- Reduces DNs stuck due to transient invoice failures.
- Particularly valuable for upcoming high-volume sale periods.

**Key people:** PM: Siddharth Chauhan · Dev: Ankit Singh

#### 3.6 Non-Blocking RFP Timestamps
**Jira:** STERP26-176

**What changed**
- RFP API no longer fails when a timestamp field is "None" for non-critical internal fields.

**Business impact**
- Customer orders no longer get blocked due to missing non-essential timestamps.
- Reduces noise and unnecessary failures in the RFP pipeline.

**Key people:** PM: Siddharth Chauhan · Dev: Ankit Singh

#### 3.7 Recovery from ER Config Fallout
**Jira:** STERP26-99

**What changed**
- Identified and re-pushed 31 DNs that were cancelled because of an ER configuration issue starting 22 Jan, 9 PM.

**Business impact**
- Ensured affected customer orders were recovered and processed.
- Cleared a discrete backlog without lingering operational debt.

**Key people:** PM: Siddharth Chauhan · Dev: Akash Mahajan

---

### 4. Stock Inwarding & Reporting UX

#### 4.1 Batch–SKU Level Inwarding Validation
**Jira:** STERP26-92

**What changed**
- The inwarding API now returns explicit confirmation or error for every SKU–batch combination in the payload.
- No more silent skipping of lines.

**Business impact**
- Prevents hidden discrepancies between what was sent vs what was actually inwarded.
- Reduces reconciliation time when PRs appear mismatched.

**Key people:** PM: Siddharth Chauhan · Dev: Manoj Jadhav

#### 4.2 More Usable Stock Ledger Report
**Jira:** STERP26-77

**What changed**
- Reordered Stock Ledger columns so high-usage fields (Date, Item, Batch, In/Out Qty, Balance, Warehouse, Voucher #, etc.) appear first.

**Business impact**
- Reduces repeated manual re-ordering by users.
- Makes day-to-day operations and analysis faster and less error-prone.

---

### 5. Reporting, Observability & Error Surfacing

#### 5.1 End-to-End DN Tracking for Leadership
**Jira:** STERP26-115

**What changed**
- Collated a comprehensive set of IDs and timestamps (order creation, DN creation, RFPs, AWB, billing, delivery, RAD, etc.) across Shopify, ERP, ER and Clickpost for each DN.

**Business impact**
- Gives leadership full lifecycle visibility and allows accurate SLA tracking and bottleneck analysis.

**Key people:** PM: Siddharth Chauhan · Dev: Ankit Singh

#### 5.2 Error Surfacing Back to ER
**Jira:** STERP26-105

**What changed**
- Internal errors are now passed to ER via their Shipping Updates API using the supported error key.

**Business impact**
- Improves vendor (ER) visibility into failures.
- Reduces diagnostic back-and-forth and accelerates joint issue resolution.

**Key people:** PM: Siddharth Chauhan · Dev: Ankit Singh

#### 5.3 Internal Error Analysis & RCA
**Jira:** STERP26-130 · STERP26-112 · STERP26-101

**What changed**
- Deep dives into Promise Engine sync issues and other failures to identify systemic causes and inform fixes.

**Business impact**
- These analyses underpinned several of the reliability and integrity fixes listed above, shortening time-to-solution.

#### 5.4 Inventory Update Table – Better Internal Understanding
**Jira:** STERP26-16

**What changed**
- Documented how inventory updates work via a dedicated table for internal reference.

**Business impact**
- Shared understanding across teams, leading to cleaner designs and less mis-implementation in future work.

#### 5.5 3PL Bin Determination & Reference Visibility
**Jira:** STERP26-73 · STERP26-72 · STERP26-75

**What changed**
- Fixed bin determination logic used by 3PL.
- Ensured DN Reference Code and Order Ref Code from ER are visible in the right places (e.g., comments).

**Business impact**
- Smoother 3PL operations; less confusion on where to pick from.
- Faster validation and troubleshooting by ops teams.

#### 5.6 COD Line Fix
**Jira:** STERP26-159

**What changed**
- Corrected an issue with COD line handling.

**Business impact**
- More accurate COD representation for finance and reconciliation.

**Key people:** PM: Siddharth Chauhan · Dev: Manoj Jadhav

#### 5.7 Generic Retry Mechanism
**Jira:** STERP26-15

**What changed**
- Introduced a generic retry mechanism for failed requests (baseline reliability utility used by other features).

**Business impact**
- Fewer transient failures surface to users/ops.
- Provides a foundation for resilient integrations.

---

## 26th Mar 2026 — Automated Batch Expiry & Manufacturing Date Update Pipeline

As part of an ongoing effort to improve operational efficiency in supply chain data management, identified a critical bottleneck where expiry dates and manufacturing dates for thousands of product batches needed to be manually updated one-by-one via Postman — a process that was time-consuming, error-prone, and unscalable.

Leveraged Claude AI as a technical collaborator to design and build an end-to-end automated pipeline from scratch, without prior programming experience. The solution involved:

- Reverse-engineered the vendor API by analysing existing Postman requests to extract authentication mechanisms, endpoint structure, and JSON payload format
- Built a Python script that reads bulk-exported CSV reports and programmatically fires API calls to the vendor's batch update endpoint, eliminating all manual intervention
- Handled complex data integrity challenges including preservation of leading-zero batch numbers and scientific notation issues that arise when bulk exports are opened in Excel — ensuring data accuracy at scale
- Implemented robust error handling with row-level skip logic for missing batch numbers or dates, and a results log exported as CSV for auditability
- Validated the solution through a structured dry run on 3 sample batches before executing the full run, confirming end-to-end data persistence at the vendor's database level
- Successfully processed **8,049 batches** in a single automated run with a 100% API success rate, replacing what would have been days of manual work

---

## 26th Mar 2026 — Democratised EDD Null Value Monitoring Alert

Identified a critical gap in operational visibility where null Estimated Delivery Date (EDD) values — a direct driver of poor customer experience and SLA breaches — were being monitored in silos, accessible only to a small group of database analysts. Product, development, and supply chain teams had no visibility into data quality failures, leading to delayed responses and unresolved systemic issues going undetected.

Leveraged Claude AI to design and deploy an automated daily monitoring solution built on top of the existing MySQL database, requiring no additional tooling or infrastructure investment. The solution involved:

- Designed a data quality metric tracking the proportion of Delivery Notes (DNs) with null EDD values on a day-by-day basis over a rolling 7-day window, with a 5% null threshold as the alarm trigger
- Built a Python-based monitoring script that connects directly to the production MySQL database, executes the diagnostic query daily, and evaluates data quality against the defined threshold automatically
- Democratised access by configuring automated HTML email reports delivered daily to cross-functional stakeholders — Product, Engineering, and Supply Chain — replacing a process previously locked behind database access privileges
- Designed actionable alert structure: null percentage per day, breached thresholds highlighted in red, enough context for each team to act independently
- Scheduled via cron for daily automated execution — continuous monitoring without manual intervention
- Proactively surfaces issues before customer impact, shifting the team from reactive firefighting to proactive data quality management

The initiative transformed a fragmented, analyst-dependent monitoring process into a scalable, democratised alerting system that empowers non-technical stakeholders to act on data quality issues in real time.

---

## 26th Mar 2026 — E2E Order Fulfilment Intelligence Dashboard

**Stack:** MySQL · Looker Studio · AI-Assisted Engineering
**Role:** Data & Operations Analyst

### Executive Summary
Designed and delivered a production-grade, end-to-end order fulfilment tracking system for a 3PL quick-commerce operation, providing real-time visibility across every stage of the order lifecycle — from Shopify checkout to last-mile delivery. The system combines a purpose-built MySQL analytics pipeline with an interactive Looker Studio dashboard.

### Business Context & Problem Statement
Quick-commerce operations run on razor-thin SLA windows. The core problem was the absence of a systematic, stage-level breakdown of where time was being lost across the fulfilment funnel.

Key operational gaps addressed:
- No granular SLA tracking per fulfilment stage — only a binary delivered/not-delivered view
- No identification of which stage (upstream processing, warehouse, shipping, last-mile) was the primary delay driver
- 3PL courier performance invisible across dark stores
- Pharma orders (subject to regulatory holds) treated identically to standard orders, distorting warehouse metrics
- Looker Studio refreshing raw MySQL data on every render, causing dashboard latency at scale

### Technical Architecture

**Stage Model — Four-Stage Fulfilment Funnel**

Key engineering decisions:
- **Null-resilience:** Every timestamp wrapped in a sequential COALESCE fallback chain, preventing null propagation from corrupting downstream stage calculations
- **Warehouse Hours Adjustment (S2):** Warehouse Execution time calculated against operational hours only (06:00–23:00), stripping out the overnight dead window using a custom `GREATEST/LEAST/TIME_TO_SEC` expression
- **Pharma Order Isolation:** A conditional CASE expression isolates pharma hold time and correctly re-anchors the ER sync clock to the unhold timestamp
- **Delay Attribution:** Each order attributed a single primary delay stage (`max_delay_stage`) — the stage with the largest absolute delay
- **SLA Breach Flag:** Binary breach column compares `final_event_time` against the promised delivery window

**Index Strategy:** Two composite covering indexes designed to accelerate Looker Studio query patterns. The initial redundant `idx_qcom_ls` index (a subset of the covering index) was identified and dropped, reducing write overhead.

**Looker Studio Layer:**
- Data Extract caching replaced live MySQL connector queries, reducing render latency by an estimated 5–10x
- Dimension filters on dark_store, courier, order_date, max_delay_stage, and breach
- Extract refresh scheduled at 06:00 daily

### Claude AI — Role in Development
Claude was used as an active engineering collaborator throughout — not merely as a reference tool:
1. Reviewed multi-CTE query structure and validated COALESCE fallback chain logic
2. Identified a silent data bug in S4 sub-stage columns where raw timestamps were being aliased as duration columns
3. Advised on GREATEST/LEAST/TIME_TO_SEC approach for warehouse-hours-adjusted S2 calculations
4. Flagged that `idx_qcom_ls` was a redundant subset of `idx_qcom_ls_cover` and recommended it be dropped
5. Provided detailed walkthrough of the Looker Studio Data Extract feature

### Outcomes & Business Impact
- Full-funnel SLA visibility across all 3PL orders, broken down by dark store, courier, and order date
- Operations teams can identify the primary delay stage (`max_delay_stage`) for any subset of orders in seconds
- Pharma hold time correctly isolated from warehouse execution metrics
- Looker Studio dashboard load times significantly reduced through Extract caching
- The S4 timestamp bug — which would have silently corrupted last-mile time analysis — was caught and corrected before any reporting was generated

---

## 28th Mar 2026 — Batch Date Sync Automation (Claude Code)

### The Problem
Supertails' batch data — expiry dates, manufacturing dates, and disabled status — lives in two places: Supertails' ERP and Elastic Run's fulfilment system. When these fall out of sync, Elastic Run operates with incorrect shelf life data, with direct downstream consequences on order eligibility and product dispatch decisions.

The process for correcting mismatches was manual: identify the batch, open Postman, construct the payload, fire the request, check the response, repeat. When a reconciliation exercise surfaced **18,063 mismatches** between the two systems, the manual approach was not an option.

### What the Script Does
The reconciliation exercise filtered the 18,063 mismatches down to **335 actionable rows** — the ones where expiry date, disabled status, or both were incorrect. Manufacturing-date-only mismatches (17,728 rows) were excluded as a deliberate scope decision, keeping the correction focused on fields with live operational impact.

The script reads that 335-row CSV and fires a corrective API call to Elastic Run for each batch, pushing the correct expiry date, manufacturing date, and disabled flag in a single payload. Each response is logged as OK, FAILED, or ERROR. Results write out to a CSV so the outcome is auditable.

Two versions of the script exist because Elastic Run operates two separate API environments: the original integration API and a newer tenant API. The tenant version adds a 300ms delay between calls — a deliberate guard against rate-limiting when running a bulk correction during business hours — and prints a progress counter every 50 rows.

### How AI Was Used
The scripts were written entirely through Claude Code. The context supplied: input CSV structure, API endpoint and authentication details, field mapping, and two skip cases (missing batch number, or both dates absent). The AI translated that into working code.

The product decisions were mine: skipping manufacturing-date-only mismatches (to avoid inadvertently overwriting good data), adding the rate-limiting delay only to the tenant script, treating missing dates as a skip rather than a zero, and writing results to a CSV. These were not defaults the AI applied — they were explicit choices made because I understood the data and the operational context.

The scripts also handle a class of data quality issue that isn't visible until you've worked with this data: batch numbers frequently contain leading zeros, which Excel silently strips. The input CSV is prepared using TextEdit to preserve them. This came from prior experience, not the AI.

### Outcome
- ~8,400 batch records corrected across two runs in Elastic Run's fulfilment system
- Full audit trail: every batch, API response, and status logged to a results CSV
- Two environment variants (original + tenant API) handled from the same input file
- Rate limiting built in for safe execution during business hours
- Reusable: the next reconciliation exercise runs the same script against a new mismatch report

### Technical Details
Two Python scripts (`update_batch_dates.py` for the original API, `update_batch_dates_tenant.py` for the tenant API). Both read `Batch_Mismatch_Report_ExclMfgOnly.csv` and POST to Elastic Run's `/integration/api/v1/batch/update` endpoint. Payload includes `item_code`, `supplier_batch_no`, and a data object with `expiry_date`, `manufacturing_date`, and `disabled`. The tenant script adds `app_source:"Supertails"` to the payload, uses a different auth token, and introduces a `time.sleep(0.3)` delay with a progress counter every 50 rows. Rows missing a batch number or both dates are skipped and logged separately.

---

## 30th Mar 2026 — E2E Dashboard for QC Supply Chain Ops (Claude Code)

### The Problem
Two weeks before building this, I designed Supertails' first supply chain performance dashboard on Looker Studio — bringing visibility to breach rates, stage-level delays, and dark store performance where none had existed before. The dashboard was used in daily leadership meetings and immediately surfaced a class of operational problems that had previously been invisible.

But as usage grew, the tool's structural limitations became the problem. It required manual filtering to produce any meaningful view, offered no opinionated defaults, and could not function without someone who knew how to operate it. Meetings stalled when that person was absent. More fundamentally, the tool was reactive: it answered questions people already knew to ask, rather than surfacing fires proactively.

### The Strategic Call
Rather than patching Looker Studio, I made the call to redesign the entire solution from the ground up. Having designed the first version myself, I had precise knowledge of what the business logic required — SLA thresholds, warehouse-hours-adjusted stage calculations, pharma vs. non-pharma separation, IST timezone handling — and exactly where the product had failed. The correct answer was not a better BI dashboard but an opinionated application: one that opened already showing yesterday's data, surfaced fires automatically, and required zero configuration to run a meeting.

### What the Dashboard Does Today

**Command Centre — the meeting view**
The default view opens on yesterday's data with no configuration required. It shows EDD breach rate, total orders, orders per day, a 7-day sparkline, and per-stage breach rates at a glance. Breach counts are clickable and open a filtered list of the exact orders driving that number. Stage health now distinguishes soft breaches (within a defined buffer, shown in amber) from hard breaches (past the buffer, shown in red) — so meetings can differentiate operational noise from genuine failures without any manual interpretation.

**Top Fires — automatic triage**
A 3-level pivot (City → Dark Store → Courier) surfaces the worst-performing combinations ranked by breach percentage. Cities are collapsed by default so the highest-priority fires are visible immediately without scrolling.

**Promise Window Analysis**
Orders segmented by the gap between order creation and the promised delivery time — bucketed into five ranges from under 45 minutes to over 24 hours. Surfaced an important insight: orders with derived EDDs cluster almost entirely in the ≤45-minute bucket, meaning they are inherently high-risk and should be interpreted separately.

**Ops Deep Dive — the investigation view**
A hierarchical table (City → Dark Store → Courier) lets ops managers drill into specific combinations, view weighted average stage times colour-coded by SLA health, and export filtered data as a CSV. Export filenames are auto-generated with active filters embedded so downloaded files are self-describing. Includes a dedicated table surfacing ≤45-minute orders incorrectly assigned to Delhivery — a recurring misassignment pattern that the pivot alone wouldn't catch.

**DN Lookup — the order-level view**
Any individual delivery note can be looked up by ID and will show the full supply chain timeline across all stages with IST timestamps, breach attribution, and the specific stage responsible for the delay. The last-mile stage (S4) is now broken into four sub-stages — AWB to pickup, pickup to RAD, RAD to delivery attempt, and attempt to delivered — each timestamped individually from the courier's own system. Stage breach pills show at a glance whether each stage was within SLA, a soft breach, or a hard breach. Replaced a manual investigation process that previously required querying the database directly.

**Full Last-Mile Visibility (S4)**
The courier's CP database is now joined into the tracking table, surfacing timestamps for pickup, rider-at-door (RAD), first delivery attempt, and delivery completion. RAD time serves as a key EDD breach signal: if the rider arrived at the customer's door before the promised time, the order is considered on-time regardless of what happened after — recognising that a missed delivery after RAD is a customer-side event, not a supply chain failure. This definition was deliberately calibrated: a previous condition counting "delivery attempted within 4 minutes of EDD" was dropped after simulation showed it was masking genuine breaches.

**Bad EDD Detection**
The system flags orders where the promised delivery time was set before the order was created — a Shopify data quality issue that would otherwise silently inflate breach rates.

**Automated Breach Alerts**
Hourly email alerts go to key stakeholders when the daily breach rate crosses 5%. The dashboard finds you when something is wrong — no one needs to check proactively.

**Export Activity Reporting**
Every CSV export is logged with the user, timestamp, active filters, and row count. A daily report lands at 10:30 PM IST showing which team members downloaded data and how much.

### How AI Was Used
This project was designed and built end-to-end using Claude Code — with zero boilerplate or starter templates. The process was not "generate and paste." It was a structured product conversation where I held every meaningful decision and used the AI purely as an execution engine.

- **I defined the business rules.** SLA thresholds, what counts as a breach, how to handle warehouse operating hours in S2, pharma vs. non-pharma separation, IST timezone handling, the 5% alert threshold, soft vs. hard breach buffers, and the precise conditions under which an EDD is considered met — none of these came from the AI.
- **I caught the AI's wrong assumptions.** The AI guessed column names that didn't exist in our schema. It initially optimised the query for in-memory compute rather than a transactional database. It defaulted to Google OAuth without recognising that our users span beyond the Supertails Google workspace. Each time, I identified the error and corrected the direction.
- **I made the architectural calls.** Switching from Google OAuth to ERPNext OAuth2 mid-build required designing a custom NextAuth provider from scratch against Frappe's OAuth2 API. Joining the courier's CP database for last-mile timestamps required understanding how reference numbers were formatted across systems (`PRD-` prefix on ER's side) and deciding which timestamps were trustworthy enough to serve as SLA signals. The AI implemented these. I decided they were necessary and why.
- **I calibrated the breach definition over time.** After last-mile data was live, I ran simulations on the impact of each breach condition. One condition — "delivery attempted within 4 minutes of EDD" — was dropped because it masked genuine breaches. This was a product judgement call backed by data, not a technical fix.
- **I identified scope boundaries.** Deferring returns tracking, building courier normalisation at the application layer instead of SQL, routing cron jobs through cron-job.org instead of paying for Vercel Pro — these were cost, complexity, and timeline trade-offs that required product and operational judgement.

### Outcome
- Live internal dashboard, accessible to all approved ERPNext users via single sign-on
- Command Centre view built for leadership meetings — zero configuration, fires surfaced automatically on open
- Soft/hard breach distinction across all stages — meetings now differentiate noise from genuine failures without manual filtering
- Full last-mile visibility: courier pickup, RAD, delivery attempt, and delivery timestamps joined from the courier's own system
- Top Fires pivot replacing the manual "what's causing this?" conversation in every ops review
- Deep Dive table with multi-select filtering, SLA-coloured stage averages, and self-describing CSV export
- DN-level timeline with S4 sub-stage breakdown, stage breach pills, and current-stage tracking for individual order debugging
- Automated breach alert emails triggered hourly when daily breach rate exceeds 5%
- Daily export activity report surfacing tool adoption across the team at 10:30 PM IST
- Full source code on private GitHub, deployable by anyone on the team

### Key Technical Decisions
- **Data architecture:** MySQL rebuild query redesigned from scratch — restructuring CTEs, adding raw timestamp columns, handling warehouse-hours-adjusted SLA calculations for S2 (06:30–22:00 IST)
- **Last-mile join:** Courier CP database joined via `cp.reference_number = CONCAT('PRD-', dn_number)`. Timestamps cast as CHAR(25) to bypass MySQL 8 error 1525 on datetime columns
- **EDD breach definition:** Three-condition OR — RAD ≤ EDD, delivered ≤ EDD+5min, or in transit with EDD not yet passed. A fourth condition (delivery attempted ≤ EDD+4min) was explicitly dropped after simulation showed it masked ~6 additional breaches on a typical ≤45-minute day
- **Soft/hard breach buffers:** S1 +30s, S2 +120s, S3 +30s. Designed to distinguish operationally tolerable overruns (amber) from genuine failures (red) without manual interpretation
- **Auth:** ERPNext OAuth2 via a custom NextAuth provider against Frappe's OAuth2 API. Any @supertails.com email allowed
- **Courier normalisation:** Built at the application layer using substring matching. Adding a new courier variant requires one line of code and a deploy — no database changes
- **Infrastructure:** Vercel Hobby (free) + cron-job.org (free) for all scheduled jobs
- **Multi-select filters:** Selections buffered internally and applied on an explicit click — preventing re-queries on every checkbox interaction. Filter state is URL-encoded and persists when navigating between views
- **Freshness:** Computed from the latest pipeline event timestamps in the data itself, not the ETL run time. Data refreshes every 30 minutes

### 27th Apr 2026 — Dashboard Updates

#### Breach Depth Breakdown for ≤45m Window
Added an expandable sub-bucket under the ≤45m promise window row in the Orders by Promise Window table. When the ▸ chevron is clicked, it shows how far breached orders ran past the 45-minute promise — expressed as absolute delivery time so severity is readable without mental arithmetic:

- **45–60m** — delivered 0–15 minutes late
- **60–90m** — delivered 15–45 minutes late
- **>90m** — delivered more than 45 minutes late
- **Still in transit** — not yet delivered at time of data snapshot

Each sub-row shows the count of breached orders in that band and its share of total ≤45m breaches. Counts are clickable and open a filtered DN list. The breakdown collapses by default; clicking the chevron or the ≤45m row header expands it.

#### DN Lookup — Multi-Mode Search
DN Lookup now accepts three identifier types from a single search bar: **DN number**, **AWB**, and **Shopify Order number**. A mode dropdown to the left of the input switches context; the placeholder text updates to match. Previously, only DN numbers were supported.

#### Mobile Optimisation
Command Centre and DN Lookup pages audited and fixed for mobile web: NavTabs overflow, KPI card layout on small screens, and long-string wrapping in DN header grid cells resolved. Touch targets on breach depth sub-rows increased to meet mobile tap-area standards.

---

## 12th Apr 2026 — ST-ERP Platform: Second Batch of Releases

### Management Summary
Key outcomes delivered in this batch:
- DN / ER reliability hardening around edge cases: SO cancellations, hold/unhold loops, silent DN auto-submit failures, COD allocation gaps
- Operational recovery improvements via bulk DN actions and targeted retriggers for stuck cohorts
- Inbound (PR) workflow made recoverable and fail-safe: putaway failures tracked, submission gated, negative-inventory blocks made actionable
- Catalog/Shopify data integrity improvement by enforcing unique variant IDs (numeric-only identifiers)
- Platform maintainability improved via configuration table work and a canonical DMS integration LLD

### 1. Order / DN / ER Sync Reliability

#### 1.1 AWB generated but not synced due to hidden DN auto-submit failure
**Jira:** STERP26-125 (Done)

**What changed**
- Identified and fixed the scenario where AWB generation succeeds but ER sync doesn't happen because DN auto-submit fails silently in backend (e.g., due to negative inventory in another warehouse).
- Flow now avoids "partial success" states where ops believes the order progressed but it's actually stuck.

**Business impact:** Fewer stuck orders requiring manual re-trigger/RFP replays. Better operational visibility and reduced turnaround time for incident triage.

#### 1.2 Prevent ER DN sync after Sales Order cancellation
**Jira:** STERP26-271 (Done)

**What changed**
- Fixed the edge case where ER DN sync was attempted after SO cancellation, causing ER to create a DN reference that ST could not store.

**Business impact:** Reduces irrecoverable stuck states and ER/ST divergence in cancellation windows.

#### 1.3 "Order on Hold" but DN still synced to ER
**Jira:** STERP26-93 (Done)

**What changed**
- Closed the gap where hold state was not reliably enforced before DN sync to ER.

**Business impact:** Prevents unintended fulfillment attempts on holds; fewer manual reversals/escalations.

#### 1.4 Prevent hold from being re-applied after unhold
**Jira:** STERP26-286 (UAT)

**What changed**
- Added a guard using `unhold_time` so that once a DN is unheld, subsequent validation hooks don't accidentally put it back on hold due to transient status states.

**Business impact:** Reduces "hold/unhold loop" incidents that stall fulfillment.

#### 1.5 COD component not allocated when DN is recreated during bin determination
**Jira:** STERP26-76 (Done)

**What changed**
- Ensured that when a DN is re-created as part of bin determination, COD fee components are carried forward correctly.

**Business impact:** Prevents accounting inconsistencies and reconciliation noise.

#### 1.6 Item Price duplication: retry mechanism for affected DNs
**Jira:** STERP26-242 · STERP26-273

**What changed**
- Implemented a targeted retry mechanism for DNs failing with "Item Price appears multiple times…" duplication conditions.

**Business impact:** Fewer DNs stuck due to transient pricing master inconsistencies; faster recovery.

### 2. Inwarding / Putaway / PR Flow Reliability

#### 2.1 Putaway failures become trackable; PR submission gated on putaway confirmation
**Jira:** STERP26-133 (UAT)

**What changed**
- On putaway confirmation receipt, system attempts stock movements; if it fails (e.g., negative inventory), it marks line items as Failed with Error Type = Negative Inventory and keeps PR in Draft
- On PR submit: blocks submission if no latest putaway confirmation exists; uses latest putaway confirmation on every retry attempt
- Makes negative inventory blocks actionable by surfacing specific SKU-batch/warehouse errors

**Business impact:** Eliminates "mystery Draft PRs" that require log-diving. Reduces incorrect submissions and improves inbound recovery speed.

#### 2.2 Incorrect expiry being sent when batch expiry cannot be resolved
**Jira:** STERP26-257 (Done)

**What changed**
- Stopped defaulting batch expiry to server current time (epoch) when expiry lookup fails/returns empty.

**Business impact:** Prevents incorrect expiry data in ER and downstream "instantly expired" stock/inwarding failures.

### 3. Inventory / Stock Integrity

#### 3.1 Hard block: balance quantity depletion cannot exceed available quantity
**Jira:** STERP26-136 (Done)

**What changed**
- Enforced strict validations across admin, user, and APIs so depletion cannot go beyond available balance.

**Business impact:** Fewer ledger integrity issues and reduced negative stock creation by invalid operations.

### 4. Catalog / Shopify Data Quality

#### 4.1 Variant ID uniqueness enforcement
**Jira:** STERP26-230 (UAT)

**What changed**
- Enforces unique variant IDs and ensures product/variant IDs are numeric-only (no dots).
- Prevents multiple ERP item codes being linked to one Shopify variant identifier.

**Business impact:** Reduces catalog ambiguity, incorrect syncs, and operational cleanup for mismatched items.

### 5. Operational Recovery / Bulk Actions

#### 5.1 Bulk submit a DN list from Excel
**Jira:** STERP26-310 (Done)

**What changed**
- Executed bulk submission for a provided DN cohort.

**Business impact:** Faster backlog clearance without one-by-one manual processing.

#### 5.2 Retrigger DN sync for "Return Initiated" DNs
**Jira:** STERP26-311 (Done)

**What changed**
- Triggered corrective sync to recover a stuck return-initiated cohort.

**Business impact:** Reduces aging returns and improves reverse lifecycle completion.

### 6. Platform Config / Documentation / RCA

#### 6.1 ER tenant error message configuration table
**Jira:** STERP26-256 (Done)

**What changed**
- Implemented an ER-tenant-specific error message config table, kept separate by design from other systems.

**Business impact:** Faster, safer error-message governance without cross-tenant coupling.

#### 6.2 Canonical DMS Integration LLD
**Jira:** STERP26-50 (Done)

**What changed**
- Produced a reusable low-level design covering API contracts, async flows, retries, failures, reconciliation, and sequence diagrams.

**Business impact:** Improves consistency across future DMS integrations; reduces rework and design drift.

#### 6.3 RCA work item
**Jira:** STERP26-246 (Done)

**What changed**
- RCA documented and closed for the referenced incident stream.

**Business impact:** Captures learnings and supports prevention-oriented fixes.

---

## 20th Apr 2026 — ST-ERP Platform: Third Batch of Releases

Here's a concise, management‑friendly set of release notes for **all tickets currently in your editor context**. I've focused on the ones that are **Done / Ready for Deployment / UAT / Sanity / In Review** so this reads like a real change set, not the entire backlog.

You can see the full issue list here:  
https://zapgift.atlassian.net/issues/?jql=key%20in%20%28STERP26-80%2C%20STERP26-57%2C%20STERP26-81%2C%20STERP26-252%2C%20STERP26-281%2C%20STERP26-21%2C%20STERP26-133%2C%20STERP26-152%2C%20STERP26-155%2C%20STERP26-232%2C%20STERP26-238%2C%20STERP26-78%2C%20STERP26-8%2C%20STERP26-49%2C%20STERP26-53%2C%20STERP26-66%2C%20STERP26-68%2C%20STERP26-30%2C%20STERP26-52%2C%20STERP26-58%2C%20STERP26-116%2C%20STERP26-117%2C%20STERP26-56%2C%20STERP26-119%2C%20STERP26-120%2C%20STERP26-121%2C%20STERP26-123%2C%20STERP26-128%2C%20STERP26-138%2C%20STERP26-142%2C%20STERP26-146%2C%20STERP26-153%2C%20STERP26-132%2C%20STERP26-162%2C%20STERP26-174%2C%20STERP26-183%2C%20STERP26-187%2C%20STERP26-189%2C%20STERP26-192%2C%20STERP26-196%2C%20STERP26-197%2C%20STERP26-201%2C%20STERP26-209%2C%20STERP26-230%2C%20STERP26-239%2C%20STERP26-67%2C%20STERP26-274%2C%20STERP26-243%2C%20STERP26-255%2C%20STERP26-161%29%20ORDER%20BY%20key

---

### Management Summary

This release hardens the ERP backbone across four themes:

- **Delivery reliability & inventory correctness**  
  - DN reservation is now driven by **actual SLEs**, reducing negative inventory and mismatched reservations (STERP26‑123).  
  - Auto‑retry for **shipping updates to ER** prevents AWB sync failures from being lost on transient 4XXs (STERP26‑243).  
  - Log archival and read‑replica work improve the stability and observability of DN/SLE behaviour (STERP26‑174, STERP26‑238, STERP26‑201, STERP26‑209).

- **Finance & purchasing controls**  
  - Draft PIs can now be safely **deleted** via RBAC (STERP26‑67).  
  - Batch expiry validation is being formalised end‑to‑end for higher data quality (STERP26‑239, coupled with STERP26‑154 in another set).

- **E‑commerce channel correctness**  
  - Variant ID uniqueness removes a large class of Shopify/ERP catalog mismatches (STERP26‑230).  
  - Offline/online channel mis‑classification has been fixed and RCA is documented (STERP26‑196, STERP26‑197).

- **3PL / putaway robustness**  
  - PR putaway handling now has a proper failure model, retry, and negative‑inventory surfacing (STERP26‑133, STERP26‑232).

Overall, the changes reduce silent failures (DNs without SLEs, AWB not reaching ER, missing putaway), make finance operations safer, and improve our ability to debug and scale reporting.

---

### Ticket‑wise Release Notes

Below I'm only listing issues that are **effectively "released" or in last‑mile stages**: `Done`, `Sanity`, `Ready for Deployment`, `UAT`, `In Review`. Others in `To Do` / `On Hold` / `In Progress` I've treated as out of scope for this set.

---

#### Delivery & Inventory Reliability

##### Change the DN Reservation Logic to consider SLE instead of DN Status
**Jira:** STERP26‑123 – https://zapgift.atlassian.net/browse/STERP26-123  
**Status:** Sanity

**What changed**

- Reservation for Delivery Notes is now computed **solely from Stock Ledger Entries** instead of DN status fields.
- DN status remains informational; the **single source of truth** for reserved stock is the SLE layer.
- Inventory is considered un‑reserved only when the relevant SLE exists and reflects the movement.

**Business impact**

- Eliminates cases where stock looked "reserved" by status but had no corresponding SLE (or vice‑versa).  
- Reduces over‑reservation and negative inventory scenarios, especially in high‑churn warehouses.  
- Downstream systems now see availability that matches actual ledger entries 1:1.

---

##### Inventory sync between ER and ST
**Jira:** STERP26‑209 – https://zapgift.atlassian.net/browse/STERP26-209  
**Status:** UAT

**What changed**

- Implemented a recurring **batch_wise_stock_balance** based reconciliation for all 3PL warehouses.  
- When ST inventory for a batch exceeds ER's, we now auto‑create **Material Transfer** entries to move surplus into the appropriate "missing" bin.  
- Transfer quantity is capped at `min(ST Balance − ER Balance, ST Available Qty)` to avoid touching reserved stock.

**Business impact**

- ER and ST are kept materially aligned on batch‑wise inventory, reducing discrepancies during pick/pack.  
- Fewer manual adjustments and "mystery" inventory gaps during audits.

---

##### 2‑fold Increase in DNs with Missing SLEs – Analysis
**Jira:** STERP26‑201 – https://zapgift.atlassian.net/browse/STERP26-201  
**Status:** In Review

**What changed**

- Performed a **time‑series analysis** on DNs with missing SLEs, highlighting a sharp rise from 24‑Feb onward.  
- Established daily counts and clusters to tie into code paths and jobs that changed around that time.

**Business impact**

- Gives engineering and ops a clear baseline on the **scale and onset** of missing‑SLE failures.  
- Feeds into remediation work (e.g. SLE creation fixes, log archival, read‑replica‑backed analysis).

---

##### Create a recurring job to archive logs at regular intervals
**Jira:** STERP26‑174 – https://zapgift.atlassian.net/browse/STERP26-174  
**Status:** UAT

**What changed**

- Added a **weekly cron job (Sunday 1 AM)** to archive high‑volume logs into a parallel table with identical schema.  
- Keeps online tables light for day‑to‑day queries while retaining full history in the archive.

**Business impact**

- Improves performance for real‑time debugging and dashboards that read from current‑period logs.  
- Reduces risk of log tables becoming operational bottlenecks without sacrificing traceability.

---

#### 3PL Putaway & PR Flow

##### Handle Putaway for PRs with submission failure
**Jira:** STERP26‑133 – https://zapgift.atlassian.net/browse/STERP26-133  
**Status:** UAT

**What changed**

- When 3PL putaway confirmation is received, the system now:  
  - Moves sellable qty to `%Unified_bin` and non‑sellable to `%Missing_bin` / `%Damage_bin`.  
  - On **negative inventory failure**, logs the line as `Failed + Negative Inventory` and keeps the PR in **Draft**.
- PR **submission is blocked** if no latest putaway confirmation exists, with a clear validation message.  
- Each submit attempt re‑uses the **latest confirmation log**, re‑posting stock once issues are fixed.

**Business impact**

- PRs can no longer slip through without valid putaway; failures are visible and actionable.  
- Negative inventory driven putaway errors are surfaced to the right stakeholders (cluster managers + ERP functional lead).  
- Reduces hidden stock mis‑postings and manual firefighting around inbound flows.

---

#### E‑commerce & Channel Correctness

##### Make Variant ID Unique
**Jira:** STERP26‑230 – https://zapgift.atlassian.net/browse/STERP26-230  
**Status:** UAT

**What changed**

- Enforced **uniqueness** of Shopify Variant IDs across ERP Item Codes.  
- Validated that both **product_id** and **variant_id** are strictly numeric, disallowing formats (like dots) that caused mapping ambiguity.  
- Backed by implementation in the e‑commerce integrations repo (`/pull/91`).

**Business impact**

- Removes 395+ known cases where a single variant pointed to multiple items.  
- Stabilises bundle and stock sync logic, reducing chances of wrong item being fulfilled or displayed online.

---

##### Incorrect identification of Offline orders
**Jira:** STERP26‑196 – https://zapgift.atlassian.net/browse/STERP26-196  
**Status:** Done

**What changed**

- Fixed logic that was classifying some **online orders as offline**, preventing DN creation.  
- Example: order **ST249354612507** now correctly flows through online DN creation.

**Business impact**

- Ensures that legitimate online orders reach pick/pack rather than silently stalling.  
- Reduces manual investigation for "no DN created" incidents on online orders.

---

##### RCA on channel classification (Offline / Online / Dropship / AHS)
**Jira:** STERP26‑197 – https://zapgift.atlassian.net/browse/STERP26-197  
**Status:** Done

**What changed**

- Performed and documented an RCA for order **channel classification** logic.  
- Clarified how channels are derived, where mis‑classifications originated, and what guardrails are needed.

**Business impact**

- Gives product and engineering a shared understanding of channel mapping rules, reducing recurrence of STERP26‑196‑type defects.  
- Supports cleaner reporting and SLA tracking by channel.

---

#### Purchase & Finance Controls

##### Enable Delete Option for Draft Purchase Invoices
**Jira:** STERP26‑67 – https://zapgift.atlassian.net/browse/STERP26-67  
**Status:** Done

**What changed**

- Introduced a **role‑based delete** capability for **Draft Purchase Invoices**.  
- Deletion is **blocked** for Submitted / Cancelled PIs.  
- Access is tied to the `Finance Invoice` profile.

**Business impact**

- Finance can clean up erroneous drafts without polluting the PI universe.  
- No risk of accidental deletion of posted invoices or accounting history.

---

##### Batch Expiry Date Validation (PR & Batch)
**Jira:** STERP26‑239 – https://zapgift.atlassian.net/browse/STERP26-239  
**Status:** On Hold (pairs with STERP26‑154)

**What's defined (for upcoming or partial rollout)**

- Unifies expiry validation between batch master and PR:  
  - Enforces `dd/mm/yyyy` format, caps expiry at **today + 9 years**, and ensures derived MFG ≤ today.  
  - Blocks PR submit and import rows that violate these rules, with clear row‑level errors.  
  - Applies GRN‑tolerance checks for non‑IWT channels.

**Business impact (once fully live)**

- Prevents impossible or non‑compliant expiry data from entering the system.  
- Aligns batch and PR level dates, improving audit readiness and reducing downstream pricing/tax errors.

---

#### Shipping / ER Integration Hardening

##### Auto‑retry on 4XX against Shipping Updates to ER
**Jira:** STERP26‑243 – https://zapgift.atlassian.net/browse/STERP26-243  
**Status:** Done

**What changed**

- Implemented an **automatic retry job** for AWB updates to ER when a **4XX** is returned from their side (known transient/integration issues).  
- Ensures that initial failures don't permanently strand a DN in an "AWB not in ER" state.

**Business impact**

- Reduces manual resync work for shipping updates.  
- Stabilises DN → ER tracking, especially during external partial outages or intermittent API errors.

---

#### Reporting & Analytics Foundations

##### Historical Shelf‑wise Inventory Reporting API
**Jira:** STERP26‑116 – https://zapgift.atlassian.net/browse/STERP26-116  
**Status:** In Review

**What changed**

- Designed and implemented a **daily EOD snapshot** of **sellable** inventory (excluding disabled batches / rejected bins).  
- Added an API that takes a **date** and returns `SKU, warehouse, available qty` for that day.

**Business impact**

- Enables demand and analytics tools to reason over **historical** (not just live) shelf‑wise inventory.  
- Supports forecasting, ageing, and SLA analysis without impacting the transactional DB.

---

##### Read replica for reports / MCP server
**Jira:** STERP26‑238 – https://zapgift.atlassian.net/browse/STERP26-238  
**Status:** Developing (close to infra‑ready)

**What's being delivered**

- A **Model Context Protocol (MCP)** server that lets users query ERPNext read‑replica data via Claude Desktop, **respecting ERP permissions**:  
  - Only `SELECT` queries allowed.  
  - Table‑level access derived from ERP roles & DocPerms.  
  - All queries forced to hit a **read replica**, with an enforced row limit.  
- Includes a `list_my_accessible_tables` helper and environment‑driven config.

**Business impact (as it goes live)**

- Powerful ad‑hoc reporting without risking writes or bypassing ERP's RBAC.  
- Offloads reporting load from the primary DB.

---

#### Misc DN / SLE Stability Work (Near Release)

These are close to release (UAT/In Review) and part of the same reliability narrative:

- **Bin Determination Revised Workflow**  
  - STERP26‑183 – https://zapgift.atlassian.net/browse/STERP26-183 – Status: UAT  
  - Clarifies and stabilises how bins are chosen for bundle and regular items, feeding into coherence fixes (STERP26‑162).

- **Bundle Coherence breaking in some cases**  
  - STERP26‑162 – https://zapgift.atlassian.net/browse/STERP26-162 – Status: In Review  
  - Validates bundle integrity across SO → DN, closes edge gaps highlighted in new cases.

- **Inventory transaction beyond batch expiry**  
  - STERP26‑255 – https://zapgift.atlassian.net/browse/STERP26-255 – Status: On Hold but scoped  
  - Defines stricter rules to skip reco on disabled/expired batches and report historical impact.

---

## 18th Apr 2026 — ER–ST Inventory Signal Analysis

### What
An audit of inventory deduction signals flowing from ElasticRun (ER) into the Supertails (ST) system, with the goal of identifying cases where ST received multiple signals for the same SKU-Batch-Warehouse combination — which should not happen in normal operations.

The output is a classified list of signal clusters, distinguishing genuine sync issues from benign system behavior.

### Why
ST and ER operate a 3-point truth system:

```
[ER Stores]  ──physical truth──▶  [ER System]  ──signal──▶  [ST System]
 (shelves)                         (ledger)                  (mirrors ER)
```

- ST **owns** the inventory; ER is **physically accountable** for it
- When a picker finds inventory missing, damaged, or expired in an ER dark store, ER deducts it from its own ledger and sends a signal to ST
- ST must process that signal and mirror the deduction in its own system

**The problem:** If ST receives more than one signal for the same SKU-Batch-Warehouse combo, it means ER still had that inventory on its books after ST had already processed the first deduction — a sign of a sync failure. Left undetected, this results in inaccurate inventory records on ST's side (phantom stock or over-deduction).

### Who
- **Siddharth Chauhan** — analysis, methodology, classification
- **ElasticRun** — source of the ThreePL Logs report (raw signal data)
- **Supertails Systems Team** — downstream stakeholders if root cause remediation is needed

### When

| Activity | Date |
|---|---|
| Analysis started | 2026-04-15 |
| Methodology finalized | 2026-04-15 |
| First full run (Apr 10–15) | 2026-04-15 |
| Second run (Apr 16 incremental) | 2026-04-16 |
| Third run (Apr 10–16 full re-run) | 2026-04-17 |
| Data coverage | Apr 10–16, 2026 |

### Where
- **Data source:** ThreePL Logs report exported from ER system (`ThreePL Logs.xlsx`)
- **Warehouses covered:** All ER dark stores (Domlur, Jakkur, Kaikondrahalli, BGRoad, Yeshwanthpur, and others)

### How

**Step 1 — Filter the dataset**
Keep only rows where `Status = "Success"` (signal received by ST) and `Status (Inventory Update Entry) = "Processed"` (ST acted on it).

**Step 2 — Group and flag**
Group by (Warehouse, Item Code, Batch). Any combo with >1 signal is flagged for classification.

**Step 3 — Classify each flagged combo**

- **Category 1 — Benign: ER Artifact (≤60s span):** All signals arrive within 60 seconds. ER system records a single picker event as multiple rows internally.
- **Category 2 — Benign: Concurrent Orders:** Two or more orders for the same SKU-Batch created at approximately the same time. Each generates a picklist; pickers independently mark the item as not found. Signals arrive minutes apart but are all legitimate.
- **Category 3 — Benign: Order Cancellation Flow (reversed voucher):** When a picker finds 0 units, ER sends two signals — one for unreserved stock, one for reserved stock released after cancellation. Detection marker: Voucher No of signal 1 > Voucher No of signal 2.
- **Category 4 — Genuinely Problematic:** Signals >60s apart AND vouchers not reversed AND not explainable by concurrent orders. Root cause lives on ST's side.

### Results (Apr 10–16, 2026)

| Category | Combos | Signals |
|---|---|---|
| Total in report | — | 493 |
| Excluded (IUE Failed) | — | 16 |
| **Filtered dataset** | **392** | **477** |
| Single-signal — no issue | 311 | 311 |
| Benign — ER artifact (≤60s) | 33 | 67 |
| Benign — cancellation flow | 43 | 86 |
| Benign — concurrent orders | 3 | 6 |
| **Genuinely problematic** | **4** | **~13** |

**Genuinely Problematic Combos:**

| Warehouse | Item Code | Batch | Signals | Notes |
|---|---|---|---|---|
| Domlur | CFOTR0002TE | 4343DCC | 3 | Signal 1 Apr 13; signals 2+3 Apr 14 (cancellation pair preceded by orphan signal 1) |
| Domlur | DTRBC0019PU | A9EB79A | 3 | Signals 1+2 cancellation pair (BPL Expired); signal 3 orphan 9 min later (BPL Missing) |
| Jakkur | CFOWF0129SH | C49A4F7 | 2 | 10hr gap between signals |
| Domlur | CFOTR0004TE | 41F8830 | 3 | Spans Apr 15–16 |

**Cleared Combos (initially flagged, then explained):**

| Combo | Explanation |
|---|---|
| CFOWF0132WH \| 52CA026 \| Kaikondrahalli | Concurrent orders (DN-26-578385, DN-26-578422); 3 signals Apr 15 4:30–4:32 pm, 6 units total |
| CLILI0003IN \| 88D7F51 \| Domlur | Concurrent orders; signals minutes apart |
| DTRJT0010BOW \| 2C88990 \| Domlur | Concurrent orders; 2 signals Apr 10 10:08 & 10:10 |

### Open Questions / Next Steps
1. **Root cause investigation:** Why did ER still hold inventory for the 4 problematic combos after ST processed the first signal?
2. **Ongoing monitoring:** Run the same analysis on new ThreePL Logs data (Apr 17+)
3. **Process improvement:** Should concurrent-order scenarios be detectable in real time to avoid false alarms in future audits?
