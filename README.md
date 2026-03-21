# 🔇 Whispr

**Decentralized. Private. Encrypted. No trace.**

Whispr is a peer-to-peer social network where your identity is a seed phrase, your messages are end-to-end encrypted, and no IP address is ever exposed. Think Discord + Twitter, but decentralized and anonymous.

> ⚠️ **v0.1.0 — Early Development** — Core networking works. Security hardening (E2E encryption, message signing, encrypted storage) is actively being implemented. Not yet recommended for sensitive communications.

## Features

- **P2P Network** — No central server. Nodes gossip messages via libp2p
- **No IP Exposed** — Nodes communicate using cryptographic IDs only
- **Seed Phrase Auth** — No email, no phone. Your 24-word seed IS your account
- **Circles** — Group chats with rooms, roles (Owner/Admin/Mod/Member), invitations
- **Feed** — Public posts with hashtags, likes, reposts, rich text formatting
- **AI Assistant** — Summarize channels, translate messages, format text
- **Auto-Update** — Nodes update themselves from GitHub releases
- **Lightweight** — ~7MB binary, ~100MB RAM, runs on a Raspberry Pi

## Quick Start

### Download

Grab the latest release from the [Releases page](https://github.com/trusts-stack-network/whispr-releases/releases):

```bash
# Linux
curl -LO https://github.com/trusts-stack-network/whispr-releases/releases/latest/download/whispr-node-linux-amd64
chmod +x whispr-node-linux-amd64
./whispr-node-linux-amd64
```

```powershell
# Windows
Invoke-WebRequest -Uri https://github.com/trusts-stack-network/whispr-releases/releases/latest/download/whispr-node-windows-amd64.exe -OutFile whispr-node.exe
.\whispr-node.exe
```

### Run

```bash
./whispr-node --port 7777 --api-port 7778 --data-dir ./data
```

Options:
```
-p, --port <PORT>          P2P port [default: 7777]
-a, --api-port <API_PORT>  API port for frontend [default: 7778]
-d, --data-dir <DIR>       Data directory [default: ./data]
-b, --bootstrap <ADDR>     Bootstrap node multiaddr
    --no-update            Disable auto-update
    --log-level <LEVEL>    Log level [default: info]
```

### Connect to the network

```bash
./whispr-node \
  -b /ip4/151.240.19.253/tcp/7777 \
  -b /ip4/45.145.164.76/tcp/7777 \
  -b /ip4/146.19.168.71/tcp/7777 \
  -b /ip4/45.132.96.141/tcp/7777
```

Access the app: **https://whispr.tsnchain.com**

### Run as systemd service

```bash
sudo cp whispr-node /usr/local/bin/
sudo tee /etc/systemd/system/whispr-node.service << 'SVC'
[Unit]
Description=Whispr Node
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/whispr-node --data-dir /opt/whispr/data
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
SVC

sudo systemctl enable --now whispr-node
```

## Architecture

```
whispr-node (single Rust binary, ~7MB)
├── P2P Layer (libp2p)
│   ├── Gossipsub — Message propagation
│   ├── mDNS — Local peer discovery
│   ├── Noise — Encrypted transport
│   └── Identify — Peer identification
├── Storage (SQLite WAL)
│   ├── Messages, Profiles, Circles
│   └── Rooms, Peers, Config
├── API (axum HTTP)
│   ├── GET  /api/status
│   ├── GET  /api/feed
│   ├── POST /api/feed/post
│   └── GET  /api/profiles
├── Crypto
│   ├── Ed25519 node identity
│   ├── SHA-256 hashing
│   └── Whispr addresses (wh1sp3r_*)
└── Auto-Update
    └── GitHub releases checker
```

## Node Rewards

**Node operators will be rewarded.** Running a Whispr node contributes to the network's decentralization and availability. A reward mechanism linked to the TSN blockchain is planned — details will be published in a dedicated document. The more reliable your node, the more you earn.

## Security Roadmap

Whispr is committed to becoming a fully private and secure communication platform. Here is our security hardening plan:

### v0.2.0 — Message Integrity (next)
- [ ] **Ed25519 message signing** — Every message cryptographically signed by its author
- [ ] **Signature verification** — Nodes reject unsigned or forged messages
- [ ] **Message deduplication** — Prevent replay attacks

### v0.3.0 — End-to-End Encryption
- [ ] **DM encryption** — X25519 key exchange + AES-256-GCM for direct messages
- [ ] **Circle encryption** — Group key distribution for private circles
- [ ] **Forward secrecy** — Key rotation so past messages stay safe even if a key leaks

### v0.4.0 — Storage Encryption
- [ ] **SQLCipher** — AES-256 encrypted database at rest
- [ ] **Encrypted node identity** — Password-protected private key
- [ ] **Secure key derivation** — Argon2 for seed phrase → key derivation

### v0.5.0 — Anti-Abuse
- [ ] **Rate limiting** — Per-peer message throttling
- [ ] **Reputation system** — Node/user scoring based on behavior
- [ ] **Spam detection** — Pattern-based + AI-assisted filtering
- [ ] **Link restriction** — New members can't post links until N messages
- [ ] **Ban propagation** — Kicked users blocked across the network

### v0.6.0 — Audit & Hardening
- [ ] **Security audit** — Third-party code review
- [ ] **Penetration testing** — Network attack simulation
- [ ] **Bug bounty program** — Rewarded vulnerability disclosure

### Current Security Status

| Layer | Status | Detail |
|-------|--------|--------|
| P2P transport | ✅ Encrypted | libp2p noise protocol |
| Node identity | ✅ Secure | Ed25519 keypair, file permissions 600 |
| IP privacy | ✅ Hidden | Only node IDs exposed, never IPs |
| Messages in transit | ✅ Encrypted | Noise protocol between peers |
| Message signatures | ⏳ Planned v0.2.0 | Not yet signed |
| DM end-to-end | ⏳ Planned v0.3.0 | Transit-only encryption for now |
| Database at rest | ⏳ Planned v0.4.0 | SQLite not yet encrypted |
| Anti-spam | ⏳ Planned v0.5.0 | No rate limiting yet |

## Network

| Node | Port | Role | Status |
|------|------|------|--------|
| seed-1 | 7777 | Bootstrap | 🟢 Active |
| seed-2 | 7777 | Bootstrap | 🟢 Active |
| seed-3 | 7777 | Bootstrap | 🟢 Active |
| seed-4 | 7777 | Bootstrap | 🟢 Active |

Anyone can run a node. The more nodes, the stronger the network.

## Roadmap

### Completed
- [x] P2P gossip network (libp2p)
- [x] Node identity & crypto (Ed25519)
- [x] SQLite storage with schema
- [x] REST API for frontend
- [x] Auto-update from GitHub releases
- [x] GitHub Actions CI (Linux + Windows)
- [x] 4 seed nodes deployed

### In Progress
- [ ] Security hardening (see Security Roadmap above)
- [ ] Frontend ↔ node integration
- [ ] Profile sync across nodes
- [ ] Circle management via P2P

### Planned
- [ ] TSN wallet integration (send/receive TSN)
- [ ] Node rewards (TSN earnings for operators)
- [ ] Premium circles (TSN payment)
- [ ] Voice channels
- [ ] Desktop app (Tauri, ~15MB)
- [ ] Mobile app
- [ ] Decentralized moderation (community votes)

## Tech Stack

- **Language**: Rust
- **P2P**: libp2p (gossipsub, mDNS, noise, yamux)
- **Storage**: SQLite (WAL mode)
- **API**: axum
- **Crypto**: ed25519-dalek, SHA-256
- **CI/CD**: GitHub Actions (Linux + Windows builds)
- **Frontend**: React + TypeScript + Tailwind (Vite)

## Part of the TSN Ecosystem

Whispr is part of the [Trust Stack Network](https://tsnchain.com) ecosystem:

- 🔗 **[TSN Blockchain](https://tsnchain.com)** — Post-quantum blockchain (Rust, ML-DSA-65, Plonky3)
- 💱 **NetherSwap** — Decentralized exchange
- 💬 **Whispr** — Decentralized private social network
- 🔍 **[Explorer](https://explorer.tsnchain.com)** — Blockchain explorer

## Contributing

Pull requests welcome. For major changes, open an issue first.

## License

MIT
