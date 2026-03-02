# Core Architecture

- An expert human leads an army of AI agents
- The human has valuable insight, and it should largely take priority. If some conflict comes up with it, explicitly debate it instead of silently ignoring the user (there have been cases, rare ones, where AI decisions were helpful. See `moond`'s bit ordering method as an example where AI and human insights combined to help make a tricky task easy).
- When interactive, interact. When not interactive, keep records in Git. When interactive, keep records in Git anyhow
- Prefer generating deterministic scripts or compilers instead of doing tasks manually, especially when they do not involve language-based tasks (refactors, renames, migrations)
- Generate plans before performing large tasks so the human can review the approach, propose changes (see the migration from `id-arena` in `dreamcomp` as an example where a new approach helped with parallel changes), and eventually commit to the plan
- Agent components, ending in `agents_.md`, extend existing agent documentation; read them as needed

# Agent Components Overview

This documentation is structured into specialized components covering different aspects of AI-assisted development:

## Development Practices

- **[code_style.agents_.md](code_style.agents_.md)** - Code structure, compilers vs manual tasks, type systems, dependency management, and language-specific patterns
- **[workflow.agents_.md](workflow.agents_.md)** - Version control, commit message standards, change tracking, and rollback procedures for AI agents
- **[general_guidance.agents_.md](general_guidance.agents_.md)** - Attribution policy, disclaimers, and responsible AI usage guidelines
- **[referenced_documents.agents_.md](referenced_documents.agents_.md)** - Documentation standards, source citation requirements, TODO:VERIFY and UNKNOWN tags
- **[ai_key.agents_.md](ai_key.agents_.md)** - AI submission key system: agent forms, task interaction spectrum, OpenClaw threat model, and how the key filters bad actors while allowing legitimate agents

## Language-Specific Guidance

- **[rust.agents_.md](rust.agents_.md)** - Rust as primary language: enum usage, no_std patterns, BTree collections
- **[web.agents_.md](web.agents_.md)** - TypeScript/Lit frontend, bundling with zshy, newtype patterns, WASM interop
- **[cxx.agents_.md](cxx.agents_.md)** - C/C++ usage limited to compatibility and compiler outputs, freestanding preferences

## Orchestration

- **[cross_repo_orchestration.agents_.md](cross_repo_orchestration.agents_.md)** - Multi-repository coordination using feed files, task distribution patterns, agent communication

Each component file includes practical examples and patterns. Refer to specific component files for detailed guidance in each area.