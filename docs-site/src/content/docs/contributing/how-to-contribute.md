---
title: How to Contribute
description: Guide for contributing to Obsidian in Enterprise.
---

## All You Need is a GitHub Account

This project is designed so that anyone with a GitHub account can contribute — no special tools required. You can edit documentation directly on GitHub, submit issues, or clone the repo for code contributions.

## Ways to Contribute

### Documentation (Easiest)

1. Click the **"Edit page"** link at the bottom of any documentation page
2. Edit the markdown file directly on GitHub
3. Submit a pull request
4. That's it — the site rebuilds automatically on merge

The knowledge base uses [Obsidian Flavored Markdown](https://help.obsidian.md/Editing+and+formatting/Obsidian+Flavored+Markdown), so you can use:
- `[[wikilinks]]` for internal links
- `> [!NOTE]` callout syntax
- Standard markdown tables, code blocks, and mermaid diagrams

### Issues and Feedback

- **Bug reports**: [Open an issue](https://github.com/cybersader/obsidian-in-enterprise/issues/new)
- **Feature requests**: Open an issue with the `enhancement` label
- **Questions**: Open a discussion

### Code

1. Fork the repo
2. Create a feature branch
3. Make your changes
4. Submit a pull request

See the [Quick Start](/obsidian-in-enterprise/guides/quickstart/) guide for setting up the development environment.

## Knowledge Base Structure

The `knowledge-base/` directory uses a temperature gradient system. When adding research or documentation:

| What You're Adding | Where to Put It |
|-------------------|-----------------|
| Quick note or idea | `00-inbox/` |
| Active research or draft | `01-working/` |
| Distilled insight or finding | `02-learnings/` (use SCREAMING_SNAKE_CASE) |
| Stable reference doc | `03-reference/` |

Session logs documenting decisions should be prefixed with the date: `YYYY-MM-DD-topic.md`
