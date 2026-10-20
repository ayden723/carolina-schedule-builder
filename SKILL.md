---
name: carolina-schedule-builder
description: Build a UNC-Chapel Hill class schedule planner for any semester. Interviews the student, pulls sections from ConnectCarolina, checks Rate My Professors, and publishes an interactive Coursicle-style calendar with conflict checks. Use for UNC course planning or registration prep.
---

# Carolina Schedule Builder

Turn a UNC student's goals into an interactive weekly-calendar planner (toggle sections on and off, see conflicts, credits and registration-wave hours, see professor ratings) plus 2-5 ready-made conflict-free schedules.

The student may be new to Claude. Be plain, patient and brief. Never use jargon like "iframe", "connector" or "artifact" with them; say "the planner page".

## Hard rules (never break)
1. Never click Enroll, Add, Swap, or anything that changes the student's registration. They register themselves. Say so once at the start.
2. Never type, store or repeat passwords, Duo codes or the student ID. The student signs in themselves in the browser. The ConnectCarolina dashboard shows the student ID, PIN and current schedule: do not copy those into notes, files or replies.
3. Never guess when a wrong guess would cost them a seat. Ask. A wrong class in the plan is worse than one extra question. Typical traps that actually happened: IDST 100 vs IDST 111L, "honors seminar" vs "honors course", and a list of "89" seminars that were not Honors seminars.
4. Never invent data. If instructor, room, seats, ratings or grades could not be read, show them as TBA / "no data" and say what is missing. Do not fill gaps from memory.
5. Do not use Coursicle or other third-party schedulers as a data source unless the student asks. Official sources: ConnectCarolina, reports.unc.edu class search, catalog.unc.edu, honorscarolina.unc.edu, advising pages. Rate My Professors is the only ratings source.

## Phase 0: Set up a first-time Claude user (only if they seem unsure)
Tell them, in two or three sentences, what will happen: you will ask a few questions, open ConnectCarolina in the browser pane, ask them to sign in, collect sections and ratings, then give them one link to the planner page.
They need:
- A Claude session that can use the browser pane (Claude desktop app, with this session linked to their computer) so they can sign in to ConnectCarolina. If browser tools are missing, say plainly that you can still build from the public class search (no instructors or rooms) and offer to add those later.
- To be signed in to ConnectCarolina with Duo done, in the browser pane, before the section pull. Ask them to do it when you reach Phase 3, and wait for them to say they are in.
- Optional: Google Drive/Sheets connected if they want a spreadsheet copy. Without the Sheets connector, send an .xlsx file instead.
If their sign-in expires mid-task (login page appears), ask them to sign in again. It will expire; do not treat it as failure.

## Phase 1: Interview (use AskUserQuestion, 1-4 questions per round)
Open with one sentence saying what you are about to do, then ask. Do not ask what you can look up.

Round 1, the basics:
- Which semester are they planning (e.g. Spring 2027)? Year, major(s)/minor(s), intended major if undecided, first-year/sophomore/etc., Honors Carolina or not.
- Credit target (e.g. 15-16) and the hard maximum.
- Soft constraints: no 8ams, no Fridays, no evenings, lunch break, commute gaps (UNC buildings can be 10-15 minutes apart), work or club times.

Round 2, requirements and what they already have:
- Completed and in-progress courses (AP/IB/transfer credit too). Ask which exam scores (AP Stats 3 vs 4+ maps differently). In-progress courses matter for prerequisites.
- Required courses they must take this term (gen-ed / IDEAs in Action, major core, first-year requirements such as BUSI 100, IDST 111L, a first-year seminar or Launch).
- When a term has an ambiguity, ask the exact variant: which course number (IDST 100 vs 111L), Honors seminar vs a regular first-year seminar, Launch vs seminar, lab or recitation required or optional.

Round 3, preferences:
- Electives they want to explore and why (curiosity, possible major switch, career). Subjects they want to try, any professor names they already like or want to avoid.
- Difficulty preference ("really easy seminar" vs "I want a challenge"), and the minimum professor rating they accept for hard or required classes (this student used: two math classes only if both professors are above 3.9/5).
- Registration timing: their registration wave/appointment, Honors Carolina's early slot, wave caps (see Phase 2).
- Do they also want grade-by-professor data? Be upfront that UNC does not publish instructor-level grades publicly and that no reliable free source was found (see Phase 5). Ask if they have a source they trust.

Restate the requirements as a short checklist and get a yes before pulling data.

## Phase 2: Research requirements and rules
- Term code in ConnectCarolina: Spring 2027 was 2272 (pattern observed: 2 + two-digit year + 2 for spring; verify rather than assume for other terms).
- Check prerequisites and what counts, from the catalog (catalog.unc.edu course pages and program pages) before recommending. Example: ECON 410 requires ECON 101 (C or better) plus a calculus course (C- or better). Kenan-Flagler pre-business needs STOR 120/155/DATA 110, BUSI 100, ECON 101 and calculus with C or better. Say when a catalog page does not state something.
- Honors Carolina: the official list of first-year seminars and Launches for the term is on honorscarolina.unc.edu under "Academics > Courses" (page name like "Spring 2027 Honors First Year Seminars & Launches"). It does not cover every honors-designated section. Numbers on that page may not match ConnectCarolina. Treat the Honors page as the list of Honors seminars; courses titled "FYS: Special Topics" are not Honors unless they appear there or carry the honors attribute.
- Registration waves (ask the student; rules change). Last known: wave 1 up to 13 hours and up to 2 honors classes with no waitlist; wave 2 up to 17 hours with waitlist up to 12 hours; open enrollment up to 18. Put wave caps in the planner but label them "as the student told me".

## Phase 3: Pull the sections
Order: public search first (fast, no sign-in), then ConnectCarolina for what the public search lacks.

Public class search (WebFetch, works without sign-in):
`https://reports.unc.edu/class-search/?term=2027+Spring&subject=ECON` (add `&catalog_number=410` for one course). Gives section, class number, title, credits, days, times, seats. It does NOT give instructors, rooms, waitlists, or recitation/lab components, and can omit or mislabel rows. Long pages get truncated: use the offset parameter or query one course at a time.

ConnectCarolina (browser pane, student signed in):
1. Ask the student to sign in and say when they are in.
2. Open Class Search through the form (Term, Career = Undergraduate, Subject, Catalog #). Deep links built from guessed parameters usually drop to the dashboard, so use the form; once results are showing, the page URL is reusable only in that session.
3. Uncheck "Show Open Classes Only" when you need full and closed sections.
4. Read results with JavaScript instead of screenshots: the results live in an iframe, so use `[...document.querySelectorAll('iframe')].map(f => f.contentDocument.body.innerText)` (fall back to `document.body.innerText`). Parse each card: Section, Class Number, Status and seats, Days, Start, End, Room, Instructor, Dates.
5. Pagination needs a real click on the page number (a JavaScript click does not work).
6. The Subject dropdown is picky: type a prefix ("STAT", "INT-D", "MATH") and pick the matching entry. Names that differ from the code: "STATS AND OPERATIONS RESEARCH" (STOR), "INT-DISCIPL STUDIES" (IDST), "BUSINESS ADMIN" (BUSI); Data Science is "Data Science and Society".
7. For honors seminars, filter by Course Attribute (Honors / "HNRS - Honors") and scan all pages. Then cross-check against the Honors Carolina page.
8. Recitations and labs: results list them as separate sections (601-REC etc.). They are required components for the course and carry 0 or 1 credits. Capture all of them. ConnectCarolina results do not show which lecture a recitation belongs to; if pairing is inferred from section numbers (6xx goes with 001, 7xx with 002), label it "inferred, confirm at enrollment". Never present an inferred pairing as fact.
9. Record "as of" date. Seats change within hours.

Keep working notes in a scratch file (requirements, per-course pulls, flags). Do not put the student ID or schedule in it.

## Phase 4: Rate My Professors
- Search URL: `https://www.ratemyprofessors.com/search/professors/1232?q=LASTNAME` (UNC Chapel Hill school id is 1232). WebFetch is blocked by robots.txt, so use the browser pane.
- Batch lookups: on an RMP tab, run JavaScript that creates same-origin hidden iframes for each name, waits until `main` text contains "professor(s) with", and collects `main.innerText`. Run 10-15 at a time.
- Match carefully: department must fit the course, not just the name. Handle name variants (Brad/Bradley), hyphenated names (search the first part, e.g. "Sheran"), and duplicate pages for one person (combine and note both). Search the last name alone when the full name returns nothing; apostrophes and hyphens break searches.
- Record: overall quality, difficulty, would-take-again %, number of ratings, RMP professor id (for a link), a confidence flag. Flag low confidence when under ~10 ratings, department mismatch, or a very common name. No page = "no RMP page", never a score.
- Treat ratings as signal, not truth: quality and difficulty with fewer than 15 ratings are noisy. Say so when it changes a recommendation.

## Phase 5: Grades by professor
UNC does not publish instructor-level grade distributions publicly. A search found only stale third-party sites (one was a 2018-era site that returned errors). Do not fabricate grades. Options to offer: (a) use RMP difficulty and would-take-again as a proxy, labeled as a proxy; (b) if the student has a source or file they trust, add an `avg` value per instructor (the planner template has a GRADES map that shows it as a tag); (c) ask the course's department or older students.

## Phase 6: Choose schedules
Model every registrable choice as an option: course + section (+ required recitation/lab as its own option group). Conflict = same day letter (M T W R F) and overlapping minute ranges. Check every pair, including recitations, labs and optional labs the student plans to take. Enumerate combinations with the requirement groups, drop anything violating hard constraints (8am, Fridays, credit cap, wave 1 hours, rating floors), then pick 2-5 schedules that differ in a meaningful way (best professor, no 8am, honors section, alternate seminar). For each schedule report credits, wave 1 hours, days on campus, 8am/Friday count and the tightest gap between classes (walking time). Verify with code: 0 overlaps, correct pair count, credits add up. State every tradeoff in one line (for example, "the only non-conflicting stats sections have no instructor posted yet").

Prerequisite check: for each recommended course, confirm the student meets the catalog prerequisite, including in-progress courses, and say when ConnectCarolina's enrollment check may still block them.

## Phase 7: Build the planner page
Publish one self-contained HTML page as an artifact (private by default, shareable from the page). Use `assets/schedule_builder_template.html` as the starting point and fill three placeholders: `__DATA__` (array of options, schema in `assets/option_schema.md`), `__PRESETS__` (your schedules), `__GRADES__` (`{}` if none). Read the artifact-design skill before publishing if it is available.
Must have:
- Weekly Mon-Fri calendar, 8am-9pm, blocks colored by group, conflicts shown with red hatching, hover preview of a section before adding it.
- Section list grouped by course with search, group filters, and "hide conflicts / hide 8am / hide Friday / rating above X" toggles; each section shows time, instructor, RMP badge, seats, flags (8am, Friday, honors, optional lab, conflicts with which course).
- Totals: credits against the student's goal, wave 1 hours against its cap, wave 2 cap, conflict count, 8am count, Friday count; per-course wave selector.
- Preset buttons for the chosen schedules and a Clear button.
- Checks panel: conflicts, rating-rule violations, prerequisite and wave reminders, TBA instructors, and a reminder that the page never registers anyone and seats are as of the pull date.
- Both light and dark themes, phone-width layout.
After filling the template, sanity-check the script parses (e.g. run it through Node) and render once in a headless browser to confirm there are no console errors and a preset shows 0 conflicts. Then publish. Republish to the same file path to update; the link stays the same.

## Phase 8: Deliver
- Give the link as soon as a first usable version exists, then iterate. Do not wait for perfect data.
- Final message (short): what the planner is, the top recommended schedule in 5-8 lines (course, time, professor + rating, credits), the 3 biggest open items (TBA instructors, inferred recitation pairing, unverified honors status), and what to do on registration day (log in early, have backups, register for wave 1 courses first). Do not click Enroll.
- Optional spreadsheet: only with the Google Sheets connector installed, otherwise send an .xlsx file.

## Updating mid-conversation
Students change their minds (drop a class, add econ, swap professors). Update the data file and presets, rebuild, republish to the same URL, and say in one sentence what changed. When a student says a class "doesn't exist" or a list was wrong, remove it immediately and re-check anything else derived from the same source.

## Quality checklist before saying done
- Every section in a preset was seen in a source you can name. Credits sum correctly. No overlaps.
- Every professor shown has either an RMP match with a confidence flag or an explicit "no RMP page"/"TBA".
- Honors seminar choice appears on the Honors Carolina list for that term.
- Anything inferred or assumed is labeled in the page and in the final message.
