# Tickets: Pockets — Personal Expense Tracking, Cash Flow & Budgeting

An opinionated personal expense tracking, cash-flow, and budgeting application for one person.
Derived from `docs/specs/high-level-spec.md`.

Work the **frontier**: any ticket whose blockers are all done. For these linear Phase 1 chains that means top to bottom.

---

## Phase 1: Expense Tracking Foundation

### 1.1 Workspace & Account Management

**What to build:** A single-user workspace with an immutable currency (set once and locked after first account or transaction). Create, rename, and classify accounts into Pocket roles (Unclassified, Daily, Credit Card, Saving, Emergency, Investment). Handle Unlinked (disconnected but preserved), Inactive (closed/retired), and Primary Daily selection (auto when one Daily exists, manual when multiple). The user can see all their accounts with their current Pocket role and change roles with a warning about downstream recalculations.

**Blocked by:** None — can start immediately.

- [ ] Workspace creation with currency selection; currency becomes immutable after first account or transaction
- [ ] Account CRUD (create, rename, delete when no history) with Pocket role assignment
- [ ] Pocket role classification UI showing all six roles; Unclassified state resolved via prompt
- [ ] Primary Daily: auto-selection when one active Daily, forced manual when multiple
- [ ] Unlinked and Inactive account states preserved for historical reporting
- [ ] Role-change flow with warning and recalculation (without overwriting manual choices)

### 1.2 Transaction Import & Classification

**What to build:** Import provider transactions (start with unlinked/manual transactions as the primary path, with provider API integration scaffolding). Each imported transaction is classified as Income, Expense, or Transfer. Pending vs posted states are visible, duplicates are handled via provider identity, and provider removals are shown as exclusions (not deletions). Unmatched transfer legs are shown in a reconciliation queue.

**Blocked by:** 1.1 Workspace & Account Management

- [ ] Manual transaction entry on unlinked accounts
- [ ] Provider import scaffolding (architecture for linking, but real provider connection deferred)
- [ ] Classification as Income, Expense, or Transfer with pending/posted/removed states
- [ ] Duplicate detection via provider identity and transaction facts; ambiguous duplicates prompt review
- [ ] Provider removals shown as exclusions preserving raw history and audit trail
- [ ] Reconciliation queue for unmatched transfer legs

### 1.3 Categories, Splits & Basic Reporting

**What to build:** Income and Expense Categories with one level of Sub-categories. Split a transaction among categories (splits must exactly total the amount). Uncategorized items highlighted for review but included in overall totals. Monthly spending reports — overall and by Category — on an accrual basis with month-over-month trends. Negative Expenses (Refund/Reimbursement) reduce accrued spending. Manual overrides (type, Category, effective date) survive re-import and recalculation.

**Blocked by:** 1.2 Transaction Import & Classification

- [ ] Category CRUD and lifecycle (rename, archive, merge with preview, delete if unreferenced)
- [ ] One Sub-category level; historical hierarchy preserved when moving sub-categories
- [ ] Transaction splits where amounts exactly total the transaction
- [ ] Uncategorized highlight and review queue
- [ ] Monthly accrual-based spending reports: overall, by Category, current-month, month-over-month
- [ ] Refund/Reimbursement as negative Expense (linked to original where possible)
- [ ] Manual override persistence (type, Category, split, effective date) surviving re-import and recalculation
- [ ] Classification precedence: manual overrides > user rules > pocket-role behavior > built-in rules > review

### 1.4 Categorization Rules

**What to build:** User-defined rules that match on description, account/pocket role, direction, amount/range, and transaction type — separate from transfer-matching rules. More-specific rules beat general ones; equally specific conflicts require review. Default categorization from built-in institution/merchant knowledge. All automation exposes its source and confidence level.

**Blocked by:** 1.3 Categories, Splits & Basic Reporting

- [ ] Categorization rule CRUD with match criteria (description, account/pocket, direction, amount, type)
- [ ] Specificity-based precedence with conflict review for equally specific rules
- [ ] Built-in default categorization from institution/merchant knowledge
- [ ] Source-and-confidence disclosure on every automated classification
- [ ] Rules kept separate from transfer-matching rules (scaffold transfer rules domain but don't implement)

---

## Phase 2: Cash Flow & Transfers

### 2.1 Transfer Model & Matching

**What to build:** A Transfer is a logical movement with exactly two Transfer legs (each a Transaction classified as Transfer). Automatically match legs with equal amount, opposite direction, different accounts, and a short date window; ambiguous matches require confirmation. Unmatched legs excluded from Income/Expense/Budget reporting and shown in a reconciliation queue. User Transfer-matching rules separate from Categorization rules. Confirmed matches persist and survive re-import.

**Blocked by:** Phase 1 complete

- [ ] Two-leg Transfer model: one logical Transfer, two Transfer-leg Transactions
- [ ] Automatic matching: equal amount/currency, opposite direction, different accounts, short date window
- [ ] Ambiguous/low-confidence matches require confirmation; confirmed matches persist
- [ ] Unmatched legs reconciliation queue (excluded from Income/Expense/Budget)
- [ ] Transfer-matching rules (separate from Categorization rules) for known counterpart accounts
- [ ] Fees as separate Expenses; split movements as separate Transfers

### 2.2 Transfer Categorization Matrix & Cash Flow

**What to build:** All ten transfer types from the matrix (Cash↔Cash, Cash→Credit Card, Credit Card→Cash, Invest, Divest, Credit Card↔Investment, Credit Card↔Credit Card, Investment↔Investment) with correct cash/no-cash boundary. Cash Flow actuals on cash basis with daily granularity. Current and available balances from provider. Pocket-level movement shown without double-counting total Cash Flow. No Income/Expense/Budget effect for any Transfer.

**Blocked by:** 2.1 Transfer Model & Matching

- [ ] All 10 transfer types correctly handled with Cash Flow, Net invested, and review-flag effects
- [ ] Cash Basis Cash Flow: money recognized only when crossing cash boundary
- [ ] Cash Flow actuals at daily granularity; pending excluded from finalized data
- [ ] Provider-reported current and available balances with source/freshness
- [ ] Pocket-level movement display (source out, destination in) without total Cash Flow change inside cash
- [ ] Excluded-from-definitive handling for Unclassified pockets

### 2.3 Virtual Accounts & Investment Tracking

**What to build:** Auto-create a Virtual Account (Investment pocket) when a Transfer identifies a known investment institution without a matching Account. Rename, merge compatible Virtual Accounts, but never link/convert or allow manual non-Transfer transactions. Track Net invested amount per Investment pocket (Invest minus Divest, adjusted by Investment transfers). A Linked Account and Virtual Account may coexist at the same institution. Referenced Virtual Accounts become Inactive, not deleted.

**Blocked by:** 2.2 Transfer Categorization Matrix & Cash Flow

- [ ] Virtual Account auto-creation on matching-rule identification of investment institution without account
- [ ] Lower-confidence creation requires confirmation
- [ ] Rename, merge compatible Virtual Accounts; no linking or conversion
- [ ] Coexistence with Linked Accounts at same institution
- [ ] Net invested amount per pocket and total (Invest - Divest ± Investment transfers)
- [ ] Inactive lifecycle for referenced Virtual Accounts; delete only when unreferenced
- [ ] Stable institution identity and matching aliases preserved across renames

### 2.4 Cash Flow Projections & Transfer Suggestions

**What to build:** Current-month daily projections and rolling 12-month monthly projections from posted actuals, pending transactions, confirmed recurring items, recurring Targets, Budget allocations as reserve needs, and labelled historical estimates. Every projection item is explainable, editable, and excludable. Transfer suggestions respect min balances, never auto-execute, protect Emergency pockets, never fund from Credit Card/Investment, and show shortfalls instead of pretending satisfaction.

**Blocked by:** 2.2 Transfer Categorization Matrix & Cash Flow

- [ ] Current-month daily projection with source labelling (posted/pending/recurring/targets/historical)
- [ ] Rolling 12-month monthly projection
- [ ] Every projection item explainable, editable, and excludable
- [ ] Transfer suggestions respecting user-defined min balances for each Cash pocket
- [ ] Never auto-execute; explain source, destination, amount, reason
- [ ] Emergency pocket protection (explicit approval required to source from)
- [ ] Never source from Credit Card or Investment pockets
- [ ] Shortfall display: partial funding, retained unfunded needs, never implied satisfaction
- [ ] Before Budget integration: use confirmed recurring cash movements and predicted shortfalls only

---

## Phase 3: Budgeting & Planning

### 3.1 Monthly Budgets & Rollover

**What to build:** Monthly Budgets for Expense Categories with three rollover policies (No rollover, Positive-only, Full). Policy changes are prospective; closed-period calculations preserved. Available budget = prior carryover + monthly allocation − accrued expenses. Clear reporting showing allocation vs actual.

**Blocked by:** Phase 1 complete (needs categories + expenses)

- [ ] Monthly Budget CRUD attached to an Expense Category or Sub-category
- [ ] Three rollover policies: No rollover, Positive-only, Full rollover
- [ ] Prospective policy changes; closed periods preserved; preview opening availability
- [ ] Available budget calculation: prior carryover + monthly allocation − accrued expenses
- [ ] Allocation vs actual reporting with month-over-month comparison
- [ ] One-time explicit adjustments only (Targets recommend, never silently apply)

### 3.2 Parent/Child Budgets

**What to build:** Hierarchical Parent Budgets that include all descendant spending, with Child Budgets as earmarks (not additional money). Child expenses reduce both child and parent availability. Overspending visible even when parent has unallocated room. Block child allocations exceeding parent; children inherit parent's rollover policy. Never present parent availability as extra beyond children.

**Blocked by:** 3.1 Monthly Budgets & Rollover

- [ ] Parent Budget includes all descendant Category spending
- [ ] Child Budget as earmark (reduces both child and parent availability, not additional)
- [ ] Sibling/uncategorized direct-parent expense reduces only unallocated parent portion
- [ ] Child overspending visible even when parent has room
- [ ] Block child allocations exceeding parent; offer to increase parent
- [ ] Children inherit parent rollover policy (no override)
- [ ] Clear UI separation of earmarked vs unallocated parent availability

### 3.3 Targets

**What to build:** One-time and recurring Targets with amount, due date, and optional description-matching rule. They recommend monthly allocations (shortfall ÷ remaining months, rounded up) but never silently change the Budget. Recurring Targets generate next due date; fixed obligations auto-complete when matched expenses reach the amount in the due window. Discretionary Targets require manual completion.

**Blocked by:** 3.1 Monthly Budgets & Rollover

- [ ] Target CRUD: amount, due date, Category, one-time or recurring
- [ ] Monthly allocation recommendation: shortfall ÷ remaining months, rounded up
- [ ] Recommendation never silently applied; user accepts or adjusts
- [ ] Recurring Target generates next due date; retains same Budget and availability
- [ ] Fixed obligation auto-completion when accrued expenses reach expected amount in due window
- [ ] Discretionary Target manual completion or cancellation
- [ ] Occurrence matching: Category, amount/tolerance, due-date window, optional description rule

### 3.4 Saving-Pocket Links & Reserves

**What to build:** Link rollover Budgets to Saving pockets in one of two modes. Dedicated reserve: Pockets reconciles effective balance vs combined positive availability + optional buffer, suggests transfers in/out. General savings: suggests funding new allocations without claiming the whole balance belongs to linked budgets. Multiple Categories may share one pocket; one Category cannot split across pockets. Links are for comparisons and suggestions, not for treating budget and cash as the same.

**Blocked by:** 3.1 Monthly Budgets & Rollover, AND 2.2 Transfer Categorization Matrix & Cash Flow (needs transfers for suggestions)

- [ ] Link a rollover Budget Category to one active Saving pocket
- [ ] Two reserve modes: Dedicated reserve and General savings
- [ ] Dedicated reserve: effective balance vs combined positive availability + buffer; suggest transfers in and out
- [ ] General savings: suggest funding from new allocations only
- [ ] Effective balance includes confirmed incoming/outgoing pending transfers; unmatched movements don't silently alter reconciliation
- [ ] Multiple Categories sharing one pocket; one Category cannot split across pockets
- [ ] Change-prompt flow: show reassigned availability, recalculate both targets, suggest but don't execute transfers
- [ ] Reject Inactive destinations

### 3.5 Budget-Driven Suggestions & Reporting

**What to build:** Unified funding priority (fixed obligations → emergency → other targets → discretionary). Dedicated reserve shortfalls before General savings. Always respect Daily/Cash-pocket minimum balances; allow partial funding; show all unfunded amounts; offer but never silently make allocation/target changes. Reporting clearly separates accrual Budget adherence from cash-basis Cash Flow — never conflated, both with 12-month views.

**Blocked by:** 3.4 Saving-Pocket Links & Reserves

- [ ] Funding priority: fixed contractual (by due date) → emergency minimum → other fixed → discretionary
- [ ] Dedicated reserve shortfalls before General savings allocation suggestions
- [ ] Respect Daily and Cash-pocket minimum balances; allow partial funding
- [ ] Show all unfunded amounts; offer but never silently make allocation or Target changes
- [ ] Separate accrual Budget adherence reporting (allocation vs actual)
- [ ] Separate cash-basis Cash Flow reporting (actual and projection)
- [ ] Combined 12-month view never conflates the two bases
- [ ] Budget adherence without smoothing historical Cash Flow