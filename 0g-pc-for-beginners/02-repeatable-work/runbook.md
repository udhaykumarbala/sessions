# 2. Repeatable Work: runbook

Presenter notes for session 2: plan before it acts, undo when it misses, and
save what works as a skill. The prompts follow the [attendee guide](README.md).
Two hosts: the driver shares their terminal, the navigator watches the chat,
keeps time and helps anyone stuck on setup.

## Before: prep (10 min)

1. Make sure `claude-0g` runs and answers as GLM. If it is missing, run the
   setup blocks from Session 1 step 3.
2. Turn off tool search for PC models. Without this, headless runs with GLM
   report "no file or shell tools" and refuse to read the project. Add it to
   the `env` block of `~/.claude-0g/settings.json`:

   ```json
   "ENABLE_TOOL_SEARCH": "0"
   ```

3. Keep personal rules short for the demo. `~/.claude-0g/CLAUDE.md` links to
   `~/.claude/CLAUDE.md`, so a long personal file is read on every request and
   its contents end up in the plan on screen. For the session, swap in a two
   line file and restore the link after.
4. Stage the two projects:
   - `~/projects/team-intro`, the Session 1 site, with an `index.html` that
     has one `<h2>` section and a short list, so `/plan` has a style to match.
   - `~/projects/launch-post/sources/` with three past tweets, one release
     note and the pricing table saved as text files. Keep the `SKILL.md` from
     the guide open in another window as the fallback.
5. Terminal font 18pt or larger. Check balance at pc.0g.ai. Stay on
   `glm-5.3-flashx`; GLM 5.3 costs about four times more per token.
6. Rehearse both demos once with `claude-0g -p` so the prompts are known to
   work, then `rm` anything they changed.

## Live (about 60 min, or 30 with the cuts below)

| Time | Slide | Who | What |
|---|---|---|---|
| 0:00 | 1 | both | Start the recording. Goal: make Claude Code on PC repeatable. |
| 0:02 | 2 | navigator | Poll in the chat. Three or more stuck: breakout with the Session 1 guide. Show and tell, two people, two minutes each. |
| 0:15 | 3 | driver 1 | Session 1 was the habits. Today, the three features that make them easy. Under a minute. |
| 0:16 | 4 | driver 1 | Without a plan it acts on its first guess. With `/plan` a wrong assumption costs one sentence. Say the 25% is best case, older models, not measured on PC. |
| 0:18 | 5 | driver 1 | The timeline: message 3 is the miss, 4 and 5 stack fixes on it. `Esc` twice, pick checkpoint 2, files and chat come back. Checkpoints do not undo terminal commands. |
| 0:20 | 6 | driver 1 | **Live in team-intro.** Run the `/plan` prompt from the guide, read the plan, reject one step, approve. Make the miss real: if the result is fine, ask for something vaguer. `Esc` twice, ask again clearly, then say the rule out loud and have it saved to `CLAUDE.md`. |
| 0:35 | 7 | driver 2 | PC models cannot search the web. Ask for the tweet with no sources, let the chat spot the guessed prices, then "Read everything in sources/ first". |
| 0:38 | 8 | driver 2 | A rule changes behaviour everywhere; a skill is a brief you call by name. Skills shaped by people: +16 points. Skills the model wrote alone: nothing. |
| 0:40 | 9 | driver 2 | The folder and the file. Top block is name and when to use it, the rest is the brief. |
| 0:42 | 10 | driver 2 | **Live in launch-post.** Get one tweet right, use the save prompt, open and edit `SKILL.md` on screen, run `/tweet` on a second topic. If `/tweet` is missing, `/exit` and start again. If the file is broken, paste the prepared one. |
| 0:50 | 11 | both | Homework. Put the guide link in the chat. |
| 0:52 | 12 | both | Questions. Stop the recording. |

For a 30 minute slot: poll only on slide 2, no show and tell, skip the Session
1 rules recap in the first demo, and fold slide 9 into the second demo when
you open `SKILL.md`. Demo 1 at 0:04, demo 2 at 0:14, homework at 0:26.

Things to avoid on screen:

- Don't `cat ~/.claude-0g/settings.json`. The key is in it.
- Don't push to GitHub during the rewind demo. Rewind only restores files
  Claude edited.
- Don't switch to a text-only model and then paste a screenshot.
- Don't open `/usage` for spend. Its cost is wrong for PC models.

If things go wrong:

- `/plan` edits instead of planning: press `Shift Tab` until the status line
  shows plan mode, then send the prompt again.
- The model says it has no tools: check `ENABLE_TOOL_SEARCH` is `"0"` in the
  settings file, `/exit`, start again.
- `/tweet` does not appear after the save: `/exit` and start `claude-0g`
  again. Skills are read at startup.
- The model writes a broken `SKILL.md`: paste the one from the guide. Say
  that this is why you read what it saves.
