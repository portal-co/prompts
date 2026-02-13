# Usage

- C/C++ is used ONLY for compatibility (C linkage, headers, etc.) and compiler outputs
- Stay freestanding as much as possible; avoid libc dependencies
- This also means to avoid C++ unless compatibility with C++ is needed or C++ is the explicit target of a compiler
- Use datatype99 and interface99 liberally when present, but don't add them by default--dependents may not want to use these dependencies transitively

# Error tracking

- Go exception-free--excaptions are non-local and should only be used or emitted when they can be proved correct
- Use Go-like or algebraic result types to express errors instead

---

# Example Scenarios

## Freestanding Compiler Output

**Context:** Generated C code for embedded systems from a higher-level IR.

**Challenge:** Target environments had no standard library; code needed to be completely freestanding.

**Solution:**
- Compiler generated pure C with no libc dependencies
- Used custom result types instead of exceptions
- All error paths explicit in generated code

**Outcome:** Generated code worked in bare-metal environments without modification.

**Key Lesson:** When targeting C/C++, stay freestanding by default. Add dependencies only when absolutely necessary.

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
