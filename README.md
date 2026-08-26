# GROUP BY & HAVING

An interactive single-slide visualiser for how SQL actually executes `GROUP BY` and `HAVING`.

Most confusion about these two clauses is really confusion about **execution order**. Rather than
explaining it, this page runs a query in front of the room — one clause at a time — and shows what
the engine is holding at each step:

```
FROM → WHERE → GROUP BY → aggregates → HAVING → SELECT → ORDER BY → LIMIT
```

Built for projection: everything lives on one 16:9 slide that scales to fill the screen, with no
scrolling and type sized to stay readable from the back of a lecture hall.

## Run it

Open `index.html` in any browser. No build step, no server, no dependencies.

Press **F** (or the ⛶ button) for fullscreen before presenting.

| Key | Action |
| --- | --- |
| `→` / `Space` | Next step |
| `←` | Previous step |
| `F` | Toggle fullscreen |

`▶ Play` walks all six steps automatically; `↺` resets to step 1.

## What you can change live

- **WHERE** — which rows survive before grouping
- **GROUP BY** — bucket by `dept` or `city`
- **Aggregate** — `COUNT(*)`, `AVG`, `SUM`, `MAX`, `MIN`
- **HAVING** — operator and threshold, or switch the clause off entirely
- **ORDER BY** — sort by the aggregate or by the group key, ascending or descending
- **LIMIT** — top N, or `0` to switch the clause off
- The SQL panel rewrites itself and highlights whichever clause is currently executing

Every panel recomputes instantly, so you can answer "what if we moved that condition to WHERE?"
by actually doing it.

## The point it makes

`WHERE` filters **rows** before grouping — a dropped row can never reach a bucket, so it can never
influence a `COUNT` or `AVG`. `HAVING` filters **groups** after aggregation, which is exactly why
it can compare against `COUNT(*)` when `WHERE` cannot: at `WHERE` time that number does not exist yet.

The last two steps extend the same argument to the other end of the pipeline. `ORDER BY` runs on
finished rows, *after* `SELECT` — which is why it can use a `SELECT` alias when `HAVING` cannot.
And `LIMIT` cuts last of all: step 8 keeps the discarded rows on screen, struck through below a
dashed line, so it is obvious that sorting decides who makes the top N and `LIMIT` only stops
counting. Change the sort direction with `LIMIT` on and watch a different clan get cut.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole thing — source of truth. Self-contained. |
| `artifact-body.html` | Generated copy with the `<html>/<head>/<body>` wrapper stripped, for hosts that supply their own document shell. Regenerate it from `index.html`; do not edit directly. |

Fonts (Figtree, Inter, IBM Plex Mono) load from Google Fonts, so the first load needs a network
connection. Everything else is offline — worth opening it once before you present.

## Data

Twelve rows of **Clan of the Year** data are embedded in `index.html` (the `DATA` array): students
divided across four clans — Maratha, Chola, Vijaya, Rajputana — who competed in four sports and
earned Gold, Silver or Bronze. It is an extract of the 20-row table used in the accompanying
lecture, trimmed so the type stays large enough to read from the back of a hall.

Three properties of this data are deliberate, and worth knowing before you present:

- **Rajputana won no Gold.** Set `WHERE` to `medal = 'Gold'` and the Rajputana bucket disappears
  entirely — rows removed before grouping can never form a group. This is the single most useful
  thing on the slide.
- **Four students have no medal at all** (`medal: null`). `WHERE medal IS NOT NULL` drops exactly
  those four, which makes every clan average jump.
- **The clans are uneven** — 4, 3, 3 and 2 students. With the default `HAVING COUNT(*) >= 3`,
  Rajputana is the one that fails, so there is always something visibly failing on screen.

Edit the `DATA` array to swap in your own example. If you add rows, check the table panel still
fits: it is sized for roughly twelve rows and clips silently rather than scrolling.
