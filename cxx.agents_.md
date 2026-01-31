# Usage

- C/C++ is used ONLY for compatibility (C linkage, headers, etc.) and compiler outputs
- Stay freestanding as much as possible; avoid libc dependencies
- This also means to avoid C++ unless compatibility with C++ is needed or C++ is the explicit target of a compiler
- Use datatype99 and interface99 liberally when present, but don't add them by default--dependents may not want to use these dependencies transitively

# Error tracking

- Go exception-free--excaptions are non-local and should only be used or emitted when they can be proved correct
- Use Go-like or algebraic result types to express errors instead
