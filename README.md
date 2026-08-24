# GROUP BY & HAVING

An interactive single-slide visualiser for how SQL actually executes `GROUP BY` and `HAVING`.

Most confusion about these two clauses is really confusion about **execution order**. Rather than
explaining it, this page runs a query in front of the room — one clause at a time — and shows what
the engine is holding at each step:

```
FROM → WHERE → GROUP BY → aggregates → HAVING → SELECT
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
- The SQL panel rewrites itself and highlights whichever clause is currently executing

Every panel recomputes instantly, so you can answer "what if we moved that condition to WHERE?"
by actually doing it.

## The point it makes

`WHERE` filters **rows** before grouping — a dropped row can never reach a bucket, so it can never
influence a `COUNT` or `AVG`. `HAVING` filters **groups** after aggregation, which is exactly why
it can compare against `COUNT(*)` when `WHERE` cannot: at `WHERE` time that number does not exist yet.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole thing — source of truth. Self-contained. |
| `artifact-body.html` | Generated copy with the `<html>/<head>/<body>` wrapper stripped, for hosts that supply their own document shell. Regenerate it from `index.html`; do not edit directly. |

Fonts (Figtree, Inter, IBM Plex Mono) load from Google Fonts, so the first load needs a network
connection. Everything else is offline — worth opening it once before you present.

## Data

Eight rows of fictional employee data are embedded in `index.html` (the `DATA` array). Kept
deliberately small so the type can stay large enough to read from a distance. Edit that array to
swap in your own example.
