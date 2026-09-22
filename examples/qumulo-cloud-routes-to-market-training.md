# QUMULO CLOUD ROUTES-TO-MARKET — REP CERTIFICATION GUIDE

---

## 📍 INSTRUCTIONS

| | |
| :-: | :-: |
| **Audience** | AEs and SEs quoting Qumulo cloud (CNQ, ANQ) |
| **Scope** | Cloud only — excludes on-prem, Cisco, HPE, Arrow, SMC |
| **Governing skill** | `qumulo-cloud-sales-quoting` — the only place these rules are reduced to a decision procedure. If this guide and the skill ever disagree, the skill wins. |
| **Use it** | Before you touch the Cloud Sizing Tool on a cloud opportunity — not after the quote is wrong |
| **QS coordinates** | Section 3 (Process & Tools) — 3.1, 3.3, 3.4 |

> **For Internal Use Only.** This guide reflects the routes-to-market and gotchas as governed by the `qumulo-cloud-sales-quoting` skill. Two of the positions cited below are dated (a Slack taxonomy post from May 2024, a RevOps position from July 2026) — confirm they're still current with RevOps before you rely on them in a live cycle.

---

## 🗺️ THE FOUR INDEPENDENT CHOICES

Every cloud deal is four independent choices. Get all four right, and the SFDC field set falls out as a pure function of them — you don't guess it, you derive it.

| Choice | Options |
| :-: | :-: |
| **1. Product** | CNQ · ANQ |
| **2. Marketplace** | AWS · Microsoft |
| **3. Transaction path** | Direct-in-marketplace · Indirect-in-marketplace (CSP/CPPO/MPO) · Indirect-outside-marketplace (reseller paper) · Direct-outside-marketplace |
| **4. Payment model** | Prepaid Commit (PPC) · Custom schedule · PayGo |

**Hard constraint:** ANQ = Azure Marketplace only. There is no AWS path for ANQ — if the customer is AWS-only, the product choice is CNQ or the deal doesn't qualify.

---

## 📋 THE SINGLE MOST-MISSED RULE

> **In-marketplace** → Channel Partner = the marketplace. Reseller = the partner.
> **Outside-marketplace** → the two reverse. Channel Partner = the partner. Reseller-of-Hyperscalers = the marketplace.

Reps get this backwards constantly because the field names don't change — only which side of the deal they point to changes, and it flips exactly once, based on whether money moves through the marketplace.

**Worked example.** Same partner, same customer, two structures:

- *CPPO through AWS Marketplace (in-marketplace):* Channel Partner = **AWS**. Reseller-of-Hyperscalers = **the partner**.
- *Reseller paper outside AWS Marketplace (outside-marketplace):* Channel Partner = **the partner**. Reseller-of-Hyperscalers = **AWS**.

If you can't say which side of that line your deal is on, you can't fill out SFDC correctly — qualify the transaction path first, every time.

---

## 🗺️ ROUTE-BY-ROUTE

### A1 — Direct private offer (PPC)

**When to qualify a deal here:** customer buys direct, no partner in the transaction, prepaid commit.

**Process:** Cloud Sizing Tool (`cloud.qumulo.sh`) → Guided Quote → approval → JIRA to `RevOps@qumulo.com` → RevOps (Patrick Keegan) builds the marketplace offer → customer accepts → cluster meters against it.

**Gotchas:**
- Offer expiration defaults to **2 weeks** — don't let it lapse mid-cycle.
- Customer discount only. **Partner discount fields must be 0/blank** — there's no partner in this path.

**Resources:** [PPC Quote](https://qumulo.atlassian.net/wiki/spaces/ST/pages/3745710119) · [Cloud Deal Categorization](https://qumulo.atlassian.net/wiki/spaces/ST/pages/3745120287) · [Guided Quoting + Sizing Tool](https://qumulo.atlassian.net/wiki/spaces/ST/pages/3240329217)

### A2 — Partner inside the marketplace (CPPO / MPO / CSP)

**When to qualify a deal here:** a partner is involved, but money still flows through the marketplace and the partner takes margin.

**Process:** same marketplace mechanics as A1, structured so the quote supports **Partner Markup vs. Partner Margin** (markup must be ≤ partner off-list). You need the partner's marketplace seller ID before you can build the quote.

**Gotchas:**
- **MPO is not allowed where a CSP is the cloud provider.** Confirm which one you're actually structuring before you build it.
- Best plain-English taxonomy of CPPO vs. MPO vs. CSP is still [Sean Gwaltney's Slack post](https://qumulo.enterprise.slack.com/archives/C4UEYNSSU/p1717170130617329) — it's **May 2024 vintage**, so verify it hasn't been superseded before you lean on it in front of a customer or partner.

### A3 — Outside marketplace (direct paper or reseller/DVAR)

**When to qualify a deal here:** genuinely no marketplace path exists. This is the exception path, not a default.

**RevOps position (Loren McWethy, 2026-07-13):** don't use this route unless there is no marketplace path available.

**Gotcha — the one that burns deals:** outside-marketplace prepay **cannot fall through to PayGo** when the balance hits $0. If a customer wants a prepaid balance with a PayGo safety net after it's exhausted, that only works in-marketplace — don't sell a fallback this path can't deliver.

### A4 — PayGo

Two distinct sub-paths — don't conflate them:

| | Public self-serve | Private discounted offer |
| :-: | :-: | :-: |
| **Who accepts the offer** | Customer, directly on the public listing | Rep builds a private offer |
| **SFDC logging** | Purchase Type = **"Public Offer - Pay-Go"** (forecasting only) | Standard PayGo quote flow |
| **Discount** | Zero — it's the public rate | Discounted — requires **Pay-Go Discount Validity Term** |
| **Term value** | N/A | Term stays at **1** |

**Resource:** [Pay-Go Quote doc](https://qumulo.atlassian.net/wiki/spaces/ST/pages/3745775635)

---

## 📊 THE SFDC FIELD LOOKUP — HOW TO DERIVE IT

The 36-row lookup returns six fields: **Deployment Target, Purchase Type, Payment Model, Channel Partner, PO/Billing Partner, Reseller-of-Hyperscalers.** Don't memorize 36 rows — derive the answer from the four choices plus the reversal rule above. Pull the authoritative row from the Guided Quote tool or RevOps; the rows below are worked examples to show the derivation, not a reproduction of the live table.

| Route | Deployment Target | Payment Model | Channel Partner | Reseller-of-Hyperscalers |
| :-: | :-: | :-: | :-: | :-: |
| A1 — Direct private offer | AWS or Azure | PPC | The marketplace | — (no partner) |
| A2 — CPPO in-marketplace | AWS or Azure | PPC / Custom | The marketplace | The partner |
| A3 — Reseller paper outside marketplace | AWS or Azure | Custom schedule | The partner | The marketplace |
| A4 — PayGo (public self-serve) | AWS or Azure | PayGo | The marketplace | — (no partner) |

Notice A1 and A2 share the same Channel Partner value (the marketplace) — the field set doesn't distinguish "no partner" from "partner, but in-marketplace." What distinguishes them is Reseller-of-Hyperscalers: blank for A1, populated for A2. That's the tell if you're staring at a filled-out record and trying to reconstruct which route it came from.

---

## 📋 CONSOLIDATED GOTCHAS — PRE-QUOTE CHECKLIST

Run this before you open the Guided Quote tool:

- [ ] Confirmed transaction path (in- vs. outside-marketplace) **before** filling Channel Partner / Reseller-of-Hyperscalers — the reversal rule depends on it.
- [ ] If ANQ: confirmed the customer has an Azure footprint — there is no AWS path for ANQ.
- [ ] If A1 direct: partner discount fields are 0/blank.
- [ ] If A1 direct: offer expiration (2-week default) fits the customer's timeline.
- [ ] If A2 partner-in-marketplace: have the partner's marketplace seller ID before building.
- [ ] If A2 and MPO is on the table: confirmed the cloud provider isn't a CSP (MPO isn't allowed there).
- [ ] If A3 outside-marketplace: confirmed there's genuinely no marketplace path — this is the exception, not the default.
- [ ] If A3 with a prepaid balance: didn't promise a PayGo fallback at $0 — that only exists in-marketplace.
- [ ] If A4 public self-serve: logged as Purchase Type "Public Offer - Pay-Go," zero discount, for forecasting only.
- [ ] If A4 private discounted: Pay-Go Discount Validity Term is set, and the term is 1.

---

## 📋 CERTIFICATION — KNOWLEDGE CHECK

Pass bar: 8/8. This is a process gate, not a partial-credit exercise — a rep who gets the reversal rule wrong on one live deal creates rework for RevOps and a bad partner experience.

**1.** A partner resells a CNQ deal to a customer through AWS Marketplace via CPPO. What goes in Channel Partner, and what goes in Reseller-of-Hyperscalers?
*Channel Partner = AWS (the marketplace). Reseller-of-Hyperscalers = the partner. In-marketplace, so the rule runs un-reversed.*

**2.** Walk the process for a direct private offer PPC deal, start to finish.
*Cloud Sizing Tool → Guided Quote → approval → JIRA to RevOps@qumulo.com → RevOps (Patrick Keegan) builds the marketplace offer → customer accepts → cluster meters against it. Partner discount fields stay 0/blank; offer expires in 2 weeks by default.*

**3.** A partner wants to structure a deal as MPO, and the cloud provider is a CSP. Can you build it that way?
*No — MPO isn't allowed where a CSP is the cloud provider.*

**4.** A customer wants outside-marketplace prepay with a PayGo fallback once the balance hits zero. What do you tell them?
*That fallback doesn't exist outside marketplace — prepay can't fall through to PayGo at a $0 balance there. If they need that behavior, the deal needs to be in-marketplace.*

**5.** A customer wants to self-serve on the public AWS listing with no rep-built offer. How do you log it, and at what discount?
*Purchase Type = "Public Offer - Pay-Go," logged for forecasting only, zero discount.*

**6.** You're building a private discounted PayGo offer instead of public self-serve. What field do you have to set, and to what value?
*Pay-Go Discount Validity Term — the term stays at 1.*

**7.** Customer wants ANQ but has no Azure footprint, all AWS. What do you do?
*Flag it — ANQ is Azure Marketplace only. Either the deal needs an Azure path, or the product should be CNQ instead.*

**8.** Same partner, same customer — once structured as CPPO in-marketplace, once as reseller paper outside marketplace. What changes in the Channel Partner / Reseller-of-Hyperscalers fields between the two?
*They swap. In-marketplace: Channel Partner = the marketplace, Reseller-of-Hyperscalers = the partner. Outside-marketplace: Channel Partner = the partner, Reseller-of-Hyperscalers = the marketplace.*

---

## 💬 RESOURCES

- [PPC Quote](https://qumulo.atlassian.net/wiki/spaces/ST/pages/3745710119)
- [Cloud Deal Categorization](https://qumulo.atlassian.net/wiki/spaces/ST/pages/3745120287)
- [Guided Quoting + Sizing Tool](https://qumulo.atlassian.net/wiki/spaces/ST/pages/3240329217)
- [Pay-Go Quote doc](https://qumulo.atlassian.net/wiki/spaces/ST/pages/3745775635)
- [Sean Gwaltney's CPPO/MPO/CSP taxonomy (Slack, May 2024)](https://qumulo.enterprise.slack.com/archives/C4UEYNSSU/p1717170130617329)
- Cloud Sizing Tool — `cloud.qumulo.sh`
- RevOps — `RevOps@qumulo.com` (marketplace offer builds: Patrick Keegan)

---

## 📍 VALIDATE BEFORE YOU QUOTE

- The full 36-row SFDC lookup isn't reproduced above — pull the authoritative row from the Guided Quote tool or RevOps rather than relying on the four worked examples in this guide.
- Sean Gwaltney's CPPO/MPO/CSP taxonomy post is May 2024 vintage — confirm it's still current before citing it to a partner.
- Loren McWethy's outside-marketplace RevOps position is dated 2026-07-13 — confirm it still holds at cert time.

---

*QS coordinates: 3.1 (CRM/data discipline — correct SFDC field derivation), 3.3 (stage-gate accuracy on cloud opportunities), 3.4 (partner-led motion coordination — A2/A3 seller ID and paper requirements). Recertify whenever the `qumulo-cloud-sales-quoting` skill changes.*
