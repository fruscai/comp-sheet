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

It does what an agent does on paper, just faster.

1. Takes what each property nearby actually sold for.
2. Nudges that price up or down for how it differs from yours — bigger, newer,
   an extra bathroom, more loading doors.
3. Adjusts for how long ago it sold, if prices have moved since.
4. Leans hardest on the sales that needed the least nudging, because those are
   the most similar and the best guide.
5. Averages what's left. That's the number.

Then it shows its work in plain sentences, one card per sale:

> **124 Oak Street** — sold for $685,000 five months ago
> Prices are up about 3% a year since then — **+$8,148**
> Yours has 130 fewer square feet — **−$14,300**
> Yours is 2 years older — **−$1,400**
> Which points to **$676,848**

## Bringing in sales from a file

Drop a CoStar export, an MLS download, or anything saved out of Excel. The
columns can be in any order, and extra columns are ignored.

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

For anything that earns rent, you also get a second opinion based on what those
sales went for compared to the rent they bring in:

> Another angle: those 3 sales went for about **9.3 times** the yearly rent they
> bring in. Yours brings in $420,000 a year, which points to **$3,915,000**.

## Keeping your work

Two safety nets, neither of which sends anything anywhere.

- **The browser remembers.** Close the tab by accident and it's all still there
  when you come back. There's a "Forget it" link if you'd rather it didn't.
- **Save to a file.** Writes out a copy of the whole page with your numbers
  tucked inside, named after the address. That file *is* the tool plus your
  data — keep it in a folder, back it up, or email it to someone. They
  double-click it and see exactly what you saw.

## The dollar amounts

Under "Change the dollar amounts" you'll find what each thing is worth — every
extra square foot, each bathroom, each year newer, and how fast prices are
moving.

**These ship as guesses.** They're reasonable nationally and wrong for any
specific neighborhood. If your sales keep landing far apart, these are usually
why. Someone who knows the local market should set them once. They're shakier
for commercial than residential — office and industrial values swing enormously
by submarket in a way one national number can't capture.

You can save the amounts to a file and load them back, so a whole office can
share one calibrated set.

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
