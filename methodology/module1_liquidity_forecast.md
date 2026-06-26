# Module 1 — Liquidity Monitoring & 13-Week Cash Forecasting

**Pages:** 01 HEALTH · 02 CASHFLOW
**Focus:** Real-time solvency vs liquidity tension · Forward cash projection · Regulatory budget structure

---

## 1. Liquidity Runway — Operational Significance

### Formula

```
Liquidity Runway (days) = Current Cash Balance
                          ─────────────────────────────
                          Monthly Fixed Cost Base / 30
```

### Threshold Framework

| Range | Status | Operational Implication |
|-------|--------|------------------------|
| > 60 days | ✅ Green | Safe buffer — two full payroll cycles covered |
| 30–60 days | ⚠️ Amber | Active monitoring required — one delayed payment triggers exposure |
| < 30 days | 🔴 Red | Immediate action — payroll cycle at risk within the month |

**Test result: 32 days → Amber**

### Why 32 Days Is Operationally Critical

A 32-day runway means the business covers exactly one payroll
cycle from current cash reserves. In Italian manufacturing,
payroll hits on the 27th of each month; INPS and IRPEF
contributions on the 16th. A single customer delaying payment
by 30 days collapses the runway below zero before the next
cycle closes.

This is the operational gap that a healthy DSCR (2.56) obscures:
debt is covered at six months, but daily cash management operates
on a five-week horizon. The two metrics measure fundamentally
different dimensions of financial health:

- **DSCR** measures whether sufficient cash will be generated
  over the next six months to service financial debt
- **Liquidity Runway** measures whether the business survives
  the next four weeks if inflows slow down

Both can be true simultaneously — and in this dataset, they are.

### Calculation Approach — Fixed Cost Normalisation

The monthly fixed cost base normalises all expense line items
to a monthly equivalent regardless of billing frequency —
quarterly leasing, semi-annual insurance, and annual licences
are all weighted proportionally. This prevents systematic
underestimation of the daily burn rate common in manual models.

---

## 2. Financial Health Monitor — 8 Normative KPIs

### KPI Framework

| Indicator | Value | Benchmark | Source | Status |
|-----------|-------|-----------|--------|--------|
| Liquidity Runway | 32 days | > 60 days | Treasury best practice | ⚠️ |
| 90-Day Minimum Balance | €224,554 | > Safety threshold | Internal forecast | ✅ |
| DSO (CNDCEC formula) | 73 days | ≤ 52 days | CNDCEC 2022 §4.1 | ⚠️ |
| Concentration Risk | 36.9% | < 25% | Best practice | ⚠️ |
| Fixed Cost Coverage | 1.1x | > 2x | Treasury best practice | ⚠️ |
| DSCR (6 months) | 2.56 | ≥ 1.0 | CNDCEC 2022 §3.2 | ✅ |
| Leverage | 1.2x | < 2.5x | Basilea III | ✅ |
| PFN / EBITDA | 2.5x | < 3.5x | Banking practice | ✅ |

**4 of 8 indicators in amber — 0 in red at current snapshot.**

### DSO — CNDCEC Official Formula

The CNDCEC standard requires both AR and revenue to be expressed
on a VAT-inclusive basis, because balance sheet receivables carry
VAT while reported revenue does not:

```
DSO = (Open Receivables [VAT-inclusive])
      ──────────────────────────────────── × 365
      (Annual Revenue × 1.22)
```

Applying the 1.22 VAT multiplier prevents systematic understatement
of DSO by approximately 18% — a methodological error common in
simplified implementations. At 73 days against a 52-day sector
benchmark, this company is extending approximately 21 days of
uncompensated financing to its customers on every invoice cycle.

### Calculation Approach — Hierarchical Alert Logic

The dashboard banner evaluates all eight indicators simultaneously
and surfaces only the highest-priority signal. The priority cascade
follows operational urgency: immediate liquidity crisis →
regulatory DSCR breach → negative 90-day projection →
sub-threshold runway → concentration risk → cost coverage.
This prevents alert fatigue — one actionable message,
not a simultaneous list.

---

## 3. 13-Week Rolling Cash Forecast — Scenario Engine

### Scenario Logic

| Scenario | Receivables Timing | Payables Timing | Purpose |
|----------|--------------------|-----------------|---------|
| Optimistic | Contractual due date — zero delay | Fixed weekly schedule | Upper bound — best case |
| Base | Due date + historical DSO per customer | Fixed weekly schedule | Central estimate — realistic |
| Pessimistic | Due date + portfolio DSO + 30 days | Fixed weekly schedule | Stress test — downside |

**Design principle:** Only receivables shift between scenarios.
Payables, tax obligations, and debt service are held constant
because suppliers, tax authorities, and banks operate on fixed
schedules regardless of the company's collection performance.
This asymmetry reflects real commercial dynamics — customers
may pay late, but outflows do not wait.

### Why Three Scenarios Instead of One

A single-point cash forecast creates false precision. Management
anchors on one number and fails to plan for deterioration.
With three simultaneous curves measured against a safety threshold,
the dashboard communicates:

- **Where cash is heading** — the trajectory
- **When it risks falling short** — the crossing point with the threshold
- **How much buffer exists** — the spread between optimistic and pessimistic

This framing produces fundamentally different treasury decisions
than a single projected balance.

### Customer-Level DSO — Base Scenario

The base scenario applies each customer's individual historical
payment behaviour rather than a portfolio average. DSO is derived
from paid invoice history per counterparty — customers with
no payment history default to a 20-day assumption
(Italian manufacturing average, Cerved 2023).
This produces a more accurate central estimate than a
single portfolio-average delay.

### Cumulative Rolling Architecture

The forecast accumulates weekly cash flows from the opening
balance forward rather than calculating discrete weekly snapshots.
This is the critical design distinction: each week's projected
balance reflects everything that has happened from day one,
not just that week in isolation.

Receivables use a one-sided date filter — collection expected
on or before week end — to capture past-due invoices that remain
uncollected. A symmetric window would exclude them, understating
the realistic cash position.

Payables use a symmetric weekly window to prevent the same
obligation from being counted in multiple cumulative periods.

### Data Model Architecture — Isolated Forecast Dimension

The scenario engine uses a dedicated week dimension table
intentionally isolated from the main date dimension.
This architectural decision gives the calculation engine
full control over date window logic, preventing report-level
slicer filters from interfering with the cumulative
rolling calculation.

### Scheduled Outflows — Explicit vs Averaged

| Outflow Component | Modelling Approach |
|-------------------|--------------------|
| Accounts Payable | Per invoice due date from AP ledger |
| Payroll | Monthly fixed amount ÷ 4 — weekly equivalent |
| F24 — VAT | Per tax schedule — exact dates and amounts |
| F24 — Income tax (IRES/IRAP) | Per tax schedule — lump sums at statutory deadlines |
| INPS contributions | Per tax schedule — monthly cadence |
| Loan repayments | Per amortisation schedule — capital + interest |

Using exact schedules rather than monthly averages changes the
shape of the weekly forecast significantly. In the test dataset,
week 2 carries a €28,100 F24 payment (June VAT liquidation +
IRES/IRAP instalments) that would be invisible in an averaged
model. The exact-schedule approach surfaces the specific week
management needs to ensure coverage.

---

## 4. Cash Budget — CNDCEC Budget di Tesoreria

### Structure

```
A   Opening cash balance                     =   €47,200
│
B   Operative inflows (AR — 6 months)        =  €516,200
│
C   Operative outflows (AP + Payroll + Tax)  = -€482,700
│   ├── Supplier payments (AP)               = -€119,000
│   ├── Payroll and social contributions     = -€260,700
│   └── Tax obligations (F24)               = -€103,000
│
D   Net operative cash flow (B – C)          =   €33,500
│
F   Cash available for debt service (A + D)  =   €80,700
│
G   Debt service — loans + leasing (6M)     =  -€31,540
│
I   Projected closing balance (F – G)        =   €49,160
```

**DSCR = F ÷ G = €80,700 / €31,540 = 2.56 ✅**

**Source:** CNDCEC "Strumenti di allerta" 2022
**Legal reference:** D.Lgs. 14/2019 — Codice della Crisi d'Impresa

### Why This Structure Matters Under D.Lgs. 14/2019

The Italian Insolvency Code (CCII) introduced a forward-looking
obligation for company boards: they must maintain adequate
organisational frameworks to detect early signs of financial
distress (Art. 2086 c.c.) and monitor specific early warning
indicators (Art. 25-novies).

The CNDCEC defines the **Budget di Tesoreria** as the preferred
instrument for computing the DSCR used in the early warning
assessment. The threshold is unambiguous:

- **DSCR ≥ 1.0** → Cash available for debt exceeds debt service
  obligations → business continuity confirmed
- **DSCR < 1.0** → Insufficient cash to service financial debt
  → early warning signal triggered under Art. 25-novies

The structure separates operative outflows (row C) from financial
debt service (row G) deliberately. This ensures the DSCR numerator
reflects true operative cash generation — not distorted by
treating loan repayments as operating costs.

### 12-Month Sustainability Test — Art. 2086 c.c.

Beyond the 6-month DSCR, the dashboard computes a 12-month
sustainability index as required by Art. 2086 c.c.:

```
12M Sustainability =
(Cash Balance + 12M Projected Inflows)
────────────────────────────────────────────────────────
(12M Operative Outflows + 12M Debt Service)

Threshold: ≥ 1.0 = sustainable
           < 1.0 = structural concern — board action required
```

**Test result: 0.60 → 🔴**

This result does not contradict the healthy DSCR of 2.56.
The 6-month window captures a period where invoice-based inflows
are well-populated. The 12-month horizon exposes the revenue gap
that emerges when the forward invoice backlog runs out —
the primary driver of the sustainability shortfall and
the core limitation identified for live implementation.

---

## 5. Key Results

| Metric | Value | Status | Benchmark | Source |
|--------|-------|--------|-----------|--------|
| Liquidity Runway | 32 days | ⚠️ | > 60 days | Treasury practice |
| DSCR (6 months) | 2.56 | ✅ | ≥ 1.0 | CNDCEC 2022 §3.2 |
| 12M Sustainability | 0.60 | 🔴 | ≥ 1.0 | Art. 2086 c.c. |
| 90-Day Minimum Balance | €224,554 | ✅ | > Safety threshold | Internal |
| 6-Month Net Closing Balance | €49,160 | ✅ | > 0 | Internal |
| DSO | 73 days | ⚠️ | ≤ 52 days | CNDCEC 2022 §4.1 |
| Health indicators in amber | 4 of 8 | ⚠️ | — | — |

---

## 6. Known Limitations and Development Roadmap

### Limitation 1 — Revenue Horizon Beyond 60 Days

Invoice-based AR inflows provide 30–60 days of reliable forward
visibility. For weeks 7–13 of the forecast, inflows in a live
implementation would thin significantly as the invoice backlog
runs out. The current model uses a synthetic dataset with
pre-distributed invoices through September.

**Planned resolution:** Integration of the customer order backlog
(portafoglio ordini) as a secondary inflow layer for the outer
weeks of the horizon. This would extend reliable visibility to
the full 13-week range and materially improve the 12-month
sustainability calculation.

### Limitation 2 — Uniform Stress Test Across All Customers

The pessimistic scenario applies a flat +30 day delay to all
customers regardless of their individual credit risk profile.
This is methodologically conservative but analytically blunt —
a high-quality customer receives the same stress as a high-risk one.

**Planned resolution:** Weight the stress multiplier by customer
credit score from the Page 3 credit risk module. High-risk
customers (score > 60) receive +45 days; medium-risk (30–60)
receive +30 days; low-risk receive +15 days. This produces a
more realistic downside scenario and links the forecasting
engine directly to the credit risk analytics.
