# Changelog — panel-review for OpenAI Codex

## 0.18.0 (first Codex release)

- Initial release of the TruVerifAI panel-review plugin for Codex CLI /
  ChatGPT desktop, generated from the shared cross-platform gate core.
- Write gate (`apply_patch`/Edit/Write) + commit gate (`git commit`/`merge`)
  with the same classifier, floor classes, and receipt/coverage protocol as
  the Claude Code plugin v0.18.x.
- Codex's `apply_patch` envelope is converted locally to a unified diff for
  classification; an unparseable envelope fails OPEN (allow).
- Known limitations / enforcement gaps (documented per audit mcp_653e9bc4):
  - Codex hooks are beta upstream; `${CODEX_PLUGIN_ROOT}` resolution and the
    `.mcp.json` `${TVAI_API_KEY}` substitution are verified per Codex release
    by `tvai doctor` — if either is unsupported on your build, the installer
    falls back to absolute paths / env injection. A wrong hook path fails
    OPEN (command not found -> allow); `tvai doctor`'s synthetic-fire check
    and the gate-health panel (reviews landing but no gate fires) are the
    detection paths.
  - An UNPARSEABLE `apply_patch` envelope fails OPEN with a loud, greppable
    `TVAI_APPLY_PATCH_UNPARSEABLE` stderr signal. Threat model: the envelope
    is host-authored; adversarially-shaped envelope content reaching this
    path is an accepted v1 boundary. A fail-closed-on-parse-failure config
    flag is planned once telemetry shows the real-world parse-failure rate.
