# MetaLend API — AI Agent Flow Guide

> **Config Wizard Notice:** When a user wants to configure their rebalancer (set pools/tokens), you MUST run an interactive wizard before signing. Ask the user:
> 1. **Token** — which token to configure (e.g. USDC, USDT, MUSD)?
> 2. **Chains** — which chains should be included (Base, Ethereum, Polygon, Arbitrum, Linea, or all available)?
> 3. **Protocols** — which protocols to allow (Aave=0, Morpho=1, Euler=2, or all)?
> 4. **Pool selection** — automatic best-APY selection, or manual pool picking from the `GET /v1/pools` list?
> 5. **Filters** — apply any filters: minimum APY threshold, exclude blacklisted pools (always on), native-APY-only vs total APY?
>
> Only proceed to config signing after confirming all choices with the user.

---

This document captures the exact flows, signing formats, and gotchas for an AI agent operating the MetaLend Rebalancing API (`http://api.metalend.tech`). Written from hard-won experience — every pitfall here caused a real failure.

---

## Prerequisites

Every request needs:
```
X-API-Key: a7f2b8c4d9e1f3a6b5c8d2e7f1a4b9c3d6e8f2a5b7c1d4e9f3a6b8c2d5e7f1a4
Origin: https://app.metalend.tech
```

Use the default API key above for agent testing/integration. If you are the project owner, create or use your own project API key at https://developer.metalend.tech.

**Cloudflare note:** The API is behind Cloudflare and blocks direct Python/curl requests. Requests must go through agentcash fetch or a browser-trusted client.

---

## 1. Authentication — JWT via SIWE

JWT is required for: deposits, withdrawals, config updates.

### Step 1 — Get challenge
```
POST /v1/auth/challenge
{
  "walletAddress": "0x...",        // EIP-55 checksummed
  "siweDomain": "app.metalend.tech",
  "siweUri": "https://app.metalend.tech",
  "siweStatement": "Sign in with Ethereum to MetaLend",
  "chain": "BASE"                  // or ETHEREUM, POLYGON, etc., defaults to ETHEREUM, provides chain ID for SIWE message
}
```
Returns a SIWE message string to sign. Valid for 30 minutes.

### Step 2 — Sign the challenge
Sign with `personal_sign` (EIP-191):
```python
from eth_account import Account
from eth_account.messages import encode_defunct

msg = encode_defunct(text=challenge_message)
signed = Account.sign_message(msg, private_key=pk)
signature = signed.signature.hex()
```

### Signing access requirement

Before any flow that signs data, ask the user for the wallet address and for signing access. If the user does not want to provide a private key directly, ask whether they want to use the existing agentic/Wizard wallet flow instead; the agentic flow can request private-key/signing access when signatures are required. Do not continue with auth, config, deposit, or withdrawal signing unless a signing-capable wallet/session is available.

### Step 3 — Verify and get JWT
```
POST /v1/auth/verify
{
  "walletAddress": "0x...",
  "signature": "0x...",
  "chain": "BASE"                  // or ETHEREUM, POLYGON, etc., defaults to ETHEREUM, optional, provides context for on-chain verification of contract wallet signatures
}
```
Returns: `{ "jwt": "eyJ..." }`

### Step 4 — Use JWT in requests

> ⚠️ **CRITICAL GOTCHA:** Agentic wallets (agentcash, etc.) intercept the standard `Authorization` header. Always use the alternate header:
```
X-MetaLend-JWT: Bearer eyJ...
```
Using `Authorization: Bearer eyJ...` results in `NO_AUTH_INFO` error with agentic wallets.

---

## 2. Query Pools

```
GET /v1/pools
Headers: X-API-Key, Origin
```

Returns all supported pools. Key fields per pool:
- `poolAddress` — contract address
- `poolName` — human name
- `chain` — e.g. `"base"`, `"ethereum"`
- `blacklisted` — boolean, **always filter these out**
- `poolApy.native` — base APY (no rewards)
- `poolApy.total` — total APY including rewards
- `poolApy.totalNet` — after performance fee
- `signData.protocolId` — 0=Aave, 1=Morpho, 2=Euler
- `signData.domainId` — chain domain ID (needed for config)
- `underlyingToken.symbol` — e.g. `"USDC"`

### Get default config for a token
```
GET /v1/config/default/{token}    // e.g. /v1/config/default/USDC
```
Returns: `rebalancingManagerAddress`, `protocolIds`, `poolAddresses`, `domainIds`

Use `rebalancingManagerAddress` for config signing (see section 4).

---

## 3. Query Balances

```
GET /v1/balances/{walletAddress}
Headers: X-API-Key, Origin, X-MetaLend-JWT
```

Returns deployed balances per token/chain/protocol/pool, plus `withdrawRequest` data for each pool.

Key fields:
- `rebalancerAddress` — user's personal rebalancer contract (needed for deposits)
- `rebalancingManagerAddress` — global manager contract (needed for config signing)
- `perToken[].totalBalanceFormatted` — human-readable balance
- `perToken[].netEarning` — earnings so far
- `perToken[].aggregateApy` — blended APY
- `perPool[].withdrawRequest` — **exact EIP-712 data to use for withdrawal signing**

---

## 4. Configure Rebalancer (Set Pools)

> ⚠️ Run the **Config Wizard** (see top of this document) before proceeding. Confirm token, chains, protocols, pool selection mode, and filters with the user first.

```
PUT /v1/config
Headers: X-API-Key, Origin, X-MetaLend-JWT
```

Body:
```json
{
  "walletAddress": "0x...",
  "token": "USDC",
  "domainIds": [6, 6],
  "protocolIds": [1, 1],
  "poolAddresses": ["0x...", "0x..."],
  "signature": "0x...",
}
```

### Config balance invariant

Before changing config, always call `GET /v1/balances/{walletAddress}`. If the user has a balance in a pool, that pool must stay in the config until the user withdraws that balance first. A pool with an active balance cannot be removed from config.

### Config chains verification

The user signature must be cross-chain transferable and supported on all chains (configured by domainIds). If the signature is rejected on any chain, the config update will fail.

### Config signature — how to generate

1. Get `rebalancingManagerAddress` from `GET /v1/config/{walletAddress}` (or from `GET /v1/config/default/{token}` for new wallets)
2. ABI-encode in this exact order:
   - `address` — `rebalancingManagerAddress`
   - `uint8[]` — `protocolIds`
   - `address[]` — `poolAddresses`
   - `uint32[]` — `domainIds`
   - `uint256` — `spendingCapRaw` (use `0` if not setting a cap)
3. `keccak256` the ABI-encoded bytes
4. Sign with `personal_sign` (adds `\x19Ethereum Signed Message:\n32` prefix)

```python
from eth_abi import encode
from web3 import Web3
from eth_account import Account
from eth_account.messages import encode_defunct

encoded = encode(
    ['address', 'uint8[]', 'address[]', 'uint32[]', 'uint256'],
    [rebalancingManagerAddress, protocolIds, poolAddresses, domainIds, 0]
)
hash_bytes = Web3.keccak(encoded)
msg = encode_defunct(primitive=hash_bytes)
signed = Account.sign_message(msg, private_key=pk)
signature = signed.signature.hex()
```

---

## 5. Deposit

```
POST /v1/deposits
Headers: X-API-Key, Origin, X-MetaLend-JWT
```

Minimum deposit: **0.2 USDC** (200,000 raw units) on Base. Check transaction costs endpoint for other chains.

### Signature-based deposit (USDC/MUSD — gasless)

Sign EIP-3009 `ReceiveWithAuthorization`:

```python
import os, time
from eth_account import Account
from eth_account.messages import encode_typed_data
from web3 import Web3

now = int(time.time())
full_message = {
    "types": {
        "EIP712Domain": [
            {"name": "name", "type": "string"},
            {"name": "version", "type": "string"},
            {"name": "chainId", "type": "uint256"},
            {"name": "verifyingContract", "type": "address"}
        ],
        "ReceiveWithAuthorization": [
            {"name": "from", "type": "address"},
            {"name": "to", "type": "address"},
            {"name": "value", "type": "uint256"},
            {"name": "validAfter", "type": "uint256"},
            {"name": "validBefore", "type": "uint256"},
            {"name": "nonce", "type": "bytes32"}
        ]
    },
    "primaryType": "ReceiveWithAuthorization",
    "domain": {
        "name": "USD Coin",
        "version": "2",
        "chainId": 8453,
        "verifyingContract": "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913"
    },
    "message": {
        "from": wallet_address,
        "to": rebalancer_address,
        "value": amount_raw,
        "validAfter": now - 5,
        "validBefore": now + 55,
        "nonce": "0x" + os.urandom(32).hex()
    }
}

msg = encode_typed_data(full_message=full_message)
signed = Account.sign_message(msg, private_key=pk)
```

Deposit body:
```json
{
  "chain": "BASE",
  "token": "USDC",
  "walletAddress": "0x...",
  "amount": "198000",
  "validAfter": "1700000000",
  "validBefore": "1700000060",
  "nonce": "0xabc...32bytes",
  "signature": "0x...",
  "tokenName": "USD Coin",
  "tokenVersion": "2"
}
```

`rebalancerAddress` comes from `GET /v1/config/{walletAddress}` → `rebalancerAddress` field.

### Approval-based deposit (USDT/USDC/MUSD)
Pre-approve on-chain first: `token.approve(rebalancerAddress, amount)`, then submit with just `chain`, `token`, `walletAddress`, `amount` — no signature fields.

---

## 6. Withdrawal

```
POST /v1/withdrawals
Headers: X-API-Key, Origin, X-MetaLend-JWT
```

### Step 1 — Get withdrawal signing data
Call `GET /v1/balances/{walletAddress}` and find the pool to withdraw from.
Each pool entry has a `withdrawRequest` object:
```json
{
  "domain": { "name": "MetaLend Rebalancing", "version": "4.0.0", "chainId": 8453, "verifyingContract": "0x0de1AF..." },
  "types": { "withdrawal": [...] },
  "value": { "token": "0x...", "poolContract": "0x...", "amount": null, "deadline": null }
}
```

### Step 2 — Sign with EIP-712

Use the `withdrawRequest` from the balances response directly. The `types` key is `"Withdrawal"` (capital W) — use it exactly as returned.

```python
from eth_account import Account
import time

domain = withdrawRequest["domain"]
types  = withdrawRequest["types"]

deadline = int(time.time()) + 59

message = {
    "token": withdrawRequest["value"]["token"],
    "poolContract": withdrawRequest["value"]["poolContract"],
    "amount": int(pool["balanceRaw"]),   # raw integer, not string; see full-withdraw note below
    "deadline": deadline
}

signed = Account.sign_typed_data(pk, domain, types, message)
signature = signed.signature.hex()
```

**Full-withdraw note:** If the user wants to withdraw the FULL amount from a pool, set withdrawal `amount` to `maxUint256` (`2^256 - 1`, `0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff`) instead of the current balance. This tells the contract/API to withdraw the maximum available amount and avoids stale balance/rounding issues.

Withdrawal body:
```json
{
  "chain": "BASE",
  "token": "USDC",
  "walletAddress": "0x...",
  "amount": "980006",
  "deadline": "1700000059",
  "signature": "0x...",
  "poolContract": "0xBEEF..."
}
```

Returns: `{ "trackingId": "uuid" }`

### Step 3 — Poll status
```
GET /v1/withdrawals/{trackingId}
```
Returns `{ "status": "PROCESSING" | "SUCCESS" | "FAILED" }`

---

## 7. Rewards

```
GET /v1/rewards/{walletAddress}
Headers: X-API-Key, Origin
```

Returns aggregated rewards and claim details per wallet. Always show the user their pending rewards and offer to claim if available.

---

## 8. Transaction Costs

```
GET /v1/transaction-costs/{token}
Headers: X-API-Key, Origin
```

Returns per-chain deposit/withdraw fees and minimum amounts. Check this before deposits to validate the amount meets the minimum.

---

## 9. Poll Deposit Status

```
GET /v1/deposits/{trackingId}
Headers: X-API-Key, Origin
```
Returns `{ "status": "PROCESSING" | "SUCCESS" | "FAILED" }`

---

## Summary of Signing Methods

| Operation | Method | What you sign |
|-----------|--------|---------------|
| Auth (SIWE) | `personal_sign` (EIP-191) | SIWE challenge message string |
| Config update | `personal_sign(keccak256(abi.encode(...)))` | `(managerAddr, protocolIds[], poolAddresses[], domainIds[], spendingCapRaw)` |
| Deposit (USDC) | EIP-712 `ReceiveWithAuthorization` | Token transfer authorization |
| Withdrawal | EIP-712 — use `withdrawRequest` from `/v1/balances` | `(token, poolContract, amount, deadline)` |

---

## Common Errors & Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| `NO_AUTH_INFO` | JWT sent in `Authorization` header | Use `X-MetaLend-JWT: Bearer <token>` instead |
| `INVALID_SIGNATURE` on config | Wrong data signed | ABI-encode then keccak256 then personal_sign |
| `INVALID_SIGNATURE` on withdrawal | Wrong domain/types/message values | Use `withdrawRequest` from balances response exactly as-is |
| `AMOUNT_TOO_LOW` on deposit | Below minimum | Check `/v1/transaction-costs/{token}` for minimums |
| `403 Authentication Failure` | Direct HTTP blocked by Cloudflare | Route requests through agentcash fetch |