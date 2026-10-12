# Notchu plugin for Claude Code

Shows your Claude Code sessions in the Mac notch with [Notchu](https://github.com/729-Ventures/notchu-releases),
and lets you approve or deny Claude Code's permission requests from the notch.

The plugin adds Claude Code `http` hooks that POST each hook event to Notchu on your own Mac
(`http://127.0.0.1:47615/hooks/claude-code`). Nothing leaves your Mac. If Notchu is not running,
the request fails at once and Claude Code carries on as usual, showing its own permission prompt.

## Requirements

- Claude Code **2.1.89 or later** (`claude --version`; update with `claude update`).
- Notchu running, with **Live approvals** switched on in Settings → Agents. The **Claude Code hooks**
  section there shows the address Notchu listens on.

## Install

1. In Notchu, open Settings → Agents and click **Copy Plugin Token Line**. It copies a line like
   `export NOTCHU_HOOK_TOKEN='…'`.
2. Add that line to your shell profile (for example `~/.zshrc`) and open a new terminal.
   (Claude Code only sends the token from an environment variable, so the plugin cannot ask
   for it during setup.)
3. Install the plugin:
   ```
   /plugin marketplace add 729-Ventures/notchu-claude-plugin
   /plugin install notchu@notchu-claude-plugin
   ```
4. Restart your Claude Code sessions.

Prefer not to use a plugin? Notchu's **Copy Hooks Snippet** gives you the same hooks, with the
token included. Merge its `"hooks"` entries into the `"hooks"` section of your
`~/.claude/settings.json` yourself; don't paste it over the file, or your other settings are lost.

## What it sends

The JSON Claude Code gives every hook (session id, working directory, tool name and input), plus
these headers so the notch can point you back to the right terminal: `TERM_PROGRAM`,
`TERM_SESSION_ID`, `ITERM_SESSION_ID`, `TMUX`, `TMUX_PANE`. An unset variable is sent empty.

## Notes

- The permission-request hook has a 605-second timeout. It is longer than Notchu's longest
  approval window, so changing the window in Notchu never needs a plugin update. Every other hook
  times out after 10 seconds: Notchu answers those at once, so a stuck Notchu can never hold up
  your prompts or tools for long.
- The plugin always uses port 47615. If Notchu had to use another port (Settings → Agents shows
  it), use the copy-snippet instead.
- If you change the token in Notchu, update `NOTCHU_HOOK_TOKEN` too.

## For maintainers

This folder is the source of truth for the published `729-Ventures/notchu-claude-plugin` repo.
`hooks/hooks.json` is generated from `ClaudeHooksSnippet.pluginHooksJSON()` in the Notchu
package, and a package test fails if the two differ. Run `claude plugin validate .` before
publishing. `.claude-plugin/marketplace.json` makes the published repo its own one-plugin
marketplace (the install commands above). Licence: MIT (`LICENSE`). Bump `version` in
`.claude-plugin/plugin.json` with every published change, or `claude plugin update` keeps users on the old copy.
