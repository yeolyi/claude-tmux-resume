<div align="center">

# claude-tmux-resume

재부팅 후에도 이어지는 Claude Code 세션

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Claude Code plugin](https://img.shields.io/badge/Claude_Code-plugin-d97757.svg)](https://docs.claude.com/en/docs/claude-code/plugins)
[![tmux-resurrect](https://img.shields.io/badge/works_with-tmux--resurrect-1bb91f.svg)](https://github.com/tmux-plugins/tmux-resurrect)

</div>

[tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect)는 창과 pane 배치를 되살리지만, pane 안은 빈 셸로 돌아옵니다. 이 플러그인은 pane마다 어떤 Claude Code 세션이 돌고 있었는지 기억해 두었다가, 복원할 때 그 자리에서 `claude --resume <id>`를 실행합니다.

## 설치

저장소를 받아 플러그인으로 불러옵니다.

```sh
git clone https://github.com/yeolyi/claude-tmux-resume ~/claude-tmux-resume
claude --plugin-dir ~/claude-tmux-resume
```

`tmux.conf`에 훅 두 줄을 추가합니다.

```tmux
set -g @resurrect-hook-post-save-all '$HOME/claude-tmux-resume/bin/save'
set -g @resurrect-hook-post-restore-all '$HOME/claude-tmux-resume/bin/restore'
```

[tmux-continuum](https://github.com/tmux-plugins/tmux-continuum)에 `@continuum-restore 'on'`을 켜 두면 저장과 복원이 자동으로 이뤄집니다.

## 동작

- `SessionStart` 훅이 pane별 세션 ID, `CLAUDE_CONFIG_DIR`, 실행 인자를 기록합니다. `/clear`, `/resume`으로 세션이 바뀌면 다시 기록합니다.
- 저장할 때는 기록을 `세션:창.pane` 위치 기준으로 옮깁니다. pane ID는 tmux를 다시 켜면 바뀌기 때문입니다.
- 복원할 때는 원래 인자에 `--resume <id>`를 붙여 실행합니다. 첫 프롬프트와 `-c`, `-p`는 제외합니다.

## 한계

- 대화만 복원됩니다. 세션이 띄운 백그라운드 작업은 복원되지 않습니다.
- 실행 인자를 `ps`로 읽기 때문에 공백이 든 값은 쪼개집니다.
- 마지막 저장 이후에 연 pane은 복원되지 않습니다.

## 라이선스

MIT
