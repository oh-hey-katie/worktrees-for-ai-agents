# Run two AI coding agents on one app at the same time

The prompts I use to have **Claude Code** and **Codex** work on my app in parallel, using git worktrees, without typing any git commands.

I'm not a developer. AI made me so fast that I stopped doing the basics. This is the one habit I brought back, and it's what lets two agents work at once without stepping on each other.

Take what helps.

---

## The idea in one paragraph

Two agents in one folder see each other's half-finished work, undo it, or sweep it into their own commits. The fix: give each agent **its own folder**. A git worktree is just another real folder on your computer, linked to the same project and checked out on its own branch. Each agent works in its own folder, and you bring the work back together one branch at a time.

## The 3 rules

1. **One folder per agent.**
2. **Agents commit. You decide when to merge.**
3. **Merge one at a time, and bring the second branch up to date with main before you merge it.**

---

## The prompts

Replace anything in `[square brackets]`. Paste each prompt into the agent named above it.

### 1. Make the folders (any agent, in your main project folder)

```text
Before we start anything new: make sure this folder is on main, everything is committed, and main is pushed to GitHub.

Then create two git worktrees next to this folder, both starting from the latest main:
- ../[app]-[task-a] on a new branch called [task-a]
- ../[app]-[task-b] on a new branch called [task-b]

If the app needs any files to build or run that git doesn't track (like .env files), copy them into both new folders.

Don't change any app code. When you're done, run git worktree list and explain each line in one plain-English sentence.
```

### 2. Give agent A its task (open it in the first new folder)

Ask first: *"What folder and branch are you in?"* Then:

```text
You're working only in this folder, on the [task-a] branch. Don't touch any other folder.

Task: [describe the feature or design change].

When it's done, build and run it so I can see it.

Commit your work to the [task-a] branch. Do not merge into main.
```

### 3. Give agent B its task (open it in the second new folder)

Ask first: *"What folder and branch are you in?"* Then:

```text
You're working only in this folder, on the [task-b] branch. Don't touch any other folder.

Task: [describe the feature or design change].

When it's done, build and run it so I can see it.

Commit your work to the [task-b] branch. Do not merge into main.
```

### 4. First merge (new session, in your main project folder)

```text
Merge the [task-a] branch into main. Don't touch the [task-b] branch.

Build the app to confirm main still builds, then push main to GitHub. Tell me in one sentence what's now in main.
```

### 5. Second agent catches up (agent B, in its folder)

```text
Main has changed since you started: the [task-a] branch was merged into it. Merge the latest main into your [task-b] branch.

If there are conflicts, stop and explain each one to me in plain English before you fix anything, and keep both changes when you fix them.

Then build the app and confirm it still works. Commit. Do not merge into main.
```

### 6. Second merge (main project folder)

```text
Merge the [task-b] branch into main. Build and run the app, then push main to GitHub.
```

### 7. Clean up (main project folder)

```text
We're done with the worktrees. Remove ../[app]-[task-a] and ../[app]-[task-b] with git worktree remove (don't just delete the folders), then delete the [task-a] and [task-b] branches, locally and on GitHub if they were pushed.

Run git worktree list to show that only this folder is left.
```

---

## Things that tripped me up

- **Claude Code desktop app:** use the Code view, and untick **Worktree** when you start a session in a folder you made yourself. Otherwise it makes its own hidden worktree.
- **Codex app:** start the chat in **Local** mode, not Worktree, for the same reason. If Codex says it's blocked from writing git files, give it more permissions (it needs to reach your main project's git data).
- **Files git doesn't track** (like `.env`) aren't copied into new worktrees. Prompt 1 handles this. If a build fails in a new folder, that's usually why.
- **iOS apps:** give each agent its own simulated iPhone (e.g. iPhone 17 and iPhone 17 Pro). Both builds share one app ID, so on the same simulator the second install replaces the first.
- **Live speech in the iOS Simulator** may not work. Test that on a real phone.

Both Claude Code and Codex can now create worktrees for you automatically. I do it by hand because I mix two tools on one app, and because seeing the folders is what made it click for me.
