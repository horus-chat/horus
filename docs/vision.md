# Vision

## Tagline

**The untraceable, real-time messenger for people who refuse phone-number identity and central message servers.**

## Problem

Mainstream messengers force a trade-off:

- **Usability** (Telegram, WhatsApp) with phone numbers, metadata-rich servers, or proprietary clouds  
- **Privacy** (Signal, Session, SimpleX) that is strong but still often tied to phone, slow, or hard to operate  

Horus targets **Signal-grade encryption** plus **SimpleX-style anonymity** (no global user directory for messaging) with a UX that still feels like a modern chat app.

## Principles

| Principle | Meaning in Horus |
|-----------|------------------|
| Maximum anonymity | No phone/email required; no persistent global messaging ID on a Horus server |
| Real-time when possible | Fast poll / hybrid transport when online; outbox when not |
| Uncrackable content | E2EE on device; relays see opaque blobs only |
| Decentralized delivery | Tor onion mailboxes; optional Waku; optional peer BLE gateway |
| Storage discipline | Short relay TTL; local history under user control |
| Blockchain minimalism | On-chain **never** stores messages — only optional username / incentives later |
| Usability | Familiar chat UI (native iOS + Android today) |

## Intended user journey (product)

1. Install → create local profile (display name + keys on device)  
2. Share invite (link / QR / nearby BLE)  
3. Peer redeems → creator accepts → encrypted chat  
4. Text, media, voice/video over sealed paths  
5. Optional: register a decentralized username that resolves to a findable invite (not to a phone)

```mermaid
flowchart LR
    I[Install] --> K[Local keys]
    K --> P[Pair]
    P --> A[Accept]
    A --> C[Chat / calls]
    C --> H[Optional @]
    H -.-> C
``` 

## What “Web3” means here

Not “chat on a blockchain.”  

Blockchain (ICP preferred in the brief) is for **optional** username registry, spam/rate-limit tooling, and later relay incentives. **Message bodies and metadata of conversations stay off-chain.**

## Design evolution (important)

The original brief explored Telegram UI forks and a single Flutter codebase. **This repository shipped a different, pragmatic path:**

- Custom **native** clients (SwiftUI + Jetpack Compose)  
- Shared **Rust** protocol (`horus-protocol`) behind FFI  
- **Embedded Tor** in-app (no Orbot requirement)  

That is the codebase you are reading. Flutter/Telegram-fork plans in `instructions.txt` are historical intent, not the current tree.
