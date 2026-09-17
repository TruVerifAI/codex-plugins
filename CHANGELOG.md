# Changelog — panel-review for OpenAI Codex

## 0.18.13
- Skills copy sync with gate core 0.19.46: the commit gate now also prints
  `target_hunk_hashes` (A5), so the skip-gate and audit skills no longer
  describe it as write-gate-only; matching updates in the deliberate and
  synthesize skills and the reason-codes reference.

## 0.18.11
- **FIX (dangling MCP config):** the plugin manifest declares
  `"mcpServers": "./.mcp.json"`, but the `.mcp.json` file itself was missing
  from the published plugin (a repo-side ignore rule silently excluded it from
  every prior publish). The file now ships: TruVerifAI HTTP server config with
  `${TVAI_API_KEY}` Bearer interpolation — no embedded secrets.

## 0.18.10
- `define-custom-floors` skill: explicit `(^|/)` path-anchor rule (a bare `^file$`
  matches only a root-level file) + resolve every `floors check` advisory before
  showing the user. (The write-gate `^file$` fix itself ships via the npm gate
  update — this bundle's gates come from `npx @truverifai/init`.)

## 0.18.9
- `define-custom-floors` skill: workflow now leads with the whole-codebase scan
  and presents the full candidate floor list up front (interview moved to a
  refine step); every floor ships thorough code-derived keywords, and path floors
  propose `exclude_paths` for test/example subtrees by default.

## 0.18.8
- `define-custom-floors` skill: scans the whole codebase and proposes a broader
  first draft on its own initiative (thoroughness over speed).


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
