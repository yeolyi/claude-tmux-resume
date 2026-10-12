<div align="center">

# claude-tmux-resume

Bring your Claude Code sessions back after a reboot.

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Claude Code plugin](https://img.shields.io/badge/Claude_Code-plugin-d97757.svg)](https://docs.claude.com/en/docs/claude-code/plugins)
[![tmux-resurrect](https://img.shields.io/badge/works_with-tmux--resurrect-1bb91f.svg)](https://github.com/tmux-plugins/tmux-resurrect)

</div>

[tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect) restores your windows and panes, but each pane comes back as an empty shell. This plugin remembers which Claude Code session was running in each pane and runs `claude --resume <id>` there on restore.

## Install

Clone it and load it as a plugin:

```sh
git clone https://github.com/yeolyi/claude-tmux-resume ~/claude-tmux-resume
claude --plugin-dir ~/claude-tmux-resume
```

Add two hooks to `tmux.conf`:

```tmux
set -g @resurrect-hook-post-save-all '$HOME/claude-tmux-resume/bin/save'
set -g @resurrect-hook-post-restore-all '$HOME/claude-tmux-resume/bin/restore'
```

[tmux-continuum](https://github.com/tmux-plugins/tmux-continuum) with `@continuum-restore 'on'` makes the whole thing automatic.

## How it works

- `SessionStart` hook records the session ID, `CLAUDE_CONFIG_DIR` and launch flags per pane, including after `/clear` and `/resume`
- On save, records are keyed by `session:window.pane`, since pane IDs change across tmux restarts
- On restore, each pane gets the same flags plus `--resume <id>`, without the initial prompt or `-c`/`-p`

## Limitations

- Only the conversation comes back. Background tasks started by the session don't.
- Flags are read from `ps`, so values containing spaces get split.
- Panes opened after the last save are not restored.

## License

MIT
