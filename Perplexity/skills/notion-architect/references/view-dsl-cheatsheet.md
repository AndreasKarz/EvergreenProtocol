# View DSL Cheatsheet

The `configure` parameter on `notion-create-view` and `notion-update-view` accepts a small DSL. This file is the quick reference — read it when creating or modifying views.

## The view types at a glance

| Type | Mandatory directive | Use for |
|------|---------------------|---------|
| `table` | — | Spreadsheet-like list. Default for most databases. |
| `list` | — | Clean vertical list, hides most properties. |
| `board` | `GROUP BY "<Prop>"` | Kanban. Property must be select, status, or multi-select. |
| `calendar` | `CALENDAR BY "<DateProp>"` | Monthly/weekly calendar keyed on a date property. |
| `timeline` | `TIMELINE BY "<Start>" TO "<End>"` | Gantt-style. Needs two date properties (or one date range). |
| `gallery` | — | Card grid. Use `COVER "<Prop>"` for cards with images. |
| `form` | — | Public/private form for row submission. |
| `chart` | `CHART <type>` | Bar, column, line, donut, or number summary. |
| `map` | `MAP BY "<PlaceProp>"` | Pins on a map from a place property. |
| `dashboard` | — | Aggregation of other views + charts. Configure via UI after creation. |

## Directives

Stack multiple directives in the `configure` string separated by semicolons:

```
FILTER "Status" = "In Progress"; SORT BY "Due Date" ASC; SHOW "Name", "Status", "Due Date"
```

### FILTER

Operators: `=`, `!=`, `<`, `>`, `<=`, `>=`, `CONTAINS`, `DOES NOT CONTAIN`, `IS EMPTY`, `IS NOT EMPTY`, `STARTS WITH`, `ENDS WITH`.

Combine with `AND`/`OR`:

```
FILTER "Status" = "Active" AND "Due Date" <= TODAY()
FILTER "Priority" = "High" OR "Priority" = "Urgent"
FILTER "Owner" IS NOT EMPTY AND "Done" = false
```

Date literals: `TODAY()`, `NOW()`, `TOMORROW()`, `YESTERDAY()`, `THIS_WEEK()`, `LAST_WEEK()`, `NEXT_WEEK()`, `THIS_MONTH()`, `LAST_MONTH()`, `NEXT_MONTH()`, `THIS_YEAR()`, `LAST_N_DAYS(7)`, `NEXT_N_DAYS(14)`, or ISO strings `'2026-04-18'`.

Checkbox filters: use `= true` or `= false`.

### SORT BY

```
SORT BY "Due Date" ASC
SORT BY "Priority" DESC, "Due Date" ASC
```

### GROUP BY

Only one group property.

```
GROUP BY "Status"
GROUP BY "Area"
```

For boards, this is mandatory.

### SHOW / HIDE

Lists visible properties. `SHOW` whitelists; `HIDE` subtracts from the default-all.

```
SHOW "Name", "Status", "Due Date", "Owner"
HIDE "Created By", "Last Edited Time"
```

### COVER

For gallery views, picks the image source:

```
COVER "Cover Image"        -- a files property
COVER PAGE_COVER            -- the page's own cover image
COVER PAGE_CONTENT          -- first image block inside the page
```

### WRAP CELLS / FREEZE COLUMNS

Table-only cosmetics:

```
WRAP CELLS true; FREEZE COLUMNS 1
```

### CLEAR

Used with `notion-update-view` to remove settings:

```
CLEAR FILTER
CLEAR SORT
CLEAR GROUP BY
```

Combine with new directives: `CLEAR FILTER; SORT BY "Name" ASC`.

## Chart configuration

```
CHART <type> [AGGREGATE <fn>] [COLOR "<Prop>"] [HEIGHT <n>] [SORT <how>] [STACK BY "<Prop>"] [CAPTION "<text>"]
```

- `<type>`: `column`, `bar`, `line`, `donut`, `number`.
- `AGGREGATE`: `count`, `count_unique`, `sum`, `average`, `min`, `max`, `median`.
- `COLOR "<Prop>"`: slice by a select/status property for colored segments.
- `HEIGHT`: chart height in UI units (1–4).
- `STACK BY "<Prop>"`: stacked bar/column segmentation.
- For `number`: displays a single aggregate. Pair with FILTER for things like "Tasks done this week".

Examples:

```
-- Tasks completed per week
CHART column; GROUP BY "Week"; AGGREGATE count; FILTER "Done" = true

-- Weight over time
CHART line; GROUP BY "Date"; AGGREGATE average; SORT BY "Date" ASC

-- Intervention types donut
CHART donut; GROUP BY "Type"; AGGREGATE count

-- Single-number KPI
CHART number; AGGREGATE count; FILTER "Status" = "In Progress"; CAPTION "Active projects"
```

## Form configuration

```
FORM CLOSE | OPEN                      -- disable/enable submissions
FORM ANONYMOUS true | false            -- allow anonymous submissions
FORM PERMISSIONS none | reader | editor -- what submitters can do with their entry afterwards
```

Example — a public health-journal form:

```
FORM OPEN; FORM ANONYMOUS false; FORM PERMISSIONS reader; SHOW "Date", "Mood", "Notes"
```

## Full worked examples

### Today's tasks (table)

```json
{
  "database_id": "<tasks_db_id>",
  "data_source_id": "<tasks_ds_id>",
  "name": "Today",
  "type": "table",
  "configure": "FILTER \"Due Date\" <= TODAY() AND \"Done\" = false; SORT BY \"Priority\" DESC, \"Due Date\" ASC; SHOW \"Name\", \"Priority\", \"Project\", \"Due Date\""
}
```

### Active projects board by status

```json
{
  "database_id": "<projects_db_id>",
  "data_source_id": "<projects_ds_id>",
  "name": "Pipeline",
  "type": "board",
  "configure": "GROUP BY \"Status\"; FILTER \"Archived\" = false; SORT BY \"Due Date\" ASC"
}
```

### Workout calendar

```json
{
  "database_id": "<workouts_db_id>",
  "data_source_id": "<workouts_ds_id>",
  "name": "Calendar",
  "type": "calendar",
  "configure": "CALENDAR BY \"Date\""
}
```

### Goals timeline

```json
{
  "database_id": "<goals_db_id>",
  "data_source_id": "<goals_ds_id>",
  "name": "Roadmap",
  "type": "timeline",
  "configure": "TIMELINE BY \"Start\" TO \"End\"; GROUP BY \"Area\""
}
```

### Biomarker trend chart

```json
{
  "database_id": "<measurements_db_id>",
  "data_source_id": "<measurements_ds_id>",
  "name": "ApoB Trend",
  "type": "chart",
  "configure": "CHART line; FILTER \"Biomarker\" = \"ApoB\"; GROUP BY \"Date\"; AGGREGATE average; SORT BY \"Date\" ASC; CAPTION \"ApoB over time\""
}
```

### Linked view on a dashboard page

Use `parent_page_id` instead of `database_id` to embed a filtered view inside another page:

```json
{
  "parent_page_id": "<dashboard_page_id>",
  "data_source_id": "<tasks_ds_id>",
  "name": "My Open Tasks",
  "type": "list",
  "configure": "FILTER \"Owner\" = @me AND \"Done\" = false; SORT BY \"Due Date\" ASC; SHOW \"Name\", \"Due Date\", \"Project\""
}
```

The `@me` literal resolves to the current user.

## Tips for building view sets

- When you create a database, make **3–5 views** minimum: the default table, a filtered "active" slice, a time-oriented view (calendar or timeline) if dates matter, a grouped view (board) for pipelines, and a chart if metrics matter.
- Name views by the question they answer. "Today", "This Week", "Overdue", "By Area", "Done last 30 days" — not "View 1", "View 2".
- Keep filter expressions readable. If a filter has more than 3 AND/OR clauses, consider whether the schema itself should help (e.g. add a formula property `"Is Actionable"` and filter on that).
- For dashboards: create a single dashboard page with linked views of each database, each filtered to show just the "actionable now" slice. Add a chart per database for trends.
