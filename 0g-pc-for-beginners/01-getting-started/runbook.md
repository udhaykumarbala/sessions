# 1. Getting Started: runbook

Presenter notes for the live part of session 1: setting up Claude Code on PC
from scratch. The steps follow the [attendee guide](README.md).

The demo runs in a sandbox shell whose `HOME` is `~/DemoHome`. Nothing from
`~/.claude`, `~/.zshrc` or `~/.local/bin` is visible there, so `claude` is
genuinely not installed and every path on screen (`~/.claude-0g/settings.json`,
`~/projects`) matches what colleagues will type. Your real setup is untouched and
other sessions keep running.

The guide sets up a separate `claude-0g` command, the same shape as your own
setup. Nothing uses plain `claude` in the sandbox, but the split keeps it free
for a Claude subscription.

Two things don't carry into the sandbox: the macOS keychain (so the key lives
in `settings.json`, the same as the 0G docs) and your `gh` login.

A different `HOME` alone isn't enough. The model sees `/Users/will/DemoHome`,
guesses that `~` means `/Users/will`, and reads or edits your real
`~/.claude-0g` and `~/.claude/CLAUDE.md`. So the demo shell also runs under
`sandbox-exec`, which blocks everything under `/Users/will` except `DemoHome`.
A wrong guess then fails harmlessly instead of touching your config.

Every run, rehearsal included, uses your usual PC account with fresh keys,
revoked straight after:

| Key | Where | Fields |
|-----|-------|--------|
| API key | Dashboard → API Keys → Create | Label `demo-ai-session-2026-10-20`, trust mode Private |
| Management key, only for the optional status line step | Settings → Management Keys | Name `demo-ai-session-2026-10-20-balance`, preset Custom, scope `account:read` only |

For a rehearsal, swap in that day's date.

## Before: prep and rehearse (15 min)

1. Make the sandbox. Homebrew goes on `PATH` so `gh` exists, like a typical
   Mac. The sandbox profile lives outside `DemoHome`, so a reset keeps it:

   ```sh
   mkdir -p ~/DemoHome && touch ~/DemoHome/.zshrc
   echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' > ~/DemoHome/.zprofile
   cat > ~/demo-sandbox.sb <<'EOF'
   (version 1)
   (allow default)
   (deny file-read* file-write* (subpath "/Users/will"))
   (allow file-read* file-write* (subpath "/Users/will/DemoHome"))
   (allow file-read-metadata (literal "/Users/will"))
   EOF
   ```

2. Open it (save this as a Terminal profile or alias, you'll run it twice).
   The `cd` comes first because a shell started outside `DemoHome` can't read
   its own working folder once sandboxed:

   ```sh
   cd ~/DemoHome && sandbox-exec -f ~/demo-sandbox.sb env -i HOME="$HOME/DemoHome" USER="$USER" LOGNAME="$USER" TERM="$TERM" LANG=en_GB.UTF-8 SHELL=/bin/zsh zsh -l
   ```

   Check it: `cat /Users/will/.claude-0g/settings.json` should fail with
   `Operation not permitted`.

   Inside it, set a git identity so commits don't stall on stage:

   ```sh
   git config --global user.name "Will Riches"
   git config --global user.email wriches@gmail.com
   ```

3. Rehearse the attendee guide's steps 1 to 6, with a throwaway key. The
   install and a headless call through PC were tested in the same kind of
   sandbox and both worked, and the guide's settings block loads without
   warnings with `glm-5.3-flashx` as the default. The interactive first-run
   screens weren't tested, so watch for what appears between `claude-0g` and
   the prompt: theme picker, folder trust, maybe an auto mode notice about
   classifier billing through `router-api.0g.ai` (harmless). Also open
   `/model` and check the nine PC models are listed.
4. Reset: `rm -rf ~/DemoHome`, then repeat 1 and 2. Delete the rehearsal key
   on pc.0g.ai.
5. Push this repo and the deck. The deck's QR code points at this session's
   folder.
6. Check everyone has a PC account with balance before the session, or guide step 2
   stalls for half the room.

## Live (about 25 min)

Screen: the sandbox Terminal on one side, Chrome on the attendee guide on the
other.

1. Open with `claude` → `command not found`. This is a Mac with nothing
   installed.
2. Work through the attendee guide, steps 1 to 8, copying each block from it.
   Use the key fields above in steps 2 and 5. Worth saying along the way:
   - Step 3: read out the "what each part does" list. `claude-0g` sits
     alongside plain `claude`, which is how you run it.
   - Step 4: ask which model it is, then open `/model`. GLM 5.3 FlashX on PC,
     not Claude, and nine models on one key.
   - Step 5: Claude Code changing its own setup, and how much context the
     prompt gives it. Expect a permission prompt when it edits
     `~/.claude-0g/settings.json`.
   - Step 6: paste a real bio, lots of it. When it gets something wrong,
     check the rule it saves is an example, not a "don't".
3. Show the second lever: `/effort max` on a tricky question, then
   `/effort low`. More thinking is slower and costs more tokens, but makes
   fewer mistakes.
4. Switch to your own setup: `~/Repos` and its `CLAUDE.md` map, `dotfiles` and
   `install.sh`, `claude-0g`. Same shape, grown over time.

Things to avoid on screen:

- Don't `cat ~/.claude-0g/settings.json`. The key is in it.
- Don't paste a screenshot after switching to a text-only model (`glm-5.3`,
  the DeepSeeks, Hunyuan). It fails with
  `API Error: 400 Model only supports text input`. The default,
  `glm-5.3-flashx`, reads images fine.
- Don't open `/usage` to show spend. Its cost is wrong for PC models (a
  one-line hello showed $0.09). Point at the status line balance instead.

If things go wrong:

- Slow replies: keep the ask small. FlashX was among the fastest models
  tested, so switching rarely helps.
- GitHub sign-in drags: skip guide step 8 and show a push from your own setup.
  Without the keychain, `gh` stores its token in plain text under
  `~/DemoHome/.config/gh`, which teardown deletes.

## After

- On pc.0g.ai, revoke the `demo-ai-session-2026-10-20` API key and delete the
  `demo-ai-session-2026-10-20-balance` management key. Both were on screen
  when created.
- `rm -rf ~/DemoHome`
- If you did guide step 8: `gh repo delete wriches/team-intro` from your normal
  shell.
