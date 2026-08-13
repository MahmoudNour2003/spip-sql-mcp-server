# 🗄️ SPIP System - SQL Server Model Context Protocol (MCP) Server

Part of the **SPIP (Smart Procurement & Invoice Processing) Ecosystem**.

A production-ready **Model Context Protocol (MCP) Server for Microsoft SQL Server** built with Node.js and TypeScript. This server exposes 5 read-only database tools to AI Agents (`execute_select`, `list_tables`, `describe_table`, `get_relationships`, `get_database_info`) while enforcing **database kernel-level Row-Level Security (RLS)** using atomic SQL batch execution.

---

## ✨ Key Features

- **Model Context Protocol (MCP) Compliance**: Standard SSE transport and direct HTTP tool execution APIs using `@modelcontextprotocol/sdk`.
- **Atomic Session Context & Row-Level Security (RLS)**: Combines `sp_set_session_context` with `SELECT` queries in a single atomic SQL batch execution, guaranteeing same-connection RLS isolation for multi-tenant users.
- **AST SQL Query Validation**: Restricts query execution strictly to read-only `SELECT` statements, blocking `INSERT`, `UPDATE`, `DELETE`, `DROP`, or DDL operations.
- **5 Built-In Tools**:
  1. `execute_select`: Runs validated SELECT queries with dynamic RLS user context (`userId`).
  2. `list_tables`: Returns all database table names and schemas.
  3. `describe_table`: Returns column names, data types, primary keys, and nullability for a table.
  4. `get_relationships`: Returns foreign key relationships between database tables.
  5. `get_database_info`: Returns database version and metadata overview.

---

## 🛠️ Tech Stack

- **Runtime**: Node.js v20+ / v22+
- **Language**: TypeScript
- **Framework**: Express.js
- **SDK**: `@modelcontextprotocol/sdk`
- **Database Driver**: `node-mssql` (Microsoft SQL Server 2019/2022)

---

## 🚀 Quick Start (Without Docker)

### 1. Installation
```bash
git clone https://github.com/MahmoudNour2003/spip-sql-mcp-server.git
cd spip-sql-mcp-server
npm install
```

### 2. Environment Setup (`.env`)
Create a `.env` file in the project root:
```ini
MCP_PORT=3001
DB_HOST=127.0.0.1
DB_PORT=1433
DB_NAME=SPIP_DB
DB_USER=AI_CHAT
DB_PASSWORD=AI@123
DB_ENCRYPT=false
DB_TRUST_SERVER_CERT=true
```

### 3. Build & Run
```bash
# Build TypeScript to JavaScript
npm run build

# Start the MCP Server
node dist/server.js
```

---

## 🧪 Testing & Verification

Run the built-in standalone test suite:
```bash
node dist/standalone-test.js
```
**Expected Output**:
```text
✅ Successfully connected to SQL Server (SPIP_DB)!
Tables count: 41
```

---

## 🔒 Row-Level Security (RLS) Execution Details

When `execute_select` is called with a `userId` parameter (e.g. `userId = 5`), the MCP Server executes the following atomic batch query:

```sql
EXEC sp_set_session_context @key = N'UserId', @value = 5;
SELECT TOP 1000 Id, InvoiceNumber, TotalAmount, Status FROM dbo.Invoices ORDER BY InvoiceDate DESC;
```

SQL Server Security Policies (`Security.InvoiceSecurityPolicy`) intercept the query and return **only the rows belonging to User 5**.

---

## 📄 License
MIT License
