# Naami

Naami is a privacy‑preserving decentralized expense tracking system with cryptographic verifiability. It combines end‑to‑end encryption, encrypted event sourcing, and on‑chain commitments on Solana to make collaborative finance practical.

- Whitepaper PDF (in this repo): [naami-whitepaper.pdf](https://raw.githubusercontent.com/NamiApp-Inc/public/main/naami-whitepaper.pdf
)

## Summary

We present Naami, a decentralized expense tracking system that achieves privacy, verifiability, and cost‑efficiency for collaborative finance. Naami introduces four core innovations: (1) wallet‑based authentication with session management, (2) group‑level end‑to‑end encryption with dynamic key distribution, (3) encrypted event sourcing with causal ordering, and (4) a triple‑root on‑chain commitment scheme that uses Merkle Trees (membership) and Merkle Mountain Ranges (append‑only expense and refund logs). Implemented on Solana with Light Protocol’s zero‑knowledge compression, Naami reduces storage costs by up to 99% while maintaining full cryptographic verifiability. The system supports logarithmic proof sizes O(log n) with constant on‑chain storage O(1), enabling practical deployments with sub‑second latency and per‑expense costs around $0.009.

## Inside the whitepaper

- System and threat model
  - Users, API server, and blockchain roles
  - Adversaries: honest‑but‑curious server, network attacker, malicious users, temporary chain reorgs
  - Security goals: confidentiality, integrity, authenticity, availability

- Architecture and layering
  - Client (crypto ops), API (Elysia.js), Business services, Data access (PostgreSQL, Redis, external repos), Storage & Blockchain
  - Clean separation: handlers ≠ business logic; services ≠ direct DB; stores isolated from external services

- Wallet‑based authentication
  - Ed25519 signatures for challenge‑response; JWT for stateless sessions
  - Session management with expiration for bounded risk windows

- End‑to‑end encryption (E2EE)
  - Primitives: Ed25519, X25519, ChaCha20‑Poly1305, BLAKE2b, Keccak256 (libsodium)
  - Password‑based key escrow using PBKDF2(200k) for multi‑device recovery without server key exposure
  - Session key distribution protocol for efficient member addition without re‑encryption

- Encrypted event sourcing
  - Immutable event log (CREATE/UPDATE/DELETE) with reduction to state
  - Causal ordering using linked predecessors (Lamport‑style)

- Triple‑root cryptographic commitments
  - Members root: Merkle Tree over member public keys (domain‑separated hashing)
  - Expenses root: MMR over expense event hashes (append‑only, O(log n) proofs)
  - Refunds root: MMR over P2P refund transactions on Solana
  - Constant on‑chain footprint: three 32‑byte roots (≈96 bytes total) per session

- Incremental on‑chain synchronization
  - One‑event incremental MMR updates for clarity and fault isolation
  - Verification via inclusion proofs against the on‑chain root

- Zero‑knowledge compression (Light Protocol)
  - Store only the root on‑chain; update via validity proofs
  - Cost model: ≈$0.01 per operation; ≈94–99% storage cost reduction depending on baseline

- Caching and performance
  - Redis cache for MMR peaks, counts, roots, sync markers; 30‑day TTL
  - Typical latencies: <10 ms client crypto, <50 ms API/DB/MMR, ≈500 ms chain finality; ≈<1 s end‑to‑end

- Security analysis (proof sketches)
  - Confidentiality under IND‑CPA of ChaCha20‑Poly1305
  - Integrity under Keccak256 collision resistance
  - Authenticity under Ed25519 SUF‑CMA

- Evaluation and scalability
  - Example costs (Solana devnet): session creation, member adds, expense updates (~$0.009 each)
  - Proof sizes grow logarithmically (e.g., ~320 bytes for ~1000 events)
  - Storage O(1) on‑chain per session enables scaling to millions of sessions

- Limitations and future work
  - Member removal and forward secrecy trade‑offs; batching updates; blockchain dependency
  - Planned: key rotation, light clients, cross‑chain, ZK privacy for balances, formal verification

- Broader applications
  - Corporate expenses, meal vouchers, shared subscriptions, lending circles, non‑profit transparency, supply‑chain cost allocation

## Architecture at a glance

- Runtime: Bun
- API: Elysia.js (TypeScript)
- Database: PostgreSQL with Drizzle ORM
- Cache: Redis
- Blockchain: Solana (Anchor Framework) + Light Protocol ZK compression
- Monorepo: Nx; Lint/Format: Biome; Tests: Bun

## Official links

- Website: https://naami.cc  
- X/Twitter: https://x.com/Naami_cc  
- YouTube: https://www.youtube.com/@Naami_cc  
- Telegram: https://t.me/naami_cc

## How to cite
```
Naami Development Team. Naami: A Privacy-Preserving Decentralized Expense Tracking System with Cryptographic Verifiability. 2025. Available at https://naami.cc
```

```bibtex
@techreport{naami-whitepaper-2025,
  title       = {Naami: A Privacy-Preserving Decentralized Expense Tracking System with Cryptographic Verifiability},
  author      = {Naami Development Team},
  year        = {2025},
  institution = {Naami},
  url         = {https://naami.cc},
  note        = {Whitepaper}
}
```
