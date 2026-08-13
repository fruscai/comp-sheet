# Comp Sheet

Comp Sheet performs the sales comparison approach in a single HTML file. There is
nothing to install, no account to create, and no internet connection required.
Nothing entered into it is transmitted anywhere.

### [Open it](https://fruscai.github.io/comp-sheet/comp-sheet.html)

That link is the entire product. It works in a phone browser as well as on a
desktop.

To keep a copy locally, download comp-sheet.html and double-click it. It is the
same file, it works offline, and it will continue working regardless of what
happens to this repository.

There is no npm start, no build step, no server and no package.json, because
there is nothing to install.

## Start here

Open the link and press Load example. That fills in a completed valuation so the
output can be seen before anything is entered.

Then open Adjustment rates and enter the figures for the local market. This
matters more than anything else on the page. The rates supplied are national
estimates and will be wrong for any specific area, and every figure downstream is
built on them. Export them to a file once and the work does not need repeating.

After that, import or enter the subject property, then import or enter the
comparable sales. Press Print for a copy to hand over, or Save to write a file
that reopens exactly as it was left.

Once the rates are set, a property takes approximately two minutes.

## What it does

The tool performs the sales comparison approach the same way it is performed on
paper, only faster.

It takes the price each comparable actually sold for. It adjusts that price for
each way the comparable differs from the subject, covering building area, age,
condition, bathrooms, loading doors and the other elements of comparison. It
applies a market conditions adjustment for the time elapsed since the sale. It
then weights the comparables requiring the least adjustment most heavily, on the
basis that requiring little adjustment is what being comparable means, and
averages what remains. That average is the indicated value.

Each comparable carries its own adjustment detail, so the figure can be explained
line by line:

> **124 Oak Street**, sold 5 months ago for $685,000, 29% weight
>
> | | |
> |---|---|
> | Market conditions, sold 5 months ago, market up 3% per year | +$8,480 |
> | Building area, subject is 130 square feet smaller | −$14,300 |
> | Age, subject is 2 years older | −$1,400 |
> | Site area, subject has 174 sq ft less site area | −$522 |
> | Indicated value | **$677,258** |

This is the part worth having. A seller arriving with a higher figure from a
listing site can read these lines and argue with them, which is a different
conversation from being handed a number with no reasoning attached.

## Importing from a file

The import accepts a CoStar export, an MLS download, or anything saved out of
Excel. Columns may be in any order and extra columns are ignored.

There is an Import CSV button beneath each section heading, one under Subject
property and one under Comparable sales. The button pressed determines what the
file is used for. Each accepts a dropped file or pasted rows. Importing
comparables does not alter the subject property, and importing a subject does not
alter the comparables, so the two may come from separate exports. If a subject
file contains more than one row, the row to use is selected during the review
step.

The import reads the heading row, determines which column is which, and then
displays every column alongside a real value taken from the file and what that
column was read as. Anything read incorrectly can be corrected from a dropdown.
Nothing is brought in until it has been reviewed.

Acres are converted to square feet. Conditions written as words are read onto the
poor to excellent scale. Rows without a sale price are omitted and counted, so
the number dropped is visible.

Sample files are in the samples folder. Three of them are deliberately awkward:

| File | Contents |
|---|---|
| with-subject.csv | CoStar style export with the subject in the first row |
| comps-only.csv | MLS style, extraneous columns, one row missing its price |
| industrial.tsv | Tab separated, including a rent column |
| nasty.csv | Sq. Ft., # Beds, Gar., Cond., Close Dt, Sold $ |
| sparse.csv | Only four columns present |
| cryptic.csv | Meaningless headings, so the import refuses and states why |

Against nasty.csv it matches 11 of the 14 columns and correctly leaves MLS #, LP
and DOM unassigned. Headings it does not recognise are never guessed at. They are
left unassigned to be set during the review step.

The names it recognises are held in a list inside the file called SYNONYMS. If an
export uses a heading it misses, either correct that column during the review
step or add the name to the list. A single real heading row improves matching for
everyone, so those are worth reporting.

## The six property types

The type selected determines which boxes appear and which adjustment rates apply.

| Type | Elements of comparison |
|---|---|
| Residential | Building area, beds, baths, garage, site area, year, condition |
| Office | Building area, year, parking, site area, condition, annual rent |
| Retail | Building area, year, street frontage, parking, site area, condition, annual rent |
| Industrial | Building area, year, clear height, loading doors, site area, condition, annual rent |
| Mixed use | Building area, units, year, site area, condition, annual rent |
| Land | Site area, road frontage |

The four income producing types also carry a rent check, which is what the
comparables sold for relative to the rent they produce. It never feeds the
indicated value. It is displayed alongside it as a second opinion:

> Rent check: those 3 comparables sold at approximately 9.3 times annual rent.
> The subject produces $420,000 annually, indicating $3,915,000.

## Adjustment rates

Adjustment rates set what each element of comparison is worth. This covers the
value of a square foot of building area, of a bathroom, of a year of age, of a
grade of condition, and the annual rate at which prices are moving.

These are supplied as estimates. They are reasonable nationally and wrong for any
specific neighbourhood. If comparables consistently land far apart from one
another, the rates are usually the reason. Someone with local market knowledge
should set them once. They are less reliable for the commercial types than for
residential, because office and industrial values vary considerably by submarket
in a way that a single national figure cannot capture.

Rates can be exported to a file and imported back, so an office can share one
calibrated set.

## Retaining work

Two mechanisms prevent work being lost, and neither transmits anything.

The browser retains what was entered. If the tab is closed accidentally,
everything is still present on return. A Clear it link is available for when that
is not wanted.

Save writes a copy of the entire page with the figures held inside it, named
after the address. That file contains both the tool and the data, so it can be
filed, backed up, or emailed. The recipient double-clicks it and sees exactly
what was on screen, without needing anything installed.

## What it is not

This is not an appraisal. A lender will not accept it and it is not USPAP
compliant.

It cannot see that a building backs onto a freeway, that the photographs are
poor, that there is a lien on the title, or that the market turned last month.
Two well chosen comparables are worth more than six poor ones. Inspect the
property and apply your own judgment.

## Privacy

Everything occurs within the browser. There is no server, no account and no
analytics, and the file contains no network code of any kind. No fetch, no
XMLHttpRequest, no external stylesheets, fonts or images. Opened with the wifi
switched off it behaves identically.

## License

MIT. See LICENSE.
