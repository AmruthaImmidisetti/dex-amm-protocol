# DEX AMM Implementation

## Project Overview
This project presents a simplified **Decentralized Exchange (DEX)** built using the **Automated Market Maker (AMM)** approach, inspired by the core mechanics of Uniswap V2.  
Instead of relying on traditional order books, this DEX enables permissionless token trading through liquidity pools managed entirely by smart contracts.

Users can supply liquidity, withdraw it later with earned fees, and swap between two ERC-20 tokens in a fully decentralized and transparent manner.

The implementation prioritizes **mathematical correctness**, **security**, and **thorough automated testing**.

---

## Key Features
- Liquidity pool creation for a single token pair
- Initial and subsequent liquidity provisioning
- Internal liquidity (LP) accounting
- Proportional liquidity withdrawal with fee inclusion
- Token swaps governed by the constant product formula
- 0.3% swap fee retained in the pool
- Automatic fee accumulation for liquidity providers
- Explicit reserve and price tracking
- Event emission for liquidity and swap operations
- Fully containerized setup using Docker
- 25+ automated test cases with coverage ≥ 80%

---

## System Architecture

### Smart Contracts

#### `DEX.sol`
- Implements the core AMM logic
- Manages token reserves and liquidity shares
- Handles swaps, pricing, and fee mechanics
- Maintains LP balances internally without a separate LP token contract

#### `MockERC20.sol`
- Lightweight ERC-20 token for testing
- Allows minting to simulate different test scenarios

---

## Design Choices
- **Internal LP tracking** is used to reduce contract complexity
- **Solidity ^0.8.x** is leveraged for native overflow/underflow safety
- Reserves are **explicitly stored**, not inferred from token balances
- Trading fees remain in the pool to increase LP value over time
- No privileged roles or centralized ownership logic is introduced

---

## AMM Mathematics

### Constant Product Invariant
The exchange enforces the invariant:


x × y = k



Where:
- `x` → reserve of Token A  
- `y` → reserve of Token B  
- `k` → constant value that increases slightly due to fees  

All swap prices are derived from this relationship.

---

### Swap Fee Logic (0.3%)
Each swap applies a 0.3% fee using the following calculation:

```

amountInWithFee = amountIn × 997
amountOut = (amountInWithFee × reserveOut) /
(reserveIn × 1000 + amountInWithFee)

```

- 99.7% of the input amount affects pricing
- 0.3% remains locked in the pool
- Liquidity providers benefit automatically

---

## Liquidity Mechanics

### Initial Liquidity
The first liquidity provider establishes the initial price:

```

liquidityMinted = sqrt(amountA × amountB)

```

---

### Additional Liquidity
Subsequent deposits must maintain the existing price ratio:

```

liquidityMinted = (amountA × totalLiquidity) / reserveA

```

---

### Liquidity Removal
Withdrawals are calculated proportionally:

```

amountA = (liquidityBurned × reserveA) / totalLiquidity
amountB = (liquidityBurned × reserveB) / totalLiquidity

````

Withdrawn amounts include earned trading fees.

---

## Environment Setup

### Prerequisites
- Docker & Docker Compose
- Git
- Node.js (only required for non-Docker execution)

---

### Docker-Based Setup (Recommended)

```bash
git clone https://github.com/gollapallijayanthi/dex-amm.git
cd dex-amm
docker-compose up -d
````

#### Compile Contracts

```bash
docker-compose exec app npm run compile
```

#### Run Tests

```bash
docker-compose exec app npm test
```

#### Generate Coverage

```bash
docker-compose exec app npm run coverage
```

Coverage remains **≥ 80%**.

#### Stop Containers

```bash
docker-compose down
```

---

### Local Execution (Without Docker)

```bash
npm install
npm run compile
npm test
npm run coverage
```

---

## Deployment

A deployment script is provided at:

```
scripts/deploy.js
```

Deploy locally using:

```bash
npm run deploy
```

---

## Contract Deployment

This implementation is designed for **local development and evaluation only**.
No testnet or mainnet deployments are included.

---

## Limitations

* Supports only one trading pair
* No slippage protection parameters
* No transaction deadline enforcement
* No flash swaps or multi-hop routing

These exclusions are intentional to keep the AMM logic minimal and auditable.

---

## Security Notes

* Solidity 0.8+ arithmetic safety
* Input validation for all user actions
* Constant product invariant enforcement
* Fee retention inside the pool
* State updates occur before token transfers
* No centralized admin privileges

---

## Verification Summary

The following checks were completed successfully:

```bash
docker-compose exec app npm run compile
docker-compose exec app npm test
docker-compose exec app npm run coverage
```

**Results**

* All contracts compile successfully
* All 25+ test cases pass
* Coverage ≥ 80%
* Docker setup builds and runs correctly

---

## Conclusion

This project demonstrates the core mechanics behind AMM-based decentralized exchanges, highlighting how liquidity pools, mathematical invariants, and incentive-aligned fee structures enable trustless trading without intermediaries.
