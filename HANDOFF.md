# HANDOFF — cloud session → local Claude Code

Written 2026-09-26. Read CLAUDE.md first; it is the source of truth. This file only covers setup and
where things stand. Delete it once the local setup is done.

## Why local
Godot must run to verify anything (CLAUDE.md: never claim it works without running it). The cloud
container has no Godot. Locally, Claude Code can run Godot directly and the developer can playtest.

## Current state
- Repo `highwaylot/jumjumaron` contains only CLAUDE.md and this file.
- Only one branch exists: `claude/worm-parkour-setup-z1z0l3`. No `main` yet.
- No Godot project exists yet. No code written. Increment 1 not started.

## Developer setup (one time)
1. Install Godot 4 (standard build, NOT .NET; we use GDScript). Note the exact version installed.
2. Install Git (or GitHub Desktop) and Claude Code.
3. Clone: `git clone https://github.com/highwaylot/jumjumaron.git`
4. Create `main` from the current branch and push it:
   ```
   cd jumjumaron
   git checkout -b main
   git push -u origin main
   ```
   Then on GitHub: Settings → General → Default branch → `main`.
5. Godot Project Manager → Create → Project Path = the `jumjumaron` folder, Renderer = Forward+,
   Version Control Metadata = Git. (Warning that the folder isn't empty is expected.)
6. Commit and push: `git add . && git commit -m "Create Godot project" && git push`
7. In the `jumjumaron` folder, run `claude`.

## Giving Claude Code access to Godot
Claude needs to call the Godot executable from the terminal. Either put it on PATH or tell Claude the
full path to it in the first message. Useful headless checks Claude can run itself:
- `godot --headless --editor --quit` imports the project and surfaces load/script errors.
- `godot --headless --check-only --script res://path/to/file.gd` parse-checks one script.
Headless checks catch errors and can measure distances (e.g. jump gap clearance). They cannot judge
feel; feel is only verified by the developer playtesting.

## First message to local Claude Code (paste this)
> Read CLAUDE.md and HANDOFF.md. Godot is at <PATH TO GODOT>, version <VERSION>. Confirm you can run it
> headless. Then propose a plan for increment 1 in small testable steps. Don't build yet.

## Open decisions for the developer
- None blocking. `main` branch setup is step 4 above.
