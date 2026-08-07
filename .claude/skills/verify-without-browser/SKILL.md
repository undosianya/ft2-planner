---
name: Verify index.html changes without a browser driver
description: How to verify JS/UI changes to this single-file app (index.html). The user tests the UI themselves by clicking through it — never attempt to launch, drive, or screenshot a real browser (no Safari automation, no chromium-cli/Playwright, no screencapture) for this project. Trigger whenever you'd otherwise reach for the `run` skill or browser automation to verify a change here.
---

# Verifying index.html without a browser driver

This app is a single static `index.html` (HTML+CSS+JS, no build step, no
test suite). **Do not try to open, drive, or screenshot a real browser
for this project** — not Safari via `osascript`, not `screencapture`,
not `chromium-cli`/Playwright, even if they happen to be available in
the sandbox. The user verifies UI/interaction changes themselves by
clicking through the app; that step is theirs, not yours.

What you're responsible for is catching everything a syntax/logic check
can catch before it gets to them. Two checks substitute for actually
clicking around:

## 1. JS syntax check via JavaScriptCore

macOS ships JavaScriptCore and exposes it through `osascript -l
JavaScript` (JXA) — this is a scripting bridge into JavaScriptCore, not
browser automation, so it's fine to use here. Extract the `<script>`
body from `index.html` and parse it with `new Function(src)` — this
compiles without executing, so it catches syntax errors (mismatched
braces, bad template literals, stray commas) without needing a DOM or
globals to be defined:

```bash
python3 -c "
import re
html = open('index.html').read()
m = re.search(r'<script>(.*)</script>', html, re.S)
open('/tmp/extracted.js','w').write(m.group(1))
"
osascript -l JavaScript -e '
var fs = $.NSString.stringWithContentsOfFileEncodingError($("/tmp/extracted.js"), $.NSUTF8StringEncoding, null);
var src = ObjC.unwrap(fs);
try { new Function(src); console.log("PARSE OK"); } catch(e) { console.log("PARSE ERROR: " + e); }
'
```

This only proves the file parses — it says nothing about behavior.

## 2. Re-implement the exact render/filter logic in Python and run it against real data

For logic changes (filter predicates, sort comparators, data
transforms), don't just re-read the diff and reason about it —
transcribe the same logic into a small Python script and run it
against the actual extracted data (e.g. `ITEM_META` /
`SEED_LIBRARY`, or whatever the JS was going to operate on), then
assert on concrete known cases. This catches the bugs static review
misses (off-by-one in season logic, wrong AND/OR combination, sort
comparator on the wrong field) because it's an actual execution, not
just a read-through.

Pattern used when building the Contents-panel season/resource/sort
filters:

```python
# parse SEED_LIBRARY / ITEM_META straight out of index.html with regex,
# so the check runs against what's actually in the file, not a hand copy
...
def sim(catFilter="All", seasons=set(), only=False):
    # same predicate as the JS matchesSeason()/renderLibrary() filter,
    # transcribed by hand
    ...
r = sim(catFilter="Crops", seasons={"Spring","Winter"})
assert not any(x["name"]=="Beet" for x in r)   # Beet is Summer-only
```

Pick 3-5 concrete spot-check cases from data you can verify by eye
(e.g. cross-check a couple of rows against the source spreadsheet) —
that's what gives confidence the transcription itself isn't wrong in
the same way the app code might be.

## Reporting to the user

Say plainly that you ran a syntax check + logic simulation, not that
you clicked through the UI — never imply visual/interactive testing
happened. The user will do that pass themselves; no need to ask them
to or apologize for not doing it.
