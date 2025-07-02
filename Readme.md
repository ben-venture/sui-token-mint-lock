# Sui Token Mint & Lock Example

A comprehensive example demonstrating secure token minting and treasury management on the Sui blockchain. This project implements a controlled token minting system with epoch-based limits, admin controls, and a faucet mechanism.

## 🏗️ Architecture Overview

This project consists of three main modules that work together to provide secure token minting capabilities:

### Core Modules

1. **`example_coin.move`** - Example token implementation
2. **`treasury_lock.move`** - Secure treasury management with minting controls
3. **`faucet.move`** - User-friendly faucet for controlled token distribution

## 🔧 Features

### Treasury Lock System
- **Epoch-based minting limits**: Configurable maximum mint amount per epoch
- **Admin controls**: Ability to ban/unban mint capabilities
- **Secure treasury management**: Treasury cap is locked and controlled
- **Automatic epoch tracking**: Mint limits reset automatically each epoch

### Faucet System
- **Shared faucet objects**: Anyone can use the faucet to mint tokens
- **Controlled distribution**: Respects epoch-based limits
- **Direct transfer**: Minted tokens are automatically transferred to the caller

### Security Features
- **Capability-based access control**: Only authorized mint capabilities can mint
- **Ban/unban functionality**: Admin can revoke minting privileges
- **Epoch boundary enforcement**: Limits are strictly enforced across epochs

## 📁 Project Structure

```
sui-token-mint-lock/
├── sources/
│   ├── example_coin.move      # Example token implementation
│   ├── treasury_lock.move     # Treasury management and minting controls
│   └── faucet.move           # Faucet for controlled token distribution
├── tests/
│   ├── treasury_lock_tests.move  # Comprehensive treasury lock tests
│   └── faucet_tests.move         # Faucet functionality tests
├── Move.toml                  # Project configuration
└── README.md                 # This file
```

## 🚀 Getting Started

### Prerequisites

- Sui CLI installed and configured
- Sui network access (local, devnet, testnet, or mainnet)

### Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd sui-token-mint-lock
```

2. Build the project:
```bash
sui move build
```

3. Run tests:
```bash
sui move test
```

## 📖 Usage Examples

### 1. Creating a Token with Treasury Lock

```move
// Initialize the token
let cap = coin::create_currency(EXAMPLE_COIN{}, ctx);

// Create treasury lock
let admin_cap = treasury_lock::new_lock(cap, ctx);

// Create mint capability with 500 max mint per epoch
let mint_cap = treasury_lock::create_mint_cap(&admin_cap, 500, ctx);

// Create faucet
faucet::create_faucet(mint_cap, ctx);
```

### 2. Using the Faucet

```move
// Mint tokens through faucet
faucet::mint_and_transfer(
    &mut faucet,
    &mut lock,
    100,  // amount to mint
    ctx
);
```

### 3. Admin Operations

```move
// Ban a mint capability
treasury_lock::ban_mint_cap_id(&admin_cap, &mut lock, mint_cap_id);

// Unban a mint capability
treasury_lock::unban_mint_cap_id(&admin_cap, &mut lock, mint_cap_id);
```

## 🔍 Module Details

### `example_coin.move`

Defines the `EXAMPLE_COIN` token and provides initialization functions:

- `init()`: Creates the token and transfers treasury cap to caller
- `create_faucet()`: Sets up a complete minting system with treasury lock and faucet
- `init_for_testing()`: Test-only initialization function

### `treasury_lock.move`

Core treasury management module with the following key structures:

#### `TreasuryLock<T>`
- Contains the locked treasury cap
- Maintains list of banned mint authorities
- Shared object accessible by all

#### `LockAdminCap<T>`
- Admin capability for managing the treasury lock
- Required for creating mint capabilities and administrative operations

#### `MintCap<T>`
- Minting capability with epoch-based limits
- Tracks minting history and enforces limits

#### Key Functions:
- `new_lock()`: Creates a new treasury lock
- `create_mint_cap()`: Creates a minting capability with limits
- `mint_balance()`: Mints tokens with limit enforcement
- `ban_mint_cap_id()` / `unban_mint_cap_id()`: Admin controls

### `faucet.move`

User-friendly faucet implementation:

#### `Faucet<T>`
- Contains a mint capability
- Shared object for public access

#### Key Functions:
- `create_faucet()`: Creates a new faucet
- `mint_balance()`: Mints tokens and returns balance
- `mint_and_transfer()`: Mints and transfers tokens to caller

## 🧪 Testing

The project includes comprehensive tests covering:

### Treasury Lock Tests (`treasury_lock_tests.move`)
- ✅ Basic minting functionality
- ✅ Epoch limit enforcement
- ✅ Epoch boundary behavior
- ✅ Ban/unban functionality
- ✅ Error handling for exceeded limits

### Faucet Tests (`faucet_tests.move`)
- ✅ Faucet creation and usage
- ✅ Token minting through faucet
- ✅ Balance verification

Run tests with:
```bash
sui move test
```

## 🔒 Security Considerations

1. **Treasury Cap Protection**: The treasury cap is locked in the `TreasuryLock` object and cannot be directly accessed
2. **Epoch-based Limits**: Minting limits are enforced per epoch and reset automatically
3. **Admin Controls**: Only admin capabilities can perform administrative operations
4. **Ban System**: Mint capabilities can be banned to prevent abuse
5. **Capability-based Access**: All operations require appropriate capabilities

## 🚀 Deployment

### Local Development
```bash
sui move build
sui client publish --gas-budget 10000000
```

### Network Deployment
```bash
# For devnet
sui client switch --env devnet
sui move build
sui client publish --gas-budget 10000000

# For testnet
sui client switch --env testnet
sui move build
sui client publish --gas-budget 10000000
```

## 📝 Error Codes

| Code | Error | Description |
|------|-------|-------------|
| 0 | `EMintCapBanned` | Mint capability is banned |
| 1 | `EMintAmountTooLarge` | Requested amount exceeds epoch limit |

## 🔗 Related Resources

- [Sui Move Documentation](https://docs.sui.io/build/move)
- [Sui Framework Reference](https://docs.sui.io/build/sui-framework)
- [Sui CLI Guide](https://docs.sui.io/build/cli)
