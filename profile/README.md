<div align="center">

<img src="https://raw.githubusercontent.com/Sconce-Labs/corridor/main/assets/logo.svg" alt="Sconce Labs" width="104" />

# Sconce Labs

**Privacy-preserving payment infrastructure on Stellar.**

We build [**Corridor**](https://github.com/Sconce-Labs/corridor) — a portable, zero-knowledge
proof of eligibility for cross-border payments.

[Live demo](https://corridor-pink.vercel.app) ·
[Architecture](https://github.com/Sconce-Labs/corridor/blob/main/ARCHITECTURE.md) ·
[Roadmap](https://github.com/Sconce-Labs/corridor/blob/main/ROADMAP.md) ·
[Contribute (Stellar Drips Wave)](https://www.drips.network/wave/stellar)

</div>

---

## The problem

Every time someone opens an account with a remittance provider, anchor, wallet, or
aid-disbursement program, they upload the same passport scan and liveness selfie
again. Each provider becomes a custodian of that identity data — and a target.
The person, meanwhile, has no reusable proof that they already passed KYC.

## What Corridor does

A person completes KYC/AML **once** with a regulated issuer and receives a
signed credential. From then on they prove *"I am cleared to use this payment
corridor"* to any number of providers with a **zero-knowledge proof** —
no documents, no identity, nothing linkable across providers.

Corridor is **Stellar-native**: corridor policy, on-chain proof verification,
the nullifier ledger, and payout gating all live in Soroban contracts on
Stellar (Protocol 25 BN254 + Poseidon2). The one cross-network piece is a small
**public issuer registry on Midnight** — the list of licensed issuers and each
issuer's current credential epoch. No shared state, no bridge to trust; a
corridor runs without it.

```
Issuer (KYC, once)              Holder's device                 Stellar / Soroban
──────────────────              ───────────────                 ─────────────────
signs a short-lived      ──▶    Noir → UltraHonk proof   ──▶    corridor_attestation.enter()
statement with a                of "I hold a valid              binds proof ↔ on-chain policy,
Grumpkin key                    issuer signature that           calls the verifier, burns a
{tier, expiry, epoch}           meets this policy"              per-corridor nullifier, records a pass
                                                                      │
                                                        operator's payout ── is_cleared()? ──▶ pay
```

- **A Stellar observer sees:** a pass was granted on corridor C, a tag, a burned
  nullifier. Not who, not their tier, not their other corridors.
- **A warranted auditor sees:** only `{tier, issuer}` for the specific passes in
  their warrant *(planned — the auditor blob is a commitment today; decryption
  is M7)*.
- **Nobody sees:** the holder's identity, documents, or the issuer↔holder link.

## Repositories

| Repo | What it is | CI |
|------|-----------|----|
| **[corridor](https://github.com/Sconce-Labs/corridor)** | Hub — architecture, docs, the Midnight issuer-registry contract, and the [web app](https://corridor-pink.vercel.app) | [![CI](https://github.com/Sconce-Labs/corridor/actions/workflows/ci.yml/badge.svg)](https://github.com/Sconce-Labs/corridor/actions/workflows/ci.yml) |
| **[corridor-contracts](https://github.com/Sconce-Labs/corridor-contracts)** | Soroban contracts (Rust) — corridor policy registry, proof↔policy binding, nullifier ledger, payout gating. **Deployed + smoke-verified on Stellar testnet** (the ZK verifier itself is a mock until M3). | [![CI](https://github.com/Sconce-Labs/corridor-contracts/actions/workflows/ci.yml/badge.svg)](https://github.com/Sconce-Labs/corridor-contracts/actions/workflows/ci.yml) |
| **[corridor-circuits](https://github.com/Sconce-Labs/corridor-circuits)** | The Noir eligibility circuit — Grumpkin Schnorr signature verification, 73 ACIR opcodes | [![CI](https://github.com/Sconce-Labs/corridor-circuits/actions/workflows/ci.yml/badge.svg)](https://github.com/Sconce-Labs/corridor-circuits/actions/workflows/ci.yml) |
| **[corridor-sdk](https://github.com/Sconce-Labs/corridor-sdk)** | `@corridor/verify` — TypeScript client SDK: read a policy, build a witness, issue/verify credentials, check clearance | [![CI](https://github.com/Sconce-Labs/corridor-sdk/actions/workflows/ci.yml/badge.svg)](https://github.com/Sconce-Labs/corridor-sdk/actions/workflows/ci.yml) |
| ~~corridor-relayer~~ | Archived — the earlier design's root-sync service, removed when Corridor moved to issuer-signed statements | — |

## Where it stands

**Pre-MVP research build**, participating in the **[Stellar Drips Wave](https://www.drips.network/wave/stellar)**.

| Layer | Status |
|-------|--------|
| Soroban contracts (registry + attestation) | ✅ 33 tests; deployed + smoke-verified on Stellar testnet |
| Noir circuit — real Grumpkin Schnorr verification | ✅ 20 tests, `nargo execute` on a real signed fixture, 73 ACIR opcodes |
| TypeScript SDK | ✅ live testnet reads, witness builder + local verifier, 3-step issuance, Grumpkin signer (25 tests) |
| Midnight issuer registry (`corridor.compact`) | ✅ compiles in CI; ⏳ Preprod deploy (M4) |
| Web app | ✅ public site + live testnet data + operator clearance checker |
| **Real on-chain ZK verifier** | ⏳ **M3** — a mock verifier stands in today; the policy binding *is* live |
| Fee-sponsoring tx-relayer + holder/operator flows | ⏳ M6 |

## Tech

Stellar · Soroban · Protocol 25 (BN254, Poseidon2) · Noir · UltraHonk ·
Grumpkin Schnorr · Midnight / Compact · TypeScript · React

## Get involved

Corridor is built in the open. Issues labelled `drips` are reward-eligible
through the SDF-funded Drips Wave pool — see
[`DRIPS.md`](https://github.com/Sconce-Labs/corridor/blob/main/DRIPS.md).

<div align="center"><sub>Built with love for Stellar.</sub></div>
