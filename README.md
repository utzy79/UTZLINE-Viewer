# UTZLINE Viewer — installable app

**Current version: v104 (RC 1.0)** (kept in lockstep with the editor's own version, since both are built from the same `source.html` — bump this line, and add a dated changelog entry, every time a new build ships; v40 through v45.6 shipped without this line being kept in sync — see `next-version-notes.md` in the project, or the editor's own README, for the full per-version detail of that stretch. The only change specific to v45.6 itself: the level-list exclusion gained the new `itp-delivery` folder.)

**v104 (2026-10-02): PINs scrambled, blank PIN = choose a new one.** PINs in `utzline-users.csv` are saved scrambled (`h1:<salt>:<sha-256>`), so the file no longer shows them. A plain PIN already in the file still works and is scrambled the next time an app saves the file. A BLANK PIN cell means reset: the next sign-in as that name asks for a new PIN (twice). Update every device before anyone adds a name: an older app cannot read a scrambled PIN. A 4-digit PIN can still be guessed from the file, so keep the file private in OneDrive too.


**v103 (2026-10-02): shorter rework folder names, archiving on.** Rework files now live in `Project Saves\RW\<item>.json`, their history in `Project Saves\RW Log\<item>\` and their PDFs in `PDFs\RW\`, shared by every app (was `Project Saves\UTZLINE ITP\Install ITP Rework`; the old folders are not read). Project archiving is ON: only the Projects app archives or restores (administrator PIN) and asks about projects unopened for 90 days; every other app drops archived projects from its lists (Dollar Summary still counts them).

**v101 (2026-10-02) — RC 1.0: Viewer plan buttons hidden when no plan is open, Home fixed.**

- **Viewer REWORKS panel: smaller, red / green.** Andrew: *"make these smaller (set by largest writing)"* and *"make them red if there is a rework logged, green if not"*. The buttons are only as wide as the longest label, and each is red when that person / group has outstanding reworks, green when not.
- **Rework PDF header fixed.** Andrew: *"builder logo to be half the size, company logo has disappeared, qr code to be smaller and go down the bottom of the page"*. The builder logo is half the size (at most 75 x 30 pt), the company logo is back (the shared rework module only looked for `company-logo.png` at the Projects root; it now looks in `logos/` first, like every other app), the QR is smaller (48 pt) and sits at the bottom right of page 1 above the footer line (page 1's text and first photo row stop above it), and the status tag no longer covers the "JOINERY REWORK" title. The three ITPs now pass the project's builder logo into the rework PDF too.
- **"Company logo -- set in the UTZLINE Projects app" row hidden.** Andrew: *"hide this on all apps now"*. (This app has a toolbar, not a title header, so there is no header logo here.)
- **Viewer: the plan buttons only show while a plan is open.** Andrew: *"all these buttons should be hidden when the rework register is open in viewer"* and *"anytime a plan is not open"*. On the project list, levels, rooms and any rework register or rework page the Viewer now shows only its name and **Home** (Share, Recolour, select / zoom / pan, layers, help and dark mode come back once a plan is open).
- **Home fixed:** *"keep the home button but fix it"* -- with a rework register or page open, Home used to act underneath it (the register stayed on top, so it looked dead). It now closes the register / page first and lands on the project list with the Reworks panel.

**v100 (2026-10-02) — RC 1.0: Viewer Reworks panel, Change folder pill, Sent to CNC, administrator PIN.**

- **Viewer: Machine shop and Factory managers buttons** beside the drafters in the Reworks panel (each with its outstanding count), and an *Or flag to* row on the rework page. A rework flagged to a group is taken off its drafter.
- **Viewer (and Site Measure) home screen: Reworks panel.** Andrew: *"viewer should look like this"* (mock-up) + *"those buttons open teh rework, within that they can chose to change the drafter, mark as sent to cnc, add comments"*. In the **Viewer** the home screen is now two columns (the project list as before on the left): on the right a **REWORKS** button and one button per drafter, each with how many reworks are waiting on that drafter (a *Not flagged to a drafter* button when some aren't flagged yet). A button opens the reworks (the register, filtered to that drafter); open one and the drafter can **pass it to another drafter**, mark it **Sent to CNC** (typing the **file name** sent), mark it **Not required**, add comments, and print / share its PDF. Reworks are read in the background for the listed projects only (never their photos). Site Measure doesn't show the panel. The Viewer asks for write permission only when a drafter actually saves something. **Change folder** is now the small bottom-right pill (the in-page "Use a Different Projects Folder" / "Choose a Different Folder" buttons are gone).
- **Change folder needs an administrator PIN, in every app.** Andrew: *"to chose another folder you must enter an administrator pin (on any app)"* and *"Andrew Utz will be the Administrator for now, but possibility to change it later"*. Pressing Change folder now opens a small numberpad that only an administrator's PIN (checked against `utzline-users.csv`) will pass, then the folder picker. The administrators are the names in `utzline-admins.json` at the Projects root (`{"admins":["Andrew Utz"]}`); until that file exists it is just Andrew Utz, and it can be changed later by editing that one file. If the user list can't be read (no folder, permission lapsed, file gone) or no administrator has a PIN in it, the change is allowed so nobody is ever locked out of a lost folder. Reconnect Folder (same folder) is unchanged.
- **"Sent to CNC"** (was "Resent to CNC"): the drafter's answer reads *Sent to CNC* on screen, in the log and in PDFs (the stored value is unchanged, so old reworks still read correctly). Andrew: *"drafter needs an option to mark the rework as Sent to CNC"* and *"and add a filename"* -- the drafter can type the **file name** sent to the CNC beside it; it is kept with the answer and shown in the log, the drafter line and the rework PDF.
- **Rework module:** new event kind `cutNotRequired` (Machine Schedule) and the file name on `drafterAction`; every app reads and shows them even where it can't write them.

**v99 (2026-10-02) — RC 1.0: each rework has its own QR code, rework PDF header redesigned.**

- Andrew: *"can each rework have its own qr code"*. Every rework's own PDF now carries **its own QR code** (top right of page 1, "Scan to open this rework"). Which ITP it opens follows the rework's **stage** (Andrew: *"it will depend on the status"*, then *"also need to think about the manufacture itp"* -> *follow the stage*): logged or cut -> **Manufacture ITP**, complete / ready to deliver -> **Delivery ITP**, delivered or closed out -> **Install ITP**. The link names the rework (`&a=rework&w=<id>`; spaces as `+` so the code stays small). A hosted copy opened from a phone-camera link hands off to the right ITP by the rework's stage (`&h=1` stops it going back and forth); the app's own Scan button just opens it where it is. The item's Rework screen opens with **that rework's card scrolled into view and ringed**. Shared `UtzQr.reworkLink / reworkApp` and `UtzRework` (every app that makes a rework PDF now carries the QR module).
- **Rework PDF header like Andrew's picture**: company logo top left, the builder's logo and — always — the **UTZLINE logo** top right (the UTZLINE mark in every app, no longer the app's own icon), the rework's QR in the gap between them, the title row and status tag underneath. With no company logo the QR makes the header a little taller, so the first row of photos may start on page 2.

**v98 (2026-10-02) — RC 1.0: the kind of drawing is in the file name.**

- Andrew: *"change JN to IFC"*. Job notes are now **IFC** (Issued For Construction): the folder under `PDFs\` is `PDFs\IFC\<Level>\<Room>\<item>\` (was `PDFs\JN\...`), and a job note's file name carries ` -- IFC -- ` (`<project> -- IFC -- <room> - <code> - <saved>.pdf`; Site Measure and the Scheduler write them). Shared folder code (`UtzItemFiles`), so every app reads the same place; the code still says "JN" internally, only the folder and the tag read IFC. Old `PDFs\JN` folders are not read any more (Andrew: happy to lose old files as long as new ones work); his existing 3749 job notes were renamed and moved to `PDFs\IFC`.
- Andrew: "we need a way to decifer if a drawing is a check measure, job note etc ... like this 3749 - New Mount Barker Hospital -- CM -- H1.AH.010 - J.170 - 2026-10-02 15-40-29". Check measure PDFs are now `<project> -- CM -- <room> - <item> - <date>.pdf` (a room's own site measure `<project> -- CM -- <room> - <date>`, a level's plan `<project> -- CM -- <level> - <date>`) and job notes `<project> -- JN -- <room> - <item> - <date>.pdf`. The " -- CM -- " / " -- JN -- " (double dash) marks the kind and is easy to search for. Short on path (Windows' 260 limit) the project part gives way first (to 8 characters), then the room part, then the project altogether; the item code is never cut before them. Files already saved keep their names. Shop drawings keep "REV n - <date> - <code>.pdf" -- every app reads the revision order off the start of that name, so it is not changed here.
- Tests: `run_sm_code_only_names.js`, `run_v65_jobnote_for_construction.js`, `run_joinery_status_and_job_notes.js`, `run_viewer_item_picker_v94.js`.

**v97 (2026-10-02) — RC 1.0: job notes and shop drawings are in room folders (with every other app).**

- Andrew: "i want every room to have a folder, then the joinery within it", then "im happy to lose old files, as long as new ones work". Job notes are saved in `PDFs\JN\<Level>\<Room>\<item>\` and shop drawings in `PDFs\SD\<Level>\<Room>\<item>\<drawing>\[Returned|Approved]\`, next to the check measures in `PDFs\CM\<Level>\<Room>\<item>\`; the room folder is the room number, the level is cut to 30 (the same names as the event folders). The old `Project Saves\Job Notes` and `Project Saves\Shop Drawings` folders are no longer read (nothing moved or deleted). "Which items have a shop drawing" scans the new layout. The 260-character path check counts the new folders. Scheduler, Machine Schedule, Solid Surface, Projects, Dollar Summary and the three ITPs changed in the same round.
- Tests: `run_sm_code_only_names.js`, `run_v65_jobnote_for_construction.js`, `run_joinery_status_and_job_notes.js`, `run_shop_drawings.js`, `run_v64_shop_drawings_sent_returned.js` (A/B), `run_viewer_shop_drawing_upload_v81.js`, `run_viewer_item_picker_v94.js`.

**v96 (2026-10-02) — RC 1.0: every room has its own folder for check measure PDFs and backups.**

- Andrew: "i want every room to have a folder, then the joinery within it", "or even PDFs\", "and do the same with backups". Check measure PDFs now go to `PDFs\CM\<Level>\<Room>\<Joinery item>\` and backups to `Backups\CM\<Level>\<Room>\<Joinery item>\` (each folder still keeps its 10 newest backups). A room's own site measure is saved in `PDFs\CM\<Level>\<Room>\` (and `Backups\CM\<Level>\<Room>\`); a level's plan in `PDFs\CM\<Level>\`. The room folder is the room NUMBER, as in the file names; an item with no room goes in "No room". File names are unchanged (still `<project> - <room> - <item> - <date>`), so a PDF that is emailed on its own still says what it is.
- The old `PDF Files\UTZLINE Site Measure\` and `Backups\UTZLINE Site Measure\` folders are no longer written to and nothing in them is moved or deleted (the app never read them back). Job notes and shop drawings keep their present folders for now — moving them changes every app, so it is a separate step.
- The 260-character path check counts the new folders (for level plans as well as items).
- Tests: `run_sm_code_only_names.js`, `run_flat_structure_interop.js`, `run_sm_room_measure_v89.js` (new check N), `run_v46_site_measure_round.js`, `run_v54_check_measure.js`, `run_viewer_item_picker_v94.js`.

**v95 (2026-10-02) — RC 1.0 (Viewer):** job notes are named "Job note - <date> - <room number> - <code>.pdf" (room number only; dropped first if the path budget is short); see the editor's entry for the export names.

**v94 (2026-10-02) — RC 1.0 (Viewer):** Add shop drawing / Add job note ask which items in the room the file is for (plan preview + tick-list, opened item ticked; see the editor's entry above).

**v93 (2026-10-02) — RC 1.0 (Viewer):** kept in lockstep with Site Measure v93 (room tick-list plan preview — Site Measure only; nothing changes in the Viewer).

**v92 (2026-10-02) — RC 1.0 (Viewer):** kept in lockstep with Site Measure v92 (room tick-list starts unticked — Site Measure only; nothing changes in the Viewer).

**v91 (2026-10-02) — RC 1.0 (Viewer):** kept in lockstep with Site Measure v91 (room Save & exit tick-list — Site Measure only; nothing changes in the Viewer).

**v90 (2026-10-02) — RC 1.0 (Viewer): the right-click menu says which room is being site measured.** Bold "Site measure: ROOM <room>" under the item title, plus how many items the room's measure covers (same menu line as the editor).

**v89 (2026-10-02) — RC 1.0 (Viewer): one site measure per room.** Open site measure on any item of a room opens the room's page (the same page the editor makes), an item with no room keeps its own, and the joinery summary lists the room's site measures plus the item's older ones. Also carries the editor's v87–v88 changes that don't show in the Viewer (sharp crop, crop window lock).

**v86 (2026-10-02) — RC 1.0: a short marker menu on tablets and phones, and nothing can be added there.**

- **Anything that isn't Windows** (the Lenovo tablet, phones, iPads, Macs): the menu on a joinery marker is only **Open joinery summary**, **Open site measure**, **View job note** (only when the item has one) and **Cancel**. No Add shop drawing, no Add job note, and the other rows (View shop drawing, View rework, Open this room's own plan, Export floor plan) are gone from the menu -- the joinery summary still lists the shop drawings, job notes and reworks to open.
- The joinery summary has no **Add shop drawing** button and no drop box on these devices, and a PDF dropped on it is ignored. Nothing can be added from the Viewer there.
- **Windows is unchanged** (the Add rows are still on the Windows Viewer). Site Measure is untouched. The Viewer decides by the device it is running on, so there is nothing to switch.
- Site Measure is still v85: this change is in the shared source, so Site Measure's next build will carry the same version number.

**v85 (2026-10-02) — RC 1.0: logos folder, reversed Machined, drag and drop only, Viewer button ink.**

- **Company and builder logos live in a `logos` folder** at the Projects root (reads `logos/` first, falls back to the old root files; `logos` is never listed as a project; the "Get ready for offline" check and the sync note include it).
- **Reversed Machined**: the status honours the Machine Schedule's `statusRetract` event (the item page and summary show the earlier stage again; history shows the entry struck through, then "reversed").
- **Drag and drop only -- no file selector**: job notes, shop drawings (the Add dialog and the summary card) are drop boxes; Insert image takes a photo / PDF dropped on the plan or pasted with Ctrl+V (the picker only opens on a touch-only tablet, where nothing can be dropped, via a long press).
- **Viewer**: text on the bright dark-mode blue buttons (Save, Choose Projects Folder, Reconnect, Confirm, the layers badge) is now dark -- white on that blue was 2.3:1, "white writing on a pale background".

**v84 (2026-10-02) — RC 1.0: Share from the check measure, builder logo at the far right of the top bar, older measure layers off by default (and deletable in Site Measure), the Viewer draws the latest measure exactly like Site Measure, Viewer room list.**

- **Share** is always on the toolbar now (it used to appear only where the device has a share sheet). On a check measure it offers "Current view" (a picture) or "Full check measure (PDF)". Where there is no share sheet (the PC app) it saves the file to Downloads instead.
- **Builder logo** sits at the far right of the top bar, on the same row as the title, and stays at the right edge when the bar is scrolled sideways.
- **Layers:** the newest save is drawn on the page exactly as Site Measure draws its own marks (bold text, halo, original colours) whenever the page has no marks of its own (always in the Viewer); every older save is a hidden reference layer -- tick it in the layers panel to audit it (tinted, thin). When you have your own save on the page, every other layer starts hidden.
- **Delete an old layer (Site Measure only):** a Delete button beside each layer that a newer save has overwritten; asks first, one at a time, never offered for the newest, never in the Viewer. The file is moved into a "Deleted layers" folder inside the item's own folder, with a small record of who deleted it and when -- nothing is erased.
- **Viewer: Room list** button (on a level's plan): the rooms on the level, alphabetical, with item counts; tap one to go to it on the plan.
- Sign-in "Opening…" cover and the shared rework module's "Logged in" display re-inlined.

**v83 (2026-10-01) — RC 1.0: the plan pages travel with the job note; Export floor plan works from the summary; status line for shop drawings (Viewer).**

- **Job note + plan pages (Viewer).** Adding a job note now draws the two A3 landscape plan pages (the close-up around the item, then the whole level plan, each with the indicator and both QR codes) and puts them at the **end of the same PDF**, after the For Construction watermark (so they are never watermarked). One file, no separate plan export per item in the project folder (Andrew: *"dont duplicate that export per joinery item in the files"*). The note's `.json` carries `withPlan: true`. If the plan can't be drawn or joined, the note is saved as before and the popup says so.
- **"Print entire job note"** on the popup shown after a note is added (and on a manual floor plan export): prints the job note and its plan pages together. From a manual export it finds the item's newest job note and joins it to the pages (a note that already carries its plan pages is printed as it is; no note: the plan pages only). On a computer it opens the print dialog; on a tablet it opens the PDF for the viewer's own Print.
- **Export floor plan (A3 PDF)** no longer writes a file into `PDF Files/Floor Plans`; the popup offers Open / Share / Print.
- **Bug fixed: "Export floor plan" did nothing until you went back to the plan.** The popup (and the "Exporting…" toast) were being drawn *under* the joinery summary screen, so it looked dead until the summary was closed. They now sit above every full screen.
- **Plan exports show the indicator and joinery code, not the status icon** (Andrew: *"show the indicator and joinery code, but not the status icon"*).
- **Viewer joinery summary: a shop drawing status line** — a coloured pill (Sent / Returned to resubmit / Approved, with its REV, who and when) in the details list and at the top of the Shop drawings card, from the newest of the latest sent revision, returned copy and approved copy (the Scheduler has had this since v46).

**v82 (2026-10-01) — RC 1.0: the red "Add rework here" QR code on the plan PDF export.**

- **The plan PDF export** (Site Measure / Viewer) has a **second QR code**, dark red, at the bottom right of each page under the first one's column and labelled **Add rework here** (Andrew: *"another qr code that takes you to the add rework option ... bottom of the page under right aligned with the current one and a different colour (red if possible) labeled add rework here"*). It carries the same item link with `a=rework`; scanning it (or opening it from the phone's camera) in the Install or Delivery ITP goes straight to that item's Add rework screen; the Manufacture ITP (no rework screen) opens the item and says rework is added in the Install or Delivery ITP.

**v81 (2026-10-01) — RC 1.0: code-only file names -- joinery codes, not descriptions, in every file and folder name (path-limit round, fourth build).**

- Andrew: *"have a real good think about how we can minimise filepaths, maybe we need to lose the joinery descriptions and just have joinery codes. give me a solid solution"* -- then *"I have no actual current files so dont care if I need to start again"*. Every folder and file kept for ONE joinery item is now named by the item's **file name** -- its joinery code (e.g. `JG.33.1`; a second item with the same code is `JG.33.1 (2)`), chosen once by UTZLINE Projects when the item is made or imported and saved on the item in `joinery-items.json` (`fileKey`), never changed afterwards -- instead of `<Level> - <Room> - <Code>` (64 characters for the pilot's `Ground Floor - G.33 - Change Cubical & Patient Consent - JG.33.1`). The level, room and description stay inside the records and `joinery-items.json`, so every screen still shows them.
- Records carry the author's **initials** and a two-digit-year stamp (`JG.33.1 -- AU - 26-10-01 16-25-35-281 - set.json`, in a level folder cut to 30 characters); imported files are **renamed** on the way in (the name they came in with is kept in the record or the `.json` beside the file and is what the screen shows). On the pilot's own folder (88 characters) the longest path is now 121 of the 163 the project folder leaves -- project folders up to about 130 characters deep work.
- **Clean break:** nothing is read under the old long names. Set the project up again in UTZLINE Projects (it gives every item its file name when the project is opened) -- the other apps pick the names up from `joinery-items.json`.
- Viewer: Same names as Site Measure. **Shop drawings can be uploaded again in the Viewer** (top of the marker menu, and from the joinery summary), as `REV n - <saved> - <code>.pdf`, with Sent / Returned to resubmit / Approved.

**v80 (2026-10-01) — RC 1.0: path-limit round, third build — the folder-path banner only when records really can't fit.**

- Andrew, with the banner on screen (*"leaves only 163 for UTZLINE's own files (long room names need about 170)"*): *"what can we do, im already in the root folder for onedrive"*. 163 is plenty for the short record names -- his longest record is 150 characters after the project folder (141 with the shortest branch) -- the 170 was set for the old long names. The banner now shows only when the project's folder path leaves less than even the shortest names need (**135**), and then says so (*"even the shortest record names need about 135, so some records may not save"*).
- A record that does have to take a short `_hash` name (a very long room-and-code) is saved and read like any other, so it no longer raises the banner: the page gets a `utz-path-limit` event (why `fallback`) and a console note. The banner stays for a record that **can't** be saved at all.

**v79 (2026-10-01) — RC 1.0: path-limit round, second build — `~` is a name Chrome refuses; record names with initials and a two-digit year.**

- Andrew's first run of the morning build showed the banner with *"leaves only 0"*: Chrome's File System Access API refuses any file name containing a `~` (it treats the tilde as a reserved Windows character), so every probe file -- and the `~hash` fallback names -- would have been refused. The markers are `_` now (`_hash`, 9 characters) and the probe name has no tilde; measured on the pilot folder the real figure is 163.
- Andrew: *"change usernames to initials, year from 2026 to 26, remove milliseconds?"* -- a record's on-disk name part is now `<initials> - <yy-mm-dd hh-mm-ss-mmm> - <kind>.json` (`AU - 26-10-01 13-09-18-862 - set.json`; the record's body still carries the full name and time, every reader folds from the body). The milliseconds stay: two saves in the same second must never land on one name. Together with the level no longer in the name, the pilot's longest record is 141 characters after the project folder (was 166; it has 163).

**v78 (2026-10-01) — RC 1.0: Windows' 260-character path limit — records for long room names were being saved empty.**

- **Why:** Andrew: *"some get corrupted from the import ... then i cant change them in the schedule"* / Set schedule's *"Couldn't save this schedule"*. Windows limits a file's full path to 260 characters; the pilot project's folder path (`C:\Users\andrewu\OneDrive - Metro Joinery\UTZLINE Pilot\3756 - Jones Radiology Mt Barker\`, 88 characters) left a schedule record for a long room name at 253 in full -- Chrome's `<name>.crswap` swap file needs 7 more, so the file was created EMPTY and the save failed. 27 of the 72 schedule files in that project's Ground Floor folder were empty.
- **Event store (shared with every app that writes records):** a new record's name no longer repeats the level its folder names -- `UTZLINE Events/<Branch>/<Level>/<Room> - <Code> -- <name> - <stamp> - <kind>.json`; everything already on disk under the long name still reads. When even that doesn't fit, the empty file is removed and the record is kept under a 9-character `~hash` name, and a banner says why; on a PC the first write to a project measures what its folder path leaves and the banner shows early when it is under the ~170 characters long room names need (*move the Projects folder nearer the drive root, or shorten the project folder's name*). Update every device: an app on the previous version doesn't see records under the new short names.
- **Backups:** a level / room / joinery item's rolling backup snapshot is now `backup_<date>_<time>.utzline.json` (+ `.png`) inside its own folder under `Backups/UTZLINE Site Measure/` -- the name used to repeat the project and the item (219 characters after the project folder for a long room name: those snapshots were being left empty on PCs). Pruning orders old and new names by their date-time.
- **Job notes:** the original file name is kept to 40 characters in the saved name.
- Test: `pdftest-projects/run_event_store_path_limit.js` (a mock folder that behaves like Windows: Andrew's path, a deeper one, a hopeless one, and Linux).

**v77 (2026-10-01) — RC 1.0: a QR code on each page of the floor plan export — the ITP apps scan it to open the room / joinery item.**

- Andrew: *"can that viewer export also generate and apply a qr code on the page, that the delivery itp can scan to open the relevant room / joinery item"* → *"All three ITPs"*. Both A3 pages now have the UTZLINE mark in the top-right corner and, under it, a QR code in a strip down the right (with the plan fitted beside it) and a caption. The code holds a link to the Delivery ITP naming the project folder, level, room and joinery code (`https://utzy79.github.io/UTZLINE-Delivery-ITP/?utz=item&p=…&l=…&r=…&j=…`): **Scan QR code** in the Install, Manufacture or Delivery ITP (v52 / v30 / v31) opens that item's checklist in whichever app scanned it; a phone's own camera opens it in the Delivery ITP (web or the installed app) once its Projects folder is connected. Made offline with the shared QR encoder (`shared/qr/qr-code.js`, qrcode-generator by Kazuhiko Arase, MIT); error correction M, about 35 mm on the page.

**v76 (2026-10-01) — RC 1.0: Open joinery summary and Export floor plan (2 × A3) on the marker menu; the export offered after every job note.**

- Andrew: *"in viewer, when a shop drawing is uploaded, then give a popup option of export floor plan, this the gives a 2 page A3 pdf. the first page is a close up and the second page is a full plan, both with the indicator flags on there"* → *"my mistake, that should have been when job note is added. create the a3 plans"*, and *"the viewer to have the right click option to open the joinery summary"*.
- **After Add job note**: once the note's PDF is saved, a popup asks **Export floor plan** / Not now. (Shop drawings are still added in the Scheduler only — nothing changed there.)
- **Export floor plan (A3 PDF)** (also its own row on the marker menu, and a button on the summary): two A3 landscape pages — page 1 a tight close-up of the plan around the item, page 2 the whole level plan — both showing **only that item's indicator**, enlarged so it reads on paper (Andrew: *"the floor plans should only show that joinery item indicator, the zoomed in one needs to be closer"*, *"the indicators larger on the pdf export"*); every other marker is left off, the plan's own drawing stays. A header with project / level / room / code, who and when. Saved as `PDF Files/Floor Plans/<Level> - <Room> - <Code> - Floor plan - <name> - <date time>.pdf`; the done popup opens or shares it.
- **Open joinery summary** (right-click / long-press a marker): the same page the schedules and Projects have — Details (description, work order #, value, status with who / when and its history, required delivery, manufacture start + lead time), Cutting file / Notes / History (the shared cards; notes can be added here), Site Measure check measures, Shop drawings (Sent / Returned, Open each — read-only), Job notes, ITPs (Manufacture / Install / Delivery: answered, signed off by whom, what's still needed, photos, the PDFs), Rework (each entry's state and drafter; *Open reworks* goes to the reworks screen for this item), Delivery location (the Delivery ITP's pin snapshot), Sub orders (per type, Open each). *Open check measure* and *Export floor plan* buttons at the top.
- Test: `pdftest-projects/run_v76_viewer_summary_export.js`.

**v75 (2026-09-30) — RC 1.0: one-finger pan, blue markers 25% smaller, the room in the marker menu, the builder's logo, the drafter step on reworks.**

- Andrew: *"all floor plans should pan / zoom with the same functionality (1 finger scroll, pinch to zoom)"*: a one-finger drag on empty plan **pans** (a tap still opens what it always did).
- Andrew: *"all icons to have this fill colour as default"* + *"make the text and icons 25% smaller"*: plan markers are drawn the Site Measure way (white ring, black ring, **blue #0011ff** fill, whatever colour they were saved with), the dot and the label text 25% smaller (the status icon follows the dot).
- The marker's right-click / long-press menu now shows the **room** as well as the joinery ID (Andrew: *"these menus to show the room number also ... across all apps that have these popups on right click"*).
- The day / night button now shares its choice with the other UTZLINE apps on the device.
- **Builder's logo** beside the project name in the top bar (set up once per builder in UTZLINE Projects).
- **Drafter step on reworks** (Viewer; Andrew: *"under view reworks here put the names of the people that have uploaded drawings into this project, then in that flag the rework, within that rework the person can either mark it as resent to CNC, Not required ... or pass it onto another drafter"*): the reworks list names the drafters (the people the Scheduler has recorded uploading shop drawings into the project; the shared name list until then); a rework can be flagged to one; on the rework page the drafter answers **Resent to CNC**, **Not required** or **Pass on to…** -- each its own event file, shown in the status log everywhere and on the rework PDF ("Drafter").

**v74 (2026-09-30) — RC 1.0: sign in every time the app is opened (tablets and phones).**

- Andrew: *"on next update, when opening the apps, it should as[k] for you to login, currently it just loads to the last user that was logged in, some of these tablets will have multiple users (employees)"*. **On a tablet or phone the app now asks who is using it** -- a full-screen *Who's using this?* list (every name in `utzline-users.csv`, plus *+ Add a new name…*) each time the app is opened, and again when it has been in the background for **10 minutes or more**. Tap your name and enter your 4-digit PIN on the usual numberpad. The name saved on the device is only treated as "the last person" now; if another app on the device signs in as someone else, this one asks again when it comes back to the front. **A PC is unchanged** (it keeps the last user), and the PIN numberpad, the registry and the name stamped on saves are as before.

**v73 (2026-09-30) — RC 1.0: records are kept one folder per level — much faster on a tablet; less loaded at start.**

- Andrew: *"how can we speed up schedule loading on the app android"* / *"all are slow"*. Every status, schedule date, cut, solid-surface tick, cutting file and note is still one small file per change (nothing is ever rewritten), but they now go in **one folder per level** — `Project Saves/UTZLINE Events/<record type>/<Level>/`, each file named `<Level> - <Room> - <Code> -- <name> - <time> - <kind>.json` — instead of one folder per joinery item. A schedule now lists a handful of level folders instead of hundreds of item folders; on the tablet each folder costs about a quarter of a second.
- Records a project already has in the old item folders are still read, and both places are shown together (a record found in both counts once). UTZLINE Projects shows **Speed up this project** on a project that still has old folders and moves them — each record copied, checked, then its old copy removed.
- **Update every tablet and PC.** An app older than this one doesn't look in the level folders, so it won't see records written by this one — and only press *Speed up this project* once every device is updated.
- **The PDF tools load when they're first needed** (Andrew: *"Is there anything we can strip out to speed it up. Any bloat"*). jsPDF, svg2pdf and pdf.js used to load every time the app opened, about 1.2 MB of code parsed before anything showed; now it loads the first time a PDF is made or a PDF plan is opened. Offline it still comes from the app's own copy.


**v72 (2026-09-30) — RC 1.0: "Get ready for offline" is quick on Android.**

- Andrew: *"it has taken 10 minutes to "get ready for site""*. The check opened every file in the ticked jobs one at a time, and on his tablet each open takes about a quarter of a second. On an Android tablet OneSync / Dropsync keep a real copy of every file, so there's nothing to download: it now just checks the ticked jobs and the names & PINs list are on the tablet (a few seconds) and says so. **Open every file (slow)** in that dialog still does the full check. Windows laptops (OneDrive "online-only" files) still get the full check, which downloads what's missing.

**v71 (2026-09-29) — RC 1.0: shop drawing revisions start at REV 0.**

- Andrew: *"all revisions start at REV 0  Not REV A  It goes 0 A B C D E etc..."*. A drawing's revisions are put in the order they were saved and labelled by position: REV 0, then REV A, B, C … A first revision saved as "REV A" before today now shows as REV 0; nothing on disk is renamed. A returned copy shows the label of the revision it answers. The same in Projects, the Scheduler and Machine Schedule.

**v70 (2026-09-29) — RC 1.0: the factory for In manufacture, every time; every save retried.**

- **🏭 for a job note too.** Andrew: *"viewer is giving me different icond for in manufacture, some of it if th ehammer and spanner, others is the factory, i want the factory throguhout"*. A job note means In manufacture, but an item whose In manufacture step hadn't landed (the Scheduler's job-note bug, fixed in Scheduler v35) or that predates the rule showed 🛠️ on its marker. It now shows 🏭 like the rest.
- **Every save is retried and checked.** Status events, check measures, overlays, backups, the names list and saved PDFs / PNGs: each is read back to check its size, and the whole write is tried again 0.5 s and 1.5 s later if it fails. On Windows, a sync client or antivirus holding a brand-new file for a moment used to fail the save and leave a 0-byte file with nothing said.
- **No message says "still syncing?" any more.** It was a guess and usually wrong. Messages now say "couldn't read … just now".

**v69 (2026-09-29) — RC 1.0: Projects on this device.** Andrew: *"the onsite apps need an option fo rthe user to pick the projectas they are working on to minimise the syunc on their device"*.

- **Plan markers 20% smaller.** Andrew: *"on he next projects update, make the indicator dots about 20% smaller (and the icons)"*. Every marker dot on a plan, and the status icon over it, is drawn at 0.8 × its saved size. This matches UTZLINE Projects v42. Nothing saved changes, and tapping a marker still uses the full size.
- **Projects on this device.** The project list has a new bar at the top: **Choose my projects**. Tick the one or two jobs this device is working on. Then:
  - Only those are listed. The rest sit behind **Show the other N projects**, and the app reads nothing from them. On a Windows tablet with OneDrive, an online-only job is never downloaded by this app.
  - **How to sync only these** says exactly what to keep on the device: the files in the main folder itself (names & PINs, logo) and each ticked job. It covers OneDrive on Windows (Always keep on this device / Free up space) and OneSync / Dropsync on Android (sync only those folders).
  - **Get ready for offline** opens every file in the ticked jobs, plus the names & PINs list. Anything online-only is downloaded while there's internet. Anything that won't open is listed. The bar then shows "✓ Ready for offline — checked today 09:15".
  - A ticked job that isn't on the device shows as **not on this device yet**, not just missing.
  - The choice is kept on this device, per main folder, under the same key in every UTZLINE app. Nothing is written to the Projects folder. **Show all projects** in the chooser goes back to the full list.

**v68 (2026-09-29) — RC 1.0.**

- **The saved copy's title block** (Save PDF / PNG, backups, the current-view snapshot). Andrew: *"this text must be scalable, it comes out great on an a1 size but when smaller or shapshot it takes over the whole title block area, it needs to have the room name, joinery name and date time username, all in readabele. multi row text."*
  - It's now rows of text: **Joinery** code (larger), **Room**, level · project, then **Saved** date, time · who.
  - All of it is sized as a share of the copy's own width (2.4%, capped for very big sheets). A snapshot's block is the same proportion of the picture as an A1's, not the whole bar.
  - Each row shrinks a little to fit, then is cut with "…". The logo sits on the right, as tall as the rows.
- Same build as Site Measure's v68. Its new "Site measure not required", "Mark check measure complete?" and PDF-page crop are Site Measure only; the Viewer can't do those.

**v67 (2026-09-29) — RC 1.0.** Andrew: *"ok, now change them all to version RC 1.0. and have that on the logos (small)"*.

- The app is now **RC 1.0** (release candidate 1.0) across the UTZLINE family. A small **RC 1.0** tag sits beside the logo in the toolbar and on the start screen, and the quick-help tip reads "RC 1.0 (build v67)".
- The build number (v67) still counts up underneath, so installed copies pick up each update. It's also what the Windows installer "Setup RC 1.0" contains.

**v66 (2026-09-29):** **Timings removed.** Andrew: *"remove timings"*.
- The **⏱ Timings** button on the Projects screen and its list are gone. Nothing about opens is stored on the device any more, and the old list is cleared.
- **Plan PDFs read offline.** The pdf.js worker was still loaded from the internet (only `pdf.min.js` was local), so reading a plan PDF needed a connection. It now uses the local `pdf.worker.min.js`, which was already in the folder and precached.
- This build is also the one inside the new Windows installer.

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
