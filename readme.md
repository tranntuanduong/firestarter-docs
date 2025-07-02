# 🔥 Firestarter Project Documentation

Firestarter is a decentralized ecosystem that includes features such as asset tokenization (via bonding curves), trading on a DEX, staking, affiliate marketing, and trust score management. The system is composed of multiple independent source code repositories, each responsible for specific components within the architecture.

---

## 📁 Source Code Overview

### 1. `firestarter-admin`
- **Description**: Admin dashboard for the entire application.
- **Features**:
  - Approve new assets/tokens.
  - Configure and trigger payout fees manually.
  - View all transactions in the system.
  - Manage the whitelist of tokens listed on the DEX.
  - Manage trust score settings (view history, configure tiers, penalties, etc.).

---

### 2. `firestarter-evm-fe`
- **Description**: The main web application for end users.
- **Key Features**:
  - Tokenize assets via bonding curve.
  - Buy/sell tokens on both bonding curve and DEX.
  - View candlestick charts and transaction history for bonding curve and DEX.
  - Widget & Referral system: share widget/link to earn affiliate rewards.
  - Trust Score management: staking, social connections, graduate tokens, and more.

---

### 3. `firestarter-backend`
- **Description**: Backend service that handles business logic and event processing.
- **Features**:
  - Listen to bonding curve creation events.
  - Listen to buy/sell/staking events.
  - Automatically list tokens on the DEX when bonding curve is completed.
  - Handle other core application logic.

---

### 4. `firestarter-evm-sm-hardhat`
- **Description**: Smart contract code for:
  - Bonding curve mechanism.
  - Forked Uniswap V2 DEX contracts.

---

### 5. `firestarter-staking-sc`
- **Description**: Smart contracts for staking functionality.

---

### 6. `firestarter-exchange-interface`
- **Description**: Frontend interface for the Uniswap V2 fork.
- **Features**: Swap tokens, manage liquidity pools, view charts and transactions.

---

### 7. `firestarter-subgraph`
- **Description**: Subgraph to index DEX transaction events.
- **Usage**: Display transaction data on the frontend.

---

### 8. `subgraph-candles`
- **Description**: Subgraph to index candlestick data.
- **Usage**: Display price charts in the main app and DEX interface.

---

### 9. `data-feed`
- **Description**: Middleware that converts and normalizes data from subgraphs.
- **Features**:
  - Fetch data from transaction and candles subgraphs.
  - Reformat data layout for frontend consumption.

---

## 🚀 Application Deployment Guide

### ✅ Deployment Order:

1. **Smart Contracts**
   - Deploy `firestarter-evm-smc-hardhat` (Uniswap V2 fork).
   - Deploy `firestarter-evm-smc-hardhat` (Bonding Curve).
   - Deploy `firestarter-staking-sc` (Staking contracts).

2. **Update Configurations**
   - Copy all deployed contract addresses into the configuration files of:
     - `firestarter-evm-fe`
     - `firestarter-admin`
     - `firestarter-backend`

3. **Deploy Subgraphs**
   - Deploy `firestarter-subgraph` (transactions).
   - Deploy `subgraph-candles` (candlestick data).
   - Recommended tool: [The Graph](https://thegraph.com/)

4. **Deploy `data-feed`**
   - Use `pairFactory` from Uniswap V2 fork to identify pairs.
   - Feed transaction and candle data to the frontend.

5. **Deploy Frontend Interfaces**
   - Deploy `firestarter-evm-fe` (main user interface).
   - Deploy `firestarter-exchange-interface` (DEX interface).
   - Deploy `firestarter-admin` (admin panel).

---

## 📌 Notes
- The system is centered around bonding curve logic and DEX interaction.
- Make sure to update `config` files with the correct contract addresses.
- Trust Score is a core mechanic for incentivizing user engagement through staking and social integrations.

---

## 🧠 For New Developers

If you're a new developer joining the team, follow this order to get familiar:

1. Understand the user flow in `firestarter-evm-fe`.
2. Explore backend logic in `firestarter-backend`.
3. Dive into smart contracts in `firestarter-evm-sm-hardhat` and `firestarter-staking-sc`.
4. Learn how data is indexed and transformed using `firestarter-subgraph`, `subgraph-candles`, and `data-feed`.
5. Understand how the admin team uses `firestarter-admin` to manage the app.

---

## 🛠 Suggested Tools
- Hardhat (Smart contract development and testing)
- The Graph CLI (Subgraph deployment)
- Node.js, TypeScript (FE/BE development)
- Vercel / Netlify / Render (Frontend hosting)
- Docker (optional, for backend or data-feed containerization)

---
![Alt text](https://github.com/tranntuanduong/firestarter-docs/blob/main/Untitled-2024-06-07-1450.png)

