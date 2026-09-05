# Pockets

An opinionated personal expense tracking and budgeting application

## High Level Overview

### Transaction Tracking

Retrieves transactions from the user's Accounts and categorizes them in default and user custom
categories and sub-categories (only 2 levels deep). Every transaction is one of **Income**,
**Expense**, or **Transfer**.

- Classifies Accounts into pockets based on transaction descriptions
  - **Daily pocket** - Used to receive income and pay expenses
  - **Credit card pocket** - Used to pay expenses
  - **Saving pocket** - Used to save money for expected, budgeted expenses over the year
  - **Emergency pocket** - Used to cover unexpected expenses
  - **Investment pocket** - Holds investments; transactions are not tracked from these accounts,
    only Invest and Divest transfers in and out
- **Virtual accounts** are materialized when a transaction description names a well-known
  institution (e.g. Fidelity) and are classified as Investment pockets; they can be renamed but
  not linked to a real Account or converted
- Allow easy categorization and rule-based categorization (Categorization rules) that overrides
  default categorization
- Provides visualizations and insights into the user's spending habits
  - Monthly spending overall and by category
  - Month over month trends overall and by category
  - Current month spending by category

### Cash Flow

- Past months cash flow
- Current month cash flow actual and projected
  - Projected based on monthly expenses and income
- Suggests transfers between pockets based on cash flow

### Budgeting

- Helps the user set budgets by categories and sub-categories
- Supports monthly and yearly (rolling over) budgets by calendar year
- Provides visualizations and insights into the user's budget adherence
  - Actual vs. budgeted spending on a period (monthly or yearly)
  - Current month actual vs. budgeted spending