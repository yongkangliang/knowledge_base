# LLM Wiki Knowledge Base

This vault contains the LLM Wiki knowledge base in Karpathy's convention.

## Directory Structure

- `concepts/` — Concept/topic pages with YAML frontmatter and wikilinks
- `entities/` — Entity pages (people, organizations, products, models)
- `comparisons/` — Comparative analyses
- `queries/` — Archived query results
- `raw/articles/` — Raw scraped articles (immutable source material)
- `raw/papers/` — Research papers
- `raw/transcripts/` — Meeting notes and interviews
- `raw/assets/` — Images and charts

## How to Use

When the user asks about AI, LLMs, or related topics, check the wiki for relevant context:
1. Search `concepts/` for topic pages
2. Search `entities/` for model/person/organization pages
3. Search `raw/articles/` for source material
4. Check `index.md` for the full table of contents

## Sync

This vault is synced via Git to `git@github.com:yongkangliang/knowledge_base.git`.
Changes committed here will be pushed automatically via the post-commit hook.
