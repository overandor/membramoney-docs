# Devnet Deployment Guide

## Prerequisites

```bash
# 1. Install Solana CLI
sh -c "$(curl -sSfL https://release.solana.com/v1.17.0/install)"
export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"

# 2. Install Anchor (avm)
cargo install --git https://github.com/coral-xyz/anchor avm --locked --force
avm install 0.29.0
avm use 0.29.0

# 3. Verify
solana --version   # 1.17.0
anchor --version   # anchor-cli 0.29.0
rustc --version    # 1.75.0 or later

# 4. Configure for devnet
solana config set --url devnet
solana-keygen new --outfile ~/.config/solana/id.json --no-passphrase
solana airdrop 2  # Request devnet SOL
```

## Build & Test

```bash
cd bitcoin-bearer-notes

# Compile the program
anchor build

# Run tests against local validator
anchor test

# If tests fail, check:
# - Is solana-test-validator running? (anchor test starts it automatically)
# - Are all dependencies installed? (npm install in project root)
# - Is the wallet funded? (solana airdrop 2)
```

## Deploy to Devnet

```bash
# Build first
anchor build

# Deploy
anchor deploy --provider.cluster devnet

# Save the deployed program ID and update Anchor.toml + lib.rs declare_id!
# Then rebuild with the real ID
anchor build
anchor deploy --provider.cluster devnet
```

## Verify Deployment

```bash
# Check program exists on-chain
solana program show <PROGRAM_ID>

# Run the smoke test against devnet
npx ts-node scripts/devnet_smoke_test.ts
```

## Important

- **Do not use mainnet** until all MAINNET_READINESS.md items are checked.
- **Do not use real BTC** in devnet. Use testnet BTC or mock values.
- **Keep EXPERIMENTAL banners** in all UI surfaces.
- Rotate `PIN_PEPPER`, `CLAIM_LINK_SECRET`, and `SECRET_KEY` before any production deployment.
