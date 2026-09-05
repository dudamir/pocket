# Pockets

An opinionated personal expense tracking and budgeting application. Helps a single household
pull transactions from its bank and credit accounts, categorize them, budget by category,
and understand its cash flow across the accounts it manages.

## Language

**Account**:
A bank or credit account that Pockets pulls transactions from. Accounts are the actual
financial institutions money lives in and moves between.
_Avoid_: bank account, credit card (as a general term)

**Pocket**:
A semantic classification applied to an account that gives it a role in the household's
finances. Each account has exactly one pocket. A pocket is not a separate place money is
stored — it is how the account is understood.
_Avoid_: wallet, bucket, fund

**Pocket role**:
The distinct role a pocket plays: **Daily**, **Credit Card**, **Saving**, **Emergency**, or **Investment**.
_Avoid_: pocket type

**Daily pocket**:
The pocket role for the account that receives income and pays ordinary expenses.
_Avoid_: current account, checking

**Credit card pocket**:
The pocket role for the credit account used to pay expenses.
_Avoid_: credit card account

**Saving pocket**:
The pocket role for the account used to save for expected, budgeted expenses over the year.
_Avoid_: savings account

**Emergency pocket**:
The pocket role for the account used to cover unexpected expenses.
_Avoid_: emergency fund

**Investment pocket**:
The pocket role for accounts holding investments. Pockets does not track transactions from
these accounts — only the movement of money into and out of them as transfers.
_Avoid_: brokerage, investment account

**Virtual account**:
An account Pockets creates on its own, with no linked bank account, when a transaction
description names a well-known institution (for example, a brokerage such as Fidelity).
Virtual accounts are automatically classified as **Investment pockets**; the user may rename
them but cannot link a real account or convert them.
_Avoid_: synthetic account, placeholder account

**Transaction**:
A single financial movement pulled from an account. Every transaction is one of three types:
**Income**, **Expense**, or **Transfer**.
_Avoid_: payment, charge (as general terms)

**Income**:
A transaction that brings money into a pocket.

**Expense**:
A transaction that moves money out of a pocket.

**Transfer**:
A transaction that moves money between accounts. Transfers into or out of an **Investment
pocket** are **Invest** and **Divest** respectively.
_Avoid_: internal transfer, account transfer

**Invest**:
A transfer that moves money from a non-investment pocket into an investment pocket.
_Avoid_: contribution, deposit

**Divest**:
A transfer that moves money from an investment pocket back into a non-investment pocket.
_Avoid_: withdrawal, redemption

**Category**:
The classification a transaction is assigned to. Two levels deep — a category may have
sub-categories, but no deeper.
_Avoid_: tag, bucket, label

**Sub-category**:
A second-level classification nested under a category.

**Default categorization**:
The initial classification Pockets assigns to a transaction automatically.

**Categorization rule**:
A user-defined rule that overrides the default categorization of matching transactions.
_Avoid_: override, custom rule

**Budget**:
A spending limit a user sets on a category or sub-category for a period (monthly or calendar-year rolling).
_Avoid_: spending limit, allowance

**Cash flow**:
The movement of money into and out of the pockets over a period.

**Cash flow projection**:
An estimate of current-period cash flow based on expected monthly income and expenses.
_Avoid_: forecast