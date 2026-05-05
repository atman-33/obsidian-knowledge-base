---
title: Inbox Projects Guide
created: 2026-05-05
type: reference
tags:
  - inbox
  - projects
  - structure
---

# Inbox Projects Guide

This folder contains lightweight working notes for products, backlog items, and optional epic groupings.

## Recommended Structure

```text
Inbox/Projects/
  README.md
  <Product>/
    Dev Notes/
      Architecture/
      Database/
      Product Notes/
    PBL/
      <general PBI notes>
      <Epic>/
        <epic-specific PBI notes>
```

## Folder Usage

- `<Product>`: Top-level folder for one product or service.
- `Dev Notes`: Product-level development notes that are shared across backlog items or epics.
- `Architecture`: Notes about system design, components, integrations, and technical decisions.
- `Database`: Notes about schemas, tables, queries, data flows, and access considerations.
- `Product Notes`: Product-specific notes that do not fit the architecture or database categories.
- `PBL`: Product backlog area. Put small or general PBI notes here when they do not belong to a specific epic.
- `<Epic>` under `PBL`: Optional grouping folder for PBI notes that belong to the same development theme.

## Working Rule

- Put cross-epic or long-lived development notes in `Dev Notes`.
- Put small or independent backlog notes directly in `PBL`.
- Create an epic folder under `PBL` only when the grouping helps clarify the backlog.

`Sample Product` in this folder is only an example of this structure.