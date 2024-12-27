# Bitcoin-Stacks Bridge Smart Contract

A secure and robust cross-chain bridge enabling asset transfers between Bitcoin and Stacks blockchains.

## Overview

The BTC-Stacks Bridge facilitates trustless asset transfers between Bitcoin and Stacks networks through a validator-based system with multiple security checks and balances.

## Features

- **Secure Cross-Chain Transfers**: Enable safe asset movement between Bitcoin and Stacks
- **Validator Management**: Multi-validator system for enhanced security
- **Deposit Management**: Structured handling of deposits with confirmations
- **Emergency Controls**: Safety mechanisms including pause functionality and emergency withdrawals
- **Balance Tracking**: Accurate tracking of user balances and total bridged amounts

## Technical Architecture

### Core Components

1. **Token Interface**

```clarity
(define-trait bridgeable-token-trait
    ((transfer (uint principal principal) (response bool uint))
     (get-balance (principal) (response uint uint)))
)
```

2. **Data Storage**

- Deposits tracking
- Validator registry
- Bridge balances
- Validator signatures

### Security Parameters

- Minimum deposit: 100,000 units
- Maximum deposit: 1,000,000,000 units
- Required confirmations: 6

## Functions

### Administrative Functions

1. **initialize-bridge**

   - Initializes the bridge in active state
   - Restricted to contract deployer

2. **pause-bridge / resume-bridge**

   - Emergency controls for bridge operations
   - Restricted to contract deployer

3. **add-validator / remove-validator**
   - Validator management functions
   - Restricted to contract deployer

### Core Operations

1. **initiate-deposit**

   ```clarity
   (tx-hash (buff 32))
   (amount uint)
   (recipient principal)
   (btc-sender (buff 33))
   ```

   - Initiates cross-chain deposit
   - Requires validator authorization
   - Validates transaction parameters

2. **confirm-deposit**

   ```clarity
   (tx-hash (buff 32))
   (signature (buff 65))
   ```

   - Confirms deposit with validator signature
   - Updates user balances
   - Requires minimum confirmations

3. **withdraw**
   ```clarity
   (amount uint)
   (btc-recipient (buff 34))
   ```
   - Processes withdrawals to Bitcoin
   - Validates balance and amount
   - Updates bridge state

### Query Functions

1. **get-deposit**: Retrieves deposit details
2. **get-bridge-status**: Checks bridge operational status
3. **get-validator-status**: Verifies validator status
4. **get-bridge-balance**: Checks user balance

### Validation Functions

1. **is-valid-principal**: Validates Stacks addresses
2. **is-valid-btc-address**: Validates Bitcoin addresses
3. **is-valid-tx-hash**: Validates transaction hashes
4. **is-valid-signature**: Validates validator signatures
5. **validate-deposit-amount**: Validates deposit amounts

## Error Handling

The contract includes comprehensive error handling with specific error codes:

| Code | Description              |
| ---- | ------------------------ |
| 1000 | Unauthorized access      |
| 1001 | Invalid amount           |
| 1002 | Insufficient balance     |
| 1003 | Invalid bridge status    |
| 1004 | Invalid signature        |
| 1005 | Already processed        |
| 1006 | Bridge paused            |
| 1007 | Invalid validator        |
| 1008 | Invalid recipient        |
| 1009 | Invalid BTC address      |
| 1010 | Invalid transaction hash |
| 1011 | Invalid signature format |

## Security Considerations

1. **Access Control**

   - Strict validator management
   - Administrative function restrictions
   - Multi-signature requirements

2. **Transaction Validation**

   - Comprehensive parameter validation
   - Double-processing prevention
   - Balance checks

3. **Emergency Measures**
   - Bridge pause mechanism
   - Emergency withdrawal function
   - Invalid transaction protection

## Usage Examples

### Initiating a Deposit

```clarity
(contract-call? .btc-bridge initiate-deposit
    0x1234567890abcdef... ;; tx-hash
    u500000              ;; amount
    'ST1234567890ABCDEF  ;; recipient
    0x9876543210...      ;; btc-sender
)
```

### Processing a Withdrawal

```clarity
(contract-call? .btc-bridge withdraw
    u500000              ;; amount
    0x9876543210...      ;; btc-recipient
)
```

## Best Practices

1. Always verify transaction status before confirmation
2. Maintain secure validator key management
3. Monitor bridge status before operations
4. Verify recipient addresses carefully
5. Check balance availability before withdrawals

## Error Resolution

1. **ERR-UNAUTHORIZED**: Verify caller has appropriate permissions
2. **ERR-INVALID-AMOUNT**: Ensure amount within valid range
3. **ERR-BRIDGE-PAUSED**: Check bridge operational status
4. **ERR-ALREADY-PROCESSED**: Verify transaction uniqueness
5. **ERR-INSUFFICIENT-BALANCE**: Confirm adequate balance
