# Pocket is a semantic layer over a one-to-one Account

Accounts represent bank or credit accounts, while a Pocket is the semantic role applied to an
Account. Every Account has exactly one Pocket, which may temporarily be Unclassified, and multiple
Accounts may share a role. This keeps imported balances and Transactions attached to real Accounts
while Cash Flow, reporting, and suggestions reason through Pocket roles. A user may change a role
after seeing its consequences; derived results are recalculated without overwriting manual
Transaction choices.
