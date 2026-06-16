# LatterFix Backend API Server

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/Node.js-18%2B-green.svg)](https://nodejs.org)
[![Express](https://img.shields.io/badge/Express-4-black.svg)](https://expressjs.com)
[![Stellar](https://img.shields.io/badge/Stellar-Horizon-blue.svg)](https://stellar.org)

<div align="center">
  <h3>TaskManager Pro — Off-Chain Cache & Stellar Sync API</h3>
  <p><i>Bridges the frontend client dashboard with the on-chain Soroban smart contracts.</i></p>
</div>

---

## Overview

This repository houses the Express.js API cache server for the TaskManager Pro platform. It caches task and metadata status in an SQLite3 database and implements cryptographic transaction verification using the Stellar Horizon SDK to ensure payments are settled securely on-chain.

---

## API Endpoints

### Tasks & Escrows (`/api/tasks`)

*   **`GET /api/tasks`**  
    Fetches all available and cached tasks (Open, InProgress, Completed, Verified, Cancelled, Disputed).
*   **`POST /api/tasks`**  
    Registers a new task, caching details (title, description, reward amount, token, creator, tags).
*   **`POST /api/tasks/:id/assign`**  
    Claims a task, updating status to `InProgress` and caching the assignee wallet address.
*   **`POST /api/tasks/:id/submit`**  
    Submits developer delivery (e.g. GitHub pull request link), updating status to `Completed`.
*   **`POST /api/tasks/:id/complete`**  
    Submits a Stellar transaction hash (`txHash`), queries the Horizon Testnet to verify successful settlement, and closes the task.
*   **`POST /api/tasks/:id/dispute`**  
    Flags the task as `Disputed`, pausing the status state machine.
*   **`POST /api/tasks/:id/cancel`**  
    Cancels an unclaimed open task, updating state to `Cancelled`.
*   **`POST /api/tasks/:id/resolve`**  
    Resolves a dispute, updating status to `Verified`.

### Contributor Profiles (`/api/profiles`)

*   **`GET /api/profiles/:address`**  
    Retrieves contributor on-chain stats (username, bio, reputation score, completed task counts).
*   **`POST /api/profiles`**  
    Creates or updates user profiles (supporting name and bio edits).

---

## Technology Stack

- **Framework**: Express.js
- **Database**: SQLite3 (Local file-based store)
- **Stellar Client**: `@stellar/stellar-sdk` (Horizon client querying Testnet transaction detail API)
- **CORS**: Enforces secure cross-origin resource sharing for web dashboard clients

---

## Setup & Running

### Prerequisites

- [Node.js](https://nodejs.org) (v18 or higher)
- NPM

### Installation

```bash
npm install
```

### Start Server

```bash
npm start
```

The server will run on `http://localhost:3001` (or the port specified by the `PORT` environment variable).

---

## Verification Logic

When verifying a payment release, the API server executes:

```javascript
const { Horizon } = require("@stellar/stellar-sdk");
const server = new Horizon.Server("https://horizon-testnet.stellar.org");

async function checkStellarTx(txHash) {
  try {
    const tx = await server.transactionDetail(txHash);
    return tx.successful;
  } catch (error) {
    return false;
  }
}
```

This ensures that state transitions to `Verified` only occur when transaction validation has succeeded on-chain.
