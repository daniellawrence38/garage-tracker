# Garage Tracker

Personal tracker for a garage gym + storage build.

The home screen is the floor plan. Click a shape and the right-hand rail answers "what does this
thing need?" — its job, its unsettled decisions, its shopping list, its millimetres.

Everything hangs off a **job**: a piece of work like *Line the walls in ply*. A job owns a zone on
the plan, its open decisions, its shopping list and its key measurement. Purchases, decisions and
notes are the anatomy of a job rather than separate lists.

Adding things is one line — `Rubber matting 16.4 m² $420 ordered` — parsed live into price,
category, zone, job and status chips. Return saves.

Single-page app served via GitHub Pages, no build step. Data lives in `data.json` in this repo,
written by the app through the GitHub API.

`design/` holds the UI spec the current build follows.
