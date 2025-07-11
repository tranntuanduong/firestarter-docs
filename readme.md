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

### 2. `firestarter-frontend`
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

### 4. `firestarter-smart-contract`
- **Description**: Smart contract code for:
  - Bonding curve mechanism.
  - Forked Uniswap V2 DEX contracts.

---

### 5. `firestarter-staking-sc`
- **Description**: Smart contracts for staking functionality.

---

### 6. `firestarter-dex`
- **Description**: Frontend interface for the Uniswap V2 fork.
- **Features**: Swap tokens, manage liquidity pools, view charts and transactions.

---

### 7. `firestarter-subgraph-transactions`
- **Description**: Subgraph to index DEX transaction events.
- **Usage**: Display transaction data on the frontend.

---

### 8. `firestarter-subgraph-candles`
- **Description**: Subgraph to index candlestick data.
- **Usage**: Display price charts in the main app and DEX interface.

---

### 9. `firestarter-data-feed`
- **Description**: Middleware that converts and normalizes data from subgraphs.
- **Features**:
  - Fetch data from transaction and candles subgraphs.
  - Reformat data layout for frontend consumption.

---

## 🚀 Application Deployment Guide

### ✅ Deployment Order:

1. **Smart Contracts**
   - Deploy `firestarter-smart-contract` (Uniswap V2 fork, Bonding curve).
   - Deploy `firestarter-staking-sc` (Staking contracts).

2. **Update Configurations**
   - Copy all deployed contract addresses into the configuration files of:
     - `firestarter-frontend`
     - `firestarter-admin`
     - `firestarter-backend`

3. **Deploy Subgraphs**
   - Deploy `firestarter-subgraph-transactions` (transactions).
   - Deploy `subgraph-subgrapgh-candles` (candlestick data).
   - Recommended tool: [The Graph](https://thegraph.com/)

4. **Deploy `data-feed`**
   - Deploy `firestarter-data-feed`
   - Use `pairFactory` from Uniswap V2 fork to identify pairs.
   - Feed transaction and candle data to the frontend.

5. **Deploy Frontend Interfaces**
   - Deploy `firestarter-frontend` (main user interface).
   - Deploy `firestarter-dex` (DEX interface).
   - Deploy `firestarter-admin` (admin panel).

---

## 📌 Notes
- The system is centered around bonding curve logic and DEX interaction.
- Make sure to update `config` files with the correct contract addresses.
- Trust Score is a core mechanic for incentivizing user engagement through staking and social integrations.

---

## 🧠 For New Developers

If you're a new developer joining the team, follow this order to get familiar:

1. Understand the user flow in `firestarter-frontend`.
2. Explore backend logic in `firestarter-backend`.
3. Dive into smart contracts in `firestarter-smart-contract` and `firestarter-staking-sc`.
4. Learn how data is indexed and transformed using `firestarter-subgraph-transactions`, `firestarter-subgraph-candles`, and `firestarter-data-feed`.
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


---
# 🔥 Firestarter Smart Contract Deployment Guide

This guide walks you through the full process of deploying the Firestarter smart contracts on `baseSepolia` (or your desired network). The deployment includes:
- **Uniswap V2 Swap Infrastructure**
- **Bonding Curve Module**
- **Staking Module**
## package: firestarter-smart-contracts

---

## 🔧 Prerequisites

- Node.js and Yarn installed
- `hardhat` installed locally or via `npx`
- A valid **wallet private key**
- Access to Infura and BaseScan API (or use provided sample keys)

---

## A. Deploy Uniswap V2 Infrastructure

### Step 1. Compile the Contracts

```bash
npx hardhat compile
```

---

### Step 2. Set up Environment Variables

You can either use a `.env` file or set variables using Hardhat CLI:

```bash
npx hardhat vars set INFURA_API_KEY e85443a82e8b4135a8c09d61e495d0e2
npx hardhat vars set BASESCAN_API_KEY PVJQDGYW2115FEPQI7RH1V1NU16H8S3TPB
npx hardhat vars set ETHEREUM_PRIVATE_KEY "your-private-key"
```

---

### Step 3. Set `CREATE2_SALT` for Deployment Versioning

This value must be unique per deployment to prevent collision:

```bash
npx hardhat vars set CREATE2_SALT "0x0000000000000000000000000000000000000000000000000000000000000090"
```

> ⚠️ Remember to **change the salt value** each time you redeploy to version your contracts properly.

---

### Step 4. Deploy Uniswap V2 Swap Contracts

```bash
yarn deploySwap:baseSepolia
```

---

### Step 5. Update Pair Factory Address

Open `scripts/get-init-code.ts` and set `PAIR_FACTORY_ADDRESS` to the deployed address.

---

### Step 6. Get `initCodeHash` of PairFactory

```bash
yarn getInitCode:baseSepolia
```

---

### Step 7. Update `SwapLibrary.sol`

Edit the `contracts/libraries/SwapLibrary.sol` file:
- Replace `factory` with new **PairFactory address**
- Replace `init code hash` with the one returned from the previous step

---

### Step 8. Re-compile Contracts

```bash
yarn compile
```

---

## B. Deploy Bonding Curve Module

### Step 9. Deploy RWA Token (ERC20)

```bash
yarn deployRWAToken:baseSepolia
```
📝 Note: You can skip this step if you already have an existing ERC20 token. Simply use the address of that token in the following steps.

---

### Step 10. Configure Fee Parameters

Edit `modules/BondingCurveFactory.ts` to update:
- Trade fee
- Launch fee
- Listing fee
- Referral fee

> Don’t forget to recompile after editing:

```bash
yarn compile
```

---

### Step 11. Deploy Bonding Curve Implementation

```bash
yarn deployImpl:baseSepolia
```

---

### Step 12. Deploy Bonding Curve Factory

```bash
yarn deployFactory:baseSepolia
```

---

### Step 13. Verify Contracts on BaseScan

#### Bonding Curve Implementation

```bash
npx hardhat verify --network baseSepolia <Implementation_Address>
```

#### Bonding Curve Factory

```bash
npx hardhat verify --network baseSepolia <Factory_Address>   "0x69E763952156090A3241b10DA83514c103A3cfec"   "0x6BB9eB52CE719eA4125a874bcE363CEc7FD123D5"   "0x0a53674a83CA9a1DD8A49d9803cC2e6D60D2a2d8"   "0xE882757e37A99d9c96374ABc0F12BaE45ca4a055"   "0xB967dcBCDc6d8AC74f0AD5AF6Aa9e9A71E08920a"   "0x835570356fD6ffC11A24EF7D5789ef437DB5067b"   "3" "50" "1000000000000000000" "3"
```

---

## C. Deploy Staking Contract

> The staking contract is deployed independently.
step 1: edit file /utils/config/dev.json --> update RWA address token (orther field can ignore)
using deploy.bash to deploy staking
---

## D. Update Backend with Deployed Addresses

Update your backend `addresses.ts`:

```ts
export const addresses = {
  accessManager: ["AccessModule#AccessManager"],
  bondingCurve: ["BondingCurveImplementationModule#BondingCurve"],
  bondingCurveFactory: ["BondingCurveFactoryModule#BondingCurveFactory"],
  rwaTokenBond: '', // remove if unused
  rwaTokenMain: ["<Your ERC20 ANLOG TOKEN>"],
  pair: ["SwapModule#Pair"],
  pairFactory: ["SwapModule#PairFactory"],
  router: ["SwapRouterModule#Router"],
  staking: ["<Staking Address>"]
};
```

Set `CRAWL_FROM_BLOCKNUMBER`:

```ts
export const CRAWL_FROM_BLOCKNUMBER =
  env.network === 'mainnet' ? 16575126 : 27809100;
```

Get this block number from when the **Swap** contracts were deployed.

---

## ✅ Done!

You’ve successfully deployed the entire Firestarter smart contract suite on `baseSepolia`.

---
# Deploy BE [updateing]

### setup env
1. setup posgresql, redis, zookeeper, kafka, minio please run docker-compose.yml file
### note: the env value will only valid for video.
before run backend service, please double check configfile (factory address, bonding address, route address...etc)
```
version: '2'
services:
  postgres:
    image: postgres:15
    container_name: postgres
    restart: always
    environment:
      - POSTGRES_USER=root
      - POSTGRES_PASSWORD=1
      - POSTGRES_DB=rwa
    ports:
      - "5434:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:latest
    container_name: redis
    restart: always
    command: ["redis-server", "--requirepass", "foobared"]
    ports:
      - "6397:6379"
    volumes:
      - redis_data:/data

  minio:
    image: minio/minio
    container_name: minio
    restart: always
    environment:
      - MINIO_ROOT_USER=development
      - MINIO_ROOT_PASSWORD=123456789
    command: server /data --console-address ":9001"
    ports:
      - "32126:9000"
      - "9001:9001"
    volumes:
      - minio_data:/data

  zookeeper:
    image: confluentinc/cp-zookeeper:7.3.2
    container_name: zookeeper
    restart: always
    environment:
      - ZOOKEEPER_CLIENT_PORT=2181
      - ZOOKEEPER_TICK_TIME=2000

  kafka:
    image: confluentinc/cp-kafka:7.3.2
    container_name: kafka
    restart: always
    depends_on:
      - zookeeper
    ports:
      - "39092:39092"
    environment:
      - KAFKA_BROKER_ID=1
      - KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:39092
      - KAFKA_LISTENER_SECURITY_PROTOCOL_MAP=PLAINTEXT:PLAINTEXT
      - KAFKA_INTER_BROKER_LISTENER_NAME=PLAINTEXT
      - KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1

volumes:
  postgres_data:
  redis_data:
  minio_data:
```

2. update env file
```
\# local development production
NODE_ENV=development
PORT='3011' # app 
WORKER_PORT='3002' # listener
NETWORK=testnet # testnet mainnet

POSTGRES_URL=postgres://root:1@localhost:5434/rwa

REDIS_URL=redis://default:foobared@localhost:6397/0

# minio config
MINIO_ACCESS_KEY=development
MINIO_SECRET_KEY=123456789
BUCKET_NAME=development
MINIO_HOST=localhost
MINIO_PORT=32126

# ipfs pinata.cloud
PINATA_JWT=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySW5mb3JtYXRpb24iOnsiaWQiOiJiY2E3MjAxNC05YjQwLTRiNDgtOTU2Yy1iYWRkNzQwYTQwMDEiLCJlbWFpbCI6Imx1Y2Fzdi53c3BhY2VAZ21haWwuY29tIiwiZW1haWxfdmVyaWZpZWQiOnRydWUsInBpbl9wb2xpY3kiOnsicmVnaW9ucyI6W3siZGVzaXJlZFJlcGxpY2F0aW9uQ291bnQiOjEsImlkIjoiRlJBMSJ9LHsiZGVzaXJlZFJlcGxpY2F0aW9uQ291bnQiOjEsImlkIjoiTllDMSJ9XSwidmVyc2lvbiI6MX0sIm1mYV9lbmFibGVkIjpmYWxzZSwic3RhdHVzIjoiQUNUSVZFIn0sImF1dGhlbnRpY2F0aW9uVHlwZSI6InNjb3BlZEtleSIsInNjb3BlZEtleUtleSI6IjVjOWNiMzk3ZTRhODRhZDc1YzY0Iiwic2NvcGVkS2V5U2VjcmV0IjoiMjU0ODc0MTZmYmQ4ZTM4MzY4OWQ4MjZhOGQ4ZmY5ZjE2NmRiYjQ4ZmVmMzVkODFhMjFjYTYwZDQ0NzQ0NWJlNSIsImV4cCI6MTc3MDE5NzY3Mn0.0VyPoNW61ia2ulnXTne0IWZteQd74PHDrAcmRWApt3Y

# rpc for mainnet, testnet
RPC=https://base-sepolia-rpc.publicnode.com
RPC_WS=wss://base-sepolia-rpc.publicnode.com

#RPC=https://base-sepolia-rpc.publicnode.com
#RPC_WS=wss://base-sepolia-rpc.publicnode.com

#RPC=https://base-sepolia.g.alchemy.com/v2/xX8Hb0UL7nxiTSXj583h0
#RPC_WS=wss://bas
```

3. install node_modules
4. generate typechain
5. sync & generate prisma
6. build code
7. run code

---
# Firestarter Frontend

1. edit file /src/smart-contract/addresses.ts
```ts
const addresses = {
  accessManager: "0x51dab2305B424682F41EE75eF7249145B71ee607",
  bondingCurve: "0xc192b472f568E7694Fd0f7Ccbbb75076b80f8aBf",
  bondingCurveFactory: "0x52e13e7A39f9aA6b5f981dC7055efD685bBaeb45",
  rwaTokenBond: [remove this one],
  rwaTokenMain: "0xc705343DE5Cb1Cd9cE6684b6ca321b31D540bf5c",
  pair: "0xEfD4FeFE9d2e3542b1102F5B7004241bE56ab7f4",
  pairFactory: "0xDE9A867A37105A156a05FcD208779aE349188cE5",
  router: "0xe0e4E95C9E4aa75516d40aBA8B320b5516b66c10",
  stakingManager: "0x93A8a091D433bb321918C4Bb468FB266e7486dc8",
  stakingBooster: "0xc23bBf2d8B28643E4B380672c0A4dBB40Dfa5469",
  USDT: "0x34f2154457ccd316f26C7a9Be5D4B45d06af665D",
  WETH: "0xF96c7bDE65A8F6d765192A64Bc75bfa05CEc3fEf",
};
```

2. update env file
```
VITE_ENV=devnet
VITE_API_URL=https://abc.com
VITE_SOCKET_PATH=wss://abc.com
VITE_NETWORK=devnet
VITE_CREATION_FEE=20
VITE_BONDING_CURVE_CAP=25000

# Google OAuth Configuration
VITE_GOOGLE_CLIENT_ID=364453051602-lxtgd2g9j3s1lr00pj5otgo9c30o1u0t.apps.googleusercontent.com

# GitHub OAuth Configuration  
VITE_GITHUB_CLIENT_ID=Ov23liOJSLQkrbZj46J0

# Discord OAuth Configuration
VITE_DISCORD_CLIENT_ID=1379791093586989108
VITE_DISCORD_CLIENT_SECRET=BioZh8WrCjR8AfhfxXk_cANhdCu8E4xc

# X/Twitter OAuth Configuration
VITE_TWITTER_CLIENT_ID=T3pWbWVEY29pR3doaldteWhUdUI6MTpja2
VITE_TWITTER_CLIENT_SECRET=9R8RECRDHOuFpZCT_7U4FwrPYI4WjAGllHmJq5x # Note: Replace with actual secret

# Blockpass KYC Configuration
VITE_BLOCKPASS_CLIENT_ID=blockpassid

# Firebase Configuration
VITE_FIREBASE_API_KEY=AIzaSyCbVOgKK54EZG3Qo3VPsbkrk4u3AziCceM
VITE_FIREBASE_PROJECT_ID=firestarter-48198
VITE_FIREBASE_AUTH_DOMAIN=firestarter-48198.firebaseapp.com
VITE_FIREBASE_STORAGE_BUCKET=firestarter-48198.firebasestorage.app
VITE_FIREBASE_MESSAGING_SENDER_ID=513830874400
VITE_FIREBASE_APP_ID=1:513830874400:web:f1647b2f315dcb7c683b45
```

3. install node_modules (currently using pnpm, please keep this)
4. build code
5. deploy with vercel or pm2 or dockers.

---
# Firestarter Admin

1. edit file /src/smart-contract/addresses.ts
```ts
const addresses = {
  accessManager: "0x51dab2305B424682F41EE75eF7249145B71ee607",
  bondingCurve: "0xc192b472f568E7694Fd0f7Ccbbb75076b80f8aBf",
  bondingCurveFactory: "0x52e13e7A39f9aA6b5f981dC7055efD685bBaeb45",
  rwaTokenBond: [remove this one],
  rwaTokenMain: "0xc705343DE5Cb1Cd9cE6684b6ca321b31D540bf5c",
  pair: "0xEfD4FeFE9d2e3542b1102F5B7004241bE56ab7f4",
  pairFactory: "0xDE9A867A37105A156a05FcD208779aE349188cE5",
  router: "0xe0e4E95C9E4aa75516d40aBA8B320b5516b66c10",
  stakingManager: "0x93A8a091D433bb321918C4Bb468FB266e7486dc8",
  stakingBooster: "0xc23bBf2d8B28643E4B380672c0A4dBB40Dfa5469",
  USDT: "0x34f2154457ccd316f26C7a9Be5D4B45d06af665D",
  WETH: "0xF96c7bDE65A8F6d765192A64Bc75bfa05CEc3fEf",
};
```

2. update env file
```
VITE_ENV=dev
VITE_NETWORK=devnet
VITE_API_URL=https://api.example.com
```
3. install node_modules (currently using pnpm, please keep this)
4. build code
5. deploy with vercel or pm2 or dockers.

---
# Firestarter-subgraph-transactions
1. edit subgraph.yaml file 
```
specVersion: 0.0.5
description: FirestarterSwap
schema:
  file: ./schema.graphql
dataSources:
  - kind: ethereum/contract
    name: Factory
    network: base-sepolia
    source:
      address: [pairFactory]
      abi: Factory
      startBlock: 27517178 //replace suitable block
    mapping:
      kind: ethereum/events
      apiVersion: 0.0.6
      language: wasm/assemblyscript
      file: ./src/mappings/factory.ts
      entities:
        - Pair
        - Token
      abis:
        - name: Factory
          file: ./abis/Factory.json
        - name: ERC20
          file: ./abis/ERC20.json
        - name: ERC20NameBytes
          file: ./abis/ERC20NameBytes.json
        - name: ERC20SymbolBytes
          file: ./abis/ERC20SymbolBytes.json
      eventHandlers:
        - event: PairCreated(indexed address,indexed address,address,uint256)
          handler: handlePairCreated
templates:
  - kind: ethereum/contract
    name: Pair
    network: base-sepolia
    source:
      abi: Pair
    mapping:
      kind: ethereum/events
      apiVersion: 0.0.6
      language: wasm/assemblyscript
      file: ./src/mappings/core.ts
      entities:
        - Pair
        - Token
      abis:
        - name: Factory
          file: ./abis/Factory.json
        - name: Pair
          file: ./abis/Pair.json
      eventHandlers:
        - event: Mint(indexed address,uint256,uint256)
          handler: handleMint
        - event: Burn(indexed address,uint256,uint256,indexed address)
          handler: handleBurn
        - event: Swap(indexed address,uint256,uint256,uint256,uint256,indexed address)
          handler: handleSwap
        - event: Transfer(indexed address,indexed address,uint256)
          handler: handleTransfer
        - event: Sync(uint112,uint112)
          handler: handleSync

```

2. register an account on https://thegraph.com/studio/login
3. create a subgraph
4. select network
5. authenticate: graph auth [your key]
6. generate & build: graph codegen && graph build
7. deploy: graph deploy test

---
# Firestarter-subgraph-candles
1. edit subgraph.yaml file
```
specVersion: 0.0.4
description: DEX trades candles (5m/15m/1h/4h/1d/1w)
schema:
  file: ./schema.graphql
dataSources:
  - kind: ethereum/contract
    name: PairFactory
    network: base-sepolia
    source:
      address: [pairFactory address]
      abi: PairFactory
      startBlock: 27517170 //suitable block
    mapping:
      kind: ethereum/events
      apiVersion: 0.0.6
      language: wasm/assemblyscript
      file: ./src/mapping.ts
      entities:
        - Pair
      abis:
        - name: PairFactory
          file: ./abis/PairFactory.json
      eventHandlers:
        - event: PairCreated(address,address,address,uint256,indexed address)
          handler: handleNewPair
templates:
  - kind: ethereum/contract
    name: Pair
    network: base-sepolia
    source:
      abi: Pair
    mapping:
      kind: ethereum/events
      apiVersion: 0.0.6
      language: wasm/assemblyscript
      file: ./src/mapping.ts
      entities:
        - Pair
      abis:
        - name: Pair
          file: ./abis/Pair.json
      eventHandlers:
        - event: Swap(uint256,uint256,uint256,uint256,address,uint256,indexed address)
          handler: handleSwap

```

2. register an account on https://thegraph.com/studio/login
3. create a subgraph
4. select network
5. authenticate: graph auth [your key]
6. generate & build: graph codegen && graph build
7. deploy: graph deploy test

---
# Firestarter-data-feed
1. setup env
```
GRAPH_CLIENT=https://api.studio.thegraph.com/query/115248/candle-v-01/version/latest
GRAPH_CLIENT_TRANSACTIONS=https://api.studio.thegraph.com/query/115248/transactions-v-01/version/latest
```
2. update WETH_ADDRESS, USDT_WETH_PAIR, WHITELIST in the /src/mappings/pricing.ts
3. install node_modules: currently we using yarn, so if you guys use npm or pnpm, maybe it can have bug here (yarn.lock is very important)
4. build srource code with yarn build
5. deploy code: using pm2 or docker its your option.

---
# Firestarter-dex
1. setup env
```
REACT_APP_CHAIN_ID="84532" /chainid
SKIP_PREFLIGHT_CHECK=true
```

2. update the config file
```ts
const config = {
  ROUTER_ADDRESS: '0xe0e4E95C9E4aa75516d40aBA8B320b5516b66c10',
  FACTORY_ADDRESS: '0xDE9A867A37105A156a05FcD208779aE349188cE5',
  INIT_CODE_HASH: '0x7c60607a50a284528a3db57b2c17f62453d764f683ae1557a4710498beba6ee0',
  WETH: '0x4200000000000000000000000000000000000006'
}
```
3. init pair USDT_WETH_PAIR that can use to calculate priceUSD on dex


