# BitLayer Protocol Documentation

_Secure Bitcoin-Native NFT Exchange with DeFi Integration_

## Overview

BitLayer is a decentralized protocol combining Bitcoin's security with advanced DeFi capabilities through Stacks Layer 2. This institutional-grade solution enables trustless NFT operations while maintaining compatibility with Bitcoin's base layer settlement.

## Key Features

### Core Infrastructure

- **Bitcoin-Secured Transactions**  
  All operations settled on Bitcoin via Stacks L2 with 10-minute finality
- **Clarity Smart Contracts**  
  Predictable execution with on-chain verification
- **Non-Custodial Design**  
  Full user control of assets with no protocol custody

### NFT Operations

- **Collateralized Minting**
  - 150% STX collateral requirement (configurable)
  - On-chain metadata storage (256 char URIs)
- **P2P Marketplace**
  - Trustless order matching
  - 2.5% protocol fee on sales
- **Fractional Ownership**
  - Share-based ownership structures
  - Transferable ownership units

### DeFi Integration

- **Yield-Generating Staking**
  - 5% base APY (adjustable)
  - Block-based reward calculation
  - Real-time yield tracking
- **Protocol-Controlled Liquidity**
  - Automated fee distribution
  - Reserve-backed market stability

## Technical Specification

### Data Structures

#### NFT Registry (`tokens` map)

```clarity
{
  owner: principal,
  uri: (string-ascii 256),
  collateral: uint,
  is-staked: bool,
  stake-timestamp: uint,
  fractional-shares: uint
}
```

#### Marketplace Listings (`token-listings` map)

```clarity
{
  price: uint,
  seller: principal,
  active: bool
}
```

### Core Functions

#### 1. NFT Minting

```clarity
(mint-nft (uri (string-ascii 256)) (collateral uint))
```

- Requires STX collateral at 150% ratio
- Enforces URI validation (1-256 chars)
- Increments global supply counter

#### 2. Asset Transfers

```clarity
(transfer-nft (token-id uint) (recipient principal))
```

- Ownership validation
- Blocked for staked assets
- Recipient blacklist prevention

#### 3. Marketplace Operations

```clarity
(list-nft (token-id uint) (price uint))
(purchase-nft (token-id uint))
```

- Price > 0 validation
- Automated fee distribution
- Atomic swap execution

#### 4. Fractional Ownership

```clarity
(transfer-shares (token-id uint) (recipient principal) (share-amount uint))
```

- Share-based transfers
- Balance checks
- Anti-overflow protection

#### 5. Yield Staking

```clarity
(stake-nft (token-id uint))
(unstake-nft (token-id uint))
```

- Time-locked staking
- Block-based yield calculation
  - Annualized 5% base rate
  - 52560 blocks/year assumption
- Auto-claim on unstaking

### Financial Parameters

| Parameter              | Value | Description                   |
| ---------------------- | ----- | ----------------------------- |
| `min-collateral-ratio` | 150%  | Minimum STX collateralization |
| `protocol-fee`         | 25bps | Transaction fee (2.5%)        |
| `yield-rate`           | 50bps | Base annual yield (5%)        |

## Error Handling

### Error Codes Table

| Code | Constant                    | Description                 |
| ---- | --------------------------- | --------------------------- |
| u100 | err-owner-only              | Unauthorized admin action   |
| u101 | err-not-token-owner         | Invalid ownership claim     |
| u102 | err-insufficient-balance    | Insufficient token balance  |
| u103 | err-invalid-token           | Nonexistent NFT ID          |
| u104 | err-listing-not-found       | Inactive marketplace entry  |
| u105 | err-invalid-price           | Zero-value price prohibited |
| u106 | err-insufficient-collateral | Below minimum collateral    |
| u107 | err-already-staked          | Duplicate staking attempt   |
| u108 | err-not-staked              | Invalid unstaking request   |
| u109 | err-invalid-percentage      | Fee parameter overflow      |
| u110 | err-invalid-uri             | Metadata URI violation      |
| u111 | err-invalid-recipient       | Self-transfer prevention    |
| u112 | err-overflow                | Arithmetic safety check     |

## Governance & Fees

### Protocol Economics

- **Fee Structure**

  - 2.5% transaction tax on sales
  - Fees accrue to protocol treasury
  - DAO governance ready (future upgrade)

- **Collateral Management**
  - STX-denominated collateral pool
  - Dynamic ratio adjustments (DAO controlled)

## Security Model

### Compliance Features

1. **FATF Travel Rule**  
   On-chain audit trails for regulatory compliance
2. **Non-Custodial Architecture**  
   Zero protocol access to user funds
3. **Bitcoin Finality**  
   All transactions inherit Bitcoin's immutability

### Audit Considerations

- Overflow protection via `safe-add`
- Reentrancy protection through state checks
- Time-locked operations using block height
- Principal validation for all transfers

## Development Guide

### Contract Interactions

**Checking NFT Info**

```clarity
(get-token-info (token-id uint))
=> {
  owner: principal,
  uri: (string-ascii 256),
  collateral: uint,
  is-staked: bool,
  stake-timestamp: uint,
  fractional-shares: uint
}
```

**Calculating Staking Rewards**

```clarity
(calculate-rewards (token-id uint))
=> uint
// Returns accumulated yield in STX

```
