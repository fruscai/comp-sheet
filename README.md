# Comp Sheet

Price a property from recent nearby sales. One HTML file, no install, no
account, no internet.

Download [`comp-sheet.html`](comp-sheet.html), double-click it, start typing.
That's the whole thing.

```bash
open comp-sheet.html          # macOS
```

There is no `npm start`, no `npm install`, no build step and no server. There's
no `package.json` because there's nothing to install — it's one file that opens
in a browser.

## What it does

The sales comparison approach, worked the way it is worked on paper — only faster.

1. Takes the price each comparable actually sold for.
2. Adjusts that price for every way the comparable differs from the subject —
   building area, age, condition, bathrooms, loading doors.
3. Applies a market conditions adjustment for the time since the sale.
4. Weights most heavily the comparables that needed the least adjustment, since
   requiring little adjustment is what being comparable means.
5. Averages what remains. That is the indicated value.

Every comparable gets its own adjustment detail:

> **124 Oak Street** — sold 5 months ago for $685,000 · 29% weight
>
> | | |
> |---|---|
> | **Market conditions** — sold 5 months ago; market up 3% per year | +$8,480 |
> | **Building area** — subject is 130 square feet smaller | −$14,300 |
> | **Age** — subject is 2 years older | −$1,400 |
> | **Site area** — subject has 174 sq ft less site area | −$522 |
> | **Indicated value** | **$677,258** |

## Importing from a file

A CoStar export, an MLS download, or anything saved out of Excel. Columns can be
in any order and extra columns are ignored.

First it asks what is in the file:

- **Comparable sales** — every row is a sale; the subject section is left alone.
- **The subject property** — fills the subject section only.
- **Both** — the subject is one of the rows; you pick which and the rest become
  comparable sales.

So the subject and the comparables can come from two separate exports, imported
one after the other, or from a single file holding everything.

Then it reads the heading row, works out which column is which, and shows you
every column with a real value beside it and what it made of it. Fix anything
wrong from a dropdown. Nothing is brought in until you've looked.

Sample files covering all of these are in [`samples/`](samples).

Acres are converted to square feet, conditions written as words are read as the
1–5 scale, and rows without a sale price are left out and counted.

The heading names it recognises are a list in the file (`SYNONYMS`) — if your
export uses something it doesn't know, either fix that one column in the review
step or add the name to the list.

## Six kinds of property

Pick one and the boxes change to match.

| Kind | What you fill in |
|---|---|
| Residential | Square feet, beds, baths, garage, lot, year, condition |
| Office | Square feet, year, parking, lot, condition, yearly rent |
| Retail | Square feet, year, street frontage, parking, lot, condition, yearly rent |
| Industrial | Square feet, year, ceiling height, loading doors, lot, condition, yearly rent |
| Mixed use | Square feet, units, year, lot, condition, yearly rent |
| Land | Lot square feet, road frontage |

For the income-producing types there is also a rent check — what the comparables
sold for relative to the rent they produce. It never feeds the indicated value;
it sits beside it as a cross-check:

> Rent check: those 3 comparables sold at approximately **9.3 times** annual
> rent. The subject produces $420,000 annually, indicating **$3,915,000**.

## Keeping your work

Two safety nets, neither of which sends anything anywhere.

- **The browser remembers.** Close the tab by accident and it's all still there
  when you come back. There is a "Clear it" link if you would rather it did not.
- **Save.** Writes out a copy of the whole page with your numbers
  tucked inside, named after the address. That file *is* the tool plus your
  data — keep it in a folder, back it up, or email it to someone. They
  double-click it and see exactly what you saw.

## Adjustment rates

Under **Adjustment rates** you set what each element of comparison is worth —
per square foot of building area, per bathroom, per year of age, per grade of
condition, and the annual rate of market change.

**These ship as guesses.** They're reasonable nationally and wrong for any
specific neighborhood. If your sales keep landing far apart, these are usually
why. Someone who knows the local market should set them once. They're shakier
for commercial than residential — office and industrial values swing enormously
by submarket in a way one national number can't capture.

Rates can be exported to a file and imported back, so an office can share one
calibrated set.

## What it isn't

Not an appraisal. A lender will not accept it.

It can't see that the building backs onto a freeway, that the photos are
terrible, that there's a lien on it, or that the market turned last month. Two
great comps beat six mediocre ones. Walk the property and use your judgment.

## Privacy

Everything happens in your browser. There is no server, no account, no
analytics, and no network code of any kind in the file — no `fetch`, no
`XMLHttpRequest`, no external stylesheets, fonts or images. Open it with the
wifi off and it works exactly the same.

## License

MIT — see [LICENSE](LICENSE).
