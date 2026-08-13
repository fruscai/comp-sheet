# Changelog

## Unreleased

### Added
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
- Paste-from-spreadsheet follows the active property kind, and the column order
  it asks for updates to match.

---

## The rewrite

Before any of the above, this was a different program: a commercial appraisal
platform of 3,407 lines with five document types, four valuation approaches,
California Proposition 13 reassessment, San Francisco transfer tax brackets and
a statistical reliability grade.

It was replaced wholesale with an 811-line single-purpose tool aimed at the
person who actually uses it. The reasoning is in [DECISIONS.md](DECISIONS.md),
and it's the most important thing in this repository.
