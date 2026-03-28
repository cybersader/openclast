---
title: Introduction
description: What is Obsidian in Enterprise and why does it exist?
---

## The Problem

Organizations want to use Obsidian for knowledge management but face blockers:

- **No browser access** — Obsidian is a desktop app; employees need installs and local data
- **No real-time collaboration** — Obsidian Sync doesn't support concurrent multi-user editing
- **No access control** — Vaults are all-or-nothing; you can't scope folders by role or department
- **Data sovereignty** — Sensitive knowledge shouldn't live on employee laptops
- **Unclear security model** — Obsidian's logic is extensible via community plugins and user scripts (Templater, Dataview, QuickAdd, custom JS). There's no established way for IT to audit, approve, or restrict what runs inside a vault. In an enterprise context, an untrusted plugin has full filesystem access and can execute arbitrary code — and there's no sandbox, no plugin signing, and no permission model to prevent it.

## The Solution

Obsidian in Enterprise delivers Obsidian through the browser with three layers:

1. **VNC Gateway** — Kasm Workspaces streams a full Obsidian desktop to the browser. Users get the complete Obsidian experience with zero installs.

2. **CRDT Sync** — A Yjs-based sync layer enables real-time collaborative editing. Multiple users can edit the same notes simultaneously with automatic conflict resolution.

3. **Mount Orchestration** — A custom service translates user identity and group membership into per-user Docker bind mounts. Each user's vault is assembled from only the folders they're authorized to access.

## Current Status

This project is in **POC phase**. The sync daemon, Obsidian plugin, and Docker container are implemented. The mount orchestrator and auth integration are pending.

## How This Documentation Works

This site is built from the project's knowledge base using [Astro Starlight](https://starlight.astro.build/) with the Obsidian theme. The knowledge base uses a **temperature gradient** system:

| Zone | Temperature | Content |
|------|-------------|---------|
| `00-inbox/` | HOT | Quick captures |
| `01-working/` | WARM | Active research and decisions |
| `02-learnings/` | COOL | Distilled insights |
| `03-reference/` | COLD | Stable architecture docs |
| `04-archive/` | FROZEN | Completed research |

All documentation lives in the GitHub repo. No Obsidian installation is needed to contribute — just edit markdown files and submit a PR.
