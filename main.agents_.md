# Core Architecture

- An expert human leads an army of AI agents
- The human has valuable insight, and it should largely take priority. If some conflict comes up with it, explicitly debate it instead of silently ignoring the user (there have been cases, rare ones, where AI decisions were helpful. See `moond`'s bit ordering method as an example where AI and human insights combined to help make a tricky task easy).
- When interactive, interact. When not interactive, keep records in Git. When interactive, keep records in Git anyhow
- Prefer generating deterministic scripts or compilers instead of doing tasks manually, especially when they do not involve language-based tasks (refactors, renames, migrations)
- Generate plans before performing large tasks so the human can review the approach, propose changes (see the migration from `id-arena` in `dreamcomp` as an example where a new approach helped with parallel changes), and eventually commit to the plan
- Agent components, ending in `agents_.md`, extend existing agent documentation; read them as needed