# 1. Getting Started: runbook

Presenter notes for the live part of session 1: setting up Claude Code on PC
from scratch. The steps follow the [attendee guide](README.md).

The demo runs in a sandbox shell whose `HOME` is `~/DemoHome`. Nothing from
`~/.claude`, `~/.zshrc` or `~/.local/bin` is visible there, so `claude` is
genuinely not installed and every path on screen (`~/.claude/settings.json`,
`~/AI`) matches what colleagues will type. Your real setup is untouched and
other sessions keep running.

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
   screens weren't tested, so watch for what appears between `claude` and the
   prompt: theme picker, folder trust, maybe an auto mode notice about
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
| 3 | Guide step 3: the `read` line, paste the key, then the settings block | Where to send requests, your key, which models, auto mode. Web search is off because PC can't do it and GLM will invent results |
| 4 | `mkdir -p ~/AI && cd ~/AI && claude`. Theme, trust. Ask `hi, which model are you?` Then open `/model` | It's GLM 5.3 FlashX on PC, not Claude. Nine PC models, one key |
| 5 | First prompt from the guide: the workspace `CLAUDE.md`. Show the file | This is the master session. It knows the map |
| 6 | Second prompt: `team-intro`. Paste a real bio, lots of it | Give it everything: dump context. It opens in Chrome when done |
| 7 | Find something you don't like in the page. Say what you want instead and ask it to save a rule. Show the rule | Never accept a miss. Check it wrote an example, not a "don't" |
| 8 | Optional: `Put the team-intro project on GitHub as a private repo`. Sign in with `! gh auth login` | Starts local, goes to GitHub when it's worth keeping |
| 8b | Optional: the balance status line, below | Claude Code can improve its own setup. Look how much context the prompt gives it |
| 9 | Switch to your own setup: `~/Repos` and its `CLAUDE.md` map, `dotfiles` and `install.sh`, `claude-0g` | Same shape, grown over time |

### 8b: PC balance in the status line

The `sk-` key can't read the balance (the router returns 403), which is why
this needs the management key. Create it in Chrome with the fields above, then
quit Claude Code (`/exit`) and store the key off screen in the sandbox shell:

```sh
read -rs "MK?Paste your 0G management key: " && echo && mkdir -p ~/.config/0g && print -r -- "$MK" > ~/.config/0g/mk && chmod 600 ~/.config/0g/mk && unset MK
```

Back in `claude`:

```text
Add my 0G Private Computer balance to the Claude Code status line, after the
model name. My management key is in ~/.config/0g/mk, never print it. Fetch
GET https://router-api.0g.ai/v1/account/balance with the header
"Authorization: Bearer <key>". It returns JSON with currency and
total_balance. A usd balance is in micro-dollars, so divide by 1,000,000 and
show it like $12.34. Cache it for a minute so the status line never waits on
the network.
```

That's the same endpoint and maths as the `dotfiles` status line, so check
what it builds against that. Not run through PC yet, so rehearse it.

Things to avoid on screen:

- Don't `cat ~/.claude/settings.json`. The key is in it.
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
