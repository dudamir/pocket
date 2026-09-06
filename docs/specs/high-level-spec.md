# Pockets

An opinionated personal expense tracking, cash-flow, and budgeting application for one person.

## Foundational Requirements

### Workspace

- A Workspace belongs to one user; shared household access is out of scope.
- The user selects one currency during setup.
- The currency becomes immutable after the first Account or Transaction is imported.
- Multiple Accounts may share a Pocket role, but every Account has exactly one Pocket.
- When only one active Daily pocket exists, it is implicitly the Primary Daily pocket. When
  multiple exist, exactly one must be selected. Without one, historical views remain available
  but transfer suggestions are disabled.

### Accounting Invariant

These accounting bases must always remain distinct:

- **Cash Flow uses Cash Basis Accounting.** Money is recognized when it crosses the cash
  boundary. Daily, Saving, and Emergency pockets are Cash pockets; Credit Card and Investment
  pockets are non-cash pockets.
- **Expense and Budget tracking use Accrual Basis Accounting.** An Expense is recognized when
  incurred, irrespective of when cash pays for it.
- A Credit Card purchase is an Expense on its effective date and consumes its Budget, but is not
  money out for Cash Flow.
- Paying a Credit Card from a Cash pocket is money out on the payment date, but is a Transfer and
  must not count as a second Expense.
- Invest is money out and Divest is money in when it crosses between Cash and Investment pockets.
- Transfers among Cash pockets show pocket-level movement but do not change total Cash Flow.
- Transactions involving Unclassified pockets are excluded from definitive Cash Flow until the
  Pocket role is resolved.

### Privacy and Security

- Encrypt user data in transit and at rest.
- Never store bank credentials; use read-only provider tokens with minimum necessary permissions.
- Do not use financial data for advertising, and exclude sensitive values from logs and analytics.
- Require strong authentication and secure session handling.
- Third-party processing is disabled by default and configurable per feature. The user may opt in,
  change consent, or revoke it at any time.
- Even after opt-in, minimize transmitted data and completely obfuscate personally identifiable
  data, including names, contact details, account/routing numbers, provider IDs, precise locations,
  identifying free text, and stable cross-user identifiers.
- Revocation stops new transmissions immediately, deletes queued payloads, requests provider-side
  deletion where available, discloses limitations, and preserves only a non-sensitive consent audit.
- Workspace deletion revokes integrations, removes active data, purges backups within a disclosed
  retention period, requests third-party deletion, isolates legally required records, and reports
  deletion status.
- Export includes user-owned domain data in JSON/CSV and human-readable summaries, but never access
  tokens, credentials, internal secrets, or meaningless third-party identifiers.

### Data Integrity and Traceability

- Imported Transactions are excluded rather than manually deleted. Provider removals remain visible
  as such, preserve raw history and audit information, and stop affecting calculations.
- Manual Transactions are not allowed on Linked Accounts. Manually maintained Unlinked Accounts are
  supported as a fallback, not promoted as a primary feature.
- Reconcile pending-to-posted and duplicate imports using provider identity and Transaction facts.
  Ambiguous duplicates require review; confirmed decisions persist.
- Every automated classification or projection exposes its source and confidence.
- Manual type, Category, split, Transfer match, Pocket role, and effective-date choices take
  precedence and survive re-import and recalculation.
- Classification precedence is:
  1. Manual overrides
  2. User Transfer matching rules
  3. Confirmed Transfer matches
  4. User Categorization rules
  5. Pocket-role behavior
  6. Built-in institution and merchant rules
  7. Unclassified or review
- Conflicts at the same precedence require review instead of an arbitrary winner.

## Expense Tracking

The minimum outcome is trustworthy accrual-based Income and Expense tracking from real provider
data.

### Accounts and Pockets

- Link bank and credit Accounts and import Transactions.
- Classify each Account into one Pocket role: Unclassified, Daily, Credit Card, Saving, Emergency,
  or Investment.
- Minimize Unclassified pockets and prompt the user to classify them.
- Allow the user to change any Pocket role after warning that Transaction treatment, Transfer
  semantics, Cash Flow, Budget, and reports may change. Recalculate derived results without
  overwriting manual Transaction choices.
- Preserve Accounts with history:
  - **Unlinked** means the provider connection was removed and may later be restored.
  - **Inactive** means closed or retired and excludes the Account from future suggestions.
- Allow renaming while preserving Account identity and history.

### Transactions and Categories

- Every imported Transaction is classified as Income, Expense, or Transfer.
- Categories are typed as Income or Expense. Transfers do not have spending Categories.
- Income and Expense may remain Uncategorized; they still affect overall totals and are highlighted
  for review, but do not affect Category-specific totals.
- Categories support one Sub-category level only.
- Income or Expense Transactions may be split among Categories; split amounts must exactly equal the
  Transaction total. Reports operate on splits while Cash Flow operates on the whole Transaction.
- A Refund is a negative Expense, reduces accrued spending, and restores Budget availability once
  Budgeting exists. Link it to the original Expense when possible.
- A Reimbursement defaults to a partial or complete negative Expense linked to the original Expense;
  the user may explicitly classify it as Income.
- Use the institution's posted date as the default accrual effective date once available; retain the
  authorization date for reference. Allow a persistent manual effective-date override.
- Categorization rules may match description, Account or Pocket role, direction, amount/range, and
  Transaction type. More-specific rules beat general ones; equally specific conflicts require review.
- Keep Categorization rules separate from Transfer matching rules.

### Category Lifecycle and Reporting

- Rename Categories without changing identity or history.
- Archive Categories to prevent new use while retaining history.
- Merge Categories only after previewing reassignment of Transactions, rules, Targets, and Budgets.
- Move a Sub-category prospectively while preserving historical hierarchy.
- Delete only unreferenced Categories.
- Show monthly spending overall and by Category, current-month spending, and month-over-month trends
  using Accrual Basis Accounting.
- Identify obvious Transfers sufficiently to exclude them from Expense totals.

## Cash Flow

The minimum outcome is trustworthy Cash Basis actuals and projections without double-counting
accrued Expenses.

### Transfer Model and Matching

- A Transfer is a logical movement with exactly two Transfer legs. Each leg is an imported or manual
  Transaction classified as Transfer and references one Account.
- One leg may temporarily be unmatched. Exclude unmatched legs from Income, Expense, and Budget
  reporting and show them in a reconciliation queue for matching, confirmation, or reclassification.
- Automatically match legs only when they have equal absolute amount and currency, opposite
  directions, different Accounts, and dates within a short window. Description supports confidence.
- Ambiguous or low-confidence matches require confirmation; user-confirmed matches persist.
- A Transfer always has exactly two legs. Fees are separate Expenses and split movements are separate
  Transfers.
- Multi-currency support is out of scope.

### Transfer Categorization Matrix

All Transfers have no Income, Expense, or Budget effect. “Cash” below means Daily, Saving, or
Emergency.

| From | To | Classification | Total Cash Flow | Net invested effect | Review |
| --- | --- | --- | --- | --- | --- |
| Cash | Cash | Ordinary Transfer | None | None | No if confidently matched |
| Cash | Credit Card | Credit card payment | Money out | None | Flag unusual amounts only |
| Credit Card | Cash | Transfer | Provisional money in | None | Yes |
| Cash | Investment | Invest | Money out | Increase destination | No if confidently matched |
| Investment | Cash | Divest | Money in | Decrease source | No if confidently matched |
| Credit Card | Investment | Transfer | None | None until confirmation | Yes |
| Investment | Credit Card | Transfer | None | Decrease source on confirmation | Yes |
| Credit Card | Credit Card | Balance transfer | None | None | No if confidently matched |
| Investment | Investment | Investment transfer | None | Decrease source and increase destination; total unchanged | No if confidently matched |
| Any | Unclassified | Transfer | Excluded from definitive totals | Recalculate after classification | Yes |
| Unclassified | Any | Transfer | Excluded from definitive totals | Recalculate after classification | Yes |

- Cash-pocket-to-Cash-pocket Transfers show source money out and destination money in at the Pocket
  level but have no total Cash Flow effect.
- Credit Card-to-Cash movements are provisionally money in but require the user to resolve whether
  they represent a refund, cash advance, correction, or another meaning.
- Credit Card-to-Investment and Investment-to-Credit Card movements require review because investing
  with credit or paying debt directly from investments is unusual.

### Investment and Virtual Accounts

- Pockets does not import or represent investment positions, trades, valuations, dividends, or fees.
- Track only Invest, Divest, and Investment transfers and show Net invested amount per Investment
  pocket and in total. Net invested amount is based on observed Transfers and may be negative.
- Create a Virtual Account only when a Transaction is classified or strongly inferred as Transfer, a
  known-institution matching rule identifies its counterpart, and no matching Account exists.
  Lower-confidence creation requires confirmation.
- Automatically classify Virtual Accounts as Investment pockets. Allow renaming, but not linking,
  conversion, manual non-Transfer Transactions, or merging with Linked Accounts.
- Preserve stable institution identity and matching aliases despite renaming. Reuse existing Virtual
  Accounts, and allow compatible Virtual Investment Accounts to merge while preserving Transfer history.
- A Linked Account and Virtual Investment Account at the same institution may coexist.
- Referenced Virtual Accounts may become Inactive but cannot be deleted. Delete only unreferenced ones.

### Actual Cash Flow and Balances

- Use the cash-side leg's posted date as the actual Cash Flow date. Pending movements are excluded from
  finalized historical Cash Flow but may appear clearly labelled in projections.
- For direct external Income or Expense involving a Cash pocket, use that Transaction's posted date.
- Current balance is the provider-reported posted balance.
- Available balance is provider-reported spendable money, or Current balance adjusted by known pending
  Transactions when unavailable. Show balance source and freshness.
- Use Transactions, not balance differences, for historical reporting.
- Virtual Investment Accounts expose Net invested amount, not balance.

### Projections and Transfer Suggestions

- Show current-month projections at daily granularity and rolling 12-month projections at monthly
  granularity.
- Projection sources, in order, are posted actuals, pending Transactions, confirmed recurring Income
  and Expenses, recurring Targets, Budget allocations as reserve needs, and clearly labeled historical
  estimates. Every item is explainable, editable, and excludable.
- Before Budgeting is implemented, suggestions use confirmed recurring cash movements and predicted shortfalls, not
  Budget or reserve semantics.
- Suggestions:
  - Use Available balance and exclude Inactive Accounts.
  - Respect a user-defined minimum balance for every Cash pocket.
  - Never execute automatically; explain source, destination, amount, and reason.
  - Allow dismissal, adjustment, or confirmation and avoid duplication after completion.
  - Never automatically source funds from Credit Card or Investment pockets.
- Emergency pockets are protected funding sources. Transfers out require explicit approval.
- When cash is insufficient, show the shortfall, partially fund where possible, retain unfunded needs
  in the projection, and never imply the need was satisfied.

## Budgeting

The minimum outcome is accrual-based monthly planning connected clearly—but not conflated—with the
physical location of cash.

### Monthly Allocations and Rollover

- A Budget is a monthly allocation for an Expense Category or Sub-category.
- Support per-Budget policies:
  - **No rollover:** reset availability each month.
  - **Positive-only rollover:** carry positive availability but not deficits.
  - **Full rollover:** carry positive and negative availability.
- Policy changes are prospective from the next monthly period by default. Preserve closed-period
  calculations, preview the new opening availability, and permit only explicit one-time adjustments.
- Replace a separate annual Budget ledger with monthly allocation plus optional Target. A yearly goal
  may recommend one-twelfth per month, adjusted for remaining time and existing availability.

### Parent and Child Budgets

- A Budget can attach to a Category or Sub-category.
- A Parent Budget includes all descendant spending; a Child Budget is an earmark within the parent,
  never additional money.
- A Child Expense reduces both Child and Parent availability. Expense assigned directly to a parent or
  unbudgeted sibling reduces only the parent's unallocated portion.
- Child overspending remains visible even when the parent still has unallocated availability.
- Never present Parent availability as additional to Child availability; clearly display earmarked and
  unallocated portions.
- Block child allocations that exceed the Parent allocation and offer to increase the Parent.
- Children inherit the Parent rollover policy and cannot override it.

### Targets

- A Target has an amount and due date and recommends, but never silently applies, a monthly allocation.
- Base the recommendation on target shortfall, current positive availability, and remaining monthly
  allocations through the due month; round upward to the currency's smallest unit. A Target due this
  month recommends the full shortfall.
- Support one-time and recurring Targets. Recurring Targets generate the next due date while retaining
  the same rollover Budget and availability.
- A recurring fixed obligation may complete when matching accrued Expenses reach its expected amount
  in its due window. One-time discretionary Targets require manual completion or cancellation.
- Match recurring occurrences by Category/Sub-category, amount/tolerance, due-date window, and optional
  Transaction-description rule. Ambiguity requires confirmation and confirmed matches persist.

### Saving-Pocket Links and Reserves

- A rollover Budget Category may optionally link to one active Saving pocket. Multiple Categories may
  share that Saving pocket, but one Category cannot split its reserve across multiple pockets.
- The link drives reserve comparisons and Transfer suggestions; it does not make Budget availability
  and Account cash the same concept.
- A Saving pocket has one reserve mode:
  - **Dedicated reserve:** Pockets controls reconciliation between its effective Account balance and
    the combined positive availability of linked Categories plus an optional buffer. Suggest Transfers
    both in and out.
  - **General savings:** Suggest funding from new allocations without assuming the whole Account balance
    belongs to linked Categories.
- A Dedicated reserve's effective balance includes confirmed incoming pending Transfers and subtracts
  confirmed outgoing pending Transfers. Unmatched or review-required movements do not silently alter
  reconciliation.
- When changing a Category's linked Saving pocket, show reassigned availability, recalculate both reserve
  targets, suggest but do not execute Transfers, preserve history, and reject Inactive destinations.

### Budget-Driven Suggestions and Reporting

- Prioritize funding in this order:
  1. Fixed contractual obligations by due date
  2. Emergency minimum requirements
  3. Other fixed Targets
  4. Discretionary Targets
- Fund Dedicated reserve shortfalls before General savings allocation suggestions.
- Respect Daily and other Cash-pocket minimum balances, allow partial funding, show all unfunded amounts,
  and offer—but never silently make—allocation or Target changes.
- Clearly separate:
  - Accrual Expense and Budget actual-versus-allocation reporting
  - Cash Basis actual Cash Flow and projection reporting
- Show current-month and rolling 12-month Budget adherence without smoothing actual historical Cash Flow.

## Phase Completion Gate

Each phase is complete only when:

- Its user outcome works end-to-end with real provider-imported data.
- Accounting rules and edge cases are covered by automated tests.
- Automation exposes source and confidence.
- Manual overrides survive re-import and recalculation.
- Review queues resolve ambiguity without silent guesses.
- Export and deletion cover all data introduced by the phase.
- Privacy and security constraints cover every new integration.
- Terminology matches `CONTEXT.md` and relevant ADRs.
- Documentation records resolved decisions.
- Deferred behavior is explicitly unavailable rather than represented by misleading partial results.
