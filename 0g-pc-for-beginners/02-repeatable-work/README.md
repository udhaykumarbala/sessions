# 2. Repeatable Work

Session 2 of [0G PC for Beginners](../). Plan before it acts, undo when it misses, and save a task you repeat as a one-word command. It assumes you did [Session 1](../01-getting-started/), so Claude Code runs on PC with `claude-0g`. The ideas behind it are in the [slides](https://claude.ai/artifact/SNto64kBmcXuSHhbfRUSgE).

## Before you start

Open a terminal and start Claude Code in your workspace:

```
cd ~/projects && claude-0g
```

Ask it which model it is. It should say GLM. If `claude-0g` isn't found, run the `PATH` line from step 1 of Session 1. Check you have balance at [pc.0g.ai](https://pc.0g.ai).

## Plan before it acts

### 1. Ask for the plan, then the work

For anything bigger than a small fix, ask for a plan first. Type `/plan` and then your request, or press `Shift Tab` until it shows plan mode. Claude reads what it needs and proposes steps, but changes nothing until you approve.

Try it on the `team-intro` site from Session 1:

```
/plan Add a "What I'm working on" section to team-intro with three
short items about my current work. Match the existing style.
```

Read the plan before you approve it. If a step is wrong, say why and ask for a new plan. Fixing a plan takes seconds; fixing the result takes much longer.

## Undo when it misses

### 2. Rewind to a checkpoint

Claude Code saves a checkpoint every time you send a message, so when a result is wrong you can roll back instead of fixing it by hand. Type `/rewind` or press `Esc` twice, then pick the message to go back to. You can restore the files, the conversation, or both.

Use it right after the plan above: if the new section isn't right, rewind to the message before the miss and ask again more clearly. Each correction you add on top of a miss keeps the miss in the chat; rewinding drops it.

Checkpoints cover the file edits Claude makes. Things it did through terminal commands, like pushing to GitHub, aren't undone.

### 3. Turn the miss into a rule

This is the Session 1 habit, so just a reminder. Say what you wanted and why, show the target, and ask it to save that as a rule:

```
The section reads like marketing. I want three plain items a teammate
can skim in five seconds. Add that to my personal rules in
~/.claude/CLAUDE.md with a short example.
```

Personal rules in `~/.claude/CLAUDE.md` apply to every project. A rule for one project goes in that project's own `CLAUDE.md`. Keep rules few and short, about things it can't guess, like tone or format, and read what it saved.

## Give it context on PC

### 4. Bring the context to it

PC models can't search the web, so the quality of the answer depends on what you hand it. Four ways:

- **A sources folder.** Make a `sources/` folder in the project and drop in what it should read: past posts, release notes, a Slack thread saved as a text file. Then start your ask with "Read everything in sources/ first."
- **A link.** Paste a URL and ask it to fetch that page. It can read a page you give it, even without search.
- **Files and screenshots.** Drag them into the terminal. Use `glm-5.3-flashx` for screenshots, since some models can't see images.
- **A command's output.** Start a message with `!` to run a command and show Claude the result, like `! ls sources`. Never do this with a key.

## Save what works

### 5. Turn a repeated task into a skill

If you've written the same brief three times, save it as a skill: a short instruction file you run with a slash command, like `/tweet`. Claude can also use it on its own when your request matches the skill's description.

Keep skills inside the project folder, so they work the same with `claude` and `claude-0g`:

```
~/projects/launch-post/
├─ sources/              past tweets, release notes, pricing
└─ .claude/skills/tweet/
   └─ SKILL.md           the skill
```

The easy way is to ask Claude to write it right after a task goes well:

```
That tweet is what I wanted. Turn how we got there into a project
skill at .claude/skills/tweet/SKILL.md, so next time I can type
/tweet and a topic.
```

Or write `SKILL.md` yourself. The top block names the skill and says when to use it; the rest is the brief:

```
---
name: tweet
description: Draft a tweet for a 0G announcement. Use when asked for a tweet or X post.
---
Read every file in sources/ first and match the voice of the past tweets.
Put the news in the first line, because people skim on their phones.
Use the real numbers from the sources. Keep it under 280 characters.
Give three options, shortest first.
```

Then type `/tweet` and a topic. If `/tweet` doesn't show up, leave with `/exit` and start `claude-0g` again.

Read and edit the `SKILL.md` before you rely on it. Skills shaped by a person help a lot; skills a model writes on its own, unchecked, add nothing on average (see below).

## Why this works

None of these studies used PC models, so take them as the direction and rough size of the effect, not a promise.

| Habit | What the research found | Measured on | Source |
|---|---|---|---|
| Plan first | Up to 25% more coding problems solved on the first try when the model plans before it writes (best case) | Pass@1 on HumanEval and MBPP | [Jiang et al., ASE 2024](https://arxiv.org/abs/2303.06689) |
| Rewind | All 18 frontier models tested got less reliable as the input grew; once a model takes a wrong turn, it doesn't recover | Accuracy on retrieval and repeat tasks; 200k+ simulated chats | [Chroma, 2025](https://trychroma.com/research/context-rot) · [Laban et al., ICLR 2026](https://arxiv.org/abs/2505.06120) |
| Your own rules | Rules written by people improved task success by about 4%; rules the model wrote for itself made it slightly worse. Either way, about 20% more cost | Task success of coding agents on real repos | [Gloaguen et al., ICLR 2026](https://arxiv.org/abs/2602.11988) |
| Context up front | 90% average score when all details came in the first message, 65% when they came over several turns | Six tasks: code, math, SQL, API calls, summaries, table captions; 15 models | [Laban et al., ICLR 2026](https://arxiv.org/abs/2505.06120) |
| Skills | Curated skills raised the pass rate by 16 points on average; skills the model wrote alone added nothing. Short, focused skills beat long documentation | Task pass rate on 84 tasks in 11 domains, 7,308 runs | [SkillsBench, 2026](https://arxiv.org/abs/2602.12670) |

## This week

- Turn one task you do every week into a skill, and use it at least twice.
- Use `/plan` on one bigger task. If it goes wrong, `/rewind` instead of fixing it by hand.
- Post in the team channel: your skill's name, one line on what it does, and one output you liked.

## Cheat sheet

| To do this | Use |
|---|---|
| Plan before it acts | `/plan`, or `Shift Tab` to plan mode |
| Undo to an earlier point | `/rewind`, or `Esc` twice |
| Run a skill | `/` and its name, e.g. `/tweet` |
| Stop it mid-task | `Esc` |
| Start fresh on a new task | `/clear` |
| Run a command yourself | Start the message with `!` |
| Pick a model, or how long it thinks | `/model`, `/effort` |
| Pick up your last conversation | `claude-0g --continue` |
