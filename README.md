# Carolina Schedule Builder (Claude skill)

A Claude skill that helps a UNC-Chapel Hill student plan a semester: it interviews you, reads your Course List and Tar Heel Tracker to see what you've already done, pulls sections from ConnectCarolina, checks Rate My Professors, and publishes an interactive weekly-calendar planner with conflict checks.

It never registers you for anything and never asks for your password. You sign in yourself.

## Use it (students)
1. Download this repo (Code > Download ZIP) or `git clone` it.
2. In Claude, open Customize (or Settings) > Skills > add/upload skill and choose the `carolina-schedule-builder` folder or its zip.
3. Install the Claude in Chrome extension (Chrome Web Store), sign in to the same Claude account, and keep Chrome open. The skill uses it to open ConnectCarolina and Rate My Professors in your own Chrome.
4. Start a chat in Claude and say: "Use the Carolina Schedule Builder skill to plan my next semester."
5. Answer a few questions. Then, when Claude asks, sign in to ConnectCarolina in the Chrome tab it opened and finish Duo. Allow the sites if the extension asks. Claude asks your permission before it reads anything from your account.

No skill upload? Paste the contents of `PROMPT.md` into a new chat instead.

## What it reads from your account (view only)
- **Course List** (Student Center > Academics > Course List): your completed, registered and transfer/test-credit courses, plus anything sitting in your shopping cart. Cart courses are not treated as registered.
- **Tar Heel Tracker** (Student Center > Academics > Tar Heel Tracker): which degree and general education requirements are still open.

Claude only views these pages. It never opens What-If, never clicks Enroll or Download, never copies your student ID, PIN, name or username, and does not save letter grades; where a prerequisite needs a minimum grade it only records a yes/no. It shows you a summary and asks you to confirm before using it. Say no and it asks the questions by hand instead.

## What is inside
- `SKILL.md`: the instructions Claude follows.
- `assets/schedule_builder_template.html`: the planner page template.
- `assets/option_schema.md`: the data format the template expects.
- `PROMPT.md`: a paste-in version for people who cannot install skills.

## Known limits
- UNC does not publish professor grade distributions; ratings come from Rate My Professors only.
- ConnectCarolina's layout can change. If a step breaks, the skill tells Claude to say so rather than guess. The Course List / Tar Heel Tracker steps were checked on one first-year account in October 2026; how completed UNC courses with letter grades appear, and the Transfer/Test Credit and Test Scores pages, were not verified.
- The Tar Heel Tracker hides most course detail inside collapsed sections, so Claude uses the Course List for what you've taken and the Tracker for which requirements are open.
- Seat counts change fast. The planner shows the date they were pulled.
- Not affiliated with or endorsed by UNC-Chapel Hill, ConnectCarolina or Rate My Professors.

## Contributing
Open an issue or pull request with new semesters' quirks (term codes, wave rules, honors lists, subject names).
