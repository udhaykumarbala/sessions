# 1. Getting Started

Session 1 of [0G PC for Beginners](../). Everything you need to set up Claude
Code on [0G Private Computer](https://pc.0g.ai) (PC) models and start your
first project. The ideas behind it are in the
[slides](https://slides.riches.im/2026-10-20%200G%20PC%20for%20Beginners%201/).

## Set up Claude Code on PC

You need a Mac (on Windows, follow the
[0G docs page](https://docs.0g.ai/developer-hub/building-on-0g/compute-network/router/clients/claude-code)
instead) and a PC account with some balance. Sign in at
[pc.0g.ai](https://pc.0g.ai) and deposit 0G.

Open the Terminal app: press `⌘ Space`, type `Terminal`, press Enter.

### 1. Install Claude Code

```sh
curl -fsSL https://claude.ai/install.sh | bash
```

If it says `~/.local/bin is not in your PATH`, run the line it gives you:

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
read -rs "KEY?Paste your 0G API key, then press Enter: " && echo
```

Then pick where your settings go, and run that one line in the same window:

| | Run |
|---|---|
| **Option 1:** new to Claude Code | `DIR=~/.claude` |
| **Option 2:** already use Claude Code with a Claude subscription | `DIR=~/.claude-0g` |

Option 2 leaves your subscription setup exactly as it is. You start PC
sessions with `claude-0g` instead of `claude`, and each keeps its own settings
and history.

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
      { "model": "glm-5.3-flashx", "label": "GLM 5.3 FlashX (default)", "description": "1M context, $0.39/$1.31 per M, sees images" },
      { "model": "glm-5.3", "label": "GLM 5.3", "description": "1.2M context, $1.40/$4.40 per M, text only" },
      { "model": "deepseek-v4-flash", "label": "DeepSeek V4 Flash", "description": "1.4M context, $0.26/$0.79 per M, text only" },
      { "model": "glm-5.3-flash", "label": "GLM 5.3 Flash", "description": "1.2M context, $0.14/$0.50 per M, text only" },
      { "model": "mimo-v2.6-flash", "label": "MiMo V2.6 Flash", "description": "1M context, $0.21/$0.42 per M, sees images" },
      { "model": "mimo-v2.6-pro", "label": "MiMo V2.6 Pro", "description": "1M context, $0.63/$1.25 per M, sees images" },
      { "model": "deepseek-v4-pro", "label": "DeepSeek V4 Pro", "description": "1.4M context, $0.79/$2.38 per M, text only" },
      { "model": "hy4-preview", "label": "Hunyuan 4 Preview", "description": "1M context, $1.25/$3.75 per M, text only" },
      { "model": "step-5-preview", "label": "Step 5 Preview", "description": "1M context, $1.46/$4.17 per M, sees images" }
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

**Option 2 only:** also paste this. It adds the `claude-0g` command and shares
your personal rules (`~/.claude/CLAUDE.md`) between the two.

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
mkdir -p ~/AI && cd ~/AI && claude
```

With option 2, type `claude-0g` instead of `claude`, here and from now on.

The first time, pick a colour theme and confirm you trust the folder. Then say
hi and ask which model it is. It should say GLM.

Next time, open Terminal and run `cd ~/AI && claude` (or `claude-0g`).

`~/AI` is your workspace, with a folder per project inside it. Rules go in
plain text files called `CLAUDE.md`, which Claude Code reads every session:

```
~/.claude/CLAUDE.md      your rules, every session
~/AI/
├─ CLAUDE.md             map of your projects
└─ team-intro/
   └─ CLAUDE.md          this project's rules
```

### 5. Your first three prompts

Set up the map of your workspace:

```text
This folder is my AI workspace. Each subfolder will be a separate project.
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

### 6. Put a project on GitHub (optional)

When a project is worth keeping or sharing:

```text
Put the team-intro project on GitHub as a private repo.
```

The first time, it will help you install the GitHub CLI and ask you to sign
in. Run the sign-in yourself by typing `! gh auth login` in Claude Code.

## Models

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
| `claude --continue` | Pick up your last conversation |
| `! command` | Run a terminal command yourself, inside Claude Code |
| Drag a file in | Attach a PDF, doc or image |
| `Shift Tab` | Change how much it asks before acting |

Your real spend is on [pc.0g.ai](https://pc.0g.ai). Claude Code doesn't know
PC prices, so ignore the cost it shows.
