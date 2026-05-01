You are a senior full-stack engineer.

Build a minimal, production-ready MVP for a **TRON wallet analyzer dashboard**.

## 🎯 Goal

Create a simple system that:

* Fetches TRON wallet transactions
* Stores them in MongoDB
* Processes basic analytics
* Displays results in a clean dashboard

---

## 🧱 Tech Stack (STRICT)

* Frontend: Next.js (App Router) + React
* UI: shadcn/ui + Tailwind
* Backend: Next.js API routes (no separate server)
* Database: MongoDB (with Mongoose)
* Cache: Redis (for caching API responses)
* Blockchain: TronWeb (TRON network)

---

## ⚙️ Core Features (KEEP IT SIMPLE)

### 1. Wallet Input

* Input field to enter TRON wallet address
* Button: "Analyze"

---

### 2. Data Fetching

* Use TronWeb or TronGrid API
* Fetch last ~50–100 transactions only (limit for performance)
* DO NOT fetch entire history

---

### 3. Database Design (Mongo)

Collections:

#### wallets

* address
* createdAt

#### transactions

* hash
* wallet
* from
* to
* amount
* timestamp

#### wallet_stats

* wallet
* totalIn
* totalOut
* netFlow
* txCount
* lastUpdated

---

### 4. Processing Logic (IMPORTANT)

For each transaction:

* if `to === wallet` → incoming
* if `from === wallet` → outgoing

Compute:

* totalIn
* totalOut
* netFlow = totalIn - totalOut
* txCount

---

### 5. Redis Caching

* Cache wallet stats for 5 minutes
* Key: wallet:{address}
* Avoid hitting TRON API repeatedly

---

### 6. API Routes

#### POST /api/analyze

* Input: wallet address
* Fetch transactions
* Store in Mongo
* Compute stats
* Cache in Redis
* Return stats

#### GET /api/wallet/[address]

* Return cached stats if exists
* Otherwise fetch from DB

---

## 🎨 Dashboard UI (shadcn)

Create a clean dashboard:

### Components:

* Card: Wallet Info
* Card: Total In
* Card: Total Out
* Card: Net Flow
* Card: Tx Count

### Table:

* Show last transactions (limit 20)

### UX:

* Loading state
* Error state
* Empty state

---

## 🧼 Constraints (VERY IMPORTANT)

* Keep everything SIMPLE
* No over-engineering
* No microservices
* No background workers
* No WebSockets
* No charts (just numbers + table)

---

## 🚀 Performance Rules

* Limit API calls
* Use Redis caching
* Use Mongo indexes:

  * wallet
  * timestamp

---

## 📁 Suggested Structure

/app
/api
/analyze
/wallet/[address]
/dashboard
/lib
db.ts
redis.ts
tron.ts
/models
wallet.ts
transaction.ts
walletStats.ts

---

## 🧠 Code Quality

* Use clean functions
* Separate logic from routes
* Use async/await properly
* Handle errors gracefully

---

## 🎯 Output

Generate:

* Full working codebase
* Minimal UI
* Clean structure
* Ready to run locally

---

## ⚡ Bonus (if simple)

* Auto-refresh button
* Copy wallet address button

---

DO NOT overcomplicate anything.
Focus on simplicity, clarity, and performance.



https://sub.lnsync.com/api/v1/client/subscribe?token=c4d6b0a3d5b6b9a5a49e1ad24e47d18e#FUi5obLP

