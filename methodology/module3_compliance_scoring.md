# Module 3 — Regulatory Compliance, Predictive Scoring & Prescriptive Analytics

**Pages:** 05 DSCR · 06 RATING · 07 ACTIONS
**Focus:** Regulatory debt-coverage testing · The horizon effect · Altman Z'-Score decomposition · From indicators to executable decisions

---

## 1. The Core Question — One Debt, Two Verdicts

The Italian Insolvency Code asks a single question of every company board:
can this business service its debt? The dashboard answers it twice, and the
two answers disagree.

- **DSCR at six months: 2.56** ✅ — debt fully covered
- **Debt sustainability at twelve months: 0.56** 🔴 — structurally at risk

Same company. Same debt. Same balance sheet. The only variable that changed
is how far ahead the model looks.

Neither number is wrong, and neither is sufficient alone. This module
documents how both are built, why they diverge, and — in its final section —
how eight indicators across seven pages resolve into three decisions someone
can act on this week.

---

## 2. DSCR — The Regulatory Test

### 2.1 The Budget di Tesoreria Structure

The CNDCEC prescribes the **Budget di Tesoreria** as the instrument for
computing the DSCR used in early-warning assessment under D.Lgs. 14/2019.
The structure is not a free-form cash forecast — it is a defined sequence:

```
A   Opening cash balance                          =    €47,200
│
B   Operative inflows — AR schedule (6 months)    =   €516,200
    Estimated future inflows (not yet invoiced)   =         €0  ← see §8
│
C   Operative outflows (6 months)                 =  −€482,700
    ├── Tax obligations — F24                     =  −€103,000
    ├── Supplier payments — AP schedule           =  −€119,000
    └── Payroll and social contributions          =  −€260,700
│
D   Net operative cash flow          (B − C)      =    €33,500
│
F   Cash available for debt service  (A + D)      =    €80,700
│
G   Debt service — loans, leasing, interest       =   −€31,540
│
I   Projected closing balance        (F − G)      =    €49,160
```

**DSCR = F ÷ G = €80,700 ÷ €31,540 = 2.56** ✅
**Threshold: ≥ 1.0 (D.Lgs. 14/2019)**

The supplier line (€119,000) is the portion of open payables scheduled to
fall due inside the six-month budget window — a narrower figure than the
full open AP balance, which the monitoring pages report separately.

### 2.2 Why the Structure Separates Operative from Financial Outflows

The separation of row C (operative outflows) from row G (debt service) is the
methodological core of the framework, not a presentational choice.

The DSCR asks a specific question: *after the business has paid everything it
needs to pay in order to keep operating, is there enough left to service the
banks?* If loan repayments were folded into operating costs, the numerator
would no longer represent operative cash generation, and the ratio would
answer a different — and much less useful — question.

This is why row F (**A + D**) is the denominator of the business's survival
and the numerator of the DSCR: it is the cash that exists once operations
have been funded, and before the banks are paid.

### 2.3 The Numerator, Decomposed

The waterfall on Page 5 decomposes row F into its constituent movements —
opening balance, customer inflows, then each outflow category in turn. The
purpose is not decoration: a DSCR of 2.56 is a single number that conceals
five moving parts, and management needs to see **which** of them is doing the
work.

In this dataset the answer is unambiguous: customer collections (€516,200)
dominate everything else, and payroll (€260,700) is by far the largest
outflow — larger than tax and supplier payments combined. The DSCR is
therefore highly sensitive to collection performance and almost inelastic
on the cost side, because payroll cannot be deferred. That single observation
tells the treasurer where to apply pressure, and it is invisible in the
ratio itself.

### 2.4 The Denominator — Real Amortisation, Not Averages

Debt service (row G) is built from the actual amortisation schedule of three
instruments, not from an annualised average:

| Instrument | Type | Cadence |
|------------|------|---------|
| Mediobanca — steel inventory revolving facility | Revolving credit | Quarterly |
| Intesa Sanpaolo — CNC machinery loan (2022) | Amortising loan | Monthly |
| UniCredit — forklift lease (2023) | Finance lease | Monthly |

Each instalment carries its own capital/interest split, and the split shifts
over time as the loans amortise — capital rises, interest falls, instalment
constant. The model reads the split from the schedule rather than assuming
a flat ratio, because the DSCR denominator is the *total* instalment
(capital + interest), while only the interest portion reaches the P&L.
Confusing the two is one of the most common errors in simplified DSCR
implementations.

**Six-month debt service: €31,540** (six monthly loan and lease instalments
plus the quarterly revolving-facility charges falling inside the window).

---

## 3. The Horizon Effect — Six Months vs Twelve

### 3.1 The Second Test

Beyond the six-month DSCR, Art. 2086 c.c. obliges the board to assess
whether the business is a going concern over a meaningful horizon. The
dashboard therefore runs a second, longer test:

```
12M Sustainability =
(Cash balance + 12-month projected inflows)
──────────────────────────────────────────────────────
(12-month operative outflows + 12-month debt service)

Threshold: ≥ 1.0 sustainable  |  < 1.0 structural concern
```

**Result: 0.56** 🔴

### 3.2 Why the Two Diverge

The six-month window is flattered by an artefact of how visibility works.

Forward inflows in this model are built from the **invoice backlog** — work
already delivered and billed. That backlog populates roughly the first two
quarters densely. Push the horizon out to twelve months and the backlog
thins: the model is now being asked about revenue from work that has not
yet been invoiced, and in some cases not yet won.

Outflows, meanwhile, do not thin. Payroll, tax, and debt service run at full
weight across all twelve months regardless of what the order book looks like.

So the ratio deteriorates not because the company's position is worsening,
but because the visible inflows run out before the certain outflows do.
**The horizon is not a parameter of the calculation. It is the calculation.**

### 3.3 Why Both Numbers Are Published

The temptation is obvious: report the metric the regulation names (DSCR),
show 2.56, and stop. That would be technically compliant and analytically
dishonest.

The two figures answer different questions, and a board needs both:

- **DSCR (6M)** answers the *regulatory* question — is there an early-warning
  signal under Art. 25-novies? No.
- **12M sustainability** answers the *governance* question — is the business
  a going concern on a horizon the board is accountable for? Not on current
  visibility.

Reporting the first without the second produces exactly the failure mode this
whole project was built to expose: a number that is true, comforting, and
incomplete.

**Important caveat on this specific figure:** the 0.56 result is partly
driven by a known limitation of the model, documented in §8. It should be
read as a **lower bound under current visibility**, not as a forecast.

---

## 4. Altman Z'-Score — Predictive Scoring

### 4.1 The Model

The dashboard uses the **Z'-Score** — Altman's 1983 revision for private,
non-listed manufacturers. This matters: the original 1968 Z-Score uses the
*market* value of equity, which does not exist for an SME. The Z' variant
substitutes book value and recalibrates every coefficient accordingly.

```
Z' = 0.717·X1 + 0.847·X2 + 3.107·X3 + 0.420·X4 + 0.998·X5
```

| Zone | Range | Interpretation |
|------|-------|----------------|
| Safe | Z' > 2.90 | Low insolvency risk |
| Grey | 1.23 ≤ Z' ≤ 2.90 | Monitoring warranted |
| Distress | Z' < 1.23 | High insolvency risk |

**Result: Z' = 2.33 → Grey Zone** 🟡

### 4.2 The Five Components

| # | Component | Ratio | Coefficient | Value | Contribution |
|---|-----------|-------|-------------|-------|--------------|
| X1 | Structural liquidity | Working capital / Total assets | 0.717 | 0.19 | 0.14 |
| X2 | Cumulative profitability | Retained earnings / Total assets | 0.847 | 0.12 | 0.10 |
| X3 | Asset productivity | EBIT / Total assets | **3.107** | **0.09** | 0.29 |
| X4 | Financial leverage | Equity / Total liabilities | 0.420 | 0.83 | 0.35 |
| X5 | Asset turnover | Sales / Total assets | 0.998 | 1.46 | 1.45 |
| | | | | **Z' =** | **2.33** |

The five contributions sum to the reported score. This reconciliation is not
cosmetic — it is a standing validation check, and it is what caught a
significant modelling error during development (Challenge 8 in the project
README).

### 4.3 Reading the Decomposition — Weight Is Not the Same as Contribution

A score is a verdict. A decomposition is a plan. The two readings of this
table point in opposite directions, and both are true:

**By contribution**, X5 (asset turnover) carries the score: 1.45 of 2.33 —
62% of the total. The company generates €1.46 of sales per euro of assets,
which is genuinely good. Strip X5 out and the business is in the distress
zone.

**By leverage**, X3 (operating profit on assets) is the lever. It is the
company's *weakest* ratio (0.09) and it carries the *heaviest* coefficient
(3.107). Every 0.01 of improvement in X3 adds 0.031 to the score —
**3.1× what the same improvement in X5 would deliver, 7.4× what X4 would.**

The gap to the safe zone is 2.90 − 2.33 = **0.57**. That is the number to
close, and X3 is the cheapest place to buy it.

### 4.4 The Structural Link to Module 2

X3 and X5 share a denominator: **total assets.** And for a manufacturer
carrying 130 days of inventory (Module 2, §5), inventory is one of the
largest items on that balance sheet.

This connects the two modules directly. The working-capital finding — that
the 146-day cash conversion cycle is inventory-driven, not collection-driven
— is not just a liquidity insight. Running the same €1.8M of sales on a
leaner asset base lifts **both** X3 and X5 simultaneously, because both are
normalised by total assets.

One structural caveat, stated precisely: **converting inventory into idle
cash is an asset swap.** Total assets are unchanged, and the Z' does not
move — although the liquidity runway improves immediately. The score
responds when the balance sheet actually shrinks: when the released capital
repays debt (which also lifts X4), or when the same revenue is supported by
fewer assets.

So the same lever serves two different objectives on two different
timescales — and the distinction between them is exactly the kind of thing
a single headline score hides.

### 4.5 What the Z'-Score Is Not

It is not a probability of default, and it is not used as one here. It was
calibrated on US manufacturing data from a different era and a different
capital-structure environment; Italian SMEs finance themselves differently
(heavier supplier credit, thinner equity). It is used in this system as a
**directional risk indicator and a decomposition tool** — a structured way
to ask *which* part of the balance sheet is weak — not as a verdict on
solvency.

---

## 5. Structural Indices — The Eight Benchmarks

Page 6 evaluates eight structural indices against regulatory and banking
thresholds simultaneously:

| Index | Threshold | Value | Status |
|-------|-----------|-------|--------|
| Altman Z'-Score | > 2.90 | 2.33 | 🟡 |
| Cash Conversion Cycle | < 60 days | 146 days | 🔴 |
| Fixed Cost Coverage | > 2× | 1.1× | 🟡 |
| DPO (CNDCEC) | > 30 days | 57 days | ✅ |
| DSCR | ≥ 1.0 | 2.56 | ✅ |
| DSO (CNDCEC) | ≤ 52 days | 73 days | 🟡 |
| Leverage | < 2.5× | 1.21× | ✅ |
| PFN / EBITDA | < 3.5× | 2.52× | ✅ |

**4 green · 3 amber · 1 red.**

> **Note on the two eight-indicator sets.** This is the *structural* index
> panel (Page 6), which is a different set from the *health* KPI panel on
> Page 1 (Module 1, §2). The two overlap on DSCR, DSO, Leverage and
> PFN/EBITDA, but Page 1 additionally monitors liquidity runway, the 90-day
> minimum balance and concentration risk, while Page 6 adds the Altman
> Z'-Score and the cash conversion cycle. Page 1 answers *"is cash at risk
> this month?"*; Page 6 answers *"is the structure sound over the cycle?"*

The panel is deliberately mixed in origin — regulatory thresholds
(DSCR ≥ 1.0), banking covenants (Leverage < 2.5×, PFN/EBITDA < 3.5×),
academic models (Altman > 2.90) and treasury practice (Fixed Cost Coverage
> 2×) sit side by side. An SME is judged by all four constituencies at once,
often in the same meeting, and the dashboard is designed to show what each
of them sees.

The reading here is coherent and worth stating plainly: **the balance sheet
is sound; the cycle is not.** Leverage, debt sustainability and debt coverage
are all comfortable. What fails is the speed at which the business converts
work into cash.

---

## 6. Prescriptive Analytics — From Indicators to Decisions

### 6.1 The Design Problem

Six pages of analytics produce dozens of measures. A finance director has
perhaps ten minutes. The gap between "the dashboard computed something" and
"someone did something" is where most financial reporting quietly fails.

Page 7 exists to close that gap. Its design constraint was deliberately
harsh: **every month, the entire system must resolve into no more than three
actions, and each one must be executable without further analysis.**

### 6.2 Anatomy of a Recommendation

Every recommendation carries four mandatory elements. If any one is missing,
it is not a recommendation — it is an observation:

1. **A named counterparty** — not "receivables", but *Alfa Meccanica*
2. **A concrete verb** — call, monitor, negotiate
3. **A quantified impact** — in euros, and in days
4. **A deadline** — by when, before the window closes

### 6.3 The Three Tiers

| Tier | Trigger logic | Time horizon |
|------|---------------|--------------|
| 🔴 **Urgent** | Largest overdue AR block held by a customer above the risk-score threshold | Act within 5 days |
| 🟡 **Important** | Collision between the forecast's minimum-balance week and a fixed outflow (F24, payroll) | Act within 20 days |
| 🟢 **Opportunity** | Largest AP invoice falling due inside the tightest forecast week — a deferral that buys headroom at no cost | Act within 10 days |

### 6.4 This Month's Output

**🔴 URGENT — Chase Alfa Meccanica.**
Three overdue invoices totalling **€62,800**. Call the accounts contact today
and secure confirmation of payment by Friday.
*Impact: +€62,800 of liquidity within 7 days. Deadline: 5 days.*

**🟡 IMPORTANT — Monitor week 4.**
Week 4 carries the minimum projected balance of the 13-week horizon. Ensure
the Gamma Costruzioni collection lands before the F24 outflow on the 16th.
*Impact: avoids an estimated €8,000 overdraft. Deadline: 20 days.*

**🟢 OPPORTUNITY — Negotiate a supplier deferral.**
Acciai Lombardi has an €18,500 invoice due 29 July, falling in the tightest
week. Request 30 additional days.
*Impact: +€18,500 of headroom for 30 days. Deadline: 10 days.*

### 6.5 Every Recommendation Is Traceable

The prescriptive layer is not a separate model. It is a **consumer of every
prior page**, and each recommendation can be traced back to the analytics
that produced it:

| Recommendation | Derived from |
|----------------|--------------|
| Chase Alfa Meccanica | Page 3 — AR aging × composite risk score |
| Monitor week 4 | Page 2 — 13-week forecast minimum · Page 4 — F24 schedule |
| Negotiate Acciai Lombardi | Page 4 — AP schedule × Page 2 — tightest forecast week |

This is the architectural point of the whole system: the six analytical pages
are not the deliverable. They are the evidence base for the seventh.

### 6.6 Temporal Validity — A Recommendation Must Be Executable

An early version of the opportunity tier proposed negotiating a payment
extension on an invoice that had **already fallen due.** The logic was sound
(largest invoice in the tightest week); the output was useless.

Prescriptive analytics that reference past events are worse than no
prescriptive layer at all: one unexecutable recommendation destroys
confidence in every recommendation beside it. The engine now applies a
temporal validity filter — opportunity recommendations consider only
obligations with a **future** due date, evaluated against the report date.

The principle generalises: *a recommendation the reader cannot act on is not
a weak recommendation. It is a defect.*

### 6.7 The Snapshot — A Reconciliation Layer

Page 7 also carries a summary table listing every headline indicator, its
value, its status, and **the page it comes from.** It serves two audiences at
once: an executive who wants the month in ten lines, and an analyst who wants
to know where a number came from — one click away.

In practice it became something else as well: a **reconciliation surface.**
Every headline figure in the project appears in one place, which makes
internal inconsistency immediately visible. More than one modelling error in
this project was caught not by inspecting a formula, but by noticing that two
pages disagreed about the same number. Where a quantity is windowed on one
page and shown in full on another — total open payables versus the six-month
scheduled subset, for example — the snapshot reports the full balance and
leaves the scope difference explicit rather than hidden.

---

## 7. Key Results

| Metric | Value | Status | Benchmark | Source |
|--------|-------|--------|-----------|--------|
| DSCR (6 months) | 2.56 | ✅ | ≥ 1.0 | CNDCEC 2022 |
| 12-Month Sustainability | 0.56 | 🔴 | ≥ 1.0 | Art. 2086 c.c. |
| Cash available for debt service (F) | €80,700 | — | — | Budget di Tesoreria |
| 6-month debt service (G) | €31,540 | — | — | Amortisation schedule |
| Projected closing balance (I) | €49,160 | ✅ | > 0 | Budget di Tesoreria |
| Altman Z'-Score | 2.33 | 🟡 | > 2.90 | Altman 1983 — private firms |
| Gap to safe zone | 0.57 | — | — | Derived |
| Leverage | 1.21× | ✅ | < 2.5× | Banking practice |
| PFN / EBITDA | 2.52× | ✅ | < 3.5× | Banking practice |
| Fixed Cost Coverage | 1.1× | 🟡 | > 2× | Treasury practice |
| Structural indices (Page 6) | 4 ✅ · 3 🟡 · 1 🔴 | ⚠️ | — | — |
| Actions generated this month | 3 — 1 urgent · 1 important · 1 opportunity | — | ≤ 3 | Page 7 |

---

## 8. Known Limitations and Development Roadmap

### Limitation 1 — The 12-Month Test Inherits the Revenue-Horizon Gap

The 0.56 sustainability result is directly affected by the limitation
documented in Module 1: forward inflows are built from the invoice backlog,
and the model has no order-book layer. Beyond roughly six months the inflow
side thins while the outflow side does not — which mechanically depresses the
ratio.

The line **"Estimated future inflows: €0"** in the cash budget (§2.1) is that
limitation made visible rather than hidden. It is deliberately not
back-filled with an assumption.

**Planned resolution:** integrate the customer order backlog (*portafoglio
ordini*) as a second inflow layer for the outer months. This is the single
highest-value improvement on the roadmap — it would convert the 12-month test
from a lower bound into a forecast.

### Limitation 2 — The Z'-Score Refreshes Annually

The Altman inputs come from the balance sheet and P&L, which update once a
year, while every other metric in the dashboard refreshes monthly. Between
annual accounts the score is effectively frozen: it cannot react to the
working-capital deterioration that the rest of the system is tracking in
real time.

**Planned resolution:** compute a rolling estimate of the Altman inputs from
the monthly operational data — inventory, receivables and payables are all
already in the model — so the score moves with the cycle rather than with
the accounting calendar.

### Limitation 3 — The Recommendation Engine Has No Memory

The three recommendations are produced by explicit business rules
(§6.3) — transparent, auditable, and entirely static. The engine cannot tell
whether last month's urgent action was executed, whether the predicted impact
materialised, or whether a customer who was chased twice needs escalating
instead of chasing a third time.

**Planned resolution:** persist recommendation outcomes month over month and
close the loop — measuring predicted impact against realised impact. That
feedback is what would turn a rule-based recommender into an engine that
learns which levers actually work in this particular business.

### Limitation 4 — Rules Encode a Single Company's Priorities

The tier thresholds (5 / 20 / 10 days) and the trigger logic reflect one
plausible set of operating priorities for a €1.8M manufacturer. A different
business — seasonal, project-based, or export-heavy — would prioritise
differently. In a live deployment these rules are a configuration layer to
be calibrated with the client, not a fixed feature of the system.
