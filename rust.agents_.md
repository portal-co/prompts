Rust is our main language of choice, used for non-web tasks for its powerful type system and performance


# Types

- Use `enum`s as much as possible; parse strings presented into rich types ASAP.
- Use `BTree` collections as they are more reliable and don't require `std`

# Platforms

- WASM does NOT mean the web; do NOT depend on `wasm-bindgen` AT ALL outside of TypeScript interop crates, which should be colocated with TypeScript packages
- Make as many crates `no_std` as possible; abstract OS interfaces into traits

---

# Example Scenarios

## Rich Type System Benefits: moond Bit Ordering

**Context:** The `moond` project leveraged Rust's enum and type system for safe bit manipulation.

**Challenge:** Bit ordering operations needed to be both safe and performant, with clear compile-time guarantees.

**Solution:**
- Used Rust enums to represent different bit ordering schemes
- Type system prevented invalid bit patterns at compile time
- AI suggested edge case handling that complemented human domain expertise

**Outcome:** The combination of Rust's type safety and collaborative problem-solving produced elegant, bug-free code.

**Key Lesson:** Parse into rich types (enums, newtypes) as early as possible. Let the type system catch errors at compile time.

---

## Migration Planning with Rich Types: dreamcomp id-arena

**Context:** Migrating `dreamcomp` from `id-arena` required changing types throughout the codebase.

**Challenge:** Ensuring type safety during migration while allowing parallel development.

**Solution:**
- Created newtype wrappers for the new arena ID types
- Used Rust's type system to make old and new IDs incompatible
- This forced explicit migration at each usage site
- Generated helper functions to bridge between old and new types during transition

**Outcome:** The compiler prevented accidental mixing of old and new arena IDs, making the migration safe and auditable.

**Key Lesson:** Use newtypes and the type system to enforce migration invariants. Let the compiler be your migration guide.

---

## Additional Examples

<!-- Add more examples here as they arise. Format:

### [Example Title]

**Context:** [What was the project/situation]

**Challenge:** [What problem needed solving]

**Solution:** [How it was approached]

**Outcome:** [What happened]

**Key Lesson:** [What to remember for future work]
-->