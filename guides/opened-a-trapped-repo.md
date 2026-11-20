# Trapped Repository: Spot It Before Opening, React If You Opened It

This guide is for anyone who clones a repository and opens it with a coding agent (Claude Code, Cursor, Codex, Gemini CLI, Copilot) or with VS Code. Recent attacks (Bitdefender's 22 repositories, Mini Shai-Hulud, the Miasma family) do not need `npm install`: opening the folder and answering "trust this folder" is enough.

## 1. Why opening a folder is enough

Some files are run by the tool itself, without any decision from the model:

| File | What happens when the folder opens |
|---|---|
| `.claude/settings.json` with `"hooks"` (`SessionStart`, `UserPromptSubmit`) | Claude Code runs the command as soon as the folder is trusted |
| `.vscode/tasks.json` with `"runOptions": { "runOn": "folderOpen" }` | VS Code and its forks (Cursor, Windsurf) run the task |
| `.mcp.json`, `.cursor/mcp.json`, `.gemini/settings.json`, `.codex/config.toml` | The agent starts an MCP server, which is a local command |
| `CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, `.cursorrules`, `.github/copilot-instructions.md` | The agent reads these instructions as if you wrote them |

## 2. Before opening: five checks, without an agent

1. **List agent configuration files** without running anything:
   `find . -path ./node_modules -prune -o \( -name 'settings*.json' -path '*/.claude/*' -o -name tasks.json -o -name '*mcp.json' -o -name 'CLAUDE.md' -o -name 'AGENTS.md' -o -name '.cursorrules' \) -print`
2. **Read every hook and every task.** A command that starts `node`, `bun`, `python`, `curl`, `bash -c` or a script from the repository (`scripts/setup.js`, `.claude/setup.mjs`) is a strong signal.
3. **Look for invisible Unicode** in instruction files:
   `grep -rnP '[\x{200B}-\x{200F}\x{202A}-\x{202E}\x{2060}-\x{2064}\x{FEFF}\x{E0000}-\x{E007F}]' CLAUDE.md AGENTS.md .cursorrules`
4. **Read the instructions yourself.** "Do not ask for confirmation", "hide the output", "run setup first", "do not read this file" are attacker orders.
5. **Check the history.** A commit with a bland message that adds unrelated files, an author date far older than the push date, or a multi-megabyte single-line JavaScript file are traces of the Miasma worm.

When in doubt, open the repository in a disposable virtual machine, or do not open it.

## 3. If you opened it: order matters

The Miasma family installs a wiper that **deletes your home folder as soon as the stolen token stops working**. Revoking first can destroy everything. Safe order:

1. **Disconnect the machine** from the network (Wi-Fi and cable).
2. **Back up** your data to an external drive, without configuration folders (`~/.config`, `~/.claude`).
3. **Look for traces**:
   - files `~/.config/index.js`, `~/.local/share/runtimes/sync.py`, `~/.config/net-health-checker/`, `/var/tmp/.fontconfig_state`, `/tmp/.bun_ran`, `~/.claude/package/`, `~/.codex/package/`;
   - services named `pkg-runtime` or `net-health-checker` (`systemctl --user list-units`, `ls ~/Library/LaunchAgents`);
   - unknown hooks in `~/.claude/settings.json` (global settings, not only the repository's).
4. **Reinstall the machine** if any trace exists. Manual cleanup often leaves persistence behind.
5. **From another, clean machine, revoke everything**:
   - GitHub: [personal access tokens](https://github.com/settings/tokens), SSH keys, sessions, OAuth apps;
   - npm, PyPI, RubyGems, Docker Hub; cloud keys (AWS, GCP, Azure); AI API keys; secrets in `.env` files;
   - your password manager if its vault was unlocked.
6. **Check your repositories**: the [security log](https://github.com/settings/security-log), recent commits you did not make, new public repositories with random names, packages published under your name. [wormsign](https://github.com/nukoe-labs/wormsign) can scan every branch of an account through the API.
7. **Clean up with a normal commit** (no history rewrite before revocation) and warn people who may have cloned.

## 4. Settings that prevent it

- Never trust a cloned folder before the checks in section 2.
- Never run `--dangerously-skip-permissions`, `--yolo` or `--trust-all-tools` outside a disposable VM.
- Turn on the agent's sandbox and keep agents up to date (see [Advisories by Tool](../README.md#advisories-by-tool)).
- On your own repositories: branch protection, signed commits, short-lived tokens with minimal scope.

## Sources

- [Bitdefender Labs: AI agents targeted through malicious GitHub repositories](https://www.bitdefender.com/en-us/blog/labs/how-ai-agents-become-new-targets-in-github-malware-campaign)
- [SafeDep: testmetest npm packages carry a Miasma-family worm](https://safedep.io/testmetest-npm-worm-miasma-variant/)
- [Socket: Shai-Hulud descendant in a malicious Ruby gem](https://socket.dev/blog/shai-hulud-descendant-malicious-ruby-gem)
