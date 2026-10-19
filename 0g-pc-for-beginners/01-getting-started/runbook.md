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

Every run, rehearsal included, uses your usual PC account with fresh keys,
revoked straight after:

| Key | Where | Fields |
|-----|-------|--------|
| API key | Dashboard → API Keys → Create | Label `demo-ai-session-2026-10-20`, trust mode Private |
| Management key, only for the optional status line step | Settings → Management Keys | Name `demo-ai-session-2026-10-20-balance`, preset Custom, scope `account:read` only |

For a rehearsal, swap in that day's date.

## Before: prep and rehearse (15 min)

1. Make the sandbox. Homebrew goes on `PATH` so `gh` exists, like a typical
   Mac:

   ```sh
   mkdir -p ~/DemoHome && touch ~/DemoHome/.zshrc
   echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' > ~/DemoHome/.zprofile
   ```

2. Open it (save this as a Terminal profile or alias, you'll run it twice):

   ```sh
   env -i HOME="$HOME/DemoHome" USER="$USER" LOGNAME="$USER" TERM="$TERM" LANG=en_GB.UTF-8 SHELL=/bin/zsh zsh -l
   ```

   Inside it, set a git identity so commits don't stall on stage:

   ```sh
   git config --global user.name "Will Riches"
   git config --global user.email wriches@gmail.com
   ```

3. Rehearse steps 1 to 5 below against the attendee guide, with a throwaway key. The
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
6. Check everyone has a PC account with balance before the session, or step 2
   stalls for half the room.

## Live (about 25 min)

Screen: the sandbox Terminal on one side, Chrome on the attendee guide on the
other.

| # | Do | Say |
|---|----|-----|
| 0 | `claude` → `command not found` | This is a Mac with nothing installed |
| 1 | Copy the install line from the guide. Run the `PATH` line it prints, then `claude --version` | One command, no Node, no Homebrew |
| 2 | Chrome: pc.0g.ai → Dashboard → API Keys → Create, with the fields above | One key, every model. Shown once, so copy it now |
| 3 | Guide step 3, block by block: the key, the folders, the settings, the `claude-0g` command, the rules link. Read out the "what each part does" list after the settings | Where to send requests, your key, which models, auto mode. Web search is off because PC can't do it and GLM will invent results. `claude-0g` sits alongside plain `claude`, which stays free for a Claude subscription. That's how I run it |
| 4 | `mkdir -p ~/projects && cd ~/projects && claude-0g`. Theme, trust. Ask `hi, which model are you?` Then open `/model` | It's GLM 5.3 FlashX on PC, not Claude. Nine PC models, one key |
| 5 | First prompt from the guide: the workspace `CLAUDE.md`. Show the file | This is the master session. It knows the map |
| 6 | Second prompt: `team-intro`. Paste a real bio, lots of it | Give it everything: dump context. It opens in Chrome when done |
| 7 | Find something you don't like in the page. Say what you want instead and ask it to save a rule. Show the rule | Never accept a miss. Check it wrote an example, not a "don't" |
| 8 | Optional: `Put the team-intro project on GitHub as a private repo`. Sign in with `! gh auth login` | Starts local, goes to GitHub when it's worth keeping |
| 8b | Optional: guide step 7, the balance status line. Create the management key with the fields above, `/exit`, run the key line, back in `claude-0g`, paste the prompt | Claude Code changing its own setup. Look how much context the prompt gives it. The API key can't read the balance (403), hence the management key |
| 8c | `/effort max` on a tricky question, then `/effort low` | The second lever after the model: more thinking, slower, more tokens, fewer mistakes |
| 9 | Switch to your own setup: `~/Repos` and its `CLAUDE.md` map, `dotfiles` and `install.sh`, `claude-0g` | Same shape, grown over time |

The status line prompt hasn't been run through PC, because it's interactive
and edits settings outside the working folder. Rehearse it tonight, and check
the result against your `dotfiles` status line, which uses the same endpoint
and maths. Expect a permission prompt when it edits `~/.claude-0g/settings.json`.

Things to avoid on screen:

- Don't `cat ~/.claude-0g/settings.json`. The key is in it.
- Don't paste a screenshot after switching to a text-only model (`glm-5.3`,
  the DeepSeeks, Hunyuan). It fails with
  `API Error: 400 Model only supports text input`. The default,
  `glm-5.3-flashx`, reads images fine.
- Ignore the cost Claude Code shows. It doesn't know PC prices. A one-line
  hello showed $0.09.

If things go wrong:

- Slow replies: keep the ask small. FlashX was among the fastest models
  tested, so switching rarely helps.
- GitHub sign-in drags: skip step 8 and show a push from your own setup.
  Without the keychain, `gh` stores its token in plain text under
  `~/DemoHome/.config/gh`, which teardown deletes.

## After

- On pc.0g.ai, revoke the `demo-ai-session-2026-10-20` API key and delete the
  `demo-ai-session-2026-10-20-balance` management key. Both were on screen
  when created.
- `rm -rf ~/DemoHome`
- If you did step 8: `gh repo delete wriches/team-intro` from your normal
  shell.
