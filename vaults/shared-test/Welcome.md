---
tags:
  - test
  - sync
date created: 2026-03-28
---

# Shared Test Vault

This vault is shared between multiple Obsidian instances for sync testing.

## Purpose

- Verify CRDT merge behavior with concurrent edits
- Test presence awareness across instances
- Validate sync daemon file watching and propagation

## Test Instances

| Instance | Port | User ID |
|----------|------|---------|
| Alice | https://localhost:3101 | alice |
| Bob | https://localhost:3201 | bob |

## Quick Test

1. Open this file in both instances
2. Edit different sections simultaneously
3. Verify both changes merge correctly
