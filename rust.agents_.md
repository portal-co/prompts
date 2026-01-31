Rust is our main language of choice, used for non-web tasks for its powerful type system and performance


# Types

- Use `enum`s as much as possible; parse strings presented into rich types ASAP.
- Use `BTree` collections as they are more reliable and don't require `std`

# Platforms

- WASM does NOT mean the web; do NOT depend on `wasm-bindgen` AT ALL outside of TypeScript interop crates, which should be colocated with TypeScript packages
- Make as many crates `no_std` as possible; abstract OS interfaces into traits