# Transfers are logical movements with exactly two legs

A Transfer links exactly two Transfer-classified Transactions, one for each Account side. A leg may
remain temporarily unmatched, but is then excluded from Income, Expense, and Budget reporting and
shown for reconciliation. Exactly two legs keeps matching and Cash Flow deterministic; fees remain
separate Expenses and split movements remain separate Transfers. Automatic matching requires equal
amount and currency, opposite directions, different Accounts, a short date window, and sufficient
supporting evidence, while ambiguous matches require user confirmation.
