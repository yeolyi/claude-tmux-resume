# claude-tmux-resume

tmux-resurrect로 tmux를 되살릴 때, pane마다 돌던 Claude Code 세션도 `--resume`으로 이어 띄운다.

## 설치

1. [tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect)를 쓰고 있어야 한다. 자동 저장·복원은 [tmux-continuum](https://github.com/tmux-plugins/tmux-continuum)을 쓰면 된다.
2. 이 저장소를 받아 Claude Code를 띄울 때 플러그인으로 넘긴다.
   ```sh
   git clone <이 저장소> ~/Developer/claude-tmux-resume
   claude --plugin-dir ~/Developer/claude-tmux-resume
   ```
3. `tmux.conf`에 resurrect 훅 두 줄을 추가한다.
   ```tmux
   set -g @resurrect-hook-post-save-all '$HOME/Developer/claude-tmux-resume/bin/save'
   set -g @resurrect-hook-post-restore-all '$HOME/Developer/claude-tmux-resume/bin/restore'
   ```

## 동작

- `SessionStart` 훅이 pane별 세션 ID, `CLAUDE_CONFIG_DIR`, 실행 인자를 `~/.local/state/claude-tmux-resume`에 적는다.
- resurrect가 저장할 때 이 기록을 `세션:창.pane` 위치 기준으로 옮겨 적는다.
- 복원할 때는 같은 인자로 `claude --resume <id>`를 입력한다. 첫 프롬프트, `--continue`, `-p`는 빼고 넘긴다.

## 한계

- 대화만 이어진다. 세션이 띄운 백그라운드 작업은 되살아나지 않는다.
- 실행 인자는 `ps`로 읽기 때문에, 공백이 든 값은 쪼개진다.
- 마지막 저장 이후에 새로 연 pane은 복원되지 않는다.
