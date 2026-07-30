# TruVerifAI panel-review for OpenAI Codex

> ⚙️ **This repository is MACHINE-WRITTEN.** It is generated and force-pushed by
> CI from the TruVerifAI product repo (`plugin-core/`). Hand edits are
> overwritten on the next release. To contribute or file issues, use the issue
> tracker here — but code changes happen upstream.

Multi-model second-opinion review for high-stakes code changes, with
**proactive review gates**: a local classifier (running entirely on your
machine) watches risky writes and `git commit`s, and blocks them with a routing
message until a four-frontier-model review covers the change.

## Install

```
codex plugin marketplace add TruVerifAI/codex-plugins
codex plugin install panel-review@truverifai
```

Then connect your account — the recommended path is the installer, which also
verifies the gates actually fire end-to-end:

```
npx @truverifai/init
```

Or manually: generate a `tvai_…` key at https://truverif.ai/settings/api-keys
and export it as `TVAI_API_KEY` (or run `tvai login`, which stores it in
`~/.truverifai/config.json`).

## What ships

- **MCP server connection** — `audit_coding`, `deliberate_coding`,
  `synthesize_coding`, `confirm_floor`, `record_outcome`, `record_gate_skip`
  (plus the financial profile).
- **Review gates** (PreToolUse hooks, beta on Codex): the **write gate** fires
  before a risky `apply_patch`/edit, the **commit gate** before a risky
  `git commit`. Both fail OPEN on any error — the gate never traps the agent.
- **Skills** — when to audit, when to deliberate, when a fast synthesize is
  enough, outcome reporting, and gate-skip etiquette.

## Privacy

The gates send TruVerifAI only a **repo fingerprint + per-hunk content
hashes** — never source code, never file paths, never diffs. Reviews you invoke
explicitly (`audit_coding` etc.) receive exactly what the agent passes them.

## Notes for Codex users

- Codex hooks are **beta** upstream; if a Codex update changes hook behavior,
  the gates fail open (never closed) until we ship a compatible release.
- The gates deny via structured JSON with a routing message the model reads;
  a denied action is an instruction to run one review, not a dead end.

Docs: https://truverif.ai/settings/mcp · Changelog: ./CHANGELOG.md
