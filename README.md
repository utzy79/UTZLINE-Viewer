# UTZLINE Viewer — installable app

**Current version: v65** (kept in lockstep with the editor's own version, since both are built from the same `source.html` — bump this line, and add a dated changelog entry, every time a new build ships; v40 through v45.6 shipped without this line being kept in sync — see `next-version-notes.md` in the project, or the editor's own README, for the full per-version detail of that stretch. The only change specific to v45.6 itself: the level-list exclusion gained the new `itp-delivery` folder.)

**v65 (2026-09-29):** **Job notes are "For Construction".** Andrew: *"a job note is IFC, and this gets a light grey watermark on the pdf stating IFC"*, then *"make that For Construction watermark"*.
- A job note added here (Add job note) gets **FOR CONSTRUCTION** in light grey across every page as it's saved.
- A protected PDF is saved as it is, and the toast says so.
- `pdf-lib.min.js` (MIT) is included and loaded only when a note is added.
- Test: `pdftest-projects/run_v65_jobnote_for_construction.js`.

**v64 (2026-09-28):** View shop drawing shows **Sent** and **Returned** drawings, each with a revision picker. Revisions read as REV A, B, C (files saved as REV 0/1/2 show as A/B/C). This is the same change as Site Measure v64.

**v63 (2026-09-28):** The Reworks screen shows what every app did to a rework. Rework round: *"also need to fix this rework conflict. rework pdfs should be user datetime stamped. they should also show the entire status log per rework"*, and *"reworks that are delivered to be green border / text and sent to bottom of page (maybe a separate selectable delivered folder)"*.
- Each rework's state now comes from the rework file **plus its log files**:
  - Cut (Machine Schedule).
  - Complete — ready to deliver (Scheduler).
  - Delivered to site (Delivery ITP), with its pin and photo. A retaken pin + photo replaces the earlier one.
  - Closed out (Install ITP).
  - An undo goes back to the state before.
  - Before this, the Viewer only saw what was written into the rework file.
- Delivered and closed-out reworks move to the green "Delivered (N)" section automatically.
- **The status log** lists every change and comment from every app, newest first, with who, when and which app.
- **Photos** load when a rework is opened. They're captioned with who and when, and the **delivery-location** photo (the latest retake) is included. The list itself keeps no photos, which saves memory on 4 GB tablets. Photos are released when the screen closes.
- Comments are written exactly as in v61, as their own files with the app name.
- A rework file that is still syncing is counted on the screen ("couldn't be read — close and reopen in a moment"), never shown as empty.
- Tests:
  - New: `pdftest-projects/run_v63_viewer_rework_fold.js`.
  - `run_v61_viewer_reworks.js` now expects the app name on each log line.
  - Site Measure / Viewer suite: 92/92.

**v62 (2026-09-28):** Button wording. Asked whether to change to "Create / Open site measure" or keep "Add / Open check measure", Andrew answered *"Stay"*, then *"Actually. Create"*.
- The joinery item's button and its long-press row now read **Create site measure** when there isn't one yet and **Open site measure** when there is. The rule for which one shows hasn't changed. The Viewer always says "Open site measure", and the Timings panel title matches.
- Nothing else is renamed. "Mark as check measured" stays as it is because it's a joinery status.
- Tests updated for the new labels.

**v61 (2026-09-28):** Two requests from Andrew.

1. **All check measures are layers.** Andrew: *"Check measure should always show all check measures. The point of the user layers is to be able to turn them off temporarily. Latest always to top."*
   - Every saved check measure is now a toggleable layer, drawn oldest to newest so the newest is on top. The layers panel lists newest first.
   - Site Measure with your own draft or save: that's your editable page (as before), and every other save is a layer.
   - Nothing of your own (and always in the Viewer): the page is the latest save's plan with no marks, and **every** save, the latest included, is a layer.
     - v60 copied the latest save's marks onto your page instead. That hid that save from the layers list and duplicated its marks into your next save.
     - That save's file is read once and reused for its layer.
   - If there's only someone's unsaved draft, it shows as a layer marked "unsaved".
2. **Viewer: Reworks screen.** Andrew: *"view rework in viewer app should open the reworks, and allow comments to be added like sent to saw with user time date logging. it needs to be like the photo"*, *"reworks that are delivered to be green border / text and sent to bottom of page (maybe a separate selectable delivered folder)"*, then *"Do the viewer rework also"*.
   - View rework (project menu, the item dialog, and the long-press row) opens a full screen like Install ITP's Outstanding reworks page.
   - Outstanding reworks are grouped by level and room, newest first. Each card shows the code and cabinet, a state pill (Logged / Cut / Manufactured), who logged it and when, the text, the latest comment, and **Show on plan** / **Open rework**.
   - Delivered and closed-out reworks sit in a separate **Delivered (N)** section at the bottom, tap to open, with a green border and text.
   - **Open rework** shows the photos (tap to enlarge) and the whole status log, newest first: logged, every state change, delivered, closed out, and every comment, each with name, date and time.
   - **Add a comment**, with a "Sent to saw" quick pick, needs a name and PIN.
   - Each comment is its own new file, named by who and when, in a log folder beside the rework file:
     - flat projects: `Project Saves/UTZLINE ITP/Install ITP Rework Log/<Level> - <Room> - <Code>/<name> - <date time> - comment.json`
     - legacy projects: `itp-install-rework/<Level>/<Room>/<Code> log/…`
   - The shared rework file (which Install ITP and Delivery ITP rewrite whole) is never written, so comments can't cause OneDrive conflict copies. Install ITP, Delivery ITP and the rework PDFs pick these comments up in their own parts of the rework round.
- Tests:
  - New: `run_v61_viewer_reworks.js`.
  - Updated for all-layers: `run_v60_open_existing.js`, `run_v54_check_measure.js`, `run_layers_discoverability_fix.js`, `run_joinery_item_dialog.js`.
  - The built-app fake folders can now create files. The full suite passes.

**v60 (2026-09-28) — important fix:** Andrew, on v59: *"when opening an existing site measure, it's bringing up the popup with the crop plan instead. we need to decipher if the button says create site measure or open site measure based on if there is one existing -- fix this important"*, and *"the view rework does not need to be in site measure app, remove it"*.

- **Cause:** the button followed "does anyone have a check measure for this item" (`checkMeasureKeySet`), but opening followed "does *this device's name* have one". So an item someone else measured, or one saved under a different name on this device, was treated as brand new: plan snapshot, crop popup and an empty page.
- **Fix: one rule for both** (`loadCheckMeasureBase` / `readCheckMeasureState`, read before leaving the plan):
  - **Exists** (any saved check measure, my draft, or anyone's unsaved draft): it opens straight in, with no crop popup. The page starts from my draft, else my latest save, else the latest save by anyone, else the newest unsaved draft by anyone. A toast says whose it opened from. Every other save is still a layer, and my Save becomes my own new layer without changing theirs.
  - **Doesn't exist:** "Add check measure", plan snapshot and crop, as before.
  - Names on this device are matched case-insensitively.
  - A folder that can't be read (mid-sync) is retried once, then reported with "still syncing? — nothing was changed" while you stay on the plan. It's never mistaken for "doesn't exist", so it can't fall back to the crop popup.
- **View rework is Viewer-only now:** the long-press row, the item dialog button and the project menu button are gone from Site Measure. Site Measure also skips the rework listing in its background scan.
- Timings step names changed ("read the check measure …", "the save it opens from is ready").
- Tests:
  - New: `run_v60_open_existing.js` (someone else's save, own save after saving, case-insensitive name, other person's draft only, brand new, unreadable folder, no View rework).
  - Updated for the new behaviour: `run_v54_check_measure.js`, `run_layers_discoverability_fix.js`, `run_v53_dialog_speed.js`, `run_viewer_fixes_and_rework.js`.
  - The full Site Measure/Viewer suite passes.

**v59 (2026-09-28):** Andrew, after field testing: *"Ok field tested and pretty good. Site measure app need to popup a small number pad when typing a measure. This to also have quick text like ctr, ftc, bhead, oall, text to be reduced to 18 and line weight to 2 as default."*

- **Measure pad.** Typing a dimension or angle label pops up a small on-screen pad (about 300 × 210 px) instead of the tablet's keyboard: 0–9, point, Space, ⌫, and the quick texts **ctr / ftc / bhead / oall**, plus **ABC**, which hands over to the full keyboard for anything else, and **Done**.
  - Quick text goes on with a space ("2400 ftc"). When the whole label is still selected, it's added on the end rather than replacing the number.
  - Keys act on press and never take focus from the label, so the caret and selection behave like a keyboard. A tap on the pad never reaches the plan.
  - The pad sits bottom-right, or whichever corner doesn't cover the label being typed. Tapping the plan still finishes editing.
  - The label is set to `inputMode "none"` so the tablet keyboard stays down; a real keyboard (PC) still types straight in.
  - Text and callout labels keep the normal keyboard.
- **Defaults: text 18, line weight 2** (were 32 / 4). A plan or check measure saved with the old untouched 32 / 4 moves to 18 / 2 once (`style.sizeDefaultsV59`). A size picked on purpose is kept, and marks already drawn keep their own size.
- Viewer: same build (it never edits, so it has no pad).
- Tests: new `run_v59_measure_pad.js` (real touch taps). `gen_test.py` gained a `makeAngle` hook. All 140 Site Measure/Viewer tests pass.

**v58 (2026-09-27):** Hides the **Schedule Backups** folder from the project list. Scheduler v29 now keeps its daily spreadsheet backups in that folder, directly in the main Projects folder (Andrew: *"a schedule backups folder directly in the main folder ... I meant in the main folder. Not the individual projects folder."*). Every app lists every folder in the main folder as a project, so each one now leaves that folder out: `isReservedRootFolderName`, the same one-line rule in every app. Tested across all 11 apps by `pdftest-projects/run_schedule_backups_folder_hidden.js`, which fails on every app's previous build and passes on the new ones.

**v57 (2026-09-27, same night):** Andrew sent the v56 **⏱ Timings** from the tablet (Level 1 · MH.028 · J.T.922). The page was shown after **154 s**: 84.8 s to list the item's layers and read his draft, then 69.4 s to read his own last save. Once the page was up, it read and drew the 3 other layers in **1.2 s**. So neither storage speed nor drawing was the problem. The open was **waiting in the storage queue** behind the background status scan that runs on every level open.

- That scan (`readJoineryStatuses`) handled every item in the project at once. For each one it walked Project Saves → Joinery Status → item (3 lookups), listed the folder, and re-read every event file. On a real project that's thousands of calls, and Android's storage layer runs them one at a time. Simulated at 20 ms per call with 150 items: the v55/v56 open waited behind about 1,100 calls (**21.9 s**). v57 opens in **0.5 s**. The cost grows with project history, which is how a build that was fine one night was slow the next.
- Fix:
  1. Item folder handles now come straight from the one Joinery Status listing, with no lookups.
  2. Event files never change once written (every status change is a new file), so each one is read once and remembered on the device in IndexedDB (`joineryStatusEvents\0<project>`). A repeat scan costs one listing per item: about 200 calls instead of about 1,100 in the same simulation.
  3. Items go through `runBackgroundQueue` at 3 at a time. It starts nothing new while the foreground is busy: from `fgBegin`/`fgEnd`, around opening a check measure, loading its other layers, and loading the job notes list. If the foreground somehow never ends, the scan carries on after 30 s. `listShopDrawingKeys` uses the same queue.
- The v56 double-tap guard, incremental layers and Timings panel all stay. Hidden layer groups are stripped from exports.
- Tests: new `run_v57_scan_priority.js` covers the queue limit, pausing for the foreground, fail semantics, the event cache (no re-read, new files picked up) and the pause during a real open. Diagnostics `diag_scan_contention.js` and `diag_open_item_photos.js` were added. The whole Site Measure/Viewer suite passes; the heavy PDF tests pass when run alone.

**v56 (2026-09-27, same day):** Andrew: *"open check measure still took upto a minute to open on tablet. this was super fast lastnight"*.

- Simulated Android storage (every folder/file call queued one at a time, 40 ms each, CPU throttled 6×) shows this build and last night's v49 making the same storage calls and opening in the same time, so storage isn't the regression. The CPU-side suspect: other people's layers were torn down and redrawn from scratch, with every photo decoded again, each time one more layer arrived (v54's background loading made that N× per open) and on every plan redraw while you drew. On a 4 GB tablet with several photo layers, that's the kind of cost that turns seconds into a minute.
- Fix: each layer's drawing is now built once and kept (`renderOverlayLayers` is incremental; groups are tagged `data-layer-idx`). A new layer appends just itself, and hide/show only toggles it. Everything is cleared when you leave the item.
- New **⏱ Timings** button on the Projects screen shows the last 10 Open check measures on this device, step by step: reading the item's layer list and your draft, the plan snapshot, save-before-leaving, reading your last save, page shown, and other people's layers drawn. It's kept on this device only, so if it's still slow on the tablet a screenshot pins it to the exact step.
- Tests: `run_v54_check_measure.js` gained v56 checks (existing layer drawings survive a new layer plus redraws, hide/show reuses the drawing, nothing is left drawn on the plan, and timings are recorded and listed). `gen_test.py`'s visible-object count ignores hidden layer groups. Site Measure/Viewer suite passes. (Three Install/Delivery ITP tests in the same folder time out on the new PIN sign-off flow. They test those apps, not this build, and are stale; fix them with those apps' next update.)

**v55 (2026-09-27, same day):** Andrew, urgent: *"site measure and viewer still gets a second popup when pressing open check measure, it should go straight to the check measure. as it did before. pressing open job note takes ages also to bring up the job note selector need to remove the delete option from long press on the site measure app (project / level / room etc)"* (and: fine on Windows, the problem is the tablet).

- **Straight to the check measure.** The long-press "Open/Add check measure" row and a double-tap on a marker now open the item's page directly — no in-between Joinery Item popup. Its two extra buttons moved onto the long-press menu: **View rework** (red, only when the item has a rework file) and, for a legacy project only, **Open this room's own plan**. In the Viewer the check-measure row is left off when nobody has saved one for that item yet (it would only open an empty page) — the same rule the old popup applied; the Viewer now uses the same background check-measure listing as Site Measure to know that.
- **Job notes open faster.** The "Project Saves/Job Notes" folder handle is remembered per project (one lookup + one listing instead of three lookups + a listing), and the list starts loading the moment the long-press menu shows a "View job note" row, so it's usually ready by the time it's tapped. (If the slow part is Android's own "open with" app picker after tapping Open on a note, that's the tablet, not the app.)
- **No delete on long-press in Site Measure.** Press-and-hold on a project, level or room row does nothing now (row tooltip is just "Tap to open"). The Viewer's own long-press is unchanged — it only hides an entry from that device's list, never deletes.
- Tests: `run_v54_check_measure.js` gained v55 checks (long-press row goes straight to the page; no delete on a project row). Updated for the new behaviour: `run_joinery_item_dialog.js`, `run_layers_discoverability_fix.js`, `run_flat_structure_interop.js`, `run_lock_popover_restyle.js`, `run_viewer_room_marker_popover.js`, `run_rooms_gate_and_delete.js` (now asserts a held room row offers no delete), `run_describe_error_fix.js` (delete-flow half removed). Retired (the feature they tested is gone): `run_delete_flow.js`, `run_delete_noop_bug.js`, `run_delete_timeout.js`, `run_removeentry_deep.js` → `retired_v55_*.js`. All 82 Site Measure/Viewer tests pass.

**v54 (2026-09-27, same day):** Andrew, on the Android apps: *"when you select open check measure on the plan, it opens another pop up with sub orders / close / open check measure on it, when you press open check measure it is very slow. it also shows sub orders if there is none for that joinery item. on site measure app, pressing open check measure on the second popup does nothing. remove sub orders from these 2 apps"* — then *"in site measure app, if there is no check measure yet, the button should say add check measure"*.

- **Sub orders removed from Site Measure and the Viewer** (the v53 button, dialog, CSS, write path and test hooks). Sub orders still live in UTZLINE Sub Orders, the ITP apps, Projects, Scheduler, Machine Schedule and Solid Surface Schedule.
- **"Open check measure does nothing" (Site Measure).** Every open rendered a snapshot of the whole plan first (the v46 first-open crop feature): the full plan SVG, every tile, serialised and then base64'd three more times over. On a big plan that could throw outright (string too long) synchronously inside the tap, so nothing happened; on a 4 GB tablet it was slow even when it worked, and it was thrown away whenever the item already had a check measure. Now the item's overlays and your draft are checked first (reads the page needed anyway, reused rather than repeated), and the snapshot is only made when you have nothing saved for that item yet. When it is made, only the plan tiles actually in view are included, it's loaded from a Blob instead of a giant base64 string, it times out after 20 s, and any failure falls back to a blank page instead of a dead button.
- **"Very slow" (both apps).** Opening a check measure used to wait for every other person's saved layer to be read in full (each a JSON file with its photos as base64, and every Save is its own layer). The page now opens as soon as the base view is read; the other layers are read one at a time afterwards and drawn as they arrive, still all visible by default. A short "Opening check measure…" toast shows straight away on a slow tablet.
- **"Add check measure" (Site Measure).** The long-press row and the Joinery Item dialog button say "Add check measure" when nobody has saved a check measure for that item yet, "Open check measure" once one exists. Known from one background listing of `Project Saves/Site Measures` alongside the status scan (3-minute freshness, IndexedDB snapshot) — no extra file lookup per tap — and updated the moment you save. Until the first scan lands it shows the old "Open check measure". The Viewer is unchanged (it already hides the button when there's nothing to open).
- Tests: new `pdftest-projects/run_v54_check_measure.js` (no Sub orders anywhere in either app; Add/Open labels; opening an already-saved item does zero plan serialisation and no crop dialog while a new item still gets it; other layers arrive after the page opens). `run_sub_orders_received.js` retired with the feature. The shared test harness (`redline_projects_test.html`, via `gen_test.py`) was regenerated from current source — it had been frozen at v47 — and four tests updated for changes since then (the Add/Open label, the v49 🏭 icon revert, the v50 modal popover). All 90 Site Measure/Viewer tests pass (the two heavy PDF-tile tests pass run on their own).

**v53 review fix (same build):** the Sub orders "mark as received" write is now strict per the family's "unreadable is not empty" rule — a mid-sync Orders file is retried once and then left untouched ("couldn't read (still syncing?) — nothing was changed"), an order unattached meanwhile writes nothing, and nothing is ever created. It writes the same pretty-printed JSON shape Sub Orders itself writes, and the Orders filename now uses Sub Orders' own `"file"` fallback for a blank name (this app's `sanitizeFileBase` falls back to `"plan"`). Three new failure-case checks in `run_sub_orders_received.js`, run against both apps.

**v53 speed fix (same build):** Andrew, the same day v52 shipped: *"site measure app is really slow again, we fixed it yesterday but something you did today has brought back all the slow downs."* Diffed every shipped build since v49 line by line: none of v46's speed fixes were lost, and v50 (popover restyle) and v51 (company logo, gate screen only) add no per-interaction file work. v52's "View rework" gate was the one piece of per-tap file I/O added today — every joinery item dialog open walked Install ITP's rework folder with uncached lookups (4 serialized storage calls per open on a legacy project, measured), and on the common project with no rework data every one of them a *miss*, the slowest kind of lookup on Android's storage layer — the exact cost v46 removed everywhere else. Fixed the same way v46 gated "View shop drawing": one background listing of which items have a rework file at all (`listReworkFileKeys`), refreshed alongside the status scan (3-minute freshness, IndexedDB snapshot for an instant first paint); a dialog open now reads nothing unless that specific item has a rework file (0 storage calls, measured — was 4). Before the first scan lands it fails open exactly as v52 did, so a real rework is never hidden. Viewer only: the "Open check measure" gate's per-open overlays-folder listing is now remembered per item for the same 3 minutes. New `pdftest-projects/run_v53_dialog_speed.js` (both apps) counts storage calls per dialog open for legacy, flat and no-rework-folder projects, and confirms items with a real open rework still show and list it.

**v53 (2026-09-27, kept in lockstep with the editor's own version number):** "Sub orders" summary + write-back on the joinery item dialog — rebuilt from the same `source.html` as the editor; see that app's own v53 entry for the full writeup. Everything applies to this app identically, INCLUDING THE WRITE PATH: unlike almost every other write in this app, "mark as received" is deliberately allowed in the Viewer too — a procurement/status update, not a measurement edit, and Andrew's request said "ALL joinery summary pages" with no Viewer carve-out — using the same narrowly-scoped `ensureProjectWritePermission()` upgrade "Add job note" already established, not a new one. Covered by the same new `run_sub_orders_received.js` (both apps checked in one test file, including this app's own write path actually landing in the real `Orders/*.json` file). `service-worker.js` cache bumped to `utzline-viewer-cache-v53`.

**v52 (2026-09-27, kept in lockstep with the editor's own version number):** "Viewer fixes" queue item (`NEXT_RUN_NOTES.md`, dictated 2026-09-27) — rebuilt from the same `source.html` as the editor; see that app's own v52 entry for the full writeup. All five items apply directly to this app (it's the one most of them were dictated about): all overlay layers already defaulted to visible (verified, no change needed); "Open check measure" is now hidden on a joinery item that has no saved check measure yet — this app's own gating (the editor keeps the button unconditional, since it's the only way to start the first one); new read-only "View rework" button (per item, red text) and "View rework" menu entry (per project), reading Install ITP's own rework files directly; the two layers panels (`#overlayLayersPanel`/`#layersPanel`) no longer overlap; foreign-layer photos are no longer double-dimmed. Covered by the same new `run_viewer_fixes_and_rework.js` (both apps checked in one test file, including both a legacy- and flat-shaped fake project for the rework paths). `service-worker.js` cache bumped to `utzline-viewer-cache-v52`.

**v51 (2026-09-27, kept in lockstep with the editor's own version number):** Read-only "Company logo" preview added to the Projects screen (`#gateList`), rebuilt from the same `source.html` as the editor — see that app's own v51 entry for the full writeup (NEXT_RUN_NOTES.md item 8's family-wide scope). Same read-only thumbnail/placeholder here, sourced from the same shared `company-logo.png` file at the Projects root; no upload/remove controls (this app never had any Projects-write UI to begin with). Covered by the same new `run_company_logo_readonly.js` (both apps checked in one test file). `service-worker.js` cache bumped to `utzline-viewer-cache-v51`.

**v50 (2026-09-26, same day):** `NEXT_RUN_NOTES.md` item 1 — the right-click/long-press context menu (`showLockPopover`) is restyled to match Install ITP's own centered-modal look, superseding the small floating icon-row popup anchored at the click point. Same shared `source.html` as the editor, so this app gets the identical restyle: a full dimmed backdrop with a centered box, a title line giving context (the joinery code for a room marker, a type label for other object types, "Site photo" for the base image), the first (most relevant) row accented, and a new "Cancel" row always last, closing the popover on either Cancel or a backdrop click. Existing class names (`.lock-popover-wrap`, `.lock-popover`) are unchanged — only their CSS — so nothing that already queried them broke. This app's own row set for a room marker (Open check measure, Add job note, View shop drawing, Cancel) is unchanged in content, only in look; a non-roomlink object still opens nothing here, per this app's own long-standing read-only restriction. Verified directly against this build (not just inferred from the shared source) via a new `pdftest-projects/run_lock_popover_restyle.js`, using a new minimal `window.__testHooks` added to `source.html` (this app had no live test-hook exposure before now). `service-worker.js` cache bumped to `utzline-viewer-cache-v50`.

**v49 (2026-09-26, same day):** Family-wide status icon revert (`NEXT_RUN_NOTES.md` item 2, schema §4) — this app's own portion, superseding v47 below. Andrew's earlier icon-sweep dictation is now fully reverted for both statuses back to what they were before that round: `machined`: 🪚 → ⚙️; `in_manufacture`: 🔨 → 🏭. Same shared `source.html` as the editor — `joineryStatusIcon` and the plan-marker `joineryDisplayIcon` both updated, rebuilt via `build.py`. `service-worker.js` cache bumped to `utzline-viewer-cache-v49`.

**v47 (2026-09-26):** Status icon change — Andrew, verbatim: "change in
manufacture to this 🔨 and machined to this 🪚." Same shared `source.html`
as the editor: `joineryStatusIcon` and the plan-marker `joineryDisplayIcon`
both updated (`in_manufacture`: 🏭 → 🔨; `machined`: ⚙️ → 🪚).

- Cache version bumped: `utzline-viewer-cache-v47`.

**v46 (2026-09-25):** see the editor's own README for the full detail — same shared `source.html`, so this app gets everything that isn't editor-only: the device Back button now steps back through the app (dialog first, then item → plan → level list → project list) instead of closing it; returning to a plan restores the last pan/zoom with no re-read; a level file that exists but can't be read is "try again", never a blank page; marker status badges paint from a cached snapshot and re-scan in the background; "View shop drawing" is offered only when one exists; PNG "current view" sharing works again (the SVG rasterisation had been failing on "--" inside markup comments); one reused IndexedDB connection; drag re-renders coalesced per frame; level names listed from a cache instead of reading every level file. The first-open plan snapshot and Save & exit are editor-only and do not apply here.

- Cache version bumped: `utzline-viewer-cache-v46`.

**v45.14 (2026-09-24):** see the editor's own README for the full detail — same shared `source.html`, so this app gets the identical fix/feature. The other-users'-overlay-layer tint (this app's own "base view" is always the single most recent overlay across everyone, so this mainly affects any OLDER layers offered underneath it) no longer washes the plan in magenta — recolored directly per-object now, not via a CSS filter. Every overlay this app reads also now carries a flattened `overlaySnapshotDataURL` preview image alongside its vector data, for other apps (UTZLINE Projects' own Joinery Item page, to start) to show without needing this app's own renderer.

- Cache version bumped: `utzline-viewer-cache-v45.14`.

**v45.13 (2026-09-24):** see the editor's own README for the full detail — same shared `source.html`, so this app gets the identical rebuild. The shared `joinery-status.json` this app reads is now event-sourced (one immutable event file per status/job-note change under `Project Saves/Joinery Status/<Level> - <Room> - <Code>/`, folded together to compute current status) instead of one shared mutable array file, eliminating a real data-loss risk once 30 people across five writer apps (some syncing in via Dropbox) are all saving into the same project. This app's own read path (`readJoineryStatuses`) picks up the same one-time, automatic, lossless migration every sibling app does; every render/badge/history call site is unchanged.

- Cache version bumped: `utzline-viewer-cache-v45.13`.

**v45.11 (2026-09-24):** see the editor's own README for the full detail — same shared `source.html`, so this app gets the identical fix. The joinery-item storage-key instability behind Andrew's "layers... only gives you one name to exclude, needs to show all names" report affected this app too, since the Viewer reads and offers the same overlay-layer list — a colliding marker's key is now cached permanently on the marker once resolved, so this build and Site Measure always agree on it regardless of what either device happens to have synced locally.

- Cache version bumped: `utzline-viewer-cache-v45.11`.

**v45.10 (2026-09-24):** see the editor's own README for the full detail — same shared `source.html`, so this app gets the identical changes. Part 1: the right-click popover's "Delete" and "Add shop drawing" rows are gone (this app never had an equivalent editor-only delete action to fall back to, since it's read-only — deleting was never possible here regardless); the old "New" button is now "Home" (`#homeBtn`), deliberately kept visible in this build too, unlike its predecessor, since it's pure read-only navigation; "Open" and "Open Project" are both gone (this app never used its own file-open buttons for anything the folder-based workflow doesn't already cover). Part 2: the "Site Measure layers" panel — already correct here (this app shows the single most-recent overlay across everyone as its base view, with every other person's latest offered as a toggleable reference layer) — gets the same new always-visible count badge on `#overlayLayersBtn` and the same "N other saved layer(s)" toast Site Measure gets, for the identical discoverability reason. Verified directly against this build (not just inferred from the shared source) as part of `run_layers_discoverability_fix.js`'s own Viewer-mode section.

- Cache version bumped: `utzline-viewer-cache-v45.10`.

**v45.9 (2026-09-23, same day):** see the editor's own README for the full detail — same shared `source.html`, so this app gets the identical change. Andrew's "manufacture status" pipeline splits again: a new "machined" stage (⚙️, rank 3) now sits between `"in_manufacture"` and `"manufactured"`, written by a brand-new sibling app, Machine Schedule; every rank at or above the old `"manufactured"` shifts up by one (manufactured → 4, delivered → 5, installed → 6). This app never writes any of these stages, but renders the same shared status badge as the editor, so its rank/icon tables needed the identical renumbering to stay correct.

**v45.8 (2026-09-23):** see the editor's own README for the full detail — same shared `source.html`, so this app gets the identical change. Andrew's job-note request ("when a joinery item gets a job note (not shop drawing) it should update the joinery status to in manufacture in the joinery register") applies here too, since the Viewer is where "Add job note" actually lives (Site Measure only ever kept "View job note" — see the v45.7 entry above). Job note filenames also moved their timestamp from the front to the end of the name, same as the editor.

**v45.7 (2026-09-23):** three requests from Andrew, sent together with two
screenshots (verbatim): (1) "site measure app needs the correct user
selector in the startup menu, same way that [UTZLINE Delivery ITP] has,
also needs to be removed from the top menubar in the floor plan as well as
the rename button (see photos)"; (2) **"Utzline viewer no longer needs the
my projects option as everything runs through the projects folder
ecosystem"**; (3) "implement the username as per the delivery itp
throughout the entire system, but instead of it opening a popup, the
button is the selector, when you pick a name it opens a numberpad to input
the pin (4 digit pin)."

- **Fixed a real, long-standing bug found while packaging this release**:
  this app's browser tab title/taskbar tooltip has said "UTZLINE Site
  Measure" since it was first split into its own app — `build.py` only ever
  inserted the PWA head tags after the shared `source.html`'s `<title>`
  line, never actually overrode the title text itself for this build (even
  though `manifest.json`/`apple-mobile-web-app-title` already correctly say
  "UTZLINE Viewer"). `build.py` now replaces it too; the editor's own
  build.py is untouched since "UTZLINE Site Measure" is correct there.
- **"My projects" removed in full** (request #2, this app's own headline
  change this release) — the personal, per-device, multi-location project
  list added back in v27 (`#gateMyProjects`, "+ Add a project…", the
  screen reading "Nothing added yet — use '+ Add a project' below to bring
  one in from anywhere on this device") is gone entirely: the HTML section,
  its CSS, `loadMyProjects`/`saveMyProjects`/`populateMyProjectList`/
  `openMyProjectEntry`, the `addMyProjectBtn` click handler, and the
  `showGateState()` gating that used to show/hide it. The per-device
  `viewerMyProjects` IndexedDB entry from before this release is simply
  never read again (nothing prunes it, but nothing reads it either — a
  harmless orphan). **Everything else about browsing a single Projects
  folder (Setup/Reconnect/List/Levels/Rooms, "Show hidden" for a
  press-and-held-away entry, shape-detection for picking a project's or a
  level's own folder directly) is completely untouched** — `viewerHiddenEntries`/
  `splitHiddenNames`/`hideEntryForViewerInstead` is a *separate* feature
  (hiding a normal list entry from just this device) that this request
  does not mention and was kept exactly as it was.
- **Shared name+PIN identity now lives here too, not just the editor.**
  See the editor's own README for the full design (ported verbatim from
  UTZLINE Delivery ITP — a native `<select>` IS the button, PIN entry via
  an on-screen numberpad, a new name via this app's own "type one thing"
  dialog). **Judgment call:** this used to be entirely absent from the
  Viewer (the old toolbar button was hidden here since the Viewer never
  saved); Andrew's own instruction to roll the new pattern out "throughout
  the entire system" is read here as no longer wanting that carve-out — a
  Viewer user may still sign delivery/install/manufacture checklists
  elsewhere on the same device, so knowing who's using it is worth showing
  even though this app itself never writes a project file. The new
  `#identityGateRow`/`#identitySelector` sit on the startup screen, in the
  same visual slot "My projects" used to occupy — visible above every gate
  state, immediately on load, with no folder needing to be chosen first
  (writing a new name is guarded with a toast if no Projects folder is
  chosen yet, since the shared `utzline-users.csv` registry lives inside
  one).
- `#userIdentityBtn` and `#fileNameBtn` (the old freeform "Set your name"
  button and the pencil "plan"/rename button) are gone from the floor-plan
  toolbar in this app too, same as the editor — see its README for the
  rename-button judgment call (the underlying `state.fileBase` mechanism
  is unchanged, only the button is gone).
- New regression test `run_identity_pin.js` (in `pdftest-projects/`) loads
  this app's own real production `index.html` bundle directly and confirms
  `#gateMyProjects`/`#myProjectListItems`/`#addMyProjectBtn` and the "My
  projects" heading text are gone entirely, while `#identityGateRow`/
  `#identitySelector` exist and are populated. `run_my_projects_list.js`/
  `run_my_projects_root_folder.js` (testing the now-removed feature) were
  deleted; see the editor's own README changelog for the rest of this
  release's test-suite detail (shared source, shared suite). Full
  regression suite re-run clean afterward: 82/86 passing, the same 4
  pre-existing environment-flake failures already documented in earlier
  versions' notes, none new.

This folder is the self-contained, installable **read-only viewer**
companion to **UTZLINE Site Measure**. It shares the exact same
underlying app code as the editor (see `source.html`'s own
`VIEW_ONLY_MODE` comment) — a mode flag read from the URL at load, not
a separate fork — but it is packaged here as its own completely
separate installable app: own name ("UTZLINE Viewer"), own icon (blue,
so it's easy to tell apart from the orange editor icon at a glance),
own `manifest.json`, and own offline cache. Installing it on Windows
(or any desktop) produces its own distinct taskbar/Start-menu/desktop
icon and its own window, separate from "UTZLINE Site Measure" — so a
drafting-office person can be given only this one, and they will never
see the editor's toolbar or be able to create/edit/delete anything.

It can open a project's folder to browse projects, levels, and rooms —
pan, zoom, view markups and dimensions, follow room-link markers, and
use Share/print — with every action that would create, edit, move, or
delete something blocked, both in the app's own logic (every actual
save/delete/insert/create function is a no-op in this mode) and at the
OS level (it only ever requests **read** permission on the folder you
pick, never write).

## How this relates to the editor app

Both apps are built from the one canonical source
(`/home/claude/redline-projects/source.html`) by near-identical
`build.py` scripts — this folder's own `build.py` is the same steps as
`redline-projects-pwa/build.py` (vendor the CDN libraries locally,
swap in local fonts, wrap in a full HTML document, register a service
worker), with the one meaningful difference being this app's
`manifest.json` sets `start_url` to `./index.html?viewer=1` — that's
what puts every launch of this installed app into read-only mode.
Whenever `source.html` changes, rebuild **both** apps
(`redline-projects-pwa/build.py` and this folder's `build.py`) from it,
and bump both service workers' `CACHE_NAME` (each already carries its
own running changelog at the top of `service-worker.js`, same
convention as the editor's).

## Getting this installed as its own Windows app

**This app lives in its own separate GitHub repository from the
editor** — not a `viewer/` subfolder of Site Measure's repo. Every app
in the UTZLINE family (Site Measure, Viewer, Install ITP, Manufacture
ITP, UTZLINE Projects, UTZLINE Scheduler, UTZLINE Delivery ITP) is its
own repo with its own GitHub Pages URL. (An earlier version of this
README described a shared repo with this app in a `viewer/` subfolder —
that's no longer how these are hosted.)

1. In this app's own repo, add every file from this bundle at the repo
   root (not inside a subfolder) — keep the `icons/` folder structure
   intact, same as the editor. It'll go live at that repo's own GitHub
   Pages URL.
2. Open that URL once in a normal browser tab while online (to let the
   service worker cache it for offline use).
3. Install it: Chrome/Edge's install icon in the address bar ("Install
   this site as an app"). Because it has its own `manifest.json`
   (different `name`/`start_url`/icons from the editor), Chrome and
   Windows treat it as a wholly separate app from "UTZLINE Site
   Measure" — its own tile/shortcut, its own icon, its own window.

## Updating this app

Same process every time a new build ships: unzip whatever's shared in
chat, upload the files into this app's own repo root (overwriting
existing ones, keeping `icons/` intact), commit, wait for GitHub Pages
to redeploy, then close and reopen the installed app to pick up the
change. **Bump the "Current version" line at the top of this README
(with a dated changelog entry) and `service-worker.js`'s `CACHE_NAME`
every single time a change ships** — both need to move together.

## Things worth knowing

- **This app never needs "readwrite" permission on anything.** The
  folder picker here always asks for read-only access — even if you
  say yes to a broader prompt by accident, every actual mutating
  function in the shared app code refuses to run while in this mode.
- **Picking a project's own folder, or even a single level's own
  folder, works too** — you don't have to pick the top-level "Projects"
  folder specifically. It detects what kind of folder you picked and
  lands you straight on the right screen (that project's level list, or
  a level's own canvas) instead of an empty or confusing list.
- **"Share" (and printing) work exactly as they do in the editor** —
  read-only mode only blocks things that would change a saved
  project's files, never viewing or exporting a copy of what's on
  screen right now.

## What's in this folder

- `index.html` — the app itself (identical app code to the editor's
  `index.html`; only ever differs in which URL launches it)
- `manifest.json`, `service-worker.js` — what makes this installable
  and offline-capable as its **own** app, separate from the editor
- `icons/` — this app's own blue-accented icon set, generated from the
  editor's orange originals so the two are easy to tell apart at a
  glance while still clearly being the same family/brand
- `jspdf.umd.min.js`, `svg2pdf.umd.min.js`, `pdf.min.js`,
  `pdf.worker.min.js`, `sans.woff2`, `mono.woff2` — bundled libraries
  and fonts (all local, no CDN), same as the editor
- `build.py` — regenerates `index.html` from the canonical source;
  only relevant if you're working on the code directly rather than
  through chat
