# Changelog — panel-review for OpenAI Codex


## 0.18.7
- Custom floor classes: adds the `define-custom-floors` skill (repo-committed
  `.truverifai/risk.json` authoring). Gate enforcement ships via the npm
  `@truverifai/init` vendored gates (cli_vendor 0.19.39).

## 0.18.6

- **Server identity is now declared properly instead of inferred.** The plugin
  declared no icon, so a client that wanted one had to scrape the `homepage`
  page for its icon links — and that page, `truverif.ai/mcp`, now 308-redirects
  and served a stub with no icon links. `homepage` now points at the canonical
  `truverif.ai/panel-review`, and the MCP server declares its icons and website
  URL directly in the initialize handshake (`serverInfo.icons` / `websiteUrl`,
  per the MCP spec).
- **Whether that is visible anywhere is client-side and mostly not implemented
  yet** — most MCP clients do not render icons for third-party servers. This
  release makes the metadata correct so it renders wherever support lands; it
  does not by itself put a mark on your screen. No behaviour change to the
  gates or the tools.


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
