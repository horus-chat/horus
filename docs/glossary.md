# Glossary

| Term | Meaning |
|------|---------|
| **BlindPipe** | Transport trait: move opaque ciphertext without understanding it (Tor mailbox, HTTP relay, hybrid). |
| **Creator / Joiner** | Invite author vs redeemer. Creator must Accept before chat is live. |
| **Double Ratchet (DR)** | 1:1 session crypto after accept; forward secrecy + PCS. |
| **FFI** | C API in `horus.h` — the supported client boundary. |
| **Hello** | Joiner’s first JSON frame after redeem (`horus: "hello"`). |
| **Invite (v2)** | Burnable `horus://invite/<base64url-json>` carrying queues, onion, optional KEM key. |
| **Mailbox** | Local HTTP service fronted by a Tor v3 onion — stores opaque blobs for a peer to poll. |
| **Mesh intro** | Group helper that creates hidden 1:1 sessions between members so fan-out works if the creator is offline. |
| **Nullifier** | Hash binding a username for uniqueness without publishing the name in the clear on-chain. |
| **Onion (v3)** | Tor hidden service address used as mailbox rendezvous. |
| **Outbox** | Local queue of ciphertext until the pipe durably accepts / receipt arrives. |
| **PQ hybrid** | ML-KEM material mixed into invite handshake root (with classical X25519). |
| **prod_send / prod_poll** | Production FFI entry points: encrypt+send / poll+decrypt. |
| **Receipt** | `horus.rcpt.v1:d|r:<id>` delivered/read acknowledgements. |
| **Sender key / SKDM** | Group inner ratchet; SKDM distributes chain seed over 1:1 DR. |
| **Transport** | Config mode: `tor` (default prod), `http` (dev), `hybrid` (Waku then Tor). |
| **Wake relay** | Optional server mapping `wake_id` → push token; never sees message bodies. |
| **Wake id** | Capability string; knowing it is enough to ping (rate-limited). |

See also: [threat model](security-threat-model.md) · [protocol](protocol.md).
