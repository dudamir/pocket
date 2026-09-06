# Pockets

An opinionated personal expense tracking and budgeting application for one person. It explains
financial activity through Accounts, Pockets, Transactions, Transfers, Categories, Cash Flow,
and Budgets.

## Language

### Workspace and accounts

**Workspace**:
The financial boundary belonging to one user. It has one currency and contains all Accounts,
Transactions, Categories, Transfers, Cash Flow, and Budgets.
_Avoid_: Household, tenant

**Account**:
A bank or credit account represented in Pockets. It may be linked to a financial institution or
maintained without a link.
_Avoid_: Pocket

**Linked Account**:
An Account connected to a financial institution from which Pockets imports Transactions and
balances.

**Unlinked Account**:
An Account without an active institution connection. Its existing financial history remains
part of the Workspace.

**Inactive Account**:
A closed or intentionally retired Account retained for historical reporting.
_Avoid_: Deleted Account

**Virtual Account**:
A transfer-only Account created to represent an unlinked investment institution. It has a stable
institution identity, may be renamed, and cannot become a Linked Account.
_Avoid_: Synthetic account, placeholder account

### Pocket classifications

**Pocket**:
The semantic classification that gives an Account its financial role. Every Account has exactly
one Pocket, although its role may temporarily be Unclassified.
_Avoid_: Account, wallet, bucket, fund

**Pocket role**:
One of **Unclassified**, **Daily**, **Credit Card**, **Saving**, **Emergency**, or
**Investment**. Multiple Accounts may have the same role.
_Avoid_: Pocket type

**Unclassified pocket**:
A temporary Pocket role used when an Account's role is not yet known. Pockets minimizes this
state and asks the user to resolve it.

**Daily pocket**:
The role for an Account used to receive Income and pay ordinary Expenses.
_Avoid_: Current account, checking

**Primary Daily pocket**:
The Daily pocket used as the default operational source or destination when more than one Daily
pocket exists.

**Credit Card pocket**:
The role for a credit Account used to incur Expenses before cash payment.
_Avoid_: Credit card account

**Saving pocket**:
The role for an Account holding cash reserved for expected needs.
_Avoid_: Savings account

**Emergency pocket**:
A protected cash-holding role for unexpected needs. It is not normally used as a suggested
funding source.
_Avoid_: Emergency fund

**Investment pocket**:
The role for an Account holding investments. Pockets represents observed transfers into and out
of it but not positions, trades, valuations, dividends, or other investment activity.
_Avoid_: Portfolio

**Cash pocket**:
A Daily, Saving, or Emergency pocket. These roles define the cash boundary used by Cash Flow.

**Non-cash pocket**:
A Credit Card or Investment pocket. Movement involving one affects Cash Flow only when the other
side crosses the cash boundary.

### Transactions and transfers

**Transaction**:
A financial entry associated with an Account. A Transaction is classified as **Income**,
**Expense**, or **Transfer** and may be pending, posted, excluded, or removed by its provider.
_Avoid_: Transfer (for the complete two-sided movement)

**Income**:
A Transaction that represents earned or received value rather than movement between the user's
Accounts.

**Expense**:
A Transaction that represents value consumed or owed. Expense recognition is independent of
when cash moves.

**Refund**:
A negative Expense that reverses accrued spending and restores Budget availability.
_Avoid_: Income

**Reimbursement**:
A negative Expense linked to an earlier Expense, including a partial repayment. The user may
instead classify it as Income when that better represents its meaning.

**Transaction split**:
A portion of an Income or Expense assigned its own Category. The splits of a Transaction total
exactly its amount.

**Transfer**:
A logical movement between exactly two Accounts. It consists of exactly two Transfer legs and
has no Income Category, Expense Category, or Budget effect.
_Avoid_: Transaction (for the complete movement)

**Transfer leg**:
A Transaction classified as Transfer that represents one side of a Transfer.

**Unmatched transfer**:
A Transfer leg without a confirmed counterpart. It is excluded from Income, Expense, and Budget
reporting until matched or reclassified.

**Credit card payment**:
A Transfer from a Cash pocket to a Credit Card pocket. It is money out for Cash Flow but not a
second Expense.

**Balance transfer**:
A Transfer between Credit Card pockets. It has no Cash Flow, Income, Expense, or Budget effect.

**Invest**:
A Transfer from a Cash pocket to an Investment pocket. It is money out for Cash Flow and
increases the destination's Net invested amount.
_Avoid_: Contribution, deposit

**Divest**:
A Transfer from an Investment pocket to a Cash pocket. It is money in for Cash Flow and
decreases the source's Net invested amount.
_Avoid_: Withdrawal, redemption

**Investment transfer**:
A Transfer between Investment pockets. It moves Net invested amount between them without
changing the Workspace total.

**Net invested amount**:
The observed Invest amount minus Divest amount for an Investment pocket, adjusted by Investment
transfers. It may be negative and is not a balance, market value, or portfolio value.

### Classification

**Category**:
A classification for either Income or Expense. A Category may contain Sub-categories but the
hierarchy is never more than two levels deep.
_Avoid_: Tag, bucket, label

**Sub-category**:
A second-level Category nested beneath a parent Category.

**Uncategorized**:
The state of an Income or Expense whose Category is unresolved. It remains in overall totals but
not Category-specific totals.

**Default categorization**:
The initial classification supplied by Pockets' built-in institution and merchant knowledge.

**Categorization rule**:
A user-defined rule that assigns Income, Expense, or Transfer classification and, where
applicable, a Category or Sub-category.
_Avoid_: Transfer matching rule

**Transfer matching rule**:
A rule that identifies counterpart Accounts and proposes or confirms a match between Transfer
legs.
_Avoid_: Categorization rule

**Manual override**:
An explicit user choice of Transaction type, Category, split, match, or effective date. It takes
precedence over automation and survives re-import and recalculation.

### Accounting and cash flow

**Accrual Basis**:
The accounting basis used for Expense and Budget tracking. An Expense is recognized when
incurred, including a Credit Card purchase before its card payment occurs.

**Cash Basis**:
The accounting basis used for Cash Flow. Money is recognized when it moves across the boundary
between Cash pockets and non-cash or external destinations.

**Cash Flow**:
Money in and money out under Cash Basis Accounting. Transfers wholly within the cash boundary do
not change total Cash Flow.

**Cash flow projection**:
An explainable estimate of future Cash Flow built from pending Transactions, confirmed recurring
Income and Expenses, recurring Targets, reserve needs, or clearly labelled historical estimates.
_Avoid_: Unexplained forecast

**Current balance**:
The posted balance reported by a financial institution.

**Available balance**:
The spendable balance reported by a financial institution, or Current balance adjusted by known
pending Transactions when no reported value exists.

### Budgeting

**Budget**:
A monthly allocation for an Expense Category or Sub-category. It uses Accrual Basis Expense
tracking and one Rollover policy.
_Avoid_: Income budget, annual ledger

**Available budget**:
The prior carried availability plus the current monthly allocation minus accrued Expenses.

**Rollover policy**:
One of **No rollover**, **Positive-only rollover**, or **Full rollover**, defining whether Budget
availability carries into the next month.

**No rollover**:
A policy in which availability resets each month.

**Positive-only rollover**:
A policy in which positive availability carries forward but a deficit does not.

**Full rollover**:
A policy in which both positive and negative availability carry forward.

**Parent Budget**:
A Budget whose availability includes all Expenses and earmarks within its Sub-categories.

**Child Budget**:
An earmarked portion of a Parent Budget, not additional availability beyond the parent.

**Target**:
A one-time or recurring amount and due date used to recommend a monthly Budget allocation. A
Target never silently changes the user's allocation.

**Dedicated reserve**:
A Saving pocket whose target balance is controlled against the positive availability of one or
more linked rollover Budgets plus an optional buffer.

**General savings**:
A Saving pocket that may receive allocation-based transfer suggestions without asserting that
its whole balance belongs to linked Budgets.
