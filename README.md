

# STX-Memechanics

Welcome to the **MemeToken** smart contract – a feature-rich, manually time-tracked memecoin protocol built on Clarity (for the Stacks blockchain). This contract includes staking, governance, vesting, reward distribution, and a unique **manual block height management** system, all designed for maximal decentralization and tokenomics experimentation.

---

## 📜 Overview

This contract defines a fungible token `MEME` with the following advanced features:

* ✅ Manual Block Height Tracking (`update-block-height`)
* 🔁 Transfer Cooldowns (every 10 blocks)
* 📈 Staking & Rewards
* 🗳️ Governance Proposals & Voting
* 🔐 Time-Locked Vesting Schedules
* 🔥 Token Burning
* 🪂 Airdrops (placeholder-ready)

---

## 🧱 Token Configuration

* **Name**: `MemeToken`
* **Symbol**: `MEME`
* **Initial Supply**: `0`
* **Max Supply**: `1,000,000,000 MEME`
* **Token Type**: Fungible (via `define-fungible-token`)

---

## ⏱️ Manual Block Height System

Since Clarity doesn’t automatically expose the chain height in every context, we simulate it manually:

* `update-block-height`: Must be called by external actors to increment block height.
* `get-block-height`: Read-only function returning current height.

🧠 **Note**: This requires off-chain scripts or automation (e.g. every block or via oracles).

---

## 🔁 Transfers with Cooldown

* Transfers can only occur once every **10 blocks** per sender.
* Enforced via `transfer-last-block` map.
* Violations trigger `ERR-TRANSFER-COOLDOWN`.

### Function

```clarity
(transfer (amount uint) (recipient principal)) → (response bool uint)
```

---

## 💸 Minting and Burning

### Mint Tokens (Owner Only)

```clarity
(mint-tokens (amount uint) (recipient principal)) → (response bool uint)
```

### Burn Tokens (Sender)

```clarity
(burn-tokens (amount uint)) → (response bool uint)
```

* Enforces `max-supply`.
* Decreases `total-supply` on burn.

---

## 🏦 Staking and Rewards

### Stake Tokens

Locks MEME for a specified duration (in blocks):

```clarity
(stake-tokens (amount uint) (lock-period uint)) → (response bool uint)
```

### Unstake Tokens

```clarity
(unstake-tokens) → (response bool uint)
```

* Checks if current block ≥ unlock block.
* Tokens are transferred back to sender.

### Staking Rewards

Owner-defined reward rate per staker:

```clarity
(set-staking-reward-rate (staker principal) (rate-per-100-blocks uint)) → (response bool uint)
```

Claim rewards earned from staked tokens:

```clarity
(calculate-and-claim-staking-rewards) → (response uint uint)
```

---

## 🗳️ Governance System

Create proposals and vote with block height-based deadlines.

### Create Proposal

```clarity
(create-governance-proposal (description (string-utf8 200)) (voting-period uint)) → (response uint uint)
```

* Stores `voting-deadline` using `current-block-height`.

### Vote on Proposal

```clarity
(vote-on-proposal (proposal-id uint)) → (response bool uint)
```

* Validates that voting period is active.
* Voting logic is a placeholder for extension.

---

## 🕰️ Time-Locked Vesting

Supports linear vesting schedules with a cliff period.

### Create Vesting Schedule (Owner Only)

```clarity
(create-vesting-schedule 
  (beneficiary principal) 
  (amount uint) 
  (cliff-period uint) 
  (vesting-duration uint)
) → (response bool uint)
```

### Claim Vested Tokens

```clarity
(claim-vested-tokens) → (response uint uint)
```

* Vests linearly over blocks.
* Nothing claimable before cliff block.
* Fully claimable after vesting period.

---

## 🔐 Access Control

* **Owner** = deployer of the contract (`tx-sender` at deployment time).
* Only the owner can:

  * Mint tokens
  * Set staking reward rates
  * Create vesting schedules

---

## ⚠️ Error Codes

| Code   | Description                  |
| ------ | ---------------------------- |
| `u100` | Unauthorized (owner only)    |
| `u101` | Insufficient balance         |
| `u102` | Transfer cooldown active     |
| `u103` | Max supply exceeded          |
| `u104` | Airdrop failure (future use) |
| `u105` | Token burn failure           |
| `u111` | No staking info found        |
| `u112` | Unlock block not reached     |
| `u113` | Proposal not found           |
| `u114` | Voting deadline passed       |
| `u120` | Vesting schedule not found   |
| `u121` | Nothing to claim             |
| `u130` | No staking rewards to claim  |

---

## 🛠 Deployment & Usage Notes

* **Clarity Contract**: Deploy on Stacks mainnet/testnet using Clarity tools or IDE.
* **Automate**: `update-block-height` must be triggered manually or by an off-chain bot to simulate passage of time.
* **Extendable**: Hooks are present for adding airdrops, advanced governance, or DAO integrations.

---

## 💡 Future Improvements

* Auto block height from Chainhook or external oracle
* Add `vote-for` and `vote-against` implementations
* Snapshot-based voting weights
* Token airdrop with Merkle proofs
* NFT integrations and meme tiers

---

## 👨‍💻 Contributors

Built for experimental memeconomics, staking research, and on-chain governance design.

---

## 📄 License

MIT License – do whatever meme magic you want 🐸
