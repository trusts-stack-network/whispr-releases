# Whispr

**Decentralized. Private. No trace.**

Whispr is a peer-to-peer social network where your identity is a seed phrase, your messages travel through a decentralized mesh, and no IP address is ever exposed. Think Discord + Twitter + Medium, but decentralized and private.

**[Launch App](https://whispr.tsnchain.com)** | **[Landing Page](https://tsnchain.com/whispr.html)** | **[TSN Ecosystem](https://tsnchain.com)**

> **v0.3.0** — UI Premium refonte + E2E Encryption. Message signing (HMAC-SHA256), DM encryption (ECDH P-256 + AES-256-GCM), group keys, rate limiting, anti-replay. Enhanced design tokens, gradient sidebar, message animations, skeleton loaders.

## What's New in v0.3.0

### E2E Encryption
- **Message Signing** — HMAC-SHA256 signature on every message
- **DM Encryption** — ECDH P-256 key exchange + AES-256-GCM encryption
- **Group Keys** — Shared symmetric keys for private circles
- **Rate Limiting** — 30 messages/min per user
- **Anti-Replay** — Message deduplication prevents replay attacks

### UI Premium Refonte
- **Enhanced Design Tokens** — 5-level background system
- **Gradient Sidebar** — Smooth gradient navigation
- **Nav Active Accent Bar** — Visual indicator for active tab
- **Card Hover Effects** — Elevation and subtle border glow
- **Message Animations** — Fade-in on new messages
- **Skeleton Loaders** — Loading placeholders for better UX
- **Focus Rings** — Accessible keyboard navigation
- **Custom Scrollbar** — Themed scrollbar design
- **Empty States** — Descriptive icons for empty views

## Features

### Social
- **Whisps** — Short posts (like tweets). Create, edit, delete, upvote/downvote
- **Articles** — Long-form blog with title, cover image, reading time, flair categories
- **Feed Sorting** — Recent / Hot / Top / Following tabs
- **Comments** — On whisps and articles
- **Profiles** — Avatar, banner, bio, whisp/article count, followers/following
- **Discover** — Find people and circles, see circles in common
- **Friends** — Send requests, accept/decline, dedicated friends page

### Communities (Circles)
- **Circles** — Public or private groups with rooms, roles, invitations
- **Roles** — Owner > Admin > Mod > Member with hierarchy enforcement
- **Room Types** — Text, Announce (mod+ only)
- **Room Categories** — Organize rooms into categories (server-persisted)
- **Message Pinning** — Pin important messages (mod+)
- **Polls** — Create polls with multiple options, real-time votes
- **Search** — Fulltext search across messages
- **Typing Indicator** — "X is typing..."
- **Slowmode** — Anti-spam cooldown per room (5s to 1h)
- **Auto-Moderation** — Word filter rules (delete/warn/mute)
- **AI Rules Configurator** — Configure moderation in natural language
- **Scheduled Events** — Plan events in circles
- **Room Bookmarks** — Pin useful links per room
- **Room Permissions** — Per-role read/write overrides
- **Onboarding** — Welcome message for new members
- **Ban System** — Ban/unban, prevents rejoin

### Direct Messages
- **Server-synced DMs** — Not local, synced via node API
- **E2E Encrypted DMs** — ECDH P-256 + AES-256-GCM (v0.3.0)
- **Disappearing Messages** — Timer: 5min, 1h, 24h, 7d
- **View-Once Media** — Image deleted after first view
- **Notifications** — Bell badge for DMs + friend requests

### Chat
- **Rich Text** — Bold, italic, strike, code, colors, @mentions, #room links
- **Edit Message Modal** — Full editor with formatting + AI Magic Edit
- **Emoji Reactions** — Quick reactions on any message
- **Image Sharing** — Drag & drop, WebP compression, lightbox
- **Reply / Forward** — Quote and copy messages
- **Message Scheduling** — Send messages at a scheduled time
- **Line Breaks** — Shift+Enter for multiline

### Security (v0.3.0)
- **Message Signing** — HMAC-SHA256 on all messages
- **DM Encryption** — ECDH P-256 + AES-256-GCM
- **Group Keys** — Symmetric encryption for private circles
- **Rate Limiting** — 30 msg/min per user
- **Anti-Replay** — Deduplication prevents replay attacks

### AI Assistant
- **AI Panel** — Sidebar with room context (last 20 messages)
- **Quick Actions** — Summarize, Translate (10 languages), Reply, Brainstorm, Format
- **AI in Chat** — Translate & format before sending
- **AI Message Editor** — Edit messages with natural language ("make it bold, add emojis...")
- **AI Circle Rules** — Configure moderation via natural language
- **Providers** — Groq + Gemini (direct browser calls, keys in Settings)

### Infrastructure
- **P2P Network** — libp2p gossipsub, mDNS, Noise encrypted transport
- **5 Nodes** — 1 main + 4 seeds, DB sync every 2min via HTTP
- **60+ API Endpoints** — axum REST API
- **20+ Database Tables** — SQLite WAL with auto-migration
- **Background Tasks** — Cleanup expired messages, send scheduled messages
- **2 Themes** — Midnight (dark) + Daylight (light)
- **5 Languages** — EN, FR, ES, RU, ZH
- **~7.6MB Binary** — Lightweight, runs anywhere

## Quick Start

### Download

```bash
# Linux
curl -LO https://github.com/trusts-stack-network/whispr/releases/latest/download/whispr-node-linux-amd64
chmod +x whispr-node-linux-amd64
./whispr-node-linux-amd64
```

### Build from source

```bash
git clone https://github.com/trusts-stack-network/whispr.git
cd whispr
cargo build --release
./target/release/whispr-node
```

### Run

```bash
./whispr-node --port 7777 --api-port 7778 --data-dir ./data
```

Options:
```
-p, --port <PORT>          P2P port [default: 7777]
-a, --api-port <API_PORT>  API port [default: 7778]
-d, --data-dir <DIR>       Data directory [default: ./data]
-b, --bootstrap <ADDR>     Bootstrap node multiaddr
    --no-update            Disable auto-update
    --log-level <LEVEL>    Log level [default: info]
```

### Connect to the network

```bash
./whispr-node \
  -b /ip4/45.145.165.223/tcp/7777 \
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
whispr-node (Rust binary, ~7.6MB)
├── P2P Layer (libp2p)
│   ├── Gossipsub — Message propagation
│   ├── mDNS — Local peer discovery
│   ├── Noise — Encrypted transport
│   └── Identify — Peer identification
├── Storage (SQLite WAL, 20+ tables)
│   ├── feed_posts, feed_votes, feed_comments
│   ├── circles, circle_members, circle_bans
│   ├── rooms, room_messages, room_categories
│   ├── pinned_messages, room_bookmarks, room_permissions
│   ├── polls, poll_votes, events
│   ├── direct_messages, friend_requests, friends
│   ├── auto_mod_rules, scheduled_messages
│   └── profiles, peers, invite_codes, node_config
├── API (axum, 60+ endpoints)
│   ├── /api/v2/feed/* — Blog/Articles
│   ├── /api/circles/* — Communities
│   ├── /api/rooms/* — Messages, pins, search, polls
│   ├── /api/dm/* — Direct messages
│   ├── /api/friends/* — Friend system
│   ├── /api/polls/* — Polls
│   ├── /api/events/* — Events
│   ├── /api/automod/* — Auto-moderation
│   ├── /api/search — Fulltext search
│   └── /api/sync/* — Node-to-node sync
├── Background Tasks
│   ├── Cleanup expired messages (30s)
│   ├── Send scheduled messages (30s)
│   └── DB sync from peers (2min)
├── Crypto
│   ├── Ed25519 node identity
│   ├── BIP39 user identity
│   └── ECDH P-256 + AES-256-GCM (E2E v0.3.0)
└── Security (v0.3.0)
    ├── HMAC-SHA256 message signing
    ├── Rate limiting (30 msg/min)
    └── Anti-replay deduplication
```

## Security Status

| Layer | Algorithm | Status |
|-------|-----------|--------|
| Node Identity | Ed25519 | Active |
| User Identity | BIP39 (24 words) | Active |
| Transport | Noise Protocol | Active |
| Message Signing | HMAC-SHA256 | Active (v0.3.0) |
| DM Encryption | ECDH P-256 + AES-256-GCM | Active (v0.3.0) |
| Group Encryption | Symmetric AES-256-GCM | Active (v0.3.0) |
| Rate Limiting | 30 msg/min | Active (v0.3.0) |
| Anti-Replay | Deduplication | Active (v0.3.0) |
| Local DB Encryption | SQLCipher | Planned |

## Roadmap

### v0.1.0 — Foundation (Done)
- [x] P2P gossip network (libp2p)
- [x] Circles with rooms, roles, invitations
- [x] Feed with posts
- [x] DMs, Friend system
- [x] AI Assistant
- [x] 5 nodes deployed

### v0.2.0 — Blog & Engagement (Done)
- [x] Whisps + Articles (blog system)
- [x] Upvote/Downvote, Feed sorting
- [x] Polls, Threads, Message pinning
- [x] Search, Typing indicator
- [x] Slowmode, Auto-moderation, AI Rules
- [x] Disappearing messages, View-once media
- [x] Events, Bookmarks, Permissions
- [x] Banner upload, Profile improvements
- [x] 60+ API endpoints, 20+ DB tables

### v0.3.0 — UI Premium + E2E Encryption (Done)
- [x] UI Premium refonte (design tokens, gradient sidebar, animations, skeleton loaders)
- [x] E2E encryption (ECDH P-256 + AES-256-GCM)
- [x] Message signing (HMAC-SHA256)
- [x] Group keys for private circles
- [x] Rate limiting (30 msg/min)
- [x] Anti-replay deduplication

### v0.4.0 — Ecosystem (Next)
- [ ] TSN wallet integration
- [ ] Node rewards (TSN earnings)
- [ ] Desktop app (Tauri)
- [ ] Mobile app

## Node Rewards

**Node operators will be rewarded.** Running a Whispr node contributes to the network's decentralization. A reward mechanism linked to the TSN blockchain is planned. The more reliable your node, the more you earn.

## Network

| Node | Port | Role | Status |
|------|------|------|--------|
| node-1 | 7777 | Main | Active |
| seed-1 | 7777 | Bootstrap | Active |
| seed-2 | 7777 | Bootstrap | Active |
| seed-3 | 7777 | Bootstrap | Active |
| seed-4 | 7777 | Bootstrap | Active |

Anyone can run a node. The more nodes, the stronger the network.

## Tech Stack

- **Language**: Rust
- **P2P**: libp2p (gossipsub, mDNS, noise, yamux, quic)
- **Storage**: SQLite WAL (rusqlite bundled)
- **API**: axum 0.7
- **Crypto**: ed25519-dalek, SHA-256, ECDH P-256, AES-256-GCM, HMAC-SHA256
- **Frontend**: React + TypeScript + Tailwind (Vite)
- **AI**: Groq + Gemini (direct browser calls)

## Part of the TSN Ecosystem

- [TSN Blockchain](https://tsnchain.com) — Post-quantum blockchain (Rust, ML-DSA-65, Plonky3)
- [Explorer](https://explorer.tsnchain.com) — Blockchain explorer
- [Whispr](https://whispr.tsnchain.com) — Decentralized private social network
- [NetherSwap](https://tsnchain.com) — Decentralized exchange

## Contributing

Pull requests welcome. For major changes, open an issue first.

## License

MIT
