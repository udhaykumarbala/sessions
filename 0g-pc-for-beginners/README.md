# 0G PC for Beginners

Getting started with AI on [0G Private Computer](https://pc.0g.ai) (PC): the
basics of working with AI, then Claude Code running on PC models. This is the
companion to a weekly session series. If you missed one, everything you need
is here.

| # | Session | Date | Covers | |
|---|---------|------|--------|---|
| 1 | Getting Started | 2026-10-20 | AI basics, and Claude Code on PC from scratch | [Slides](https://slides.riches.im/2026-10-20%200G%20PC%20for%20Beginners%201/) · [Runbook](01-getting-started.md) |

## What PC gives you

- **Open models** like GLM, DeepSeek and MiMo, all through one API key
- **Privacy**, with TEE-attested providers
- **Low cost**, paid per token with no subscription

And what to expect:

- **Slower replies.** Give it fewer, clearer asks.
- **No web search.** Give it the page, or a URL to fetch.
- **More slips.** Most AI tools are tuned for Claude, so check the work.

## AI basics

1. **Pick the right tool.** A CLI agent like Claude Code works on your machine
   and has the most power. Use specialised tools when the output is visual.
2. **Give it everything.** Low entropy in, low quality out. Paste the docs,
   PDFs, Slack threads and examples you liked. Don't tidy it.
3. **One chunk at a time.** Ask for one piece, review it, then move on.
4. **Never accept a miss.** Every edit you make by hand is a rule you haven't
   written. Say what you wanted, and ask it to save that as a rule.
5. **Write rules that work.** Show it the target with an example, and say why.
   A list of "don't"s says what's wrong, not what good looks like.

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

Then paste this whole block in the same window. It writes your settings and
keeps a backup of any you already had.

```sh
mkdir -p ~/.claude
[ -f ~/.claude/settings.json ] && cp ~/.claude/settings.json ~/.claude/settings.bak
cat > ~/.claude/settings.json <<EOF
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://router-api.0g.ai",
    "ANTHROPIC_AUTH_TOKEN": "$KEY",
    "ANTHROPIC_API_KEY": "",
    "ANTHROPIC_MODEL": "glm-5.3",
    "ANTHROPIC_DEFAULT_FABLE_MODEL": "glm-5.3",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "glm-5.3",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "0gm-1.0-35b-a3b",
    "CLAUDE_CODE_MAX_CONTEXT_TOKENS": "983616"
  },
  "modelOverrides": {
    "claude-sonnet-5": "0gm-1.0-35b-a3b"
  },
  "permissions": {
    "defaultMode": "auto",
    "deny": ["WebSearch"]
  }
}
EOF
unset KEY
```

This sends requests to PC with your key, uses `glm-5.3` as the main model and
the cheap `0gm-1.0-35b-a3b` for background jobs, and starts in auto mode so
you aren't asked to approve every step. Web search is off because it doesn't
work through PC and can make results up.

> [!WARNING]
> Already use Claude Code with a Claude subscription? This switches it over to
> PC. Ask Will about running both side by side instead.

### 4. Make your workspace and start

```sh
mkdir -p ~/AI && cd ~/AI && claude
```

The first time, pick a colour theme and confirm you trust the folder. Then say
hi and ask which model it is. It should say GLM.

Next time, open Terminal and run `cd ~/AI && claude`.

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

Switch any time with `/model` followed by the name, e.g.
`/model glm-5.3-flash`.

| Model | Use it for | Context | Images in | $ per M tokens, in / out |
|-------|------------|---------|-----------|--------------------------|
| `glm-5.3` | Default for most work | 1.2M | No | 1.40 / 4.40 |
| `glm-5.3-flash` | Quick, simple jobs | 1.2M | No | 0.14 / 0.50 |
| `deepseek-v4-pro` | A second opinion on hard problems | 1.4M | No | 0.79 / 2.38 |
| `mimo-v2.6-pro` | Screenshots and images | 1M | Yes | 0.63 / 1.25 |

The GLM and DeepSeek models can't see images. Pasting a screenshot into one
fails with `Model only supports text input`, so switch to `mimo-v2.6-pro`
first.

Image and video models can't run Claude Code, but Claude Code can call them
for you through the PC API.

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
