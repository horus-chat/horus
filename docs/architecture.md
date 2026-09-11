# Architecture

## Layers

```mermaid
flowchart TB
    UI["apps/mobile — SwiftUI + Compose"]
    AD["packages/adapters — JNI / Swift / C++"]
    PR["horus-protocol — E2EE, invites, BlindPipe"]
    REL["infrastructure/relays — dev HTTP + optional wake ping"]
    UI --> AD --> PR
    PR -.-> REL
```

UI never implements crypto. It calls `horus.h`. Relays never see plaintext.

```mermaid
flowchart LR
    subgraph ios["iOS"]
        SV[SwiftUI]
        HB[HorusBridge.swift]
        XC[HorusProtocol.xcframework]
        ET[EmbeddedTorEngine]
        SV --> HB --> XC
        SV --> ET
    end
    subgraph and["Android"]
        CM[Compose]
        VM[SessionViewModel]
        JN[HorusBridge JNI]
        SO[libhorus_protocol.so]
        TA[tor-android]
        CM --> VM --> JN --> SO
        VM --> TA
    end
    XC --- Rust[(same Rust crate)]
    SO --- Rust
```

See also: [vision.md](vision.md) · [protocol.md](protocol.md) · [transport.md](transport.md) · [apps.md](apps.md).

## Transports

| `transport` | Pipe |
|-------------|------|
| `hybrid` | `HybridPipe`: sealed bridge prefer, Tor fallback (**app default**) |
| `tor` | Onion mailbox (`ProdPipe`) |
| `http` | Dev `RelayClient` |

Apps keep using `prod_send` / `prod_poll` when Tor/hybrid is active.

```mermaid
flowchart LR
    Send[prod_send] --> Pipe{BlindPipe}
    Pipe -->|tor| Onion[Onion mailbox]
    Pipe -->|hybrid| Waku[Waku then Tor]
    Pipe -->|http| Dev[Dev relay]
    Poll[prod_poll] --> Pipe
```

## UI → protocol mapping

| UI action | Protocol call |
|---|---|
| First launch | `horus_init(config)` (+ Tor engine outside FFI) |
| Create invite | `horus_create_invite()` |
| Paste / redeem invite | `horus_connect` / accept invite helpers |
| Accept peer | `horus_accept_incoming()` |
| Send message | `horus_prod_send(chat_id, text)` |
| Poll | `horus_prod_poll` / `horus_prod_poll_any` |

```mermaid
sequenceDiagram
    actor User
    participant UI as App UI
    participant FFI as horus.h
    participant Rust as horus-protocol
    User->>UI: Send
    UI->>FFI: horus_prod_send
    FFI->>Rust: Double Ratchet + pipe
    Rust-->>UI: ok / error
    loop Poll
        UI->>FFI: horus_prod_poll_any
        FFI->>Rust: decrypt one frame
        Rust-->>UI: plaintext once
    end
```

All crypto and relay logic stays in Rust. UI apps only call the FFI surface (`horus.h`).

## Themes

Default tokens: `packages/core/themes/default.json` (and app-local `HorusColors`).

## Blockchain

ICP Motoko `@` registry (nullifier + commitment). **Messages never on-chain.** Code: `packages/blockchain/canister`.

```mermaid
flowchart LR
    Phone[Phone] -->|HTTPS GET/PUT| GW["canister.raw.icp0.io"]
    GW --> Motoko[Username registry]
    Phone -.->|Tor chat stays here| Tor[Onion mailbox]
```
