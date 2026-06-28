---
name: LLM Wiki Schema
description: Karpathy's LLM Wiki convention — directory structure, frontmatter, linking rules
---

# LLM Wiki Schema

## Directory Structure

```
~/.hermes/wiki/           # Root = Obsidian vault
├── SCHEMA.md             # Layer 3: conventions, structure rules, domain config
├── index.md              # Sectioned table of contents with one-line summaries
├── log.md                # Chronological operation log
├── raw/                  # Layer 1: immutable source materials
│   ├── articles/         # Web articles, clippings
│   ├── papers/           # PDFs, arxiv papers
│   ├── transcripts/      # Meeting notes, interviews
│   └── assets/           # Images, charts referenced by sources
├── entities/             # Layer 2: entity pages (people, orgs, products, models)
├── concepts/             # Layer 2: concept/topic pages
├── comparisons/          # Layer 2: comparative analyses
└── queries/              # Layer 2: archived query results
```

## Principles

1. **Layer 1 (raw/)**: Immutable source material. Never edit after ingestion.
2. **Layer 2 (entities/, concepts/, comparisons/, queries/)**: Human-curated pages with YAML frontmatter and `[[wikilinks]]`.
3. **Layer 3 (SCHEMA.md, index.md, log.md)**: Meta-conventions and navigation.

## Frontmatter Convention

```yaml
---
title: "Page Title"
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: [tag1, tag2]
sources: ["https://..."]
---
```

## Linking Rules

- Every concept page must have at least 2 `[[wikilinks]]` to related concepts/entities
- Cross-reference between layers: raw articles link to concepts, concepts link to entities
- Use `index.md` as the main navigation hub
