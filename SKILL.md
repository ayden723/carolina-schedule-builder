---
name: carolina-schedule-builder
description: Build a UNC-Chapel Hill class schedule planner for any semester. Interviews the student, reads their Course List and Tar Heel Tracker in ConnectCarolina (view only, with permission) to see what they already have, pulls sections, checks Rate My Professors, and publishes an interactive Coursicle-style calendar with conflict checks. Use for UNC course planning or registration prep.
---

# Carolina Schedule Builder

Turn a UNC student's goals into an interactive weekly-calendar planner (toggle sections on and off, see conflicts, credits and registration-wave hours, see professor ratings) plus 2-5 ready-made conflict-free schedules.

The student may be new to Claude. Be plain, patient and brief. Never use jargon like "iframe", "connector" or "artifact" with them; say "the planner page".

## Hard rules (never break)
1. Never click Enroll, Add, Swap, or anything that changes the student's registration. They register themselves. Say so once at the start.
2. Never type, store or repeat passwords, Duo codes or the student ID. The student signs in themselves in the browser. The ConnectCarolina dashboard shows the student ID, PIN and current schedule: do not copy those into notes, files or replies. Do not take screenshots of the Student home page or any page that shows the ID or PIN, and do not repeat the student's name or username; read specific pages as text and keep only the fields you need.
3. Never guess when a wrong guess would cost them a seat. Ask. A wrong class in the plan is worse than one extra question. Typical traps that actually happened: IDST 100 vs IDST 111L, "honors seminar" vs "honors course", and a list of "89" seminars that were not Honors seminars.
4. Never invent data. If instructor, room, seats, ratings or grades could not be read, show them as TBA / "no data" and say what is missing. Do not fill gaps from memory.
5. Do not use Coursicle or other third-party schedulers as a data source unless the student asks. Official sources: ConnectCarolina, reports.unc.edu class search, catalog.unc.edu, honorscarolina.unc.edu, advising pages. Rate My Professors is the only ratings source.
6. Reading the student's own records (Course List, Tar Heel Tracker) is view-only and needs a clear yes first. Before opening anything, tell the student in one sentence what you will read and why ("your Course List and Tar Heel Tracker, so I don't have to ask what you've already taken"). If they say no, hesitate, or no browser can open ConnectCarolina, skip Phase 1b and ask the Round 2 questions instead. Only navigate and view. Never open What-If, never use any Apply, Order, Submit or Download CSV control, never open the Shopping Cart or Enrollment pages in this phase, and never click anything that saves or changes data.

## Phase 0: Set up a first-time Claude user (only if they seem unsure)
Tell them, in two or three sentences, what will happen: you will ask a few basic questions, open ConnectCarolina in the browser, ask them to sign in, read their Course List and Tar Heel Tracker (view only) to see what they've already done, ask a few follow-ups, collect sections and ratings, then give them one link to the planner page.
They need:
- Claude in Chrome: the Claude Chrome extension, installed in the student's own Chrome, signed in to the same Claude account, with Chrome open. Claude works in its own tab group in that Chrome, in new tabs next to the student's tabs, and the student signs in to ConnectCarolina there themselves. The extension may ask them to allow connectcarolina.unc.edu and ratemyprofessors.com; if a site is blocked or waiting for approval, say so and wait instead of working around it. Leave the tabs Claude opened until you are done, then close the ones you created. If Claude in Chrome is not installed or not connected, say so plainly, tell them how to add it (Chrome Web Store, "Claude" extension, sign in), and meanwhile offer to build from the public class search only (no instructors or rooms) and add those later.
- To be signed in to ConnectCarolina with Duo done, in that browser, before Phase 1b. Ask them to do it right after Round 1 of the interview, and wait for them to say they are in. The same sign-in is reused for the section pull in Phase 3, so they should not need to sign in twice unless it expires. Leave the site's cookie banner alone.
- Optional: Google Drive/Sheets connected if they want a spreadsheet copy. Without the Sheets connector, send an .xlsx file instead.
If their sign-in expires mid-task (login page appears), ask them to sign in again. It will expire; do not treat it as failure.

## Phase 1: Interview (use AskUserQuestion, 1-4 questions per round)
Open with one sentence saying what you are about to do, then ask. Do not ask what you can look up.

Round 1, the basics:
- Which semester are they planning (e.g. Spring 2027)? Year, major(s)/minor(s), intended major if undecided, first-year/sophomore/etc., Honors Carolina or not.
- Credit target (e.g. 15-16) and the hard maximum.
- Soft constraints: no 8ams, no Fridays, no evenings, lunch break, commute gaps (UNC buildings can be 10-15 minutes apart), work or club times.

### Phase 1b: Read their records (after Round 1, before Round 2)
Goal: learn what they have completed, what they are registered for, what is only in their shopping cart, and which requirements are still open, so Round 2 only asks about gaps. Follow hard rule 6: say what you will read, get a yes, then ask them to sign in to ConnectCarolina and wait until they say they are in. If you cannot reach ConnectCarolina or they decline, skip this phase and ask the full Round 2 list below.

Where things are (checked on a real student account in October 2026; menus change, so navigate by the names you see and do not guess URLs):
- Sign in at connectcarolina.unc.edu (LOGIN). The landing page is the Student home, a grid of tiles. Do not screenshot it: the Personal Information tile shows the student ID.
- Open the **Student Center** tile. It opens in a new tab titled "Student Center". In its top navigation open **Academics**. The menu includes Course List, Tar Heel Tracker, General Education Progress, Transfer/Test Credit, Test Scores, View Unofficial Transcript, Grades, Advisor Information and Majors, Minors & Degrees. It also has What-If and several Apply/Order items: never open those.
- The Student home also has an "Academic Progress & Planning" tile with a Tar Heel Tracker link. That opens an older page titled "My Academic Requirements" with less detail. Use the Student Center route instead.
- Clicking a menu item right after opening the menu can land on the wrong item (it opened General Education Progress once). After each click check the tab title; if it is wrong, reopen Academics, find the item by its text, and click again.
- Page content sits in a same-origin frame inside the Student Center tab (`iframe#main_iframe`). Read it as text with JavaScript through that frame's `contentDocument` instead of screenshots. Plain page-text reads return only the page title. If a tool says a tab is gone, list tabs again and use the current one.

Read in this order:
1. **Course List** (main source for what they have). One table with the columns Course, Description, Term, Grade, Units, Status, Repeat Code, Req Designation. Rows are ARIA rows (`[role=row]`), not an HTML `<table>`: the course code is in the first cell of each row and the other columns follow as grid cells. Statuses seen so far:
   - **In Progress**: registered. This can be a future term (a student who already registered for next term sees those rows here), so check the Term column before treating it as "this term".
   - **In Cart**: in the shopping cart only. NOT registered. Never count it as taken or enrolled. Ask whether they still plan to take it.
   - **Transferred (Course)**: transfer credit. Often listed as "<SUBJECT> GENERAL ELECTIVE" with no course number and Grade "TR". That does not satisfy a specific course unless the row names one.
   - **Transferred (Test)**: AP/IB/other test credit, listed like "CREDIT FOR AP <EXAM NAME> TEST" with Grade "BE". The row does not say which course it replaces; check the Tracker or ask.
   - Completed UNC courses did not exist on the account tested (a first-year), so how they appear is unverified. Expect a letter grade and a different status label. If you see a status you have not listed here, show it to the student and ask what it means.
   - Repeat Code and Req Designation were empty on the account tested. Do not rely on them.
   - Do not press "Download CSV".
2. **Tar Heel Tracker** (which requirements are open). The header reads "N of M Requirements Satisfied" with buttons for Open, View PDF and View All. Every requirement carries a status: SATISFIED, SATISFIED - IP (satisfied only if the in-progress course finishes with a passing grade) or NOT SATISFIED. Detail inside collapsed sections is not included in a normal text read, and View All opens only some of it. To see which courses fill one requirement, expand just that section by clicking its header (view only). Use the Tracker for: open general education categories, pre-business and major requirement groups, minimum-grade reminders, and the catalog year.
3. Transfer/Test Credit and Test Scores pages were not opened during testing. Open them only if a decision depends on an exact exam score (for example AP Stats 3 vs 4+) and the Course List and Tracker do not settle it; otherwise ask the student.

What to record (only these):
- Completed courses and test/transfer credit: course code, term, credits, and what it counts as.
- Registered courses (In Progress) with their term, and cart courses (In Cart) kept in a separate list.
- Open requirements and which are satisfied only by in-progress courses.
- Grades: do not copy letter grades or GPA into notes or the planner. When a decision needs one, record a yes/no flag, for example "ECON 101: completed, meets the C-or-better prerequisite". If you cannot tell, mark it "unsure, ask the student".

If the records look stale or odd (a course missing that they say they took, transfer credit not yet posted, a different major than they named), do not pick a side. Show the difference and ask.

Show the student a short summary and get a yes or corrections before relying on it: "Done: X, Y, Z. Registered: A, B. In your cart (not registered): C, D. Credit from tests/transfer: E, F. Still open: G, H." Label it "from your Course List and Tracker as of today". Keep these notes in the scratch file without the student ID, name, username or grades.

Round 2, only what the records did not answer (skip any question they already settled, and say so: "Your Course List shows you've done X, so I won't ask about it"):
- Anything the records could not give: exam scores or transfer credit that have not posted yet (AP Stats 3 vs 4+ maps differently), courses taken elsewhere, what the cart courses are for, or, if Phase 1b was skipped, all completed and in-progress courses (AP/IB/transfer too). In-progress courses matter for prerequisites.
- Required courses they must take this term (gen-ed / IDEAs in Action, major core, first-year requirements such as BUSI 100, IDST 111L, a first-year seminar or Launch). Use the Tracker's open requirements as the starting list and ask which they want this term.
- When a term has an ambiguity, ask the exact variant: which course number (IDST 100 vs 111L), Honors seminar vs a regular first-year seminar, Launch vs seminar, lab or recitation required or optional. The records do not settle these.

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

ConnectCarolina (Claude in Chrome, student signed in):
1. If they already signed in for Phase 1b, check that the session is still live and carry on. Otherwise ask them to sign in and say when they are in.
2. Open Class Search through the form (Term, Career = Undergraduate, Subject, Catalog #). Deep links built from guessed parameters usually drop to the dashboard, so use the form; once results are showing, the page URL is reusable only in that session.
3. Uncheck "Show Open Classes Only" when you need full and closed sections.
4. Read results with JavaScript instead of screenshots: the results live in an iframe, so use `[...document.querySelectorAll('iframe')].map(f => f.contentDocument.body.innerText)` (fall back to `document.body.innerText`). Parse each card: Section, Class Number, Status and seats, Days, Start, End, Room, Instructor, Dates.
5. Pagination needs a real click on the page number (a JavaScript click does not work).
6. The Subject dropdown is picky: type a prefix ("STAT", "INT-D", "MATH") and pick the matching entry. Names that differ from the code: "STATS AND OPERATIONS RESEARCH" (STOR), "INT-DISCIPL STUDIES" (IDST), "BUSINESS ADMIN" (BUSI); Data Science is "Data Science and Society".
7. For honors seminars, filter by Course Attribute (Honors / "HNRS - Honors") and scan all pages. Then cross-check against the Honors Carolina page.
8. Recitations and labs: results list them as separate sections (601-REC etc.). They are required components for the course and carry 0 or 1 credits. Capture all of them. ConnectCarolina results do not show which lecture a recitation belongs to; if pairing is inferred from section numbers (6xx goes with 001, 7xx with 002), label it "inferred, confirm at enrollment". Never present an inferred pairing as fact.
9. Record "as of" date. Seats change within hours.

Keep working notes in a scratch file (requirements, per-course pulls, flags). Do not put the student ID, PIN, name, username or letter grades in it; course codes and credit status from their records are fine.

## Phase 4: Rate My Professors
- Search URL: `https://www.ratemyprofessors.com/search/professors/1232?q=LASTNAME` (UNC Chapel Hill school id is 1232). WebFetch is blocked by robots.txt, so use Claude in Chrome.
- Batch lookups: on an RMP tab, run JavaScript that creates same-origin hidden iframes for each name, waits until `main` text contains "professor(s) with", and collects `main.innerText`. Run 10-15 at a time.
- Match carefully: department must fit the course, not just the name. Handle name variants (Brad/Bradley), hyphenated names (search the first part, e.g. "Sheran"), and duplicate pages for one person (combine and note both). Search the last name alone when the full name returns nothing; apostrophes and hyphens break searches.
- Record: overall quality, difficulty, would-take-again %, number of ratings, RMP professor id (for a link), a confidence flag. Flag low confidence when under ~10 ratings, department mismatch, or a very common name. No page = "no RMP page", never a score.
- Treat ratings as signal, not truth: quality and difficulty with fewer than 15 ratings are noisy. Say so when it changes a recommendation.

## Phase 5: Grades by professor
UNC does not publish instructor-level grade distributions publicly. A search found only stale third-party sites (one was a 2018-era site that returned errors). Do not fabricate grades. Options to offer: (a) use RMP difficulty and would-take-again as a proxy, labeled as a proxy; (b) if the student has a source or file they trust, add an `avg` value per instructor (the planner template has a GRADES map that shows it as a tag); (c) ask the course's department or older students.

## Phase 6: Choose schedules
Model every registrable choice as an option: course + section (+ required recitation/lab as its own option group). Conflict = same day letter (M T W R F) and overlapping minute ranges. Check every pair, including recitations, labs and optional labs the student plans to take. Enumerate combinations with the requirement groups, drop anything violating hard constraints (8am, Fridays, credit cap, wave 1 hours, rating floors), then pick 2-5 schedules that differ in a meaningful way (best professor, no 8am, honors section, alternate seminar). For each schedule report credits, wave 1 hours, days on campus, 8am/Friday count and the tightest gap between classes (walking time). Verify with code: 0 overlaps, correct pair count, credits add up. State every tradeoff in one line (for example, "the only non-conflicting stats sections have no instructor posted yet").

Prerequisite check: for each recommended course, confirm the student meets the catalog prerequisite, including in-progress courses, and say when ConnectCarolina's enrollment check may still block them. Use the Phase 1b summary (completed, registered, test/transfer credit, minimum-grade flags) as the source, and say which prerequisites rest on an in-progress course finishing with a passing grade. Cart courses do not count as taken or registered. Never recommend a course the records show as already completed, already registered, or already satisfied by test or transfer credit unless the student wants a repeat or the course counts for something else; if it is unclear whether a requirement is already met, ask.

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
- If Phase 1b ran: the student confirmed the summary of their records, nothing in a preset duplicates a completed, registered or already-satisfied course, cart courses were not treated as taken, and no student ID, PIN, name, username or letter grade appears in the notes, the planner page or the final message.
