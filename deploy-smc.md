# 🔥 Firestarter Smart Contract Deployment Guide

This guide walks you through the full process of deploying the Firestarter smart contracts on `baseSepolia` (or your desired network). The deployment includes:
- **Uniswap V2 Swap Infrastructure**
- **Bonding Curve Module**
- **Staking Module**
# package: firestarter-smart-contracts

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
