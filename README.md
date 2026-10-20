# Carolina Schedule Builder (Claude skill)

A Claude skill that helps a UNC-Chapel Hill student plan a semester: it interviews you, pulls sections from ConnectCarolina, checks Rate My Professors, and publishes an interactive weekly-calendar planner with conflict checks.

It never registers you for anything and never asks for your password. You sign in yourself.

## Use it (students)
1. Download this repo (Code > Download ZIP) or `git clone` it.
2. In Claude, open Customize (or Settings) > Skills > add/upload skill and choose the `carolina-schedule-builder` folder or its zip.
3. Start a chat in the Claude desktop app so Claude can use the browser pane, and say: "Use the Carolina Schedule Builder skill to plan my next semester."
4. When Claude asks, sign in to ConnectCarolina in the browser pane and finish Duo.

No skill upload? Paste the contents of `PROMPT.md` into a new chat instead.

## What is inside
- `SKILL.md`: the instructions Claude follows.
- `assets/schedule_builder_template.html`: the planner page template.
- `assets/option_schema.md`: the data format the template expects.
- `PROMPT.md`: a paste-in version for people who cannot install skills.

## Known limits
- UNC does not publish professor grade distributions; ratings come from Rate My Professors only.
- ConnectCarolina's layout can change. If a step breaks, the skill tells Claude to say so rather than guess.
- Seat counts change fast. The planner shows the date they were pulled.
- Not affiliated with or endorsed by UNC-Chapel Hill, ConnectCarolina or Rate My Professors.

## Contributing
Open an issue or pull request with new semesters' quirks (term codes, wave rules, honors lists, subject names).
