# kobipana/pikit — fork notes

Personal fork of [adrianapan/pikit](https://github.com/adrianapan/pikit), used by the user's Sulaco
NixOS flake (`~/sulaco`, see `.agents/skills/sulaco-pi-coding-agent/SKILL.md` there).

## How it is consumed

- Sulaco's Home Manager pi slice (`~/sulaco/modules/home/cli/pi-coding-agent.nix`) loads
  `git:github.com/kobipana/pikit@main` with every extension except `agent/extensions/mcp`.
  Pi's built-in MCP is used instead (`~/.pi/agent/mcp.json`).
- Hosts: Nostromo (NixOS) and a MacBook. The pi theme is a generated `stylix` theme; `slop`
  is still shipped.
- Work on `main`, commit and push, then run `pi update --extensions` on each host and restart pi
  or `/reload`. A Nix rebuild does not move the checkout.
- Never edit Pi's managed checkout under `~/.pi/agent/git/github.com/kobipana/pikit/`;
  `pi update` resets it.
- Do not propose upstream PRs or issues unless the user asks.

## Changes carried on top of upstream 1.1.9

Keep these when touching the affected files:

- `agent/themes/slop.json` has no custom color keys. Pi 1.3.0 rejects themes with keys outside
  its schema, and `theme.fg()` throws on unknown tokens. Extensions default to standard Pi tokens:
  `borderMuted` for separators, `mdLink` for chat mode, `customMessageLabel` and `thinking*` for
  the footer.
- `applyColor` in `chat-input` and `footer` falls back to plain text on unknown tokens; `spinners`
  accepts hex colors.
- `subagents` passes the inherited parent model as `provider/id`. Children run with
  `--no-extensions` plus pikit's own extensions, so extension-registered providers such as
  `claude-bridge` are unavailable there; agents pin a built-in provider in frontmatter instead.
- `typebox` is a `peerDependencies` (`"*"`) entry (Pi provides it and warns about local copies).
- The `startup` dashboard counts MCP servers from `~/.pi/agent/mcp.json`, `configs/mcp.json`
  and the project's `.pi/mcp.json`.

## Checks

- `npm run check` (`tsc --noEmit`) needs `npm install`; a quick syntax check is
  `esbuild --loader=ts < file.ts`.
- Validate theme JSON with Pi's own `validateThemeJson` from
  `@earendil-works/pi-coding-agent/dist/modes/interactive/theme/theme-schema.js`.
