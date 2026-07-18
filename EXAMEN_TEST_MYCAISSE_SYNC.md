# MyCaisse QA Test — Cloud Synchronisation

| | |
|---|---|
| **Name** | |
| **Date** | |
| **Start time** | |
| **End time** | |

**Duration: 3h30 max.** You may hand in your report and leave as soon as you are done.
Send the full report (with screenshots) to **jiajun@3e-tech.fr**.

---

## 1. Your environment

You work on **two sides at the same time**:

| Side | Where | Access |
|---|---|---|
| **Caisse** (POS device) | Sunmi tablet via **AnyDesk** (ID given at briefing) | App is installed, already connected to your restaurant |
| **Cloud** (admin dashboard) | Your own browser: https://admin.preprod.ignis-paris.fr | `akhil@test.local` / `akhil123` |

### All credentials

| What | Value |
|---|---|
| Cloud dashboard login | `akhil@test.local` / `akhil123` |
| Your restaurant | **QA Test Akhil** |
| Caisse settings (cloud side) | https://admin.preprod.ignis-paris.fr/restaurants/36f6b1dc-63cf-4b74-8620-2a83d212173a/caisse |
| Catalog editor (cloud side) | https://admin.preprod.ignis-paris.fr/restaurants/36f6b1dc-63cf-4b74-8620-2a83d212173a/online-ordering/catalogue |
| Caisse PIN — Staff | `1234` |
| Caisse PIN — Owner | `9999` |
| Caisse PIN — Admin | `187669993` |
| Caisse cloud account (only if the app asks to reconnect) | `akhil@test.local` / `akhil123` |

**Full access on the caisse:** from the home screen, tap **Information** → enable **mode maintenance**.

### ⚠️ Three rules

1. On the cloud dashboard you are allowed on **exactly two pages**: the *Caisse settings* page and the *Catalog editor* page (the two links above). Everything else — other pages of your restaurant, other restaurants, platform-level pages — is **off-limits**. Touching another restaurant ends the test.
2. **Never disable Synchronisation** in the settings — it is on by default and must stay on for the whole session. Testing the on/off switch is out of scope.
3. MyCaisse is certified **NF525** (French fiscal law): a printed Note or a paid Ticket **cannot be edited or deleted — by design**. Cancelling creates a counter-document. Do not report that as a bug.

---

## 2. What you are testing

**MyCaisse** is a French fiscal POS (cash register) for restaurants, running on an Android tablet. It works **offline-first**: everything is stored in a local database, and a background **synchronisation** mirrors data to the cloud and back.

How sync works — this is the heart of the test:

- A sync round runs automatically **every 2 minutes** (default, configurable). Local changes are pushed a few seconds after you save them.
- **Both directions**: changes made on the caisse go up; changes made on the cloud dashboard come down.
- **Conflicts** are resolved **freshest-wins**: whichever side was modified most recently overwrites the other.
- The **sync chip** at the top of the home screen shows the sync state (pending count). Tap it → **Synchroniser** forces a sync round immediately — use this constantly, don't sit waiting 2 minutes.
- Scope: **settings** and **catalog** sync both ways. **Orders do NOT sync** — an order you create on the caisse never appears on the cloud; that is by design, not a bug. Only the fiscal documents it produces (Note, Ticket) are backed up one-way to the cloud — and that backup is not your focus today.

**Your central mission: find as many real bugs as possible in the settings sync, the catalog sync, and the sales flow that consumes the synced data.** A reproducible bug with clear steps is worth more than anything else you produce today.

### What we grade

| Weight | What |
|---|---|
| **50%** | Bugs found — severity-weighted; each needs repro steps, expected vs actual, screenshot |
| **20%** | Test case quality — well-designed cases, edge cases, not just happy paths |
| **15%** | Part 4 — automation strategy document |
| **15%** | Report clarity and structure |

AI tools (ChatGPT, Claude, Gemini…) are **allowed and encouraged** for everything. Internet allowed. At the end of your report, briefly describe how you used them.

**Manage your time freely** across the four parts — just make sure Part 4 (the automation document) gets done.

---

## 3. Key terms — everything in one place

All the vocabulary you need, French UI terms included. Refer back here whenever a word is unclear.

| Term (as in the UI) | Meaning |
|---|---|
| **Commande / Order** | An order being taken (a table, a customer). Lives **only on the caisse — orders are never synced** to the cloud |
| **Note** | The printed bill. An order becomes a Note once printed. Backed up one-way to the cloud as a fiscal document |
| **Ticket** | Fiscal receipt created at payment (digitally signed, immutable). Backed up one-way to the cloud |
| **Encaisser / Encaissement** | Taking the payment |
| **Recette** | Sales history / daily revenue screen |
| **Duplicata** | Official reprint of an existing document |
| **Annulation** | Cancellation — creates a counter-ticket, never deletes |
| **Remise** | Discount |
| **Sur Place / Emporter / Livraison** | Dine-in / take-away / delivery |
| **TVA, HT, TTC** | VAT, excl. tax, incl. tax |
| **Commerçant** | Merchant (company legal info shown on tickets) |
| **Paramètres** | Settings module on the caisse |
| **Produits** | Catalog module on the caisse (products & categories) |
| **Sync chip** | Indicator at the top of the home screen showing sync state / pending count |
| **Synchroniser** | Force a sync round now (button in the sync chip popup) |
| **Freshest-wins** | Conflict rule: the most recently modified side overwrites the other |
| **NF525** | French fiscal law for cash registers: fiscal documents are signed and immutable |

---

## PART 1 — Settings synchronisation

**Caisse side:** home screen → **Paramètres** (PIN required). Focus on the tabs **Commerçant** (legal/company info printed on every ticket), **Impression** (ticket footer, "print 0 or 1" option), **Réductions** (automatic discounts per service type: % + minimum threshold in €).

**Cloud side:** the *Caisse settings* page (link above) shows the same modules and lets you edit the same values.

**Task:** write your test cases (format and number are up to you — see "Format" at the end), execute them, report every bug.

Directions worth probing — this list is a starting point, not a limit:

- Caisse → cloud: change a value on the caisse, force a sync, verify the dashboard.
- Cloud → caisse: change a value on the dashboard, force a sync on the caisse, verify — including **while the same settings screen is already open** on the caisse.
- Boundary values: discount of 0%, 100%, more than 100%, negative, decimals; empty threshold; huge numbers.
- Text fields: very long text, accents (é à ç), special characters, emoji, the `{restaurant_name}` variable in the ticket footer.
- Validation parity: does the cloud accept values the caisse UI refuses (or the reverse)? What lands on the other side after sync?
- Do the changed settings **actually take effect** in a sale? (e.g. "print 0 or 1" changes how many tickets print at payment; Commerçant info appears on the printed ticket; discounts auto-apply above the threshold.)

---

## PART 2 — Catalog synchronisation

Your restaurant comes pre-loaded with a real catalog. This is your baseline — verify it first:

| Data | Expected |
|---|---|
| Categories | **32** |
| Products | **187** (166 with a photo) |
| TVA rates | **3** — A = 5.5%, B = 10%, C = 20% |
| Option group | **1** — group *STARTER* (items *Nem*, *Cabbage salad*, +0.00 €, max 1) on product **Set menu 2 courses** (category *Set Menu*) |
| Payment methods | **2** — Carte bancaire, Espèces |

**Caisse side:** home screen → **Produits** (create/edit products and categories locally). **Cloud side:** the *Catalog editor* (link above).

**Task:** write your test cases, execute them, report every bug.

Directions worth probing:

- Both directions: create, rename, edit price, deactivate, delete — a product and a category — on each side; verify the other side after sync.
- Price integrity: change a TTC price on the cloud — is HT/TVA still coherent on the caisse? Change a product's TVA rate — what does the next printed ticket show?
- Edit the option group / its items on the cloud — what happens on the caisse when ordering *Set menu 2 courses*?
- A product **already inside an open order** on the caisse: change its price / deactivate it / delete it on the cloud, sync, then look at the order.
- Images: change/remove a product photo on the cloud.
- Weird data: duplicate names, empty fields, very long names, special characters, price 0, negative price.
- Do the two payment methods behave at payment time? Create a new payment method if the UI allows it — does it sync?

---

## PART 3 — Sales flow

The point of a POS is selling. Verify that the **synced catalog and settings behave correctly in real sales**.

The flow: **create an order** (Sur Place / Emporter / Livraison) → **add products** (options included) → optional discount → **print the Note** (the bill — order card turns green) → **Encaisser** (payment — creates the fiscal Ticket). Kitchen printing is a separate button and does not change the card colour.

**Task:** run real sales end-to-end and report every bug. Check at least:

- Totals, TVA breakdown (HT / TVA / TTC) on the printed ticket vs the screen — especially for products whose price or TVA **you changed in Part 2**.
- *Set menu 2 courses* with its option — is the option printed, is the price right?
- Automatic discounts from Part 1 (threshold reached / not reached).
- Payment: single, **mixed** (several payment methods on one note), **partial** (pay only part of the items), cash change calculation.
- **Recette** screen (sales history): totals per service type, TVA table, Duplicata reprint, cancellation of a ticket.
- Anything that crashes, freezes, miscalculates, or displays incoherent data between two screens.

---

## PART 4 — Automation strategy document (no code)

Today everything was manual. Write a **1–2 page document** (in your report) describing how you would automate testing for this product. We want your **thinking**, not code.

It must cover:

1. **What you would automate first and why** — risk-based: where do the worst bugs live?
2. **Test layers** for this architecture (Android app + cloud backend + web dashboard): what belongs to unit / API / end-to-end.
3. **The hard problem — two-sided sync:** how do you make automated sync tests deterministic (test data seeding, triggering sync instead of waiting, asserting on both sides, simulating conflicts)?
4. **Tooling** you would pick, and why it fits this stack.
5. **What you would deliberately keep manual.**
6. **Where AI tools fit** in your QA workflow.

---

## Format — your call

There is no imposed template. **Structure your test cases, bug reports and the whole report however you think a good QA should** — your choice of structure is itself part of what we evaluate.

Two non-negotiables only:

- A test case must show what you tested, how, and what you observed.
- A bug must be **reproducible**: steps, expected vs actual, severity, screenshot. A bug we cannot reproduce from your steps counts half.

*Good luck — go find those bugs.*
