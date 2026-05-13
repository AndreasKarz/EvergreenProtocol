# Capture Workflows

This reference covers the operational side of the skill: turning free-form user input (dictated text, screenshots, forwarded messages) into Notion database entries. Read this when the user's task is capture or logging, not structure-building.

## The universal capture pattern

Whatever the input type, the same four steps apply:

1. **Identify the target data source.** Match the input to one known data source. Use the user's memory of the workspace, recent conversation context, or a quick `notion-search`. If there's more than one plausible target, ask once and remember the answer for the rest of the session.
2. **Fetch the schema.** Before writing properties, `notion-fetch` the target data source (or the database that contains it) to get the exact property names and types. Never assume — property names drift ("Due" vs "Due Date"), option lists vary.
3. **Map the input to properties.** Extract each field explicitly. When a field is ambiguous, prefer underfilling over guessing — empty properties are safer than wrong ones.
4. **Create and confirm.** Call `notion-create-pages` (or `notion-update-page` for updates), show the user the page URL and a one-line summary of what was captured.

The verbal confirmation matters. Users forward a screenshot and assume it worked; a short "created 3 entries in Biomarkers, here are the URLs" closes the loop.

## Voice / text capture

### Parsing dictated entries

Users speak in natural language. Expect patterns like:

- **Timestamp first:** "gestern 90 Min Zone 2 gelaufen, 10.2 km, Puls avg 138"
- **Timestamp embedded:** "Wiegen heute Morgen 82.4 kg, Waist 89"
- **Implicit timestamp:** "Magnesium Glycinate 400 mg vor dem Schlafengehen" (today, unless context says otherwise)
- **Batch:** "Einkauf: Spargel, Lachs, Avocado, Kaffee" (multiple items, one target DB)

### Resolving dates

German and English date language both appear:

| Phrase | Resolution (today = Apr 18, 2026) |
|--------|-----------------------------------|
| "heute" / "today" | 2026-04-18 |
| "gestern" / "yesterday" | 2026-04-17 |
| "vorgestern" | 2026-04-16 |
| "morgen" / "tomorrow" | 2026-04-19 |
| "letzte Woche Dienstag" | The Tuesday of the previous calendar week. Apr 18 is Sat → previous week's Tuesday is Apr 7. |
| "diesen Dienstag" | The Tuesday of the current week. Apr 14 if already past, else the upcoming Tue. |
| "Ende März" | Assume last day of March in the most recent March (2026-03-31). |
| "vor 3 Tagen" | 2026-04-15. |

When unsure, ask. Don't silently pick a date the user didn't specify.

If the user dictates a time with date ("heute 19:30"), set `date:<Prop>:is_datetime` to 1 and send `start` as `2026-04-18T19:30:00`. For Europe/Zürich users, don't append a timezone suffix unless you know it — Notion will use the user's workspace default.

### Mapping patterns

For a Workouts data source with properties `Date (date+datetime), Type (select), Duration min (number), Distance km (number), Avg HR (number), Notes (rich_text)`:

User says: "gestern 90 Min Zone 2 gelaufen, 10.2 km, Puls avg 138, fühlte mich gut"

Map to:

```json
{
  "Date": [via expanded keys],
  "date:Date:start": "2026-04-17T00:00:00",
  "date:Date:is_datetime": 1,
  "Type": "Zone 2",
  "Duration min": 90,
  "Distance km": 10.2,
  "Avg HR": 138,
  "Notes": "fühlte mich gut"
}
```

Note: if "Zone 2" is not an existing Select option, Notion will create it. If the user says "Cardio" but the options are "Zone 2 / HIIT / Strength / Mobility", pick the closest match and mention in the confirmation: "captured as Zone 2 — let me know if that's wrong". Don't silently invent new options for things that look like typos.

### The "which database?" decision

If you have multiple plausible targets (e.g. user says "log 82.4 kg" and both a "Weight" DB and a "Measurements" DB exist), ask:

> "Das passt zu 'Weight' oder 'Measurements (Biomarker: Gewicht)'. Welche meinst Du?"

Remember the answer for the conversation. Don't ask again for subsequent similar entries.

## Screenshot capture

You can see images natively. When the user pastes a screenshot, read it like a human would: identify the content type, extract the data points, then route to the right database.

### Common screenshot types

| Screenshot source | Typical target DB | What to extract |
|-------------------|-------------------|-----------------|
| Lab report (blood panel) | Measurements + Labs | Test name, value, unit, reference range, date. One row per test. |
| Fitness tracker (Garmin, Whoop, Oura ring summary) | Workouts, Sleep, Weight | Activity type, duration, avg/max HR, calories, distance, sleep stages, HRV, RHR. |
| Scale app | Weight | Date, weight, body fat %, waist. |
| Grocery receipt | Meals or Expenses | Date, items, amount. Usually better captured as a single Notes entry unless the user has a structured expenses DB. |
| Chat excerpt (WhatsApp, Slack) | Notes or Meetings | Date, participants, key points. Capture as rich_text, don't try to parse. |
| Calendar screenshot | Meetings or Events | Title, date+time, participants, location. |
| Recipe | Recipes or Meal plan | Title, ingredients, instructions. Usually a fresh page with Markdown content. |
| Product page / Article | Resources or Reading list | Title, URL (if visible), author, date saved, tags. |

### Lab report workflow (worked example)

User pastes a screenshot of a lab result and says "auf die Biomarker-Seite speichern".

1. **Fetch** the Measurements data source and the Biomarkers data source (the latter to match names).
2. **Read the image.** Identify each test: e.g. "LDL-C: 128 mg/dL (ref: <100)", "HDL-C: 62 mg/dL (ref: >40)", "ApoB: 92 mg/dL (ref: <80)", "HbA1c: 5.4 % (ref: <5.7)". Note the report date from the header.
3. **Match each test to an existing Biomarker row.** Use `notion-search` with `data_source_url` pointing at Biomarkers: `query: "LDL-C"` → get the page ID. If a biomarker isn't in the DB yet, create it first (with Category and Unit and reference ranges).
4. **Create one Measurement per test** under the Measurements data source, setting Biomarker (relation → the matched row), Value, Date (= report date), Lab (if visible on report), Notes (anything flagged out of range).
5. **Return a summary:** "Created 4 measurements under 2026-04-15. 1 flagged out of range: ApoB 92 > ref 80." Plus URLs to each measurement page or a filtered view.

### Fitness tracker workflow

User pastes a Garmin workout summary screenshot.

1. Identify the activity type from the screenshot (running, cycling, strength). Match to the Workouts `Type` select options; if no match, pick the closest and note it in the confirmation.
2. Extract duration, distance, avg/max HR, calories, elevation.
3. Extract the date from the timestamp in the screenshot (not "today" — use the date shown).
4. Create one Workout page with the extracted data. Put unparseable details in Notes.

### When the screenshot is ambiguous

If the screenshot content doesn't clearly map to any known database, ask:

> "Ich sehe [beschreibe kurz was im Screenshot ist]. Soll das in [Option A], [Option B], oder als Notiz auf einer Seite landen?"

Don't force-fit the content into a structure that doesn't match.

### Multi-row screenshots

Some screenshots contain lists (lab panels with 10 tests, a week of workouts, etc.). For these:

- Extract all rows first into an intermediate list.
- Show the user a brief summary ("Ich erfasse 10 Biomarker: LDL-C, HDL-C, ApoB, ...") and ask for confirmation before creating them all, especially if >5 rows.
- Use a single `notion-create-pages` call with the `pages` array containing all rows. This is atomic and fast.
- If any row fails validation (e.g. Biomarker relation can't be matched), report that row and continue.

## Idempotency and duplicates

Users re-forward things. Some patterns:

- **Check for duplicates before creating** when the data has a natural key. Weigh-in for today + a specific time → search Weight with `data_source_url` and filter for same date/time. If a match exists, offer to update instead of duplicate.
- **For tracker imports with timestamps**: the timestamp is usually unique enough. Make "Date+Time" effectively the natural key.
- **For named entities** (Biomarkers, Interventions, Projects): always search first. Never create a new Biomarker called "LDL-C" if one already exists.

When you detect a likely duplicate:

> "Ich sehe, dass für 2026-04-18 schon eine Weight-Messung mit 82.4 kg existiert ([URL]). Überschreiben oder trotzdem neuen Eintrag anlegen?"

## Batching

The `pages` array in `notion-create-pages` accepts up to 100 pages in one call. Use this whenever the user gives multiple items in one message. Batching is faster and atomic from the user's perspective. Don't loop one-by-one.

## Edge cases

- **Unit conversions.** If the screenshot has pounds and the DB is in kilograms, convert before storing. Mention the conversion in Notes or in the response: "182 lb → 82.6 kg".
- **Languages.** German screenshots are common. "Gesamtcholesterin" = "Total Cholesterol". "Nüchternblutzucker" = "Fasting Glucose". When in doubt, keep the source language in Notes and map the canonical English name to the Biomarker relation.
- **Blurry or partial screenshots.** If you can't read a value confidently, say so. Don't guess numbers. "Der HbA1c-Wert ist im Screenshot nicht klar lesbar — kannst Du ihn mir tippen oder ein schärferes Bild senden?"
- **Multiple pages per call.** If the user forwards a 3-page PDF export as screenshots, process them in order and create one atomic `notion-create-pages` batch with all extracted rows.
- **Private/sensitive data in screenshots.** If a screenshot contains data beyond what the user asked about (e.g. an email inbox with many conversations, only one of which is relevant), focus only on the requested entry. Don't log or reference the surrounding content.

## What a good capture response looks like

Short, concrete, linked:

> Erfasst: 4 Messungen zum 2026-04-15 in Measurements, zugeordnet zu LDL-C, HDL-C, ApoB, HbA1c. ApoB mit 92 liegt über dem Referenzbereich (<80) und ist in den Notes markiert.
>
> - [LDL-C · 128 mg/dL](https://notion.so/...)
> - [HDL-C · 62 mg/dL](https://notion.so/...)
> - [ApoB · 92 mg/dL ⚠️](https://notion.so/...)
> - [HbA1c · 5.4 %](https://notion.so/...)

Not: "I successfully created 4 database entries based on your lab results. Please let me know if you need any further assistance." — generic, no URLs, no useful summary.
