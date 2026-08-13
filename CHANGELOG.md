# Changelog

## Unreleased

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
- Project docs: `CLAUDE.md`, `DECISIONS.md`, this file.

### Fixed
- **Column matching fired on unrelated headings.** A `-1` returned by `indexOf`
  for "not found" was being compared against arithmetic that also produced `-1`,
  so any heading the same length as a known name matched it. `Submarket` was
  being read as the lot size, because both "Submarket" and "land area" are nine
  characters long. Replaced with a real word-boundary test.
- **The table started with three empty rows** and padded back to three whenever
  work was reloaded. It now starts with one and importing clears the empties.
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
person who actually uses it. The reasoning is in [DECISIONS.md](DECISIONS.md),
and it's the most important thing in this repository.
