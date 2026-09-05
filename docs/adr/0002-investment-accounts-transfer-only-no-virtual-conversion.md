# Investment accounts are transfer-only; virtual accounts are not convertible

Pockets does not pull or track transactions from investment accounts — it only sees money move
into and out of them via Invest and Divest transfers. When a transaction description names a
well-known institution (e.g. Fidelity), Pockets materializes a virtual account and classifies it
as an Investment pocket. Virtual accounts are rename-only and can never be linked to a real
account or converted. This keeps the scope tight: we deliberately do not attempt investment
tracking, which would require position and valuation data outside this app's mandate.