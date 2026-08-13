# Working on Comp Sheet

Read this before changing anything. The hard part of this project is not the
code — it's resisting the urge to make it bigger.

## Who uses this

An assistant helping a real estate agent. Someone running property sales out of
a spare room. Not an analyst, not an appraiser, not a fund.

If a change only makes sense to someone with a finance background, it does not
belong here. This tool already got built the wrong way once — as an
institutional appraisal platform — and had to be thrown out. See
[DECISIONS.md](DECISIONS.md).

## Two rules that override everything

**1. Use the vocabulary of the trade, written clearly.**

This is a valuation tool. Use the words a CMA or an appraisal uses — *subject
property, comparable sales, adjustments, elements of comparison, indicated
value, market conditions, condition, gross adjustment*. An agent's assistant
reads these every day.

Two failure modes, and this project has hit both:

*Consultant filler* — "dispersion band", "reconcile the indications", "gross
adjustment burden", "ingest". Words that add ceremony, not meaning.

*Baby talk* — "Shape" for condition, "nudge" for adjustment, "How we got there"
for the adjustment detail, "Bring in a file" for import. Writing down to the
reader is its own kind of disrespect, and it makes the output useless to hand to
a client.

| Wrong (either way) | Right |
|---|---|
| Shape · what kind of shape is it in | Condition |
| nudge · change | adjustment |
| the house you're pricing | subject property |
| recent houses sold nearby | comparable sales |
| How we got there | Adjustment detail |
| Suggested price | Indicated value |
| Solid / Rough | High confidence / Limited confidence |
| Somewhere between X and Y | Range of value: X to Y |
| dispersion band, reconcile, ingest | (never) |

Adjustment lines are written the way a grid reads — the element being adjusted,
then how the two properties differ, then the amount:

```
Building area      Subject is 130 square feet smaller      −$14,300
Market conditions  Sold 5 months ago; market up 3% per year  +$8,480
```

Full sentences. Professional register. No exclamation marks, no cheerleading,
and no shortening a real term to sound friendlier.

**2. Faster than doing it by hand, or it has no reason to exist.**

The price updates on every keystroke. There is no Run button and there must
never be one. Anything that adds a step between typing and seeing the answer is
a regression.

## Hard constraints

- **One file.** `comp-sheet.html`. No build step, no bundler, no `npm install`,
  no dependencies. Someone must be able to double-click it.
- **No network code. Ever.** No `fetch`, no `XMLHttpRequest`, no WebSocket, no
  external stylesheets, fonts, images, or scripts. Open it with wifi off and it
  behaves identically. This is a promise printed on the page — do not break it.
- **Nothing leaves the machine.** `localStorage` and Blob downloads only.
- **ES5-flavoured JavaScript.** `var`, `function`, no arrow functions or
  template literals in the shipped file. It runs off a `file://` path on
  whatever browser someone happens to have.

## How it's put together

Everything is data-driven off one table so adding a property kind doesn't mean
touching the rendering code.

```
CLASSES          the six property kinds; fields + starting dollar amounts
  └ fields[]     built by F(); each has a `kind` that says how it behaves
compare()        one sale vs. yours → a list of plain-English nudges
work()           runs every sale, weights them, produces the answer
draw*()          render from CLASSES: drawSubject, drawHead, drawRows, drawRates
snapshot/restore save and reload, including which property kind was active
```

Field `kind` values, and what `compare()` does with each:

| kind | behaviour |
|---|---|
| `size` | the main measurement; worth so much per unit |
| `land` | lot area; worth so much per **1,000** sq ft |
| `count` | whole countable things; worth so much each |
| `age` | year built; worth so much per year newer |
| `shape` | the 1–5 condition picker |
| `rent` | never nudges anything; drives the rent multiple only |

### Adding a property kind

Add an entry to `CLASSES`, add its key to `CLASS_ORDER`, add an example to
`EXAMPLES`. Nothing else. The subject form, table header, rows, settings panel
and paste instructions all build themselves from it.

`EXAMPLES[kind].mine` and each sale's array must be **in the same order as
`fields`** — they're matched by index.

### Teaching it a new column heading

`SYNONYMS` maps a box to the heading names real exports use. Add freely — a name
that never turns up costs nothing.

Matching is exact (100) → whole words (80) → any substring (62), with a small
bonus for longer names so `Lot Size` beats a bare `Lot`. Then columns are
assigned strongest-first, one column per box.

Two-letter names like `sp`, `fb`, `yb` are safe: they only ever land on an exact
or whole-word hit, never as a substring. Names of four or more characters also
match as a substring, so watch those — `lot`, `sf` and `rent` sit inside plenty
of unrelated headings. There was already one bug here where every nine-character
heading matched every nine-character synonym; see DECISIONS.md.

Where the line falls, measured against the files in `samples/`:

| Heading in the file | Result |
|---|---|
| `Sq. Ft.` `# Beds` `Baths (Full)` `Yr. Built` `Gar.` `Cond.` | matched — punctuation is stripped first |
| `Situs Addr.` `Close Dt` `Sold $` `RBA` `GLA` `COE` | matched |
| `MLS #` `LP` `DOM` `Buyer` `Submarket` | correctly left alone |
| `COL_A` `COL_B` | no match; the import refuses and says why |

Unrecognised headings are never guessed at — they are left unassigned for the
user to set in the review step.

### Adding a field to an existing kind

Add one `F(...)` line and a starting amount in that kind's `rates`. If it needs
behaviour none of the six `kind` values cover, add a branch in `compare()` —
and write the sentence it produces as a sentence.

## Testing

There's no test runner. Check it like this:

```bash
# syntax
node -e 'const s=require("fs").readFileSync("comp-sheet.html","utf8");
  require("fs").writeFileSync("/tmp/cs.js",s.split("<script>")[1].split("</script>")[0]);'
node --check /tmp/cs.js
```

Then serve the folder and drive it in a browser. Useful checks, all of which
have caught real bugs:

- Loop all six kinds calling `switchClass(k); loadExample();` and confirm each
  gives a different sensible price. **Stub `window.confirm` to return true
  first** — `switchClass` asks before clearing, and a headless `confirm`
  returns false, so every kind silently stays residential and every result
  looks identical. That exact thing happened.
- `document.querySelector('.sheet').scrollWidth` must be **≤ the `.scroll`
  container's `clientWidth`** at 1280px wide. The table overflowed by 196px
  once and hid two columns off-screen.
- Type into a row and confirm `document.activeElement` is unchanged. `refresh()`
  must never rebuild `#rows`, or the cursor jumps out mid-word.
- Save, switch kinds, restore, and confirm the property kind and all fields come
  back.
- Import a file whose headings are deliberately awkward — extra columns, a
  `Submarket` column, acres instead of square feet, conditions as words, an
  address with a comma in it, and a row with no price. Check what each column
  matched to, not just that it didn't crash.

## Things that look like improvements and are not

- A Run button, a wizard, or steps.
- Cap rates, DCF, IRR, NPV, price-per-buildable-foot, absorption.
- Percentage-based adjustments. Agents argue in dollars.
- Auto-filling a missing field with an assumption. Blank means skipped, and the
  card says so.
- Charts. The per-sale cards are the explanation.
- A backend, accounts, or sync.

## Known rough edges

- Below ~1,200px browser width the sales table scrolls sideways. It scrolls
  cleanly rather than clipping, but stacking each sale into a card on narrow
  screens would be better.
- Typing `685000` stays `685000`; it isn't reformatted to `685,000` as you go.
- Dates are typed by hand — no date picker.
- The starting dollar amounts are national guesses and are much weaker for the
  commercial kinds than for residential.
