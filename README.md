# ledger-lite

A small banking API (accounts, deposits, withdrawals, transfers) built with .NET and hosted on Azure.

**Live:** https://ledgerlite-api-joao.azurewebsites.net/health

## Where it is today
- A health check endpoint, live on Azure.
- Automatic deployment: every push to `main` builds the app, runs the tests, and deploys only if they pass.
- A local SQL Server database running in Docker, ready for the next step.

## What's next
1. Accounts and transactions stored in the database.
2. Endpoints to create accounts, deposit, withdraw and transfer money.
3. Transfers that are all-or-nothing: money never leaves one account without arriving in the other.
4. Tests for the money rules (no negative amounts, no overdrafts).
5. A cloud database, and a simple React page on top.

## Run locally
```bash
dotnet run --project api
dotnet test
```
