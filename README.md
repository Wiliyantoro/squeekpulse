# SqueekPulse 🐹 — Wheel Integrity Radar for Squeekchain

An independent, read-only monitor of the gap between the WHEELY9000 **live wheel
measurement** and what is actually **settled on-chain** (SqueekCore, Sepolia).
Squeekchain's own docs note that the live wheel reading can run ahead of the
settled total — SqueekPulse makes that gap visible, quantifies it, and scores it.

## The concept: the Integrity Index

Every 3 seconds the app computes, entirely client-side:

- **Settlement lag** = `measurement.cumulativeWorkUnits − builder.anchor.cumulativeWork`
- **Lag %** of measured work
- **Measurement freshness** vs `staleAfterSeconds`
- **Oracle / settlement-queue health** (`incidentOpen`, queue state, oracle state)
- **Integrity Index 0–100** = 100 − (lag% × 4, capped 60) − freshness penalty (25/60) − incident (50) − queue anomaly (30)

A lag spike, stale sensor, open incident or rejected oracle closure drops the
score instantly. The formula is displayed in the app — transparent and reproducible.

## Why it is safe by design

- **Read-only**: pure `GET` against `https://api.squeekchain.com/v1/status`. No wallet, no keys, no signatures, no writes — nothing to exploit.
- **Untrusted-input discipline**: every large number is parsed with `BigInt` (per the official API docs: never floating point); unknown/`unavailable` statuses are rendered as *unknown*, never as zero.
- **Keyless + CSP-locked**: no backend, no cookies; `Content-Security-Policy` restricts `connect-src` to the official API only; no `eval`, no third-party scripts.
- **Polite client**: single endpoint, `If-None-Match`/304 reuse, respects `Retry-After` on 429 with backoff, 8 s timeouts. Far below the documented rate limits (publicFast 30 rps).
- **No fabricated data**: on fetch failure the UI says so and retries — it never shows invented numbers.

## Files

- `index.html` — the whole app (zero dependencies, single file)
- `squeekchain.json` — ownership/attribution manifest referenced by the project's repository attribution

## Attribution

Built by **7tomars** (Squeekchain Genesis member, SQUEEK #7154 holder) as a
**Buildooooor** project. Live demo: see the project page on Squeekchain Genesis.
