# 1. Getting Started

Session 1 of [0G PC for Beginners](../). Everything you need to set up Claude
Code on [0G Private Computer](https://pc.0g.ai) (PC) models and start your
first project. The ideas behind it are in the
[slides](https://slides.riches.im/2026-10-20%200G%20PC%20for%20Beginners%201/).

## Set up Claude Code on PC

You need a Mac or a Linux machine (on Windows, follow the
[0G docs page](https://docs.0g.ai/developer-hub/building-on-0g/compute-network/router/clients/claude-code)
instead) and a PC account with some balance. Sign in at
[pc.0g.ai](https://pc.0g.ai) and deposit 0G.

Open a terminal. On a Mac, press `⌘ Space`, type `Terminal`, press Enter.

### 1. Install Claude Code

```sh
curl -fsSL https://claude.ai/install.sh | bash
```

If it says `~/.local/bin is not in your PATH`, run the line it prints. On a
Mac it looks like this:

```sh
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc
```

Check it worked with `claude --version`.

### 2. Create an API key

On [pc.0g.ai](https://pc.0g.ai), go to **Dashboard → API Keys → Create**.
Name it after the machine, like `claude-code-macbook`. Pick trust mode
**Private** if you want every request to run inside a TEE.

Copy the key. It starts with `sk-` and is only shown once.

### 3. Point Claude Code at PC

Run this, paste your key and press Enter. Nothing shows while you paste, so
the key stays off your screen.

```sh
printf "Paste your 0G API key, then press Enter: "; read -rs KEY; echo
```

Then pick where your settings go, and run that one line in the same window:

| | Run |
|---|---|
| **Option 1 (recommended):** a separate `claude-0g` command for PC | `DIR=~/.claude-0g` |
| **Option 2:** make plain `claude` use PC | `DIR=~/.claude` |

Option 1 keeps PC apart from plain `claude`, so a Claude subscription, now or
later, keeps working alongside it. You start PC sessions with `claude-0g`, and
each keeps its own settings and history. Option 2 switches `claude` itself
over to PC, so don't pick it if you already use Claude Code with a
subscription.

Now paste this whole block in the same window. It writes your settings and
keeps a backup of any you already had.

```sh
mkdir -p "$DIR"
[ -f "$DIR/settings.json" ] && cp "$DIR/settings.json" "$DIR/settings.bak"
cat > "$DIR/settings.json" <<EOF
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://router-api.0g.ai",
    "ANTHROPIC_AUTH_TOKEN": "$KEY",
    "ANTHROPIC_API_KEY": "",
    "ANTHROPIC_MODEL": "glm-5.3-flashx",
    "ANTHROPIC_DEFAULT_FABLE_MODEL": "glm-5.3",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "glm-5.3",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "0gm-1.0-35b-a3b",
    "CLAUDE_CODE_MAX_CONTEXT_TOKENS": "983616"
  },
  "modelOverrides": {
    "claude-sonnet-5": "0gm-1.0-35b-a3b"
  },
  "modelPicker": {
    "replaceBuiltInOptions": true,
    "options": [
      { "model": "glm-5.3-flashx", "label": "0G: GLM 5.3 FlashX (default)", "description": "1M context, $0.39/$1.31 per M, sees images" },
      { "model": "glm-5.3", "label": "0G: GLM 5.3", "description": "1.2M context, $1.40/$4.40 per M, text only" },
      { "model": "deepseek-v4-flash", "label": "0G: DeepSeek V4 Flash", "description": "1.4M context, $0.26/$0.79 per M, text only" },
      { "model": "glm-5.3-flash", "label": "0G: GLM 5.3 Flash", "description": "1.2M context, $0.14/$0.50 per M, text only" },
      { "model": "mimo-v2.6-flash", "label": "0G: MiMo V2.6 Flash", "description": "1M context, $0.21/$0.42 per M, sees images" },
      { "model": "mimo-v2.6-pro", "label": "0G: MiMo V2.6 Pro", "description": "1M context, $0.63/$1.25 per M, sees images" },
      { "model": "deepseek-v4-pro", "label": "0G: DeepSeek V4 Pro", "description": "1.4M context, $0.79/$2.38 per M, text only" },
      { "model": "hy4-preview", "label": "0G: Hunyuan 4 Preview", "description": "1M context, $1.25/$3.75 per M, text only" },
      { "model": "step-5-preview", "label": "0G: Step 5 Preview", "description": "1M context, $1.46/$4.17 per M, sees images" }
    ]
  },
  "permissions": {
    "defaultMode": "auto",
    "deny": ["WebSearch"]
  }
}
EOF
unset KEY
```

Claude Code now runs on `glm-5.3-flashx`, lists the PC models in `/model`, and
won't ask before every step (auto mode). Web search is off because it doesn't
work through PC and makes up results.

**Option 1 only:** also paste this. It adds the `claude-0g` command and shares
your personal rules (`~/.claude/CLAUDE.md`) with plain `claude`.

```sh
mkdir -p ~/.local/bin
cat > ~/.local/bin/claude-0g <<'EOF'
#!/bin/sh
# Claude Code on 0G PC, with its own settings and history in ~/.claude-0g
export CLAUDE_CONFIG_DIR="$HOME/.claude-0g"
exec claude "$@"
EOF
chmod +x ~/.local/bin/claude-0g
mkdir -p ~/.claude && touch ~/.claude/CLAUDE.md && ln -sf ~/.claude/CLAUDE.md ~/.claude-0g/CLAUDE.md
```

If `claude-0g` isn't found later, run the `PATH` line from step 1.

### 4. Make your workspace and start

```sh
mkdir -p ~/projects && cd ~/projects
```

Then start Claude Code: type `claude-0g` if you chose option 1, or `claude` if
you chose option 2. Use the same command from now on.

The first time, pick a colour theme and confirm you trust the folder. Then say
hi and ask which model it is. It should say GLM.

Next time, open a terminal and run `cd ~/projects && claude-0g` (or `claude`
with option 2).

`~/projects` is your workspace, with a folder per project inside it. Rules go in
plain text files called `CLAUDE.md`, which Claude Code reads every session:

```
~/.claude/CLAUDE.md      your rules, every session
~/projects/
├─ CLAUDE.md             map of your projects
└─ team-intro/
   └─ CLAUDE.md          this project's rules
```

### 5. Your first three prompts

Set up the map of your workspace:

```text
This folder is my workspace. Each subfolder will be a separate project.
Create a CLAUDE.md here that says so and keeps a one-line list of the
projects. Update it whenever we add one.
```

Start a project. Give it as much as you can: paste text, drag in files and
screenshots.

```text
Create a project folder called team-intro and build a one-page website
introducing me to the team. Here's my background: [paste a bio, your
LinkedIn about section, anything]. Open it in Chrome when it's done.
```

When it gets something wrong, fix it for good:

```text
[Say what you wanted instead, and why.] Add that to my personal rules in
~/.claude/CLAUDE.md with a short example.
```

That path works with `claude-0g` too, because option 1 links its rules to the
same file.

### 6. Put a project on GitHub (optional)

When a project is worth keeping or sharing:

```text
Put the team-intro project on GitHub as a private repo.
```

The first time, it will help you install the GitHub CLI and ask you to sign
in. Run the sign-in yourself by typing `! gh auth login` in Claude Code.

### 7. Show your PC balance in the status line (optional)

Here you get Claude Code to change its own setup. Reading your balance needs a
separate management key, because API keys can't see it. On
[pc.0g.ai](https://pc.0g.ai), go to **Settings → Management Keys → Create**,
pick **Custom** and tick only `account:read`. Name it like
`claude-code-balance`.

Back in your terminal (outside Claude Code), run this and paste the key. It's
saved where only you can read it.

```sh
printf "Paste your 0G management key, then press Enter: "; read -rs MK; echo; mkdir -p ~/.config/0g && (umask 077; printf '%s\n' "$MK" > ~/.config/0g/mk); unset MK
```

Then start Claude Code and give it this:

```text
Show my 0G PC balance in your status line, next to the model name. Here's how
status lines work: https://code.claude.com/docs/en/statusline. My settings are
in $CLAUDE_CONFIG_DIR/settings.json, or ~/.claude/settings.json if that isn't
set. Get the balance from https://router-api.0g.ai/v1/account/balance using
the key in ~/.config/0g/mk as a Bearer token, and never print the key.
total_balance is in micro-dollars, so show it like $12.34, and refresh it in
the background at most once a minute.
```

It will ask before changing its own settings. Say yes, and the balance shows
up below the prompt.

## Models and effort

Two settings change what you get. `/model` picks the model, and `/effort`
sets how long it thinks before answering: `/effort low`, `medium`, `high`,
`xhigh` or `max`. More effort is slower and uses more output tokens, but makes
fewer mistakes on tricky problems. In our tests, FlashX on `low` got a
counting puzzle wrong one time in two, and on `max` it got it right both
times, using two to three times the tokens. Leave effort alone for most work,
raise it for hard problems and lower it for quick jobs.

Type `/model` to pick from the nine PC models in your menu. Start with these:

| Model | Use it for | Context | Images in | $ per M tokens, in / out |
|-------|------------|---------|-----------|--------------------------|
| `glm-5.3-flashx` | Default for everything, screenshots included | 1M | Yes | 0.39 / 1.31 |
| `glm-5.3` | Long, hard jobs | 1.2M | No | 1.40 / 4.40 |
| `deepseek-v4-flash` | The cheapest that's still reliable | 1.4M | No | 0.26 / 0.79 |

A text-only model can't see screenshots. Pasting one in fails with
`Model only supports text input`, so switch back to `glm-5.3-flashx` first.

Claude models are on PC too, at 5 to 15 times the price, so they're left out
of the menu. Image and video models can't run Claude Code, but Claude Code can
call them for you through the PC API.

## Everyday basics

| | |
|---|---|
| `Esc` | Stop it mid-task |
| `/clear` | Start fresh when you move to a new task |
| `claude-0g --continue` | Pick up your last conversation in this folder |
| `claude-0g --resume` | Choose an older conversation to pick up |
| `! command` | Run a terminal command yourself, inside Claude Code |
| Drag a file in | Attach a PDF, doc or image |
| `Shift Tab` | Change how much it asks before acting |

With option 2, use `claude` instead of `claude-0g`.

Your real spend is on [pc.0g.ai](https://pc.0g.ai). Claude Code doesn't know
PC prices, so ignore the cost it shows.
