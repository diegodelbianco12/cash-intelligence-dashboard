# Module 2 — AR/AP Risk Analysis & Working Capital

**Pages:** 03 CLIENTS · 04 SUPPLIERS
**Focus:** Receivables concentration and credit risk · Composite customer scoring · Working capital cycle decomposition · Regulatory early-warning monitoring (Art. 25-novies CCII)

---

## 1. The Core Question — Size Is Not Risk

Open the receivables ledger and one fact dominates: the largest customer,
Alfa Meccanica Spa, carries €373,644 in open receivables — the single
biggest exposure in the portfolio. Standard reporting stops there:
biggest customer, biggest number, watch that account.

But the customer with the **highest risk score is not the biggest
customer.** Alfa scores 61/100. A smaller account, Gamma Costruzioni Srl,
scores 71/100 — the worst in the portfolio — on roughly half the open
exposure.

These are two different risks:

- **Concentration risk** — how much of the business depends on one
  name (Alfa)
- **Behavioural risk** — how unreliably a customer actually pays (Gamma)

A ranking by revenue captures neither cleanly. This module quantifies
both, then applies the same discipline to the money going out —
payables and the working capital cycle.

---

## 2. Accounts Receivable — Concentration & Exposure

### 2.1 Concentration Risk

Concentration risk measures each customer's share of total receivables
exposure. The metric is bounded 0–1 and benchmarked against a
prudential ceiling:

```
Concentration Risk (customer) = Customer receivables exposure
                                ───────────────────────────────
                                Total portfolio receivables exposure
```

| Range | Status | Interpretation |
|-------|--------|----------------|
| < 25% | ✅ Green | Diversified — no single-customer dependency |
| 25–40% | ⚠️ Amber | Elevated — monitor the account closely |
| > 40% | 🔴 Red | Critical — one customer moves the whole cash position |

**Test result: top customer (Alfa Meccanica) at 37% → Amber.**

More than a third of the receivables portfolio rides on one counterparty.
This does not show up in the P&L — Alfa is a good, high-volume customer.
It shows up in cash the moment that single account slows down: a 30-day
delay from Alfa alone is enough to collapse the 32-day liquidity runway
documented in Module 1.

### 2.2 AR Aging Distribution

The aging analysis distributes total open receivables (€837,416)
across overdue bands:

| Band | Amount | Share |
|------|--------|-------|
| Current (not yet due) | ~€520,000 | ~62% |
| Overdue 1–30 days | ~€140,000 | ~17% |
| Overdue 31–60 days | ~€90,000 | ~11% |
| Overdue 90+ days | ~€90,000 | ~11% |

Roughly **38% of the portfolio is past due**, and the most severe band
(90+ days) holds ~€90,000 — receivables where recovery risk rises
materially and, under the CNDCEC framework, provisioning questions begin.
The aging feeds the collection-prioritisation logic: overdue amount
weighted by customer risk score determines which accounts to chase first.

*(Note: the 61–90 day band carries no balance in the current snapshot —
a realistic month-to-month outcome, not a data gap.)*

### 2.3 The Risk Map — Reading Exposure and Behaviour Together

The scatter plot maps every customer on two axes:

- **X — average invoice size (€):** single-invoice exposure the customer
  represents
- **Y — average payment delay (DSO, days):** how slowly the customer pays

The quadrant that matters is top-right: **large invoices paid late** —
high value at risk for a long time. A customer with small invoices paid
late is an annoyance; a customer with large invoices paid late is a
treasury problem. The map lets management see the combination at a glance,
rather than reading two separate columns and joining them mentally.

---

## 3. Composite Credit Risk Score

### 3.1 What the Score Combines

Each customer receives a single 0–100 risk score that blends the
behavioural and structural dimensions visible in the client table.
The score is directional — higher means riskier — and combines:

| Dimension | Column | Directional logic |
|-----------|--------|-------------------|
| Payment slowness | DSO per customer | Higher DSO → higher risk |
| Payment reliability | Delay frequency (share of invoices paid late) | Late more often → higher risk |
| Payment volatility | Delay variance | Erratic, unpredictable timing → higher risk |
| Portfolio exposure | Concentration risk | Larger share of the book → a given behaviour matters more |

The first three describe *how the customer behaves*; concentration acts
as an **amplifier** — the same late-payment pattern is more dangerous
from a customer who represents a third of the portfolio than from one
who represents one percent. Each dimension is normalised to a common
0–100 scale before being combined into the composite.

### 3.2 Why the Composite Beats Any Single Metric

The comparison that motivates the whole design:

| Customer | Open receivables | Concentration | DSO | Composite score |
|----------|------------------|---------------|-----|-----------------|
| Alfa Meccanica | €373,644 | 37% | 27 days | **61** |
| Gamma Costruzioni | €185,172 | 20% | 59 days | **71** |

Rank by size → Alfa is the concern. Rank by DSO → Gamma. Rank by
concentration → Alfa. The composite resolves the conflict into one number
and correctly identifies Gamma as the highest behavioural risk: it pays
effectively every invoice late (delay frequency ≈ 1.0) and carries the
highest DSO in the book, even though it is smaller than Alfa.

Aggregated across the portfolio, receivables in the high-risk tier
(score > 70) total **€185,172 — 22.1% of the open book sitting with the
least reliable payers.** That single figure reframes the collections
conversation: not "who owes the most," but "how much of what we're owed
sits with customers who don't pay reliably."

### 3.3 Handling Customers With No Payment History

Several customers (Zeta, Eta, Epsilon) have no recorded paid-invoice
history in the current dataset. For these, the payment-behaviour
dimensions are **undefined, not zero** — the model returns a blank
rather than a value.

This distinction is deliberate. A DSO of 0 would mean "collects
instantly — the best possible payer," which is the opposite of
"no data yet." Coercing missing history to zero would silently flag the
least-known customers as the safest, exactly inverting the intended
signal. The behavioural columns therefore remain blank until a payment
pattern exists, and those customers are treated as *unscored* rather than
*low-risk*. This mirrors the scenario-integrity principle from Module 1:
missing customer history must never be allowed to produce a falsely
reassuring number.

---

## 4. Accounts Payable & DPO

### 4.1 DPO — CNDCEC Formula, Symmetric With DSO

Days Payable Outstanding measures how long the company takes to pay its
own suppliers. To keep it methodologically consistent with the DSO from
Module 1, both sides are expressed VAT-inclusive — payables on the
balance sheet carry VAT, purchased-goods cost does not:

```
DPO = (Open Payables [VAT-inclusive])
      ──────────────────────────────────── × 365
      (Annual Purchases × 1.22)
```

**Test result: DPO = 57 days.**

The company pays suppliers on a 57-day cycle. Read alone this is
unremarkable; its significance appears only next to DSO (Section 5).

### 4.2 Scheduled Outflows by Week

The weekly outflow view separates two components rather than averaging
them into a single burn rate:

- **Fixed weekly costs** — the recurring base (~€11K/week in the test
  data)
- **Supplier invoice outflows** — actual AP due dates from the ledger,
  which spike unevenly (a €48K concentration in week 1, further peaks in
  weeks 3–4 and week 8)

Averaging these would hide the peaks. The explicit-schedule approach shows
management exactly which weeks carry the heavy payables load — the same
philosophy applied to the F24 tax calendar in Module 1.

### 4.3 Payment Urgency

The supplier table ranks open payables by days-to-due, flagging overdue
items. In the test snapshot the ledger is dominated by past-due invoices
(materie prime, componenti, lavorazioni), which is itself a signal:
a company stretching supplier payments is financing itself through its
suppliers — cheap, but a relationship and reliability risk if it persists.

---

## 5. Working Capital — The Cash Conversion Cycle

### 5.1 The Identity and Its Decomposition

The Cash Conversion Cycle measures how many days elapse between paying for
materials and collecting from customers — the length of time cash is
trapped inside operations:

```
CCC = DSO + DIO − DPO
    =  73 + 130 −  57
    = 146 days
```

Where:

- **DSO = 73 days** — time to collect from customers
  (Module 1, CNDCEC VAT-inclusive formula)
- **DIO ≈ 130 days** — inventory holding period, from balance-sheet
  inventory in the annual accounts against cost of goods
- **DPO = 57 days** — time taken to pay suppliers

**Test result: CCC = 146 days → Critical.**

### 5.2 Where the Cash Is Actually Trapped

This is the analytical centre of the module. The naïve reading of working
capital stress is "we collect slower than we pay" — the DSO/DPO gap.
That gap here is only **+16 days** (73 − 57), and the dashboard flags it
as *within norm*. On that metric alone, the company looks fine.

The CCC tells the real story. At 146 days, the dominant driver is not the
collection-vs-payment gap — it is **inventory (≈130 days).** A made-to-order
mechanical manufacturer buys steel and components, holds them through a
long production cycle, ships finished goods, and only then starts the
73-day collection clock. Cash is committed at the start and recovered
almost five months later.

This is why the tension documented in Module 1 exists — DSCR 2.56 but only
32 days of runway. The money is real and the business is profitable;
it is simply locked in inventory and receivables rather than sitting in
the account. **Working capital, not profitability, is the constraint.**
The levers follow directly from the decomposition: the +16-day collection
gap is not the priority — shortening the inventory-holding period, and
tightening collection on the highest-risk accounts (Section 3), is.

### 5.3 DSO/DPO Over Time

The trend chart tracks collection days (customers) against payment days
(suppliers) across the observation window. Two patterns stand out:
customer collection days are **volatile**, spiking well above the
reference line in several periods, while supplier payment days are
**stable and low.** The asymmetry is the working capital problem in one
picture — inflows arrive unpredictably, outflows leave on a fixed
schedule. It also validates the forecasting design choice from Module 1:
holding payables constant across scenarios and shifting only receivables
reflects exactly this observed behaviour.

---

## 6. Regulatory Early Warning — Art. 25-novies CCII

### 6.1 The Four Monitored Signals

The Italian Insolvency Code (D.Lgs. 14/2019) defines specific, measurable
early-warning triggers under Art. 25-novies. The dashboard monitors all
four automatically, each against its statutory time and quantitative
threshold:

| Creditor | Time threshold | Quantitative threshold | Reference | Status |
|----------|----------------|------------------------|-----------|--------|
| Employees (payroll) | Overdue > 30 days | > 50% of monthly payroll | lett. a) | ✅ OK |
| Trade suppliers | Overdue > 90 days | Overdue > not-overdue | lett. b) | ✅ OK |
| Revenue Agency (VAT) | At liquidation | Unpaid VAT > €5,000 | lett. c) | 🔴 ALARM |
| INPS (social security) | Overdue > 90 days | Omitted > €5,000 (micro) / €11,000 (other) | lett. d) | ✅ OK |

### 6.2 The Triggered Signal — VAT

Three of four signals are within their statutory limits. The fourth is not:

**Unpaid overdue VAT: €8,200 — above the €5,000 threshold of
Art. 25-novies lett. c) → formal early-warning signal.**

Under the CCII this is not a discretionary internal flag: crossing the VAT
threshold is one of the conditions that obliges the qualified creditor
(here the Revenue Agency) to notify the company, and obliges the board to
react under its Art. 2086 c.c. duty to detect distress early. The value of
automating it is timing — the signal appears in the monthly report the
moment the threshold is crossed, months before it would otherwise surface
as a formal notice. Early warning is only useful if it is early.

---

## 7. Key Results

| Metric | Value | Status | Benchmark | Source |
|--------|-------|--------|-----------|--------|
| Total open receivables | €837,416 | — | — | AR ledger |
| High-risk receivables (score > 70) | €185,172 (22.1%) | ⚠️ | — | Composite score |
| Top-customer concentration | 37% | ⚠️ | < 25% | Best practice |
| Highest customer risk score | 71 / 100 | 🔴 | — | Composite score |
| Receivables past due | ~38% of portfolio | ⚠️ | — | AR aging |
| DPO | 57 days | — | — | CNDCEC formula |
| Gap DSO − DPO | +16 days | ✅ | — | Within norm |
| Cash Conversion Cycle | 146 days | 🔴 | < 60 days | DSO + DIO − DPO |
| Art. 25-novies signals triggered | 1 of 4 (VAT) | 🔴 | 0 | CCII lett. c) |

---

## 8. Known Limitations and Development Roadmap

### Limitation 1 — Credit Scores Require Payment History

Customers with no paid-invoice history are unscored rather than scored
(Section 3.3). This is the correct handling of missing data, but it means
new customers carry no behavioural signal until a payment pattern
accumulates. In a live implementation, a cold-start proxy — external
commercial credit data (e.g. Cerved/CRIF scores) — would seed an initial
risk estimate before internal history exists.

### Limitation 2 — Inventory Is Modelled at Aggregate Level

The DIO component of the CCC (≈130 days) is computed at portfolio level
from balance-sheet inventory against cost of goods, not decomposed by
product line or material type. A production-grade version would break
inventory into raw materials, work-in-progress, and finished goods, each
with its own holding period — turning the single 130-day figure into an
actionable map of *where* in the production cycle cash is trapped.

### Limitation 3 — Links Into the Forecast Stress Test

This module produces the customer risk scores that Module 1's stress
scenario currently lacks. The planned integration (noted in Module 1)
weights the pessimistic-scenario delay by each customer's score —
high-risk customers (score > 60) receive a larger stress delay than
low-risk ones — replacing the current uniform +30-day assumption with a
risk-differentiated downside. Module 2 is the prerequisite that makes that
upgrade possible.
