- NO `unsafe` unless directed and required
- Make all crates, whenever possible, `forbid(unsafe_code)` and `no_std`. If a `struct` implements `Default` or has a clear constructor, make it `non_exhaustive` if it may need extensions
- Do NOT use `wasm_bindgen` for WASM unless either it is already there or required (Web APIs). For web support ensure it is in a seperate crate, with a `WasmTarget` trait or similar to target wasm_bindgen or another WASM environment (You can use a feature, but clearly document it as a target instead of an actual feature)
- Use context parameters whenever you take in multiple `FnMut`s or similar traits to allow data reuse between them
```rust
fn do_thing<Ctx>(context: &mut Ctx, callback_1: &mut (dyn FnMut(&mut Ctx, Thing1) + '_), callback_2: &mut (dyn FnMut(&mut Ctx, Thing2) + '_)){
    <<code>>
}
```
- Take and support `dyn Trait` when possible