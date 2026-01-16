We use `TypeScript` as our web platform language, and for interoperability with web-based apps. We use [Lit](https://lit.dev) as our frontend framework (despite the most common framework being React).

# General

- Our library bundler of choice is `zshy`, which runs `tsc` on files to ensure they pass typechecking
- Use newtypes:

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

This is not common style, but we use it for type safety
- Use `#`-private fields; NEVER use the typescript `private` modifier
- Generated typescript should follow these same practices

# Backend/Codegen

- Prefer Rust for these usecases, unless JS-specific features are needed AND there is no Rust equivalent (`jsaw-core`, built on SWC, handles most analysis of JS that would need to be performed)
- Existing TypeScript backends and codegen are fine
- Cloudflare Workers, and compatible runtimes, are a partial exception--since they are hardened, using TypeScript is fine so long as it stays secure

# Frontend

- For dependencies, use compiled TypeScript source. Failing that, use a TypeScript library, which may already exist. Failing that, use a Rust WASM binary, but remember WASM is not web-exclusive and the `wasm-bindgen`-based library should be colocated with the frontend.
