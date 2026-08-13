# Contributing to Comp Sheet

Read this before changing anything. The difficult part of this project is not the
code. It is resisting the urge to make it larger.

## Who uses this

An assistant supporting a real estate agent. Someone running property sales from
a spare room. Not an analyst, not an appraiser, not a fund.

If a change only makes sense to somebody with a finance background, it does not
belong here. An earlier version of this tool was an institutional appraisal
platform of 3,407 lines, with four valuation approaches, California Proposition
13 reassessment and a statistical reliability grade. It was replaced with the
current one because the audience is an agent's assistant, not an analyst.

## Two rules that override everything else

### Use the vocabulary of the trade, written clearly

This is a valuation tool, so it should use the words a CMA or an appraisal uses.
That means subject property, comparable sales, adjustments, elements of
comparison, indicated value, market conditions, condition and gross adjustment.
An agent's assistant reads these terms every working day.

There are two ways to get this wrong.

The first is consultant filler. Phrases such as dispersion band, reconcile the
indications, gross adjustment burden and ingest. These add ceremony rather than
meaning.

The second is writing down to the reader. Using Shape in place of condition,
nudge in place of adjustment, or How we got there in place of the adjustment
detail. That produces output which cannot be handed to a client.

| Wrong in either direction | Correct |
|---|---|
| Shape, what kind of shape is it in | Condition |
| nudge, change | adjustment |
| the house you are pricing | subject property |
| recent houses sold nearby | comparable sales |
| How we got there | Adjustment detail |
| Suggested price | Indicated value |
| Solid, Rough | High confidence, Limited confidence |
| Somewhere between X and Y | Range of value: X to Y |
| dispersion band, reconcile, ingest | never |

Adjustment lines are written the way a grid reads. The element being adjusted
comes first, then how the two properties differ, then the amount:

```
Building area      Subject is 130 square feet smaller        -$14,300
Market conditions  Sold 5 months ago, market up 3% per year   +$8,480
```

Write in full sentences and in a professional register. Avoid em dashes, avoid
sentence fragments used for emphasis, avoid bold text placed mid sentence for
effect, and never shorten a real term to make it sound friendlier.

### It must be faster than working by hand

The indicated value recalculates on every keystroke. There is no Run button and
there must never be one. Anything that introduces a step between typing and
seeing the result is a regression.

## Constraints that cannot be traded away

The tool is one file, comp-sheet.html. There is no build step, no bundler, no npm
install and no dependencies, because somebody has to be able to double-click it.

There is no network code of any kind. No fetch, no XMLHttpRequest, no WebSocket,
no external stylesheets, fonts, images or scripts. Opened with the wifi switched
off it must behave identically. This is a promise printed on the page and it
should not be broken.

Nothing leaves the machine. Only localStorage and Blob downloads are used.

The JavaScript is written in an ES5 style, using var and function rather than
arrow functions or template literals, because it runs from a file:// path in
whatever browser somebody happens to have.

## How it is put together

Everything is driven from one table, so adding a property type does not require
touching the rendering code.

```
CLASSES          the six property types, their fields and starting rates
  fields[]       built by F(), each carrying a kind that says how it behaves
compare()        one comparable against the subject, producing adjustment lines
work()           runs every comparable, weights them, produces the value
draw*()          renders from CLASSES: drawSubject, drawHead, drawRows, drawRates
snapshot/restore saving and reloading, including the active property type
```

Each field carries a kind, and compare() handles each one differently:

| kind | Behaviour |
|---|---|
| size | the main measurement, worth a set amount per unit |
| land | site area, worth a set amount per 1,000 sq ft |
| count | whole countable items, worth a set amount each |
| age | year built, worth a set amount per year |
| shape | the condition picker, poor through excellent |
| rent | never adjusts anything, drives the rent check only |

### Adding a property type

Add an entry to CLASSES, add its key to CLASS_ORDER, and add an example to
EXAMPLES. Nothing else is required. The subject form, the table headings, the
rows and the adjustment rates all build themselves from that entry.

EXAMPLES[type].mine and each sale array must be in the same order as fields,
because they are matched by position.

### Teaching it a new column heading

SYNONYMS maps a field to the heading names that real exports use. Names can be
added freely, since a name that never appears costs nothing.

Matching runs exact first at 100, then whole word at 80, then substring at 62,
with a small bonus for longer names so that Lot Size beats a bare Lot. Columns are
then assigned strongest first, one column per field.

Two letter names such as sp, fb and yb are safe, because they are only ever
accepted on an exact or whole word match and never as a substring. Names of four
characters or more also match as substrings, so those need watching. The names
lot, sf and rent all sit inside plenty of unrelated headings.

One bug here is worth remembering. Matching used indexOf compared against index
arithmetic, and when indexOf returned -1 for "not found" the arithmetic sometimes
also produced -1, so the comparison passed. Every heading matched every synonym
of the same character length, which is how Submarket came to be read as the lot
size, both being nine characters. Test what each column maps to, not simply that
nothing throws an error.

Measured against the files in samples:

| Heading in the file | Result |
|---|---|
| Sq. Ft., # Beds, Baths (Full), Yr. Built, Gar., Cond. | matched, punctuation is stripped first |
| Situs Addr., Close Dt, Sold $, RBA, GLA, COE | matched |
| MLS #, LP, DOM, Buyer, Submarket | correctly left unassigned |
| COL_A, COL_B | no match, and the import refuses and states why |

Unrecognised headings are never guessed at. They are left unassigned for the user
to set during the review step.

### Adding a field to an existing type

Add one F() line and a starting amount in that type's rates. If it needs
behaviour that none of the six kinds cover, add a branch in compare(), and write
the sentence it produces as an actual sentence.

## Why it works the way it does

Adjustments are expressed in dollars rather than percentages, so each one can be
compared against a known local figure and changed. Market conditions is the one
exception, because it is a rate.

Comparables requiring more than 35% gross adjustment are excluded and the card
states the figure. A blank box is omitted from the adjustment rather than filled
with an assumption.

Weighting is closeness multiplied by freshness, calculated as 1/(1+3 times gross
adjustment) multiplied by 0.5^(months/18). Requiring little adjustment is what
comparable means, and an old sale describes an older market. Both curves are
deliberately gentle, because sharper ones allow a single comparable to dominate
and the value then swings considerably as sales are added.

The range is the spread of the comparables rather than a confidence interval. An
earlier version computed a proper standard error using Kish effective sample
sizes. That was more sophisticated and less honest, because comparables are not a
random sample of anything and the statistics implied a rigour that was not
present.

The rent multiple never feeds the indicated value. Blending two methods into one
figure conceals which one is doing the work. Displayed alongside each other, a
disagreement between them is visible and useful.

Saving writes the entire page with the data held inside it, rather than producing
a JSON export. A non technical user knows what to do with a file that opens,
whereas a JSON file requires the tool, an import step, and knowing that those two
things belong together. Two details matter in the implementation. The opening
angle bracket is escaped in the payload so that an address containing a closing
script tag cannot truncate the file, and any existing data block is removed
before the new one is added so that repeated saves do not stack copies.

Import opens inside the section it fills. Both buttons previously opened one
shared panel positioned elsewhere on the page, which read as a single control
rather than two. Pressing Import beneath Subject property now expands the panel
inside that card. The button pressed is what the file is for, so there is nothing
to declare and nothing to select.

## Testing

There is no test runner. Check the syntax like this:

```bash
node -e 'const s=require("fs").readFileSync("comp-sheet.html","utf8");
  require("fs").writeFileSync("/tmp/cs.js",s.split("<script>")[1].split("</script>")[0]);'
node --check /tmp/cs.js
```

Then serve the folder and drive it in a browser. The following checks have each
caught a real bug.

Loop through all six types calling switchClass(k) then loadExample() and confirm
each produces a different and sensible value. Stub window.confirm to return true
first, because switchClass asks before clearing and a headless confirm returns
false, which leaves every type on residential and makes every result look
identical.

Confirm that the scrollWidth of .sheet is less than or equal to the clientWidth
of its .scroll container at 1280 pixels wide. An overflow here hides columns off
screen.

Type into a row and confirm that document.activeElement has not changed.
refresh() must never rebuild the rows, because the cursor then jumps out mid
word.

Save, switch types, restore, and confirm that the property type and every field
return correctly.

Import a file with deliberately awkward headings, meaning extra columns, a
Submarket column, acres in place of square feet, conditions written as words, an
address containing a comma, and a row with no price. Check what each column
mapped to, not simply that it did not crash.

## Changes that look like improvements and are not

Do not add a Run button, a wizard, or steps. Do not add cap rates, DCF, IRR, NPV,
price per buildable foot or absorption. Do not convert adjustments to
percentages, because agents argue in dollars. Do not auto-fill a missing field
with an assumption, because blank means omitted and the card says so. Do not add
charts, because the adjustment detail is the explanation. Do not add a backend,
accounts or sync.

## Known rough edges

Below approximately 1,200 pixels of browser width the comparables table scrolls
sideways. It scrolls cleanly rather than clipping, but stacking each comparable
into a card on narrow screens would be better.

Typing 685000 leaves it as 685000 rather than reformatting to 685,000 while
typing.

Dates are typed by hand and there is no date picker.

The supplied adjustment rates are national estimates and are considerably weaker
for the commercial types than for residential.

## Commit messages

Write them the way the owner writes them. One line, starting with the date, then
plainly what was done, joined with commas and "and".

    July 16, worked through exercises 1 through 4 in arrays and completed all housekeeping
    August 13, gitignored commands.md and updated decisions

Lower case after the date. No title case, no colons splitting a subject from a
description, no semicolons, and no multi paragraph body explaining the reasoning.
The reasoning goes in DECISIONS.md if it is a decision, or LOG.md if it is
something learned. The commit message says what changed and nothing else.
