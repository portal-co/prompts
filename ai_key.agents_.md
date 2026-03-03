# AI Key System (Summary)

The AI submission key system filters AI-generated code contributions through a multi-layer pipeline before they reach human review. It distinguishes between agent *forms* (autonomous, interactive, batch), calibrates trust based on *task open-endedness* and *interaction level*, and defends against OpenClaw-pattern supply chain attacks while permitting legitimate agents.

## Four-Layer Filter

| Layer | What it checks |
|-------|---------------|
| 1. Syntax | Key present and well-formed |
| 2. Provenance | Key traces to a known, trusted agent chain |
| 3. Behavioural heuristics | Static signals of prompt injection / exfiltration / jailbreak |
| 4. RASP | Runtime: base64 payloads, env-var probing, suspicious URLs, obfuscated commands |

On a HIGH or CRITICAL RASP alert the agent receives a structured self-assessment request (see `crates/check-ai-key/src/halt_prompt.md` in [`portal-co/tiny-rasp`](https://github.com/portal-co/tiny-rasp)) that uses Constitutional AI reasoning rather than a blunt halt command.

Note: the RASP is intentionally narrow in scope — it targets the most urgent supply chain signals with fast response, and is designed to be integrated into existing CI pipelines rather than serve as a comprehensive security solution.

**Full document:** [`portal-co/tiny-rasp` — `ai_key.agents_.md`](https://github.com/portal-co/tiny-rasp/blob/main/ai_key.agents_.md)
