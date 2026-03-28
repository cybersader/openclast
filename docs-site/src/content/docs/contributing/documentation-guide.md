---
title: Documentation Guide
description: How documentation is structured and built.
---

## How the Docs Site Works

This documentation site is built with [Astro Starlight](https://starlight.astro.build/) and pulls content from two sources:

1. **Hand-written guides** in `docs-site/src/content/docs/` — structured onboarding content (this page, for example)
2. **Knowledge base** in `knowledge-base/` — research, decisions, and architecture docs imported via [starlight-obsidian](https://starlight-obsidian.vercel.app/)

The knowledge base files are written in Obsidian Flavored Markdown. The `starlight-obsidian` plugin converts wikilinks, callouts, embeds, and frontmatter to Starlight-compatible format at build time.

## Supported Markdown Features

### Standard Markdown
All GitHub Flavored Markdown is supported: headings, lists, tables, code blocks, images, links.

### Obsidian Extensions (in knowledge-base/ files)

| Syntax | What It Does |
|--------|-------------|
| `[[Page Name]]` | Internal wikilink |
| `[[Page Name\|Display Text]]` | Aliased wikilink |
| `[[Page Name#Heading]]` | Link to heading |
| `![[Page Name]]` | Embed another page |
| `![[image.png]]` | Embed an image |
| `> [!NOTE] Title` | Callout/admonition |
| `> [!WARNING]- Collapsed` | Collapsible callout |
| `` ```mermaid `` | Mermaid diagrams |
| `$E = mc^2$` | Inline math |

### Starlight Extensions (in guides/ files)

Starlight guides support [MDX](https://mdxjs.com/) and Starlight's built-in components:

```mdx
import { Card, CardGrid, Tabs, TabItem } from '@astrojs/starlight/components';

<Tabs>
  <TabItem label="Docker">Docker instructions here</TabItem>
  <TabItem label="Manual">Manual setup here</TabItem>
</Tabs>
```

## Building Locally

```bash
cd docs-site
bun install
bun run dev     # Dev server at http://localhost:4321
bun run build   # Production build
```

## Deployment

The site deploys to GitHub Pages automatically via GitHub Actions on push to `main`. The workflow:

1. Checks out the repo (includes `knowledge-base/`)
2. Installs dependencies in `docs-site/`
3. Runs `astro build`
4. Deploys to GitHub Pages

No Obsidian installation is needed at any point in this pipeline.
