# Strangemood Revival

**Original:** Strangemood — a decentralized marketplace for software licenses on
Solana, by Evan Conrad, Helena Merk, Marc Espín, Mauricio Neira and jacobpedd.
Upstream development stopped on 2022-02-09, and the original repository at
`github.com/strangemood/strangemood` no longer resolves.

**Revival:** [ExpertVagabond/strangemood-gamestore](https://github.com/ExpertVagabond/strangemood-gamestore),
started 2026-02-16.

All code under `strangemood/` is the original authors' work, preserved with its
full commit history back to 2021-12-08. This file describes only what was added
on top.

## What Was Revived

Strangemood is a protocol — closer to email or RSS than to a storefront — that
anyone can build a game or software store on. Sellers receive voting tokens that
collectively govern a community treasury funded by 5% of sales.

## What Was Added

Security hardening of the Anchor on-chain program
(`strangemood/programs/strangemood/src/lib.rs`), which had no input validation
and used unchecked arithmetic:

| Added | Purpose |
|---|---|
| `validate_string_field` | bounds string inputs against `MAX_URI_LENGTH` (256) |
| `validate_amount` / `validate_price` | bounds token amounts against `MAX_TOKEN_AMOUNT` |
| `safe_multiply` / `safe_add` | `checked_mul` / `checked_add`, returning `ProgramError::ArithmeticOverflow` instead of wrapping |
| `verify_account_key` | asserts a passed account matches the expected `Pubkey` |

Unchecked arithmetic in a program that computes payouts and treasury splits is
the class of bug that killed several Solana protocols outright, so it was the
first thing worth closing.

Also added: a README describing the protocol and repository layout.

## Layout

```
strangemood/programs/   Anchor on-chain program (Rust) — original, plus the
                        validation helpers above
strangemood/src/        TypeScript client SDK — original
```

## Build

```bash
yarn install
anchor build
anchor test
```

## Attribution

The Strangemood protocol, its design and its implementation are the work of the
authors named above. This repository is a revival: the contribution here is the
security hardening listed under "What Was Added" and nothing else.
