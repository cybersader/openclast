---
title: Quick Start
description: Get the POC running locally in 5 minutes.
---

## Prerequisites

- Docker and Docker Compose
- Git
- Node.js 20+ or Bun

## Clone and Run

```bash
git clone https://github.com/cybersader/clasty.git
cd clasty

# Copy environment file
cp .env.example .env

# Start all services
docker-compose up -d
```

This starts:
- **Obsidian** at `https://localhost:3001` (VNC web interface)
- **Yjs server** at `ws://localhost:1234` (CRDT sync hub)
- **MinIO** at `http://localhost:9001` (object storage console)

## Multi-Instance Testing

To test sync between two Obsidian instances:

```bash
# Start the test environment (Alice + Bob)
docker-compose -f docker-compose.test.yml up -d

# Alice: https://localhost:3101
# Bob:   https://localhost:3201
```

Both instances share `vaults/shared-test/`. Edit a file in one, watch it appear in the other.

## Build from Source

```bash
# Sync daemon
cd sync-daemon && bun install && bun run build

# Obsidian plugin
cd obsidian-plugin && bun install && bun run build
```

## Documentation Site

```bash
cd docs-site && bun install && bun run dev
```

Opens the documentation site at `http://localhost:4321`.
