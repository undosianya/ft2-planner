# ft2-planner

A farm layout planner for **Farm Together 2** — sketch out where everything goes
before you spend the currency.

**→ [Open the planner](https://undosianya.github.io/ft2-planner/)**

It's one HTML file. No install, no account, no internet needed once it's loaded.

---

## Why

The game went from 7×7 plots to 9×9, which means re-planning a farm you'd already
settled into. Dragging things around in-game costs time and money; dragging them
around here costs nothing (or well, less time, no money).

---

## What it does

**Ground.** Mark every tile as grass, plowed, road, water, building, or locked
(for plots you don't own yet).

**Planting.** Assign a crop, tree, flower, animal, or fish to plowed land and
water. The list ships with everything in the game and is editable in the app —
add new content as it releases without touching the code.

**Editing in bulk.** Drag a rectangle to paint it. Place exact sizes (18x3, 24×24,
whatever you save as a preset). Copy a finished area and stamp it wherever you
want it. Alt-click to fill a whole plot. Full undo.

**Zoom.** From the whole farm at a glance down to individual tiles, with plot
letters and numbers along the edges so you can find your way back to a spot.

**Multiple farms.** Each farm holds as many named sub-farms as you like, each with
its own layout and its own size.

**A running count** of what's planted, how many tiles of each, and how many plots
are actually in use — which is the bit that tells you whether a re-plan fits.

---

## Saving your work

Your layouts are saved in your own browser, on the device you're using. Nothing
is sent anywhere. There's no server, no account, and nothing for anyone to see.

That also means it doesn't follow you between devices, and clearing site data for
this domain wipes it.

**Use Export.** One `.json` file holds every farm, every sub-farm, and any content
you've added or recoloured. It's your backup, and it's how you move a layout from
laptop to tablet. Import puts it all back.

---

## Running it offline

Download `index.html` and open it — it works exactly the same with no connection.

One catch: opening a file straight from disk (a `file://` address) blocks browser
storage, so autosave won't work. Export before you close, or serve the folder
locally instead:

```sh
python3 -m http.server
# then open http://localhost:8000
```

---

## Keyboard

| Key | Does |
| --- | --- |
| `B` | Paint a rectangle |
| `M` | Select an area |
| `V` | Paste what you copied |
| `I` | Pick up what's on a tile |
| `H` / hold `space` | Pan |
| `G` `P` `R` `W` | Grass, plowed, road, water |
| `Enter` | Fill the selection |
| `Delete` | Clear the selection to grass |
| `Ctrl`+`Z` | Undo (add `Shift` to redo) |
| `F` | Fit the whole farm on screen |
| `Esc` | Drop the selection |

Scroll to zoom, right-drag to pan, alt-click to fill a plot. The `?` button in
the app has the full list.

---

## Editing the tool

Everything lives in `index.html` — markup, styles, and code in one file. The top
of the code section is a config block covering the parts most worth changing:

- `CONFIG` — farm and plot dimensions
- `TILE_TYPES` — the ground types and their colours
- `CAT_HUE` — colour ranges used for auto-assigning crop colours
- `KEYMAP` — keyboard shortcuts
- `SEED_LIBRARY` — the starting crop, tree, flower, animal, and fish lists

No build step. Edit the file, commit it, reload the page. Replacing `index.html`
doesn't affect anything you've already saved — storage is tied to the URL, not
the file.

---

## Not official

A fan tool, not affiliated with or endorsed by Milkstone Studios. Farm Together 2
and everything in it belongs to them. The item names are here so the planner is
useful; the game is theirs.

---

## License

<!-- Without a LICENSE file, default copyright applies: people can read and fork
     this on GitHub but have no right to reuse it elsewhere. To add one:
     Add file → Create new file → name it LICENSE → use the "Choose a license
     template" button. MIT is the usual permissive pick; CC0 is closer to public
     domain. Delete this section if you'd rather not license it.            -->

MIT — see [LICENSE](LICENSE). Take it, change it, share it.
