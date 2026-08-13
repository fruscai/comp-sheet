# Changelog

## Unreleased

### Added

There is now an Import CSV button under each section heading, one under Subject
property and one under Comparable sales. The button you press is what the file is
for, so there is no separate import screen and nothing to choose. Each one takes
a dropped file or pasted rows. Importing comparables does not touch the subject,
and importing a subject does not touch the comparables, so the two can come from
separate exports.

The import reads abbreviated headings. Gar., Cond., Sq. Ft., Yr. Blt, SP, COE,
Close Dt and Bdrms all work, along with other short forms these exports use.
Two-letter names are only accepted on an exact or whole-word match, so they
cannot attach themselves to an unrelated column.

Sample files are in the samples folder. Three of them are deliberately awkward so
the header matching can be tested against something realistic. One has the
subject property included in the file, one is comparables only with junk columns
and a row missing its price, and one is tab separated with a rent column.

There are six property types now. Residential, office, retail, industrial, mixed
use and land. Choosing a type changes which boxes appear, what a square foot is
worth, and how fast prices are assumed to be moving. All of it comes from one
table in the file, so the subject form, the table headings, the rows and the
adjustment rates are all built from the same place.

For the four types that produce rent there is a rent check. If two or more
comparables carry an annual rent figure, the page works out what those sales went
for relative to the rent they produce and says what that implies for the subject.
It never feeds the indicated value. It sits beside it so a disagreement between
the two is visible.

Work is kept in two ways. The browser remembers what you typed, so closing the
tab by accident costs nothing. Save writes out a copy of the whole page with your
figures inside it, so opening that file later brings everything back, including
which property type you were using.

Every property type has its own worked example, so the Load example button does
something useful whichever type you are on.

### Changed

The wording throughout now uses the language of the trade. The screen reads
subject property, comparable sales, adjustments, indicated value, market
conditions, condition and gross adjustment. It previously used plainer words like
Shape, nudge and Suggested price, which are not what a CMA says and are not what
you would want to hand to a client. Condition is graded poor, fair, average, good
and excellent. Confidence reads high, moderate, limited or insufficient.

Adjustment lines are laid out the way an adjustment grid reads. The element being
adjusted comes first, then how the two properties differ, then the amount.

Clear all now also clears what the browser was holding, rather than leaving it to
come back on the next visit.

Pasted rows go through the same reading and checking step as a dropped file, so
the columns no longer have to be in a fixed order.

### Fixed

The comparables table used to open with empty rows sitting in it, and it refilled
them whenever saved work was reloaded. It now says there are no comparable sales
yet, and rows only appear when you import a file or press Add.

The adjustment detail used to show a card for every comparable saying it could
not be counted, back when the subject property boxes were still empty. Those
cards exist to show the arithmetic, so when there is no arithmetic there is now
no card and no heading. The banner at the top already says what is missing.

Switching property type used to empty the subject property boxes while leaving
the comparables alone. Every comparable then reported that it could not be used,
which made the whole page look broken. Anything the new type still has a box for
now carries across, the same way the comparables already did.

Column matching used to fire on headings that had nothing to do with the field.
The cause was indexOf returning -1 for "not found" and being compared against
arithmetic that could also produce -1, so the comparison passed. Any heading the
same length as a known name matched it, which is how Submarket came to be read as
the lot size. Both are nine characters. It now uses a word-boundary test.

The comparables table ran wider than the space it had, 1,210 pixels inside 1,014,
so the condition dropdown and the delete button sat off the right edge on a
normal laptop. Column headings now wrap onto two lines, which lets the columns be
narrow while the headings stay readable words rather than abbreviations.

Saved work written by an older version used to be restored over newer code. The
snapshot in question came from the switching bug above, so opening a fixed copy
kept showing the broken state and it looked as though the file had never changed.
Saved work now carries a version number and anything unrecognised is left alone.

Sale dates are recognised in more forms, including Closed, Date closed,
Settlement date and Recording date. Files using those headings previously had
every row thrown out for having no sale date.

Wording in the adjustment detail is correct for single items now. It used to say
"1 fewer full baths" and "1 fewer garage spaces".

---

## The rewrite

Before any of the above this was a different program. It was a commercial
appraisal platform of 3,407 lines, with five document types, four valuation
approaches, California Proposition 13 reassessment, San Francisco transfer tax
brackets and a statistical reliability grade. It worked, and all ten of its
internal arithmetic checks passed.

It was replaced with an 811 line tool aimed at the person who actually uses it.
The reasoning is in CLAUDE.md and it is the most useful thing in this repository.
