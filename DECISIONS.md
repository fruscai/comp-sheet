# Decisions

Why this is the way it is, including the things that were tried and dropped.

---

## 1. The first version was thrown away

**What happened.** The brief was "a real estate valuation tool using CoStar plus
our own data, as an in-company tool." Scoping questions came back asking for all
three appraisal approaches reconciled, all four commercial asset classes, and a
desktop app. So that got built: 3,407 lines, five document types (sale comps,
lease comps, rent rolls, operating statements, construction cost reports), four
valuation approaches, California Proposition 13 reassessment solved through a
loaded cap rate, San Francisco transfer tax brackets, and a reliability grade
computed from Kish effective sample sizes.

It worked. All ten of its internal arithmetic self-checks passed.

It was still wrong. The actual user is an assistant helping an agent price a
property — not a REIT analyst. The verdict was blunt: *"WAY too complicated…
super corporate jargon… needs to be usable by a secretary essentially."*

**What was kept.** Nothing but three ideas: run everything locally, never
silently invent a missing value, and always show the arithmetic.

**The lesson, which is the point of this file.** Scoping questions asked of
someone enthusiastic will happily expand scope. They do not reveal who is going
to sit in front of the thing. Establish the user before establishing the feature
list.

The old version is archived outside this repo. It is not a fallback, and none of
it should be merged back in.

---

## 2. Dollar adjustments, not percentages

Every nudge is a whole dollar figure: `$12,000` a full bathroom, `$110` a square
foot.

Percentages are how appraisal software does it and how the discarded version did
it. But an agent can look at "$12,000 a bathroom" and say *"not around here,
it's more like eight"*. Nobody argues productively with "a 4.2% net adjustment."
Making the numbers arguable is the point — they ship as guesses and need local
correction.

The one exception is market movement, which is genuinely a rate: prices moving
X% a year, compounded over the months since each sale.

---

## 3. No Run button

The answer recalculates on every keystroke.

This is the entire justification for the tool existing. Doing this on paper is
perfectly possible — it's just slow. A tool that makes you fill in a form and
then press a button is not meaningfully faster than paper. One that shows the
price moving as you type is.

Consequence: `refresh()` must never rebuild the sales table, or focus would jump
out of the box mid-word. It redraws only the answer, the rent line and the
explanation cards.

---

## 4. Sales that need too much nudging get dropped

If adjusting a sale moves it more than **35%** of its price, it's excluded from
the answer and its card says why: *"We had to change it by 41%, which means it
is not really comparable to your house."*

The alternative — quietly averaging in a bad comp — produces a confident number
built on a property that isn't relevant. Better to lose a data point loudly.

Related: a blank box is skipped, never filled with an assumed value. This
carried over from the discarded version, where it was the one principle worth
keeping.

---

## 5. Weighting is closeness times freshness

```
closeness = 1 / (1 + 3 × how much we nudged it)
freshness = 0.5 ^ (months old / 18)
weight    = closeness × freshness
```

A sale that barely needed adjusting is a better guide than one that needed a
lot, because needing little adjustment *is* what similar means. And an old sale
describes an older market.

Both curves are gentle on purpose. Something sharper would let one comp dominate
completely, and the answer would swing wildly as sales are added.

---

## 6. The range is the spread of the sales, not statistics

The band is drawn from how far apart the adjusted sales land, padded to a
minimum of ±3% and capped at ±12%. The confidence wording is four plain buckets:
*Solid, Reasonable, Rough, Very rough.*

The discarded version computed a proper standard error using Kish effective
sample sizes. It was more sophisticated and less honest: comps are not a random
sample of anything, so the statistical machinery implied a rigour that wasn't
there. "These sales disagree quite a bit, treat this as a wide guess" is both
truer and more useful.

---

## 7. Saving: browser memory plus a self-contained file

Two independent nets.

The browser remembers automatically via `localStorage`, so an accidentally
closed tab costs nothing.

**Save to a file** clones the live page, strips out everything generated at
runtime, appends the data as a `<script type="application/json">` block, and
downloads the result named after the address. That file *is* the whole tool plus
the data — double-click it and it reopens exactly as it was.

Chosen over a `.json` export because a non-technical user knows what to do with
a file that opens. A JSON file requires the tool, an import step, and knowing
those two things go together. It also means a saved sheet can be emailed to
someone who has never seen the tool, and it just works.

Two details that matter: `<` is escaped to `<` in the payload so an address
containing `</script>` can't truncate the file, and any existing data block is
removed before the new one is added so re-saving doesn't stack copies. Both are
tested.

On load, data baked into the file wins over browser memory.

---

## 8. Six broad property kinds, not residential subtypes

A narrower split was proposed first — house, condo, small multi-unit, land —
with condo-specific HOA handling and a gross rent multiplier for duplexes. The
call was for broad classes instead: **residential, office, retail, industrial,
mixed use, land.**

The method is identical for all six. Only three things change: which boxes
appear, what a square foot is worth, and whether rent is relevant. So the fields
are a data table (`CLASSES`) and every part of the interface builds itself from
it. Adding a kind is one entry plus an example.

Switching kinds warns before clearing, since boxes that don't exist in the new
kind would otherwise silently stop counting. Address, price, date, square feet
and year survive every switch.

---

## 9. Rent is a second opinion, never part of the answer

For the four income-producing kinds there's an optional yearly rent column. With
two or more sales carrying it you get one extra sentence: *"those 3 sales went
for about 9.3 times the yearly rent they bring in, which puts yours at
$3,915,000."*

It deliberately does not feed the main number. Mixing two methods into one
figure hides which one is doing the work. Shown side by side, a disagreement is
visible and useful — and if the gap exceeds 15% the tool says so and tells you
to work out which one you trust.

This is the rent multiple agents already use. It is not a cap rate, and it
should not become one.

---

## 10. Fields wrap instead of the table scrolling

The sales table initially ran 1,210px wide inside a 1,014px space, hiding the
condition dropdown and the delete button off the right edge on an ordinary
laptop. Fixed by letting column headers wrap to two lines so the columns
themselves can be narrow while the headings stay readable words rather than
abbreviations like "Bd/Ba".

Every kind now fits within 1,014px at a 1,280px window. Narrower windows still
scroll sideways — see the rough edges in [CLAUDE.md](CLAUDE.md).

---

## 11. Importing shows its guess before it acts

Files can be dropped in any column order. The heading row is read and each
column matched to a box, then **every** column is shown back — its heading, a
real value from the file, and what we made of it — with a dropdown to correct
anything wrong. Nothing is added until the user agrees.

The alternative was demanding a fixed column order, which is what the first
paste feature did. It's less code and it's useless: nobody rearranges a CoStar
export before importing it, they just give up.

Showing the guess also handles the honest problem, which is that the list of
heading names (`SYNONYMS`) is assembled from what these exports *probably* call
things. It has never been checked against every real export. A wrong guess is
therefore expected, and the interface is built around correcting one in two
seconds rather than around being right every time.

A bug worth remembering: matching originally used `indexOf` compared against
index arithmetic, and when `indexOf` returned `-1` for "not found" the
arithmetic sometimes also produced `-1`, so the comparison passed. Every heading
matched every synonym of the same character length — `Submarket` was read as the
lot size because "land area" is also nine characters. Found by importing a file
with deliberately awkward headings and checking *what each column matched to*
rather than just that nothing crashed. Test for the mapping, not for the absence
of an exception.

---

## 12. Files live in Downloads

Deliverables go straight to `~/Downloads`, not into a new folder in the home
directory. Downloads is where this user actually looks. The one exception is
this repo, which needs its own folder for git — so it sits at
`~/Downloads/comp-sheet/`.
