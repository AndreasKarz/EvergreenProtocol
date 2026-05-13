# Schema Patterns

Concrete patterns for building Notion databases via `notion-create-database` and `notion-update-data-source`. Read this when designing any non-trivial schema.

## Property types — the full list

Use these in a `CREATE TABLE (...)` statement. Column names go in double quotes, option lists use single quotes.

| Type | Syntax | Notes |
|------|--------|-------|
| Title | `TITLE` | Every data source needs exactly one. Name it what makes sense ("Name", "Task", "Entry", "Date"). |
| Plain text | `RICH_TEXT` | Stores formatted text. |
| Select (single) | `SELECT('Option A':blue, 'Option B':green)` | Colors: default, gray, brown, orange, yellow, green, blue, purple, pink, red. |
| Multi-select | `MULTI_SELECT('tag1':blue, 'tag2':pink)` | Same color list. |
| Status | `STATUS` | Like select but grouped into To-Do / In Progress / Complete. Default options are created automatically. |
| Number | `NUMBER` or `NUMBER FORMAT 'dollar'` | Formats: `number`, `number_with_commas`, `percent`, `dollar`, `euro`, `pound`, `yen`, `rouble`, `rupee`, `won`, `yuan`, `real`, `lira`, `rupiah`, `franc`, `hong_kong_dollar`, `new_zealand_dollar`, `krona`, `norwegian_krone`, `mexican_peso`, `rand`, `new_taiwan_dollar`, `danish_krone`, `zloty`, `baht`, `forint`, `koruna`, `shekel`, `philippine_peso`, `dirham`, `riyal`, `ringgit`, `leu`, `argentine_peso`, `uruguayan_peso`, `singapore_dollar`. |
| Date | `DATE` | Set values using the `date:<PropName>:start`, `date:<PropName>:end`, `date:<PropName>:is_datetime` keys. |
| Person | `PEOPLE` | Set with user UUID or comma-separated UUIDs. |
| Checkbox | `CHECKBOX` | Set with `"__YES__"` or `"__NO__"`. |
| URL | `URL` | If the column name is literally "URL" or "url", set it via `"userDefined:URL"`. |
| Email | `EMAIL` | |
| Phone | `PHONE_NUMBER` | |
| Files | `FILES` | File attachments. Cannot always be created by the connector; if upload is not exposed, ask the user to upload through the Notion UI. |
| Relation (one-way) | `RELATION('<data_source_id>')` | Target shows nothing back. |
| Relation (two-way) | `RELATION('<data_source_id>', DUAL)` | Back-reference auto-named (e.g. "Related to Tasks"). |
| Relation (two-way, named) | `RELATION('<data_source_id>', DUAL 'BackRef Name')` | Give the back-reference a meaningful name. |
| Rollup | `ROLLUP('<RelationProp>', '<TargetProp>', '<function>')` | See Rollup section. |
| Formula | `FORMULA('<expression>')` | Notion formula language, see Formula section. |
| Unique ID | `UNIQUE_ID` or `UNIQUE_ID PREFIX 'PRJ'` | Auto-incrementing identifier with optional prefix. Max one per data source. |
| Created time | `CREATED_TIME` | Auto-filled. |
| Last edited time | `LAST_EDITED_TIME` | Auto-filled. |
| Created by | `CREATED_BY` | Auto-filled user. |
| Last edited by | `LAST_EDITED_BY` | Auto-filled user. |

You can append `COMMENT 'description'` to any column to add a property description that shows up in the Notion UI.

## Rollup functions

When you add a `ROLLUP('RelationProp', 'TargetProp', 'function')`, the valid functions are:

- `count_all`, `count_values`, `count_unique_values`, `count_empty`, `count_not_empty`
- `percent_empty`, `percent_not_empty`
- `sum`, `average`, `median`, `min`, `max`, `range`
- `earliest_date`, `latest_date`, `date_range`
- `show_original`, `show_unique`
- `checked`, `unchecked`, `percent_checked`, `percent_unchecked`

Example: the count of active tasks linked to a project:

```sql
"Active Tasks" ROLLUP('Tasks', 'Status', 'count_not_empty')
```

## Formulas

Notion formulas use Notion's own formula language (not SQL, not JavaScript). Common patterns:

```sql
-- Days until due
"Days Until Due" FORMULA('if(prop("Due Date"), dateBetween(prop("Due Date"), now(), "days"), "")')

-- Overdue flag
"Overdue" FORMULA('if(and(prop("Due Date") < now(), prop("Status") != "Done"), "⚠️ Overdue", "")')

-- Progress percentage from done-count rollup
"Progress" FORMULA('if(prop("Total Tasks") > 0, prop("Done Tasks") / prop("Total Tasks"), 0)')

-- Age in days
"Age" FORMULA('dateBetween(now(), prop("Created"), "days")')

-- Priority score combining urgency and importance
"Score" FORMULA('prop("Urgency") * 2 + prop("Importance")')
```

Key functions: `prop("Name")`, `if(cond, a, b)`, `and()`, `or()`, `not()`, `empty()`, `dateBetween(a, b, "days"|"weeks"|"months")`, `dateAdd(date, n, "days")`, `formatDate(d, "YYYY-MM-DD")`, `now()`, `length()`, `slice()`, `replace()`, `test(str, regex)`, `format()`, `toNumber()`, `round()`, `ceil()`, `floor()`.

When the user asks for a formula, try to express the intent first in plain English, then write the formula. If it's more than ~120 characters or involves 3+ nested if-statements, create a simpler version first and iterate.

## Relation patterns

### One-way relation

```sql
CREATE TABLE (
  "Name" TITLE,
  "Project" RELATION('<projects_data_source_id>')
)
```

Only the Tasks side shows the Project link. Projects don't see Tasks. Use this when the back-reference would be noise (e.g. a Contacts database where tasks reference a contact but the contact page shouldn't be cluttered with task links).

### Two-way relation (DUAL)

```sql
CREATE TABLE (
  "Name" TITLE,
  "Project" RELATION('<projects_data_source_id>', DUAL 'Tasks')
)
```

Tasks see the Project; Projects see a "Tasks" back-reference. This is the default for most cross-database links.

### Self-relation — two-step build

You cannot create a self-relation in the initial `CREATE TABLE` because the data source ID doesn't exist yet. Build in two steps:

**Step 1:** Create the database without the self-relation.

```json
{
  "title": "Tasks",
  "schema": "CREATE TABLE (\"Name\" TITLE, \"Status\" STATUS)"
}
```

Capture the returned data source ID from the `<data-source url="collection://...">` tag. Call it `DS_ID`.

**Step 2:** Add both sides of the self-relation with `notion-update-data-source`:

```json
{
  "data_source_id": "DS_ID",
  "statements": "ADD COLUMN \"Parent Task\" RELATION('DS_ID', DUAL 'Subtasks' 'subtasks'); ADD COLUMN \"Subtasks\" RELATION('DS_ID', DUAL 'Parent Task' 'parent_task')"
}
```

The four-argument form `DUAL 'synced_name' 'synced_id'` is only required for self-relations — the synced_id links the two sides explicitly.

## Common multi-database layouts

### Minimal project/task

Two databases. Most useful starting point.

- **Projects** — Name (title), Status (select), Area (multi-select), Due (date), Owner (people), Notes (rich_text).
- **Tasks** — Name (title), Status (status), Priority (select), Due (date), Project (relation → Projects, DUAL 'Tasks'), Done (checkbox).

Add to Projects: `"Open Tasks" ROLLUP('Tasks', 'Done', 'unchecked')` for a live count.

### PARA (Tiago Forte)

Four databases with relations flowing Areas → Projects → Tasks, and Resources as a side reference.

- **Areas** — Name (title), Description (rich_text), Active (checkbox).
- **Projects** — Name, Status (status), Area (relation → Areas, DUAL 'Projects'), Due (date).
- **Tasks** — Name, Status (status), Project (relation → Projects, DUAL 'Tasks'), Due (date), Priority (select).
- **Resources** — Name, Type (select: Article/Video/Book/Tool), Area (relation → Areas, DUAL 'Resources'), URL, Read (checkbox).

Add a single top-level page "PARA Dashboard" with linked database views pointing at filtered slices (Active projects, Today's tasks, Unread resources).

### Health / longevity tracking (EverGreen-style)

Aimed at tracking interventions, inputs, and outcomes over time.

- **Biomarkers** — Name (title), Category (select: Metabolic, Cardiovascular, Inflammation, Hormonal, Renal, Hepatic, Hematology, Nutritional, Cognitive), Unit (rich_text), Ref Low (number), Ref High (number), Measurements (two-way relation → Measurements).
- **Measurements** — Date (date, title replaced by formula for title display), Biomarker (relation → Biomarkers, DUAL 'Measurements'), Value (number), Lab (select), Notes (rich_text).
  - Title strategy: set title = rich_text auto-composed via formula "Biomarker + date". Or use a `UNIQUE_ID PREFIX 'M'` and let the title be an explicit short label.
- **Labs** — Date (date + is_datetime 0), Lab (select), Panel (select: Basic, Extended, Advanced, Custom), Cost (number FORMAT 'franc'), Measurements (relation → Measurements, DUAL 'Lab'), Notes.
- **Interventions** — Name (title), Type (select: Supplement, Medication, Practice, Diet, Exercise Protocol), Start (date), End (date, optional), Active (formula based on End), Dose (rich_text), Frequency (select), Rationale (rich_text), Source (URL).
- **Workouts** — Date (date+datetime), Type (select: Strength, Zone 2, HIIT, Mobility, Walk), Duration min (number), Distance km (number), Avg HR (number), Max HR (number), Notes.
- **Sleep** — Date (date), Total sleep min (number), Deep min (number), REM min (number), HRV ms (number), RHR (number), Score (number 0-100).
- **Weight** — Date (date), Weight kg (number), Body fat % (number), Waist cm (number).
- **Meals** — Date+time (date+datetime), Type (select: Meal, Fasting Start, Fasting End), Items (rich_text), kcal (number), Protein g (number).

Formulas worth adding to Interventions:
- `"Active Today" FORMULA('if(empty(prop("End")) or prop("End") >= now(), true, false)')`
- `"Duration Days" FORMULA('if(empty(prop("End")), dateBetween(now(), prop("Start"), "days"), dateBetween(prop("End"), prop("Start"), "days"))')`

### August Bradley PPV (Pillars, Pipelines, Vaults)

More complex — skip unless the user specifically asks. Summary: Pillars = high-level life areas, Pipelines = flow databases (Projects, Tasks), Vaults = reference stores (Notes, Resources). The gimmick is heavy cross-relations and a central Dashboard. This pattern rewards expert users but overwhelms newcomers.

## Naming conventions

- Database names: singular-collective noun, no emoji in the *name* (the icon carries that). "Task" or "Tasks" — pick one and be consistent. Common: "Tasks", "Projects", "Areas", "Goals", "Notes", "Resources", "Habits", "Meetings", "People", "Meals", "Workouts", "Measurements", "Biomarkers", "Interventions", "Labs".
- Property names: Title Case, readable. "Due Date" not "due_date". "Start Date" not "start". "Avg HR" is fine if it saves column width.
- Option values: consistent casing per property. Status is usually "Not started / In progress / Done" or a variant.
- Icons: pick one good emoji per database; don't mix styles. For health tracking: 💪 Workouts, 🩺 Labs, 🧪 Biomarkers, 💊 Interventions, 😴 Sleep, ⚖️ Weight, 🍽️ Meals.

## Anti-patterns to avoid

- **Over-normalization.** Don't create a "Task Status" database just to hold the four status values. Use a Status or Select property. Reserve separate databases for things that need their own properties (a Project is a real thing; a Status option isn't).
- **Deep nesting.** Notion supports page-inside-page, but relations scale better than hierarchy for anything past two levels.
- **The "one mega-database" trap.** Don't put Tasks, Projects, Goals, and Notes all into one "Items" database with a Type property. Views won't save you — the property unions become unmanageable.
- **Orphan relations.** Every relation should have a purpose. If you add a relation and don't immediately think of at least one rollup or one view that uses it, reconsider.
