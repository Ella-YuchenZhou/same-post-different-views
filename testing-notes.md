# Same Post, Different Views — testing notes

## Scope and opening the prototype

Open `index.html` directly in a modern browser. No installation, build, server, external libraries, API, or live data is required. The optional local preview server is not required to use the file.

All records are fictional. This model demonstrates ordering and first-five visibility, not YouTube's real algorithm, public opinion, attitude changes, or factual accuracy. No user testing or personal reflection is claimed here.

Refresh restores the initial data. Edits are held in memory only. Reset demo restores initial likes, Most liked, Supportive as the mixed starting category, and five visible comments. It also resets the editor to C01.

## Fixed reference values

IDs are zero-padded, so ID ascending means C01 before C02, and so on. All timestamps use UTC. Every category has three comments.

| ID | Category | Initial likes | Timestamp (UTC) |
| --- | --- | ---: | --- |
| C01 | Supportive | 92 | 2026-09-04 12:00 |
| C02 | Concerned/Opposed | 120 | 2026-09-02 12:00 |
| C03 | Conditional | 80 | 2026-09-12 12:00 |
| C04 | Unclear | 12 | 2026-09-08 12:00 |
| C05 | Supportive | 80 | 2026-09-12 12:00 |
| C06 | Concerned/Opposed | 80 | 2026-09-06 12:00 |
| C07 | Conditional | 105 | 2026-09-03 12:00 |
| C08 | Unclear | 66 | 2026-09-10 12:00 |
| C09 | Supportive | 34 | 2026-09-07 12:00 |
| C10 | Concerned/Opposed | 58 | 2026-09-11 12:00 |
| C11 | Conditional | 24 | 2026-09-09 12:00 |
| C12 | Unclear | 98 | 2026-09-01 12:00 |

Fixed baseline, calculated from INITIAL data using Mixed viewpoints with Supportive first:

`C05, C10, C03, C08, C09`

This is not the previous result and is not the initial Most liked display. It remains fixed after any edit, switch, or reset.

Initial full Most liked order:

`C02, C07, C12, C01, C03, C05, C06, C08, C10, C09, C11, C04`

Initial full Newest order:

`C03, C05, C10, C08, C11, C04, C09, C06, C01, C07, C02, C12`

## Browser checks to perform

Actual-result cells below are intentionally blank. Automated logic checks recorded later do not fill these browser-test results. These are not tests performed by the student.

| Check | Steps | Expected result | Actual result |
| --- | --- | --- | --- |
| Direct file opening | Open index.html directly, without a server. | Page works locally; 5 comments; Most liked selected; initial first five C02, C07, C12, C01, C03. | |
| Like sorting and ties | Reset; select Most liked; expand all. | Full order matches the reference. C03 and C05 precede C06 because their timestamps are newer despite equal likes. C03 precedes C05 because their likes and timestamps match and C03 has the smaller ID. | |
| Time sorting | Reset; select Newest; expand all. | Full order matches the reference. C03 precedes C05 at the same timestamp. Likes do not affect order. | |
| Mixed: Supportive first | Reset; select Mixed viewpoints; choose Supportive. | First five C05, C10, C03, C08, C09; category counts 2/1/1/1; overlap 5/5; Entered and Left both None. | |
| Mixed: Concerned/Opposed first | Select that starting category. | First five C10, C03, C08, C05, C06; counts 1/2/1/1; overlap 4/5; Entered C06; Left C09. | |
| Mixed: Conditional first | Select that starting category. | First five C03, C08, C05, C10, C11; counts 1/1/2/1; overlap 4/5; Entered C11; Left C09. | |
| Mixed: Unclear first | Select that starting category. | First five C08, C05, C10, C03, C04; counts 1/1/1/2; overlap 4/5; Entered C04; Left C09. | |
| Position comparison | Compare the four Mixed starting categories and the position rows. | Shared comments can change rank. C05 is #1, #4, #3, and #2 respectively. Baseline rank for C05 always stays #1. | |
| All IDs | Expand under each mode and each Mixed start. | Exactly 12 distinct IDs, C01 through C12, with ranks 1–12; no omissions or duplicates. Collapse restores five. | |
| Editing in Most liked | Reset; select C04 in the editor; enter 999; Apply likes. | C04 becomes first. First five C04, C02, C07, C12, C01. Baseline is unchanged; overlap 0/5. | |
| Editing in Newest | Reset; choose Newest; set C04 to 999. | Like display updates, but all IDs remain in the same order. | |
| Editing in Mixed | Reset; choose Mixed; set C04 to 999; repeat with another start. | Like display updates, but changing likes does not change any ordering under the selected start. Groups use timestamps and IDs. | |
| Invalid likes | Try empty input, spaces, -1, 1.5, 1.0, 1e3, +2, letters, and 9007199254740992. Submit each. | Each is rejected with a visible error; data and current order stay unchanged. Input gets focus. | |
| Valid likes | Submit 0, 12, and 0003. | Accepted as 0, 12, and 3. Only nonnegative safe whole numbers are stored. | |
| Composition counts | Reset and compare the first five cards with the summary in each mode; repeat after edits and expansion. | Counts exactly match the first five and sum to 5. Initial Most liked counts are 1/1/2/1. Expansion does not include ranks 6–12 in the summary. | |
| Fixed comparison | Reset, switch several modes, and edit likes. | Baseline IDs remain C05, C10, C03, C08, C09. Initial Most liked overlap is 1/5; entered C02, C07, C12, C01; left C05, C10, C08, C09. | |
| Reset | Edit likes, select Mixed/Unclear, expand all, then Reset demo. | All original likes restored; Most liked active; five shown; editor C01/92. Selecting Mixed next reveals Supportive as the start. | |
| Refresh | Edit likes and reload the page. | Same fixed initial dataset and initial state return. | |
| Category reasons | Open Why this label? on several cards. | Each reveals its own one-sentence reason; no stance is inferred from emoji alone. | |
| Keyboard | Use Tab and Shift+Tab; activate buttons with Enter/Space; use select arrow keys; open a reason. | Visible focus; labeled controls; all actions usable without a mouse; summaries accessible. | |
| Desktop and mobile | Inspect at 1280px, 800px, 390px, and 320px wide; also at 200% browser zoom. | Readable content, no horizontal page overflow, controls fit; narrow layouts place summaries below the comments. | |
| Assistive technology | Navigate with a screen reader and change sorting. | Controls have names and state; changes announce first-five IDs and overlap; label meaning is not conveyed only by color. | |

Category-count sequences above always use Supportive / Concerned/Opposed / Conditional / Unclear.

## Temporary-data edge cases

Use the browser developer console after opening the page. These temporary arrays do not alter the fixed dataset or the displayed state. Console snippets are test instructions, not additional application dependencies.

### One empty category

```javascript
const noUnclear = INITIAL_COMMENTS.filter(c => c.category !== 'Unclear');
sortComments(noUnclear, 'mixed', 'Unclear').map(c => c.id);
```

Expected: `C05, C10, C03, C09, C06, C11, C01, C02, C07` — 9 unique IDs, no Unclear comments, no infinite loop even when the starting category is empty.

Actual result:

### Unequal category sizes

```javascript
const uneven = INITIAL_COMMENTS.filter(c =>
  ['C01', 'C05', 'C09', 'C10', 'C03', 'C11'].includes(c.id)
);
sortComments(uneven, 'mixed', 'Supportive').map(c => c.id);
```

Expected: `C05, C10, C03, C09, C11, C01` — sizes 3/1/2/0, all six IDs exactly once. Exhausted categories are skipped; remaining comments are retained.

Actual result:

### Entirely empty set

```javascript
sortComments([], 'mixed', 'Supportive');
```

Expected: `[]`, returning immediately.

Actual result:

## Starting-category comparison experiment

Keep initial data, labels, likes, timestamps, and within-category sorting constant. Change only the Mixed starting category. Record first-five IDs in order, category counts, overlap, entered/left IDs, and rank changes. Use the four expected sequences above to check the implementation, not as participant observations.

The question is whether a change in starting category changes visible membership or position. With this 3/3/3/3 dataset, the starting category gets two of the first five slots. This experiment does not establish a fairest rule or measure anyone's attitude.

Student observations/results:

## Checks actually executed by Codex

### Automated logic checks — 13 groups passed

Executed using the bundled Node.js runtime. The test runner extracted the actual inline JavaScript through the pure sorting, summary, parsing, and state-initialization functions into a Node VM. Assertions were checked against independently specified expected orders; it did not execute DOM interactions.

1. Exactly 12 unique records, three per category, valid likes/timestamps, and nonempty text/reasons.
2. Full Most liked ordering, including the timestamp and ID tie-breaks.
3. Full Newest ordering, including the ID tie-break.
4. Mixed starting Supportive: exact first five, 12 unique output IDs, correct composition.
5. Mixed starting Concerned/Opposed: same checks.
6. Mixed starting Conditional: same checks.
7. Mixed starting Unclear: same checks.
8. Fixed baseline and initial Most liked overlap, entered/left IDs, and category counts.
9. C04 changed to 999 likes: Most liked changes, Newest/Mixed stay unchanged, initial source and baseline remain unchanged.
10. All 16 possible subsets of categories × four starts (64 configurations): termination, matching output length, no duplicate or omitted IDs, including all-empty input.
11. Unequal 3/1/2/0 distribution: exact full expected order.
12. Input parsing: invalid forms rejected, valid integers accepted, unsafe integers rejected.
13. Reset state factory: fresh independent data, original likes, Most liked, Supportive, and collapsed state.

### HTTP availability check — passed

The optional local preview returned HTTP 200 with Content-Type text/html. This verifies serving the file, not browser rendering or behavior.

### Browser tests — not executed

The Playwright package was available, but its expected Chromium executable was absent. No browser was installed. Opening a preview is not counted as testing. Direct-file execution, actual button/form events, complete Reset interaction, visual layout, keyboard behavior, and screen-reader announcements remain unverified in a browser. The manual actual-result fields above are intentionally blank.

No participant testing was performed. No personal reflection has been written for the student.


## Deployment verification — 2026-10-12 (executed by Codex)

This section records later checks and supplements the earlier historical verification limits. The original notes and blank student/manual result fields above are preserved. These are automated checks by Codex, not tests performed by the student.

- Created a separate public repository: https://github.com/Ella-YuchenZhou/same-post-different-views
- GitHub Pages source: main branch, repository root.
- Published page: https://ella-yuchenzhou.github.io/same-post-different-views/
- GitHub reported the initial deployment commit 580128a82fcfe3e11e9e508cd4de77d83ed4b16a as built with no build error.
- Playwright used the installed Google Chrome executable in headless mode to test the actual HTTPS page. No additional browser installation was needed.

Actual browser automation results: all ten check groups passed.

1. HTTPS page returned HTTP 200; title and initial Most liked first five matched expectations.
2. Expansion produced exactly the expected 12 unique IDs, including like/time/ID tie handling.
3. Newest full ordering matched the reference.
4. All four Mixed starting categories produced expected first-five IDs, overlap counts, and category counts.
5. Editing C04 to 999 likes changed Most liked only; Newest/Mixed ordering and fixed baseline remained correct.
6. Empty, negative, decimal, decimal-form integer, and exponent-form inputs were rejected without changing data.
7. Full Reset interaction restored original likes, Most liked, five comments, and Supportive as the Mixed start.
8. Keyboard Enter activated a sorting button and a classification-reason disclosure.
9. No horizontal page overflow was detected at viewport widths 1280, 800, 390, and 320 CSS pixels.
10. Refresh restored defaults; no JavaScript page errors were observed during the checks.

Remaining limits: no real participant testing, no physical mobile-device testing, no screen-reader testing, no exhaustive keyboard navigation audit, no 200% zoom check, and no comprehensive visual-design/accessibility audit. Viewport overflow assertions do not establish full visual correctness. No personal reflection is supplied.
