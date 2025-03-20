# Automated Portfolio Management Protocol (APMP)

## Overview

The Automated Portfolio Management Protocol (APMP) is a non-custodial DeFi primitive enabling institutional-grade portfolio management on Bitcoin-secured infrastructure. The protocol facilitates:

- Dynamic multi-asset portfolio management
- Programmatic rebalancing strategies
- Cross-chain asset aggregation
- Enterprise-grade risk management frameworks
- Gas-optimized execution via Stacks L2

## Key Features

### Core Functionality

- **Threshold-based Rebalancing**: Autonomous portfolio adjustments based on time (24h) or market conditions
- **Multi-Asset Support**: Native integration with STX, sBTC, and SIP tokens
- **Bitcoin Security**: Cryptographic verification of management strategies anchored to Bitcoin
- **Capital Efficiency**: Batch settlements and L2 optimizations

### Institutional Features

- Customizable allocation strategies (1-10 assets)
- Performance-based fee structures
- Real-time portfolio health monitoring
- Cross-protocol yield position management

## Technical Specification

### Data Structures

#### Portfolio Configuration

```clarity
{
    owner: principal,
    created-at: uint,
    last-rebalanced: uint,
    total-value: uint,
    active: bool,
    token-count: uint
}
```

#### Asset Allocation

```clarity
{
    target-percentage: uint,  // Basis points (10000 = 100%)
    current-amount: uint,
    token-address: principal
}
```

### Protocol Parameters

| Parameter                  | Value | Description                            |
| -------------------------- | ----- | -------------------------------------- |
| `MAX-TOKENS-PER-PORTFOLIO` | 10    | Maximum supported assets per portfolio |
| `BASIS-POINTS`             | 10000 | Precision for percentage calculations  |
| `protocol-fee`             | 25    | 0.25% management fee (in basis points) |

## Smart Contract Functions

### Portfolio Management

| Function                      | Parameters                                 | Description                                                      |
| ----------------------------- | ------------------------------------------ | ---------------------------------------------------------------- |
| `create-portfolio`            | `(initial-tokens, percentages)`            | Initialize portfolio with token addresses and target allocations |
| `rebalance-portfolio`         | `(portfolio-id)`                           | Execute rebalancing according to strategy parameters             |
| `update-portfolio-allocation` | `(portfolio-id, token-id, new-percentage)` | Modify target allocation for specific asset                      |

### Administrative

| Function                      | Description                          |
| ----------------------------- | ------------------------------------ |
| `initialize`                  | Contract ownership management        |
| `calculate-rebalance-amounts` | Simulation of rebalance requirements |

## Error Codes

| Code | Error                    | Description                     |
| ---- | ------------------------ | ------------------------------- |
| u100 | ERR-NOT-AUTHORIZED       | Unauthorized access attempt     |
| u101 | ERR-INVALID-PORTFOLIO    | Nonexistent portfolio ID        |
| u102 | ERR-INSUFFICIENT-BALANCE | Insufficient asset balance      |
| u103 | ERR-INVALID-TOKEN        | Unsupported token contract      |
| u104 | ERR-REBALANCE-FAILED     | Rebalancing execution failure   |
| u105 | ERR-PORTFOLIO-EXISTS     | Duplicate portfolio creation    |
| u106 | ERR-INVALID-PERCENTAGE   | Allocation outside 0-100% range |
| u107 | ERR-MAX-TOKENS-EXCEEDED  | Portfolio asset limit exceeded  |
| u108 | ERR-LENGTH-MISMATCH      | Parameter length mismatch       |
| u109 | ERR-USER-STORAGE-FAILED  | Portfolio list storage failure  |
| u110 | ERR-INVALID-TOKEN-ID     | Nonexistent asset index         |

## Usage Examples

### Portfolio Creation (Clarinet)

```clarity
(contract-call? .apmp create-portfolio
    (list 'ST1PQHQKV0RJXZFY1DGX8MNSNYVE3VGZJSRTPGZGM.sip10-token
          'ST1PQHQKV0RJXZFY1DGX8MNSNYVE3VGZJSRTPGZGM.sbtc-token)
    (list u6000 u4000))  // 60% SIP, 40% sBTC
```

### Rebalancing Execution

```typescript
// Stacks.js implementation
const rebalanceTx = await makeContractCall({
  contractAddress: "SP3FBR2AGK5H9QBDH3EEN6DF8EK8JY7RX8QJ5SVTE",
  contractName: "apmp",
  functionName: "rebalance-portfolio",
  functionArgs: [uintCV(123)],
  sender: userAddress,
});
```

## Security Architecture

### Core Protections

1. **Non-Custodial Design**: Users retain full asset custody
2. **Bitcoin Finality**: All operations settle on Bitcoin L1
3. **Input Validation**:
   - Percentage precision (0.01% granularity)
   - Portfolio ownership checks
   - Asset whitelisting

### Audit Considerations

- Reentrancy protection through state locks
- Precision loss prevention in percentage math
- Front-running mitigation via batch settlements
