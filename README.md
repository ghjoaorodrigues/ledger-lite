# ledger-lite

A small accounts & transactions API in **ASP.NET Core (.NET 10) + EF Core + SQL Server**, with a React frontend, deployed on **Azure**.

**How it's built**: the .NET backend (`api/`) is hand-written. The repo enforces that with a hook that blocks AI edits in `api/`.
The tests, the React frontend and the CI drafts were AI-assisted, and reviewed by me.

## Architecture
```
React (Vite, Azure Static Web Apps)
        │  HTTPS / JSON
        ▼
ASP.NET Core minimal API (Azure App Service, Linux)
        │  EF Core
        ▼
Azure SQL Database   (local: SQL Server 2022 container)
```

## API contract
| Method | Route | Body | Returns |
|---|---|---|---|
| GET | `/health` | | `200 "ok"` |
| POST | `/accounts` | `{ "owner": "Ana", "currency": "EUR" }` | `201` account |
| GET | `/accounts` | | `200` list of accounts with balance |
| GET | `/accounts/{id}` | | `200` account with balance, or `404` |
| POST | `/accounts/{id}/transactions` | `{ "type": "Deposit" \| "Withdrawal", "amount": 100.00, "description": "..." }` | `201` transaction; `400` invalid amount; `409` insufficient funds |
| GET | `/accounts/{id}/transactions` | | `200` list, newest first |
| POST | `/transfers` | `{ "fromAccountId": 1, "toAccountId": 2, "amount": 50.00 }` | `201`; atomic (one DB transaction) |
| GET | `/accounts/{id}/summary?month=2026-10` | | *(should)* totals by type for the month |

Rules: amounts are `decimal`, > 0, max 2 decimals. Transfer between different accounts with the same currency only.

## Run locally
```bash
docker run -d --name ledger-sql -e ACCEPT_EULA=Y -e MSSQL_SA_PASSWORD='<your-local-pw>' -p 1433:1433 mcr.microsoft.com/mssql/server:2022-latest
cd api && dotnet user-secrets set "ConnectionStrings:Ledger" "Server=localhost,1433;Database=Ledger;User Id=sa;Password=<your-local-pw>;TrustServerCertificate=True" && cd ..
dotnet run --project api
dotnet test
cd frontend && npm install && npm run dev
```
