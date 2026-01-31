# Structure

- Prefer compilers: determinstic programs or scripts which perform automated tasks. Production-ready compilers should be in a language at least as expressive as Rust, but if you just have a task, you can create an initial compiler in Rust, or scripting languages like Python, JavaScript, or TypeScript. Especially prefer compilers if you are a human or frontier model (currently Claude Opus 4.5 and Gemini 3 are adequate)
- Keep all scripts and compilers as Git blobs; retain them through at least one commit or progress report
- Avoid calling LLMs unless required, ESPECIALLY at build and runtime
- Keep types seperate whenever possible; don't make things stringly-typed and use newtypes heavily

TypeScript newtypes are a common pattern:
```ts
class Newtype{
    readonly #wrapped: Oldtype;
    constructor(wrapped: Oldtype){
        this.#wrapped = wrapped;
    }
    get wrapped(): Oldtype{
        return this.#wrapped;
    }
}
```

- On types, prefer them as much as possible, using TypeScript over JavaScript (use node `--experimental-strip-types`, for instance). Annotate Python programs, and lean towards Rust as the main language whenever possible, though functional languages can provide the needed expressivity as well
- Keep a single source of truth; compile or process with citations it into derived artifacts

- Language Choice:
Rust: for production applications and compilers
C/C++: for compatibility or as an output of a compiler ONLY
TypeScript: for production websites and adjacent tooling not better served by Rust
Go: for robust scripts which are intended to last
Python: for one-time or few-time scripts (still make sure they are checked into Git though)

- When parsing a structure (NOT a Rust `enum`) that needs to be unambiguous,

1. Keep a status field in it, unsigned, set to `-1` on init
2. When parsing, increment the status field instead of returning the structure, which happens at the end
3. The status then represents data as follows: `-1` means no match, `0` means it was unambiguous, any other value means multiple structures matched


# Dependencies

- Minimize library dependencies, including standard library depeendencies, but
- Don't reinvent the wheel; if something already exists out there, use it
This includes opaque blobs; we have plenty of binary recompilation tools you can attempt to use. Prefer open-source though
