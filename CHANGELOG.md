# Changelog

## Unreleased

### Added
- **An Import CSV button under each section heading.** One under *Subject
  property*, one under *Comparable sales*. Which button you press is what the
  file is for — no separate import screen and nothing to choose. Each accepts a
  dropped file or pasted rows. Importing comparables leaves the subject alone
  and vice versa, so the two can come from separate exports.
- **Abbreviated headings are recognised.** `Gar.`, `Cond.`, `Sq. Ft.`, `Yr. Blt`,
  `SP`, `COE`, `Close Dt`, `Bdrms` and similar short forms these exports really
  use. Two-letter names only match exactly or as a whole word, so they cannot
  land on an unrelated column.
- **Sample files** in `samples/` covering the three shapes this has been tested
  against: a file with the subject included, a comparables-only file with junk
  columns and a row missing its price, and a tab-separated industrial file.

### Changed
- **Rewritten in the language of the trade.** The screen now reads *subject
  property*, *comparable sales*, *adjustments*, *indicated value*, *market
  conditions*, *condition* and *gross adjustment* — the words a CMA uses —
  instead of the plainer-than-plain wording it had ("Shape", "nudge", "How we
  got there", "Suggested price"). Condition is graded poor / fair / average /
  good / excellent. Confidence reads high / moderate / limited / insufficient.
- **Adjustment lines are laid out as a grid reads**: the element being adjusted,
  how the two properties differ, then the amount.

### Added
- **Bring in sales from a file.** Drop a CoStar export, an MLS download or
  anything saved out of Excel. Columns can be in any order and extra columns are
  ignored — the heading row is read and each column matched to a box. You then
  see every column, a real value from the file, and what we made of it, with a
  dropdown to correct anything wrong. Nothing is added until you say so. Rows
  with no sale price are left out and counted.
  - Acres are converted to square feet when the heading says acres.
  - Conditions written as words (`Excellent`, `Good`, `Average`, `Poor`) are read
    as the 1–5 scale.
  - Addresses containing commas survive, because the file is parsed properly
    rather than split on commas.
- **Six property kinds** — residential, office, retail, industrial, mixed use
  and land. Picking one changes which boxes appear, what a square foot is worth
  and how fast prices are assumed to be moving. Driven by a single `CLASSES`
  table, so the subject form, table header, rows, settings and paste
  instructions all build themselves from it.
- **Rent second opinion** for the four income-producing kinds. With two or more
  sales carrying a yearly rent, the page shows what those sales went for
  compared to the rent they bring in, and what that implies for yours. It never
  feeds the main number, and it flags a gap wider than 15%.
- **Saving, two ways.** The browser now remembers your work automatically, with
  a "Forget it" link and a warning if the browser refuses to remember anything.
  "Save to a file" writes out a copy of the whole page with the data baked in —
  reopening that file restores everything, including which property kind was
  active.
- A worked example per property kind, so "See an example" does something useful
  whichever kind you're on.
- Project notes: `CLAUDE.md` and this file.

### Fixed
- **The sales table no longer starts with empty rows.** It opens saying "No
  sales yet" and rows appear only when you bring in a file or press Add.
- **"How we got there" no longer fills up with cards that explain nothing.**
  When your own boxes weren't filled in yet, every sale produced a card reading
  "Not counted — need the square feet of yours first". Those cards exist to show
  the arithmetic, so when there is no arithmetic there is now no card, and the
  heading stays hidden.
- **Switching property kind wiped your property's boxes** while keeping the
  sales' values, so every sale reported that it couldn't be used. Anything the
  new kind still has a box for now carries across, the same way the sales do.
- **Column matching fired on unrelated headings.** A `-1` returned by `indexOf`
  for "not found" was being compared against arithmetic that also produced `-1`,
  so any heading the same length as a known name matched it. `Submarket` was
  being read as the lot size, because both "Submarket" and "land area" are nine
  characters long. Replaced with a real word-boundary test.
- **The sales table hid two columns.** It ran 1,210px wide inside 1,014px of
  space, pushing the condition dropdown and the delete button off the right edge
  on an ordinary laptop. Column headers now wrap to two lines so the columns can
  be narrow while the headings stay readable. Every kind now fits.
- Pluralisation in the explanation cards — "1 fewer full baths" and "1 fewer
  garage spaces" now read correctly.
- Dropped a redundant clause; each card's heading already says when the sale
  happened, so the first line no longer repeats it.
- Sale-date detection now recognises `Closed`, `Date closed`, `Settlement date`
  and `Recording date`. Files using those headings previously had every row
  thrown out for having no sale date.

### Changed
- `Start over` now also clears what the browser remembered, rather than leaving
  it to reappear on the next visit.
- Pasting rows now goes through the same reading and checking step as a dropped
  file, so the columns no longer have to be in a fixed order.

---

## The rewrite

Before any of the above, this was a different program: a commercial appraisal
platform of 3,407 lines with five document types, four valuation approaches,
California Proposition 13 reassessment, San Francisco transfer tax brackets and
a statistical reliability grade.

It was replaced wholesale with an 811-line single-purpose tool aimed at the
person who actually uses it. The reasoning is in [CLAUDE.md](CLAUDE.md), and it
is the most useful thing in this repository.
