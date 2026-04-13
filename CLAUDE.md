# Knowledge Base Framework

> This file defines the universal rules for building and maintaining a knowledge base.
> It is domain-agnostic. Domain-specific configuration lives in `DOMAIN.md`.
> Claude Code reads this file automatically when working in this directory.

---

## Quick Start

Common operations — just say these to Claude Code:

| Command | What it does |
|---------|-------------|
| `ingest raw/<source>` | Read a raw source, generate/update wiki pages |
| `ingest all new` | Ingest all sources where `ingested: false` in registry |
| `query <question>` | Ask a question against the wiki, get a synthesized answer |
| `lint` | Run a health check on the entire wiki |
| `lint <page>` | Check a specific page for issues |
| `status` | Show stats: total pages, gaps, confidence distribution |

---

## Principles

These are non-negotiable rules for all wiki content:

1. **Cite your sources.** Every factual claim must reference which raw source it comes from. Never say "X is slow" — say "X is slow (source: raw/slate, README: 'still beta')".
2. **Separate fact from inference.** If the LLM is making a judgment or inference not directly stated in the raw sources, mark it clearly: `[inference]` or `> Note: This is an inference based on...`.
3. **Never fabricate.** If raw sources don't contain information for a section, leave it as `<!-- TODO: No data in current sources -->`. An honest gap is better than a plausible lie.
4. **Link aggressively.** Every page should link to related pages with `[[wikilinks]]`. An orphan page (linked from nowhere, linking to nothing) is a red flag.
5. **Prefer depth over breadth.** A detailed page about one concept is more valuable than shallow pages about ten concepts.
6. **Respect the raw layer.** Never modify anything in `raw/`. It is the source of truth. All derived knowledge lives in `wiki/`.
7. **Make every operation traceable.** Every change to the wiki must be logged in `wiki/log.md` with what changed, why, and what sources were used.

---

## Project Structure

```
<knowledge-base>/
├── CLAUDE.md                  ← This file. Universal framework rules.
├── DOMAIN.md                  ← Domain-specific configuration.
├── raw/                       ← Raw sources. READ-ONLY. Never modified by LLM.
├── raw-registry.md            ← Catalog of all raw sources with metadata.
├── wiki/                      ← LLM-owned knowledge wiki.
│   ├── index.md               ← Master catalog of all wiki pages, by category.
│   ├── log.md                 ← Chronological record of all operations.
│   ├── tags.md                ← All tags and which pages/sources use them.
│   ├── gaps.md                ← Known knowledge gaps and TODOs.
│   ├── entities/              ← One file per project, tool, org, or person.
│   ├── concepts/              ← One file per technical concept or pattern.
│   ├── summaries/             ← One file per raw source (its distilled summary).
│   ├── comparisons/           ← Cross-cutting comparisons across entities.
│   └── synthesis/             ← High-level insights and overviews.
└── outputs/                   ← Query results, reports, slides, charts.
```

### Directory Rules

| Directory | Owner | Rules |
|-----------|-------|-------|
| `raw/` | Human | LLM reads only. Human adds sources here. |
| `wiki/` | LLM | LLM creates, updates, and maintains all files. Human rarely edits directly. |
| `outputs/` | LLM | Query results and generated reports. Can be filed back into `wiki/`. |

---

## Raw Registry

`raw-registry.md` is the master catalog of all raw sources. It lives in the project root (not inside `wiki/`). The LLM reads this before every operation to understand what sources exist.

### Format

```markdown
# Raw Registry

## Sources

### <id>
- **name**: Human-readable name
- **type**: repo / article / spec / docs / paper / notes / video-notes
- **path**: raw/<folder-or-file>
- **url**: Original URL (if applicable)
- **description**: One-line description of what this source is
- **tags**: [auto-generated tags]
- **added**: YYYY-MM-DD
- **ingested**: true / false
- **last-ingested**: YYYY-MM-DD (if ingested)
- **notes**: Any human-added context about this source
```

### Example Entry

```markdown
### slate
- **name**: Slate
- **type**: repo
- **path**: raw/slate
- **url**: https://github.com/ianstormtaylor/slate
- **description**: Customizable React-based rich text editor framework
- **tags**: [editor, framework, react, contenteditable]
- **added**: 2026-04-10
- **ingested**: false
- **last-ingested**: —
- **notes**: Plate's underlying foundation. Still marked as beta.
```

### Auto-Tagging Rules

When the LLM ingests a new source or registers it in the registry:

1. **Analyze the source content** to determine appropriate tags.
2. **Read all existing tags** from `raw-registry.md` and `wiki/tags.md` first.
3. **Reuse existing tags** if they cover the same concept. Do not create synonyms.
   - Example: if `editor` already exists, do not create `text-editor` or `rich-text-editor`.
4. **Create a new tag** only if no existing tag covers the concept.
5. **Tag format**: all lowercase, hyphen-separated. Example: `collaborative-editing`, `plugin-system`.
6. **Be specific but not excessive.** Aim for 3–7 tags per source. Each tag should be meaningful and reusable across multiple sources.

---

## Wiki Page Types and Templates

Every `.md` file in `wiki/` must follow its type-specific template. All pages share a common frontmatter structure and a `## Related` section at the end.

### Common Frontmatter

```yaml
---
title: Page Title
type: entity | concept | summary | comparison | synthesis
created: YYYY-MM-DD
updated: YYYY-MM-DD
confidence: high | medium | low | draft
tags: [tag1, tag2, tag3]
sources: [source-id-1, source-id-2]
---
```

**Confidence levels:**

| Level | Meaning |
|-------|---------|
| `high` | Based on thorough reading of primary sources. Claims are well-supported. |
| `medium` | Based on partial reading or secondary sources. Generally reliable but may have gaps. |
| `low` | Based on limited information. May need significant revision. |
| `draft` | Placeholder or skeleton. Needs substantial work. |

The LLM must update `confidence` as content improves or as new sources are ingested.

---

### Template: Entity

**Purpose:** Describe a specific project, tool, library, organization, or person.
**File location:** `wiki/entities/<name>.md`
**Naming:** Use the project/entity's canonical short name. Example: `slate.md`, `prosemirror.md`.

```markdown
---
title:
type: entity
created:
updated:
confidence:
tags: []
sources: []
---

## Overview
<!-- What is this? One paragraph summary. -->

## History
<!-- When created? Key milestones, major versions, current maintenance status. -->

## Architecture
<!-- Core architectural decisions. Data model, rendering strategy, state management. -->

## Key Features
<!-- Most important features. Explain, don't just list. -->

## Strengths
<!-- Where does it excel? Be specific and cite sources. -->

## Weaknesses
<!-- Where does it fall short? Be specific and cite sources. -->

## Ecosystem
<!-- Community size, plugin ecosystem, corporate backing, adoption. -->

## Related
<!-- [[wikilinks]] with brief reason for each link. -->
```

---

### Template: Concept

**Purpose:** Explain a technical concept, design pattern, or architectural idea.
**File location:** `wiki/concepts/<name>.md`
**Naming:** Use the concept's canonical name. Example: `plugin-architecture.md`, `collaborative-editing.md`.

```markdown
---
title:
type: concept
created:
updated:
confidence:
tags: []
sources: []
---

## Definition
<!-- Clear, precise definition. -->

## Why It Matters
<!-- What problems does it solve? Why should someone care? -->

## How It Works
<!-- Technical explanation of the mechanism or pattern. -->

## Implementations
<!-- How do different projects implement this? Be specific per project. -->

## Trade-offs
<!-- Tensions and trade-offs inherent in this concept. -->

## Open Problems
<!-- Unsolved or debated aspects. Frontier questions. -->

## Related
```

---

### Template: Summary

**Purpose:** Distill a single raw source into key takeaways.
**File location:** `wiki/summaries/<source-id>-summary.md`
**Naming:** Source id + `-summary` suffix to avoid collision with entity pages. Example: `slate-summary.md` for `raw/slate`.

```markdown
---
title:
type: summary
created:
updated:
confidence:
tags: []
sources: []
---

## Source Info
<!-- Type, URL, date accessed, size/scope of the source. -->

## Key Takeaways
<!-- The 3–7 most important things from this source. -->

## Detailed Notes
<!-- Deeper notes organized by topic. Reference specific files/sections in the raw source. -->

## Relevance
<!-- How does this source connect to the broader knowledge base? What existing pages does it inform or challenge? -->

## Questions Raised
<!-- Questions this source raises that aren't answered by current knowledge. These feed into gaps.md. -->

## Related
```

---

### Template: Comparison

**Purpose:** Horizontal comparison across multiple entities on a specific dimension.
**File location:** `wiki/comparisons/<topic>.md`
**Naming:** Describe what's being compared. Example: `table-implementations.md`, `selection-models.md`.

```markdown
---
title:
type: comparison
created:
updated:
confidence:
tags: []
sources: []
---

## Scope
<!-- What exactly is being compared? Which entities? On what dimensions? -->

## Comparison Table
<!-- Structured comparison in markdown table format. -->

## Analysis
<!-- Deeper analysis beyond the table. Patterns, surprises, insights. -->

## Recommendation
<!-- Implications and which approach seems best. Mark as [inference] if this is LLM judgment. -->

## Related
```

---

### Template: Synthesis

**Purpose:** High-level insights that emerge from combining knowledge across multiple pages.
**File location:** `wiki/synthesis/<topic>.md`
**Naming:** Describe the synthesis topic. Example: `future-of-web-editors.md`, `architecture-patterns-overview.md`.

```markdown
---
title:
type: synthesis
created:
updated:
confidence:
tags: []
sources: []
---

## Thesis
<!-- Core insight or argument. What is the main point? -->

## Evidence
<!-- Supporting evidence drawn from multiple wiki pages and raw sources. -->

## Implications
<!-- What follows from this thesis? What should change, be built, or be investigated? -->

## Open Questions
<!-- What remains uncertain or unresolved? -->

## Related
```

---

## Operations

### Ingest

**Trigger:** `ingest raw/<source>` or `ingest all new`

**Purpose:** Read a raw source, extract knowledge, generate/update wiki pages.

**Procedure:**

1. **Read context files:**
   - Read `CLAUDE.md` (this file) for rules.
   - Read `DOMAIN.md` for domain-specific configuration.
   - Read `raw-registry.md` for existing source catalog.
   - Read `wiki/index.md` for existing wiki structure.
   - Read `wiki/tags.md` for existing tag vocabulary.

2. **Analyze the raw source using type-specific read strategy:**

   | Source type | Read strategy |
   |------------|---------------|
   | `repo` | README.md → docs/ directory → package.json (or equivalent build/config files) → src/ directory structure (understand organization, don't read every file) → core module entry points → CHANGELOG.md or release notes → examples/ if present |
   | `article` | Read the full text. |
   | `spec` | Read the full spec. Focus on definitions, APIs, and key constraints. |
   | `docs` | Read table of contents first. Prioritize conceptual/architectural docs over API reference. |
   | `paper` | Abstract → Introduction → Conclusion → Methods (in that order). |
   | `notes` | Read the full text. |
   | `video-notes` | Read the full text. Treat as secondhand summary, mark confidence accordingly. |

3. **Register in raw-registry.md:**
   - If the source is not yet registered, add a new entry with all fields.
   - Auto-generate tags following the tagging rules.
   - Set `ingested: true` and `last-ingested` to today's date.

4. **Generate or update wiki pages:**

   **Depth is tier-aware.** Check `DOMAIN.md` for the source's tier. Tier 1 sources get comprehensive, deeply detailed pages. Tier 2 sources get solid but focused pages. Tier 3 sources get concise pages focused on what's transferable. If no tier is defined, default to medium depth.

   a. **Always create a Summary page** (`wiki/summaries/<source-id>-summary.md`).

   b. **Create Entity pages** if the source is about a specific project/tool that doesn't already have an entity page.

   c. **Create or update Concept pages** for each significant technical concept. If a concept page already exists, update it (especially `## Implementations`) rather than creating a duplicate.

   d. **Update Comparison pages** if the new source adds information relevant to an existing comparison. Do not create new comparison pages during ingest unless `DOMAIN.md` specifically requests one.

   e. **Update Synthesis pages** if the new source significantly changes or supports an existing synthesis. Do not create new synthesis pages during ingest.

5. **Update system files:**
   - `wiki/index.md` — add any new pages.
   - `wiki/tags.md` — add any new tags or tag assignments.
   - `wiki/gaps.md` — add questions/gaps discovered during ingest; remove gaps this ingest resolved.

6. **Run local lint** on all pages created or modified in this ingest. Fix any issues found.

7. **Log the operation** in `wiki/log.md`.

8. **Git commit** with message: `ingest: <source-id>`.

**Re-ingesting a source:** If a source has `ingested: true` and the user runs ingest again, treat it as an update. Read the existing wiki pages first, then compare with the raw source for changes. Update pages with new information, bump `updated` dates, and adjust `confidence` if needed. Update `last-ingested` in the registry. Log as `[re-ingest]` in log.md.

**Batch ingest (`ingest all new`):** Find all sources in `raw-registry.md` where `ingested: false`. If the registry doesn't exist yet, scan `raw/` directory to discover sources and create the registry first. Process sources in tier order: Tier 1 first, then Tier 2, then Tier 3, then untiered. Within the same tier, process alphabetically. This ensures the most important sources establish the wiki's foundation before less critical ones are added. Run one combined lint pass and one git commit at the end, not per source.

---

### Query

**Trigger:** `query <question>` or just asking a natural language question.

**Purpose:** Answer a question using the knowledge base. Optionally enhance the wiki.

**Procedure:**

1. **Read context:**
   - Read `wiki/index.md` and `wiki/tags.md` to locate relevant pages.
   - Read `raw-registry.md` to know what raw sources are available if deeper lookup is needed.

2. **Read relevant pages:**
   - Start with the most relevant pages based on the question.
   - Follow `[[wikilinks]]` to find additional relevant information.
   - If wiki pages are insufficient, go back to `raw/` sources for more detail.

3. **Synthesize the answer:**
   - Cite which wiki pages and raw sources informed the answer.
   - Distinguish facts from inferences.

4. **Save substantial outputs:**
   - If the answer is substantial (more than a few paragraphs), save to `outputs/` as `.md`.
   - Include frontmatter: `title`, `query`, `date`, `sources-consulted`.

5. **File back to wiki (optional but encouraged):**
   - If the query produced new insights, update relevant wiki pages.
   - If a new synthesis page should exist, create it.
   - If knowledge gaps were revealed, add them to `wiki/gaps.md`.

6. **Log the operation** in `wiki/log.md`.

7. **Git commit** (only if wiki files were changed) with message: `query: <short description> → outputs/<filename>.md`.

---

### Lint

**Trigger:** `lint` (full wiki) or `lint <page>` (single page).

**Purpose:** Check wiki health and fix issues.

**Procedure:**

1. **Structural checks:**
   - Broken `[[wikilinks]]` (target page doesn't exist).
   - Orphan pages (not linked from any other page, not in index).
   - Pages missing required template sections.
   - Pages with empty or `<!-- TODO -->` sections.
   - Pages with invalid or incomplete frontmatter.

2. **Content checks:**
   - Pages with `confidence: low` or `confidence: draft` — flag for improvement.
   - Claims without source citations.
   - Tag inconsistencies (synonyms, unused tags, misspellings).
   - `sources` in frontmatter don't match actual content references.
   - Stale information (source re-ingested but page not updated).

3. **Relationship checks:**
   - Concepts mentioned frequently but without their own concept page.
   - Entities referenced but without their own entity page.
   - Suggest new comparison pages where 3+ entities discuss the same concept differently.
   - Suggest new synthesis pages where patterns emerge across multiple pages.

4. **Auto-fix safe issues:**
   - Fix broken wikilinks where the target is obvious (typo).
   - Normalize tags.
   - Add missing pages to `wiki/index.md`.
   - Update `wiki/tags.md`.

5. **Record complex gaps:**
   - Add to `wiki/gaps.md` with checkboxes.
   - Format: `- [ ] <description> (found by lint on YYYY-MM-DD)`

6. **Generate lint report** → `outputs/lint-report-YYYY-MM-DD.md`.

7. **Log the operation** in `wiki/log.md`.

8. **Git commit** with message: `lint: <summary of fixes>`.

---

### Status

**Trigger:** `status`

**Purpose:** Quick overview of the knowledge base's current state.

**Procedure:**

1. Count total pages by type (entities, concepts, summaries, comparisons, synthesis).
2. Count confidence distribution (high / medium / low / draft).
3. Count total raw sources and how many are ingested vs pending.
4. Count open gaps in `wiki/gaps.md`.
5. List the 5 most recent operations from `wiki/log.md`.
6. Report any urgent issues (broken links, orphan pages).
7. Print a concise summary to the terminal. Do not save to file unless asked.

---

## System File Formats

### wiki/index.md

```markdown
# Wiki Index

> Auto-maintained by LLM. Do not edit manually.
> Last updated: YYYY-MM-DD

## Entities
- [[slate]] — Customizable React-based editor framework
- [[prosemirror]] — Disciplined rich-text engine

## Concepts
- [[plugin-architecture]] — How editors structure extensibility

## Summaries
- [[slate-summary]] — Summary of raw/slate

## Comparisons
- [[selection-models]] — How editors handle selection

## Synthesis
- [[future-of-web-editors]] — Where the ecosystem is heading
```

### wiki/log.md

Newest entries first.

```markdown
# Operation Log

## YYYY-MM-DD

### [ingest] slate
- **Source:** raw/slate
- **Pages created:** slate.md, slate-summary.md
- **Pages updated:** plugin-architecture.md
- **Tags added:** slate, immer
- **Gaps found:** No data on Slate's collaboration support
- **Notes:** Still in beta. Entity confidence set to medium.

### [query] "How does Slate handle nested blocks?"
- **Pages consulted:** slate.md, plugin-architecture.md
- **Output:** outputs/slate-nested-blocks.md
- **Wiki updates:** Added section to slate.md
```

### wiki/tags.md

```markdown
# Tags

> Auto-maintained by LLM. Do not edit manually.
> Last updated: YYYY-MM-DD

## editor
Sources: slate, prosemirror, lexical, tiptap
Pages: [[slate]], [[prosemirror]], [[lexical]], [[tiptap]]

## collaborative-editing
Sources: prosemirror
Pages: [[collaborative-editing]], [[prosemirror]]
```

### wiki/gaps.md

```markdown
# Knowledge Gaps

> Added by ingest, query, and lint. Check off when resolved.

## Content Gaps
- [ ] slate.md: "Weaknesses" section empty (ingest 2026-04-10)
- [ ] No concept page for "collaborative editing" (lint 2026-04-10)

## Source Gaps
- [ ] Pretext repo not yet in raw/ (from DOMAIN.md)
- [ ] No articles about CRDT vs OT ingested

## Questions
- [ ] How does Lexical handle undo/redo vs Slate? (query 2026-04-11)
```

---

## Conventions

### File Naming
- All lowercase, hyphen-separated (`kebab-case`).
- Example: `plugin-architecture.md`, `collaborative-editing.md`
- Never: `PluginArchitecture.md`, `plugin_architecture.md`
- Filenames must be **globally unique** across all wiki subdirectories.

### Wikilinks
- Format: `[[filename]]` — no path prefix, no `.md` suffix.
- Example: `[[slate]]`, `[[plugin-architecture]]`
- Never: `[[entities/slate]]`, `[[slate.md]]`
- Include a brief reason: `- [[slate]] — Plate's underlying framework`
- **Obsidian setting:** Set `Settings → Files & Links → New link format` to **"Shortest path when possible"** so that `[[slate]]` resolves correctly regardless of which subdirectory the file is in.

### Language
- All wiki content in **English**.
- Users may query in any language; answers match the user's language.
- Technical terms use their canonical English form.

### Dates
- Always `YYYY-MM-DD`.

### Git
- Commit after every operation.
- Message format: `<operation>: <description>`
- Examples:
  - `ingest: slate`
  - `ingest: batch — prosemirror, lexical, tiptap`
  - `query: selection model comparison → outputs/selection-models.md`
  - `lint: fixed 3 broken links, added 2 concept pages`
  - `manual: added pretext repo to raw/`

---

## Extending the Framework

### Adding New Page Types

1. Define the template in this file under `## Wiki Page Types and Templates`.
2. Create the directory under `wiki/`.
3. Add the new type to the frontmatter `type` enum.
4. Add a section in `wiki/index.md`.
5. Run `lint` to verify.

### Adding New Source Types

1. Add the type to the raw-registry format.
2. Define its read strategy in `## Operations > Ingest`.
3. No structural changes needed.

### Adding New Operations

1. Define trigger, purpose, and procedure in `## Operations`.
2. Add to the Quick Start table.
3. Define log format and git commit format.

---

## Reminders for Claude Code

- **Always read `DOMAIN.md` alongside this file.** It has domain-specific context.
- **Always read `raw-registry.md` and `wiki/index.md` before any operation.** Know what exists before creating.
- **Prefer updating existing pages over creating new ones.** Duplication is worse than density.
- **When in doubt, add to `wiki/gaps.md`** rather than guessing or fabricating.
- **Every session should end with a git commit** if any files were changed.
