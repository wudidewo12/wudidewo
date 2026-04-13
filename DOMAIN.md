# Domain: Rich Text Editor Ecosystem

> This file configures the knowledge base for the rich text editor domain.
> It works alongside `CLAUDE.md` which defines the universal framework rules.
> Replace this file to repurpose the knowledge base for a different domain.

---

## Domain Overview

This knowledge base covers the **rich text editor ecosystem on the web** — frameworks, libraries, architectural patterns, related tooling, and web platform standards. Its primary purpose is to support architectural decision-making and feature development for [Plate](https://github.com/udecode/plate), a plugin-based rich text editor framework built on Slate and React.

The scope is intentionally broader than just "editors Plate competes with." It includes:
- Editor frameworks and libraries (direct competitors and alternatives)
- Underlying primitives and rendering approaches
- Cross-domain architecture inspirations (data management, service architectures)
- Web platform standards relevant to text editing
- Techniques for collaboration, layout, performance, and extensibility

---

## Source Tiers

Sources are organized into tiers based on their relevance to Plate's architectural decisions. Tiers affect ingest depth — Tier 1 sources get the most thorough treatment.

### Tier 1: Core Comparison Targets

These are the architectures Plate should be compared against directly. Entity pages for these should be **comprehensive** — every template section filled in depth.

| Source | Why it matters |
|--------|---------------|
| **ProseMirror** | Architectural benchmark. Best substrate for schema, transforms, plugins, and serious document structure. The center of gravity for disciplined rich-text engines. |
| **Lexical** | Strongest non-ProseMirror engine. Modern runtime: immutable state, command system, own reconciliation. Backed by Meta with real product pressure-testing. |
| **Tiptap** | Not the engine winner — the productization winner. Massive extension surface, polished DX, strong ecosystem capture. Compare as a developer product, not as a better engine. |
| **Pretext + Premirror** | Most important future-facing lane. Tackles multiline text measurement and layout without DOM reflow. Strongest path toward solving unsolved page-layout problem on web. |

### Tier 2: Important, But Not the Main Bet

These provide tactical ideas and specific inspirations. Entity pages should be **solid but focused** on what makes them unique.

| Source | Why it matters |
|--------|---------------|
| **Slate** | Plate's direct ancestor. Shaped Plate's mental model. Study to understand inheritance and its limits, not as the future benchmark. **Note:** Despite being Tier 2, Slate's entity page should be treated with near-Tier-1 depth because Plate is built on top of it — understanding Slate deeply is prerequisite to understanding Plate's constraints. |
| **edix** | Experimental, framework-agnostic, small contenteditable state manager. Scalpel, not spine. Worth studying for lightweight surfaces. |
| **use-editable** | Tiny React hook for custom editable surfaces. Reminder that not every surface deserves the full Plate stack. |
| **rich-textarea** | Native textarea behavior + highlighting, decoration, autocomplete. Inspiration for lightweight, high-polish text surfaces. |
| **@react-libraries/markdown-editor** | Markdown-first editing with SSR support. Focused editing package that stays controllable. |
| **BlockNote** | Block-based editor built on ProseMirror + Tiptap. Notion-like editing experience. |
| **CKEditor 5** | Established commercial editor. Custom data model and rendering pipeline. Strong enterprise adoption. |
| **Quill** | Classic editor. Delta-based data model. Large existing user base but aging architecture. |
| **Remirror** | ProseMirror wrapper with React integration. Similar positioning to Tiptap but different approach. |
| **TinyMCE** | Long-standing commercial editor. Traditional architecture. Wide adoption in legacy systems. |

### Plate Itself

Plate's own repo is also a raw source. It is not ranked in the tier system because it is the **subject** of this knowledge base, not a comparison target. Its entity page should be the most comprehensive page in the entire wiki — it serves as the central reference that all comparisons relate back to. Ingest Plate with maximum depth.

### Tier 3: Cross-Domain Architecture Inspirations

These are not editors. They provide architectural patterns worth studying. Entity pages should be **concise** — focus on what Plate can learn from them.

| Source | Why it matters |
|--------|---------------|
| **urql** | Highly customizable pipeline architecture through exchanges. Relevant for editor infrastructure design. |
| **TanStack DB** | Normalized collections, sub-millisecond live queries, instant optimistic writes. Smart inspiration for derived editor state. |
| **VS Code + LSP** | Stable core editor + protocolized external intelligence. The right mental model if Plate ever grows semantic services, AI analyzers, or structural linting. |
| **EditContext API** | Web platform primitive for custom text editing with more control than contenteditable. Track aggressively, don't bet on it yet. |
| **Open UI Richer Text Fields** | Standards work for rich text controls (mentions, autocomplete, highlighting, structured chips). Signal about where the platform is heading. |

---

## Key Concepts to Track

These are the technical concepts most relevant to this domain. During ingest, the LLM should watch for information about these and create/update concept pages accordingly. This list is not exhaustive — new concepts should be added as they emerge from sources.

### Editor Architecture
- `document-model` — How editors represent document structure (tree, flat list, etc.)
- `schema-enforcement` — How editors validate and constrain document structure
- `plugin-architecture` — How editors structure extensibility
- `rendering-strategy` — How editors render content (DOM reconciliation, virtual DOM, direct DOM, custom rendering)
- `state-management` — How editors manage and update editor state
- `command-system` — How editors expose operations and handle user actions
- `normalization` — How editors maintain structural invariants

### Editing Behavior
- `selection-model` — How editors represent and manipulate selection
- `input-handling` — How editors process keyboard, IME, clipboard, drag-and-drop
- `undo-redo` — History management approaches
- `marks-and-decorations` — Inline formatting and visual overlays
- `node-views` — Custom rendering for specific node types
- `void-elements` — Non-editable embedded content (images, embeds, widgets)

### Collaboration
- `collaborative-editing` — Real-time multi-user editing
- `crdt` — Conflict-free replicated data types
- `operational-transform` — OT-based collaboration
- `yjs-integration` — Yjs as a collaboration layer

### Layout and Rendering
- `pagination` — Page-aware layout and composition
- `text-measurement` — Measuring text dimensions for layout
- `virtual-rendering` — Rendering only visible content for performance

### Developer Experience
- `api-surface` — Public API design and stability
- `typescript-integration` — Type safety and developer tooling
- `testing-patterns` — How editors approach testing
- `documentation-quality` — Quality and completeness of docs

---

## Comparison Pages to Create

These comparisons should be created once enough entity pages exist (at least 3 entities with relevant data). The LLM should suggest creating these during lint when prerequisites are met.

- `document-models-compared` — How each editor represents documents
- `plugin-systems-compared` — Extension/plugin architectures across editors
- `selection-models-compared` — Selection handling approaches
- `collaboration-approaches` — CRDT vs OT vs other approaches
- `rendering-strategies-compared` — DOM strategies across editors
- `developer-experience-compared` — DX, docs, onboarding, TypeScript support
- `performance-characteristics` — Runtime performance comparisons
- `react-integration-approaches` — How React-based editors integrate with React

---

## Synthesis Pages to Create

These should be created once the knowledge base has enough depth (10+ entity pages, 15+ concept pages). The LLM should suggest creating these during lint.

- `state-of-web-editors` — Current landscape overview and trends
- `architecture-patterns-overview` — Common patterns and where the field is moving
- `plate-architectural-position` — Where Plate sits relative to the ecosystem
- `unsolved-problems` — Problems no editor has solved well yet
- `future-directions` — Where web editing is heading (EditContext, Pretext, AI, etc.)

---

## Domain-Specific Ingest Rules

### For editor repos (Tier 1 and Tier 2):

In addition to the standard repo read strategy from `CLAUDE.md`, pay special attention to:

1. **Data model** — How is the document represented? Find the core types/interfaces.
2. **Plugin/extension system** — How do users extend the editor? What's the API?
3. **Rendering pipeline** — How does the editor go from state to DOM?
4. **Selection handling** — How is selection represented and manipulated?
5. **Input processing** — How are keyboard events, IME, clipboard handled?
6. **Collaboration support** — Is there built-in collab? What approach?
7. **Testing approach** — How does the project test itself?
8. **Bundle size / dependencies** — What's the footprint?

### For cross-domain repos (Tier 3):

Focus narrowly on what's architecturally transferable to an editor context:
- What pattern or abstraction does this project use that could apply to editors?
- Don't try to fully document the project — document what Plate can learn from it.

### For web standards (specs, docs):

Focus on:
- What capability does this provide?
- What editor problems does it solve?
- What's the browser support / timeline?
- How would Plate adopt or prepare for it?

---

## Plate-Specific Context

This section provides context that the LLM should be aware of when making comparisons or recommendations.

### Plate's Current Stack
- Built on **Slate** as the underlying editor framework
- Uses **React** for rendering
- Plugin-based architecture
- TypeScript-first
- Open-source, MIT licensed

### Plate's Architectural Priorities

When doing architecture or performance comparisons:
1. Prefer `Plate vs Slate` first — direct inheritance pressure
2. Use `ProseMirror` and `Lexical` for deeper runtime or architecture direction
3. Use `Tiptap` more for product-layer or packaging cost than raw engine performance
4. Use `Pretext` and `Premirror` for pagination, composition, and layout-aware editing questions

### Key Questions Plate Is Trying to Answer
- Should Plate continue building on Slate, or adopt a different foundation?
- How should Plate handle pagination and layout (the Pretext/Premirror direction)?
- What's the best plugin/extension architecture for Plate's use cases?
- How should Plate approach collaboration?
- Where should Plate's DX compete with Tiptap?
