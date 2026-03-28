---
tags:
  - test
  - sync
  - concurrent-edit
date created: 2026-03-28
---

# Sync Test Document

Use this file to test concurrent editing between instances.

## Section A — Alice edits here

This section is for Alice to edit. After editing, verify Bob's instance reflects changes.

Content: [EDIT THIS LINE]

## Section B — Bob edits here

This section is for Bob to edit. After editing, verify Alice's instance reflects changes.

Content: [EDIT THIS LINE]

## Section C — Both edit simultaneously

Both Alice and Bob should edit this section at the same time to test CRDT merge.

Line 1: [BOTH EDIT THIS]
Line 2: [BOTH EDIT THIS]
Line 3: [BOTH EDIT THIS]

## Merge Log

Record merge results here:

| Time | Who Edited | Section | Result |
|------|-----------|---------|--------|
| | | | |
