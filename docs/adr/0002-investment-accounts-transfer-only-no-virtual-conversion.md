# Investment accounts are transfer-only; virtual accounts are not convertible

Pockets does not import or track positions, trades, valuations, or other activity inside Investment
Accounts. It represents only observed Invest, Divest, and Investment transfers and derives a Net
invested amount rather than market value. When a Transfer identifies a known investment institution
without a corresponding Account, Pockets may create a renameable Virtual Account with stable
institution identity and matching aliases. A Virtual Account cannot be linked or converted, although
compatible Virtual Investment Accounts may merge; a Linked Account at the same institution may
coexist. This deliberately avoids expanding Pockets into an investment tracker.
