---
date created: 2026-03-28
date modified: 2026-03-28
temperature: working
tags:
  - session-log
  - testing
  - sync
  - docker
  - tailscale
  - multi-instance
related:
  - "[[ARCHITECTURE_COMPONENTS]]"
  - "[[testing]]"
  - "[[crdt-sync-plugin]]"
  - "[[decision-log]]"
---

# 2026-03-28 — Multi-Instance Sync Testing Methods

## Context

To properly test our CRDT sync, we need two (or more) Obsidian instances that:
1. Share the same underlying filesystem (same vault data)
2. Appear as separate network identities (different IPs, hostnames, machine IDs)
3. Run independent sync daemons and plugin instances

This enables testing concurrent edits, merge behavior, presence awareness, and potentially tricking Obsidian Sync into treating containers as separate devices.

## Key Discovery: What Makes Instances "Different"

### For Our CRDT Sync

| Identity Layer | Source | Uniqueness |
|---------------|--------|------------|
| Plugin Client ID | `identity.ts` → `crypto.randomUUID()` | Auto-unique per plugin install |
| State files | `.crdt/{path}.{clientId}.yjs` | No collision (keyed by client ID) |
| Sync daemon USER_ID | `USER_ID` env var | Set per container |
| WebSocket connection | Yjs server tracks by connection | Auto-unique per daemon process |

Each container automatically gets unique identity for our sync — no special configuration needed.

### For Obsidian Sync (if we want to trick it)

| Identity Layer | Default in Docker | Fix |
|---------------|-------------------|-----|
| Machine ID | Shared with host (`/var/lib/dbus/machine-id`) | Mount unique file per container |
| Network identity | Different Docker bridge IPs | Tailscale for unique tailnet IPs |
| Hostname | Container name | Set unique `hostname:` in compose |
| Vault ID | Assigned per device by Sync server | Auto-unique if machine ID differs |

## Three Approaches (Layered by Complexity)

### Approach A: Docker-Only (Simplest)

Two linuxserver/obsidian containers sharing a bind-mounted vault directory. Separate per-instance `.obsidian/` via Docker volumes.

**What it tests:** CRDT merge via Yjs server, presence awareness, concurrent edits.
**What makes them "different":** Separate container IPs, USER_IDs, plugin client IDs, WebSocket connections.
**Limitation:** Both on same Docker bridge — not convincing as separate "devices" for Obsidian Sync.

**Implementation:** `docker-compose.test.yml` in project root.

**How to use:**
```bash
# Start multi-instance test environment
docker-compose -f docker-compose.test.yml up -d

# Access Alice's Obsidian
open https://localhost:3101

# Access Bob's Obsidian
open https://localhost:3201

# Watch sync daemon logs
docker-compose -f docker-compose.test.yml logs -f sync-daemon-alice sync-daemon-bob
```

### Approach B: Docker + Tailscale (Realistic Network Identity)

Each container runs its own Tailscale client, getting a unique tailnet identity (100.x.x.x IP, MagicDNS hostname). Combined with unique machine IDs, this convincingly simulates separate devices.

**What it adds over Approach A:**
- Unique Tailscale IP per container
- MagicDNS names (`obsidian-alice.tailnet`, `obsidian-bob.tailnet`)
- Encrypted WireGuard mesh between containers
- Can trick Obsidian Sync with unique machine IDs

**Tailscale integration options:**

| Option | Complexity | Isolation | Notes |
|--------|-----------|-----------|-------|
| Sidecar container | Medium | Full | Separate container running `tailscaled`, shares network namespace via `network_mode: service:obsidian-alice` |
| Built into image | High | Full | Install Tailscale in Dockerfile, start in entrypoint |
| Subnet router | Low | Partial | Single Tailscale node routes to Docker network |

**Sidecar pattern (recommended):**
```yaml
obsidian-alice:
  image: linuxserver/obsidian:latest
  # ... standard config ...
  volumes:
    - ./test-ids/alice-machine-id:/var/lib/dbus/machine-id:ro  # Unique machine ID
  network_mode: "service:ts-alice"  # Share Tailscale network

ts-alice:
  image: tailscale/tailscale:latest
  hostname: obsidian-alice
  environment:
    - TS_AUTHKEY=${TS_AUTHKEY_ALICE}
    - TS_STATE_DIR=/var/lib/tailscale
  volumes:
    - ts-alice-state:/var/lib/tailscale
  cap_add: [NET_ADMIN, SYS_MODULE]
  devices: [/dev/net/tun:/dev/net/tun]
```

**Setup for Obsidian Sync trick:**
```bash
# Generate unique machine IDs for each instance
mkdir -p test-ids
uuidgen > test-ids/alice-machine-id
uuidgen > test-ids/bob-machine-id

# Generate Tailscale pre-auth keys (one-time, ephemeral)
# Via Tailscale admin console or CLI:
# tailscale up --auth-key=tskey-auth-xxx
```

### Approach C: Tailscale + Separate Hosts (True Multi-Device)

Containers on genuinely different machines connected via Tailscale.

- Alice's container on host A, Bob's on host B
- Shared vault via Tailscale + SMB/NFS or Syncthing
- Yjs server accessible over tailnet
- Most realistic but most complex

**When to use:** Final validation before enterprise deployment. Not needed for POC testing.

## Shared Filesystem Considerations

| Concern | Solution |
|---------|----------|
| `.obsidian/workspace.json` conflicts | Per-instance Docker volumes for `/config` |
| Plugin `data.json` (stores client ID) | Per-instance config volumes handle this |
| File write visibility | Bind mounts = instant visibility across containers |
| `.crdt/` state files | Keyed by client ID, no collision |
| Inode/lock contention | CRDT handles merge; no strict locking needed |

## Decision: Start with Approach A, Design for B

**DECISION-PENDING-004: Multi-Instance Testing Strategy**
- **Choice:** Implement Approach A (Docker-only) immediately via `docker-compose.test.yml`
- **Rationale:** Tests our CRDT sync without extra infrastructure. Approach B (Tailscale) is additive — same compose file with extra services.
- **Status:** Pending
- **Next:** If Obsidian Sync testing needed, layer Tailscale sidecars onto same compose file

## Open Questions

- [ ] Does Obsidian Sync check machine ID on every connection or only on initial device registration?
- [ ] Can we use Tailscale's `--advertise-tags` to simulate different user roles?
- [ ] What happens when two sync daemons watch the same inotify path — do they race?
- [ ] Should we add a test orchestrator that injects edits and verifies merge correctness?

## Next Steps

1. Create `docker-compose.test.yml` with Approach A
2. Create `vaults/shared-test/` with test content
3. Test: spin up both instances, edit in one, verify in other
4. If Obsidian Sync testing needed: add Tailscale sidecars (Approach B)

## Sources

- [Docker Networking](https://docs.docker.com/engine/network/)
- [Docker Bind Mounts](https://docs.docker.com/engine/storage/bind-mounts/)
- [Tailscale in Docker](https://tailscale.com/kb/1282/docker)
- [linuxserver/obsidian](https://hub.docker.com/r/linuxserver/obsidian)
- [Yjs Testing Patterns](https://docs.yjs.dev/)
- [Obsidian Forum: Multiple Instances](https://forum.obsidian.md/t/multiple-instances-of-one-vault/94568)
