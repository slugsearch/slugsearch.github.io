# Slug Search

Leaderboard for the Stevenson quarter-long campus scavenger hunt. It's one static page (`index.html`) that reads the Google Form's response sheet and ranks everyone automatically. No server, no build step.

## How it works

Residents submit the Google Form → responses land in the Google Sheet → the page fetches a published CSV of that sheet every 5 minutes and rebuilds the board.

Until a sheet link is set, the page shows example data (Person A, Person B, …).

## Connect the real sheet

1. **Lock down the responses sheet.** Share the responses spreadsheet with RA staff only (not "anyone with the link"). It contains emails, photo links and written answers.
2. **Add a public tab.** In that spreadsheet, add a tab named `Public` and put this in cell A1:

   ```
   =QUERY('Form Responses 1'!A:C, "select A, B, C where C is not null", 1)
   ```

   That copies only Timestamp, Email Address and the challenge column.
3. **Publish only that tab.** File → Share → Publish to web → choose the `Public` tab → Comma-separated values (.csv) → Publish. Copy the link.
4. **Paste the link** into `sheetCsvUrl` in the `CONFIG` block near the bottom of `index.html`, and set `totalClues` (number of challenges) and `endsOn` (last day, `YYYY-MM-DD`).

Optional: add a "Your name" question and a "Your house" question to the form. The board uses them automatically when present (it looks for header words like `name` and `house`). Without a name question it shows the part of the email before the `@`. Without a house question the House vs House section hides itself. If you add columns, widen the `QUERY` range to include them.

## Settings (`CONFIG` in index.html)

| Setting | What it does |
| --- | --- |
| `sheetCsvUrl` | Published CSV link of the `Public` tab |
| `totalClues` | How many challenges exist this quarter |
| `endsOn` | Last day of the hunt |
| `evolve` | Clue counts where a slug turns from stump → critter → volleyball on the map |
| `columns` | Header words used to find each column |
| `requireApproval` | If `true`, a find only counts once an `Approved` column says yes |
| `mapMarkers` / `mapCrowd` | Big numbered pins on the map / small icons for everyone else |

Each challenge counts once per resident. Ties go to whoever reached that count first.

## Hosting

Settings → Pages → Deploy from branch → `main` / root. The site will be at `https://titan-trials.github.io/SlugSearch/`.
