# Claude Code session management (resume deterministically)

Claude Code stores sessions on disk and supports resuming by UUID, which lets you resume without an interactive picker.

## Where sessions are stored

Sessions are stored under:

- `~/.claude/projects/<escaped-project-path>/`
- Session files are JSONL: `~/.claude/projects/<escaped-project-path>/<session-uuid>.jsonl`

The `<escaped-project-path>` is your project path with `/` replaced by `-`.

## Resume by UUID

```bash
claude --resume "<session-uuid>"
```

Or:

```bash
claude --session-id "<session-uuid>"
```

> [!NOTE]
> Use `--fork-session` if you want to resume but create a new session ID instead of reusing the original.

## List sessions for the current directory

```bash
project_dir="$HOME/.claude/projects/$(pwd | sed 's|/|-|g')"
ls -1 "${project_dir}"/*.jsonl | xargs -n1 basename -s .jsonl
```

## Get the most recent session UUID

```bash
project_dir="$HOME/.claude/projects/$(pwd | sed 's|/|-|g')"
ls -t "${project_dir}"/*.jsonl | head -1 | xargs basename -s .jsonl
```

## Convenience function (bash/zsh)

Add this to your `~/.bashrc` or `~/.zshrc`:

```bash
claude_session_id() {
  local project_dir
  project_dir="$HOME/.claude/projects/$(pwd | sed 's|/|-|g')"
  ls -t "${project_dir}"/*.jsonl | head -1 | xargs basename -s .jsonl
}
```

Then:

```bash
claude --resume "$(claude_session_id)"
```

## Save a session UUID locally (repo-safe)

> [!CAUTION]
> Session UUID files should stay local. Do not commit them.

```bash
echo "<session-uuid>" > .claude-session
claude --resume "$(cat .claude-session)"
```

If you want a belt-and-suspenders approach, add `.claude-session` to `.git/info/exclude` (local-only ignore).
