## Jira Data Model

- `inspect-jira` turns each Jira issue into one `Issue` and its status, resolution and assignee changes into `Event`s (`ingestion/src/ingestion/jira_issues.py`).
- `writer.write_jira_day` writes them as two parquet tables per day: `jira_issues` and `jira_events` (`ingestion/src/ingestion/writer.py`, `JIRA_ISSUES_SCHEMA` / `JIRA_EVENTS_SCHEMA`).
- Source: the Apache Jira REST API (`/rest/api/2`), `search` with `expand=changelog`, and `issue/<key>` when a changelog came back cut short.
- How the issues are fetched is in [Extract Jira](extract_jira.md).



## JIRA data returned

Each issue (specific JIRA e.g ARTEMIS-2435 ) has a set of fields associated and a list of comments. There is also a changelog which shows all the updates to the JIRA fields - such as change in status . 


## JIRA Snapshot

Whenever the JIRA is updated it will appear in the parquet file for that day - with the latest values. So if the extraction runs each day

```
Artemis-XXXX JIRA was created on 10 September 2026

Extraction picked up change on 11 September and placed in parquet file for 10 September

Artemis-XXXX JIRA changed status on 21 September 2026

Extraction picked up change on 22 September and placed in parquet for 22 September - with all current JIRA values

```

### Backfill behaviour

If we backfill for a year on the 2 November we see the lastest JIRA snapshot as of the 2 November. So for example:

```
Artemis-XXXX JIRA was created on 10 September 2026

Artemis-XXXX JIRA changed status on 21 September 2026

Artemis-XXXX JIRA changed status again on the 16 October 2026

We run the backfill on 2 Nov. Artemis-XXXX will be included in the 16 October only. If we want to see the history of status we need to look in the changelog  - captured in the JIRA_events

```

## JIRA_events

The JIRA events captures the change logs items. It also addes some context to the Item which is taken from the parent JIRA structure. 

JIRA events only record the change log items for the fields - "status", "resolution", "assignee". The other change log items are not tracked.


<div class="pipeline">
<style>
.pipeline{--ink:#788d79;--dark:#536b56;--paper:#fafaf2;--wash:#dce8d5;margin:30px 0;color:var(--dark)}
.pipeline svg{display:block;width:100%;height:auto;overflow:visible}
.pipeline svg path,.pipeline svg ellipse,.pipeline svg circle,.pipeline svg rect{vector-effect:non-scaling-stroke}
.pipeline .sketch{stroke:var(--ink);stroke-width:2.5;stroke-linecap:round;stroke-linejoin:round;fill:none}
.pipeline .light{stroke:#acbcaa;stroke-width:1.5;stroke-linecap:round;fill:none}
.pipeline .zone{stroke:#acbcaa;stroke-width:1.5;stroke-dasharray:6 8;fill:none}
.pipeline .section-bg{fill:var(--paper);stroke:none}
.pipeline .wash{fill:var(--wash);stroke:none}
.pipeline .zone-title{font-size:14px;letter-spacing:.12em;fill:#81917c;font-family:system-ui,sans-serif}
.pipeline .name{font-size:17px;fill:var(--dark);text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .msg{font-size:15px;fill:var(--dark);text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .msg,.pipeline .smallnote{paint-order:stroke;stroke:var(--paper);stroke-width:6px;stroke-linejoin:round}
.pipeline .arrow{stroke:var(--ink);stroke-width:2.2;fill:none;stroke-linecap:round;stroke-linejoin:round}
.pipeline .smallnote{font-size:12px;fill:#80917d;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .mono{font:13.5px ui-monospace,SFMono-Regular,Menlo,monospace;fill:var(--dark);white-space:pre}
.pipeline .type{font:12px ui-monospace,SFMono-Regular,Menlo,monospace;fill:#80917d;text-anchor:end}
.pipeline .pk{font-weight:700}
.pipeline .mono .dropped{fill:#a9b6a6}
.pipeline .start{text-anchor:start}
.pipeline .pipeline-caption{text-align:center;max-width:none;margin-top:12px;font:14px system-ui,sans-serif;color:#80917d}
@media (max-width:760px){.pipeline .scroller{overflow-x:auto;padding-bottom:8px}.pipeline .scroller svg{min-width:1100px}}
</style>
<div class="scroller">
<svg viewBox="20 20 1380 960" role="img" aria-label="Data model of the Jira extract. On the left, the Jira issue JSON returned by the REST API: key; fields with summary, issue type, status, resolution, resolution date, priority, created and updated, reporter, creator and assignee each with name and display name, components, and comment with its total and the full comments list (id, created and updated times, author, update author and body), which Jira returns but the extract does not keep; and changelog with total and histories, each history having an id, a created time, an author and items with field, from, fromString, to and toString. The fields map through _issue() to the jira_issues table, one row per issue, keyed by key. The changelog histories map through _events() to the jira_events table, one row per history item for status, resolution and assignee only, keyed by change_id and field. jira_events.key refers to jira_issues.key, one issue to many events.">
<defs><marker id="jd-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- Zones -->
<rect class="zone" x="30" y="30" width="560" height="920" rx="18"/>
<rect class="zone" x="770" y="30" width="620" height="920" rx="18"/>
<rect class="section-bg" x="43" y="44" width="150" height="26" rx="6"/>
<rect class="section-bg" x="783" y="44" width="160" height="26" rx="6"/>
<g class="zone-title"><text x="53" y="62">JIRA REST API</text><text x="793" y="62">PARQUET TABLES</text></g>

<!-- Jira issue JSON -->
<rect class="wash" x="62" y="92" width="496" height="40" rx="8"/>
<rect class="sketch" x="60" y="90" width="500" height="710" rx="10"/>
<text class="name" x="310" y="118">issue (search · issue/&lt;key&gt;)</text>
<g class="mono">
<text x="80" y="160">issue</text>
<text x="80" y="184">├─ key</text>
<text x="80" y="208">├─ fields</text>
<text x="80" y="232">│  ├─ summary</text>
<text x="80" y="256">│  ├─ issuetype.name</text>
<text x="80" y="280">│  ├─ status.name</text>
<text x="80" y="304">│  ├─ resolution.name</text>
<text x="80" y="328">│  ├─ resolutiondate</text>
<text x="80" y="352">│  ├─ priority.name</text>
<text x="80" y="376">│  ├─ created · updated</text>
<text x="80" y="400">│  ├─ reporter {name, displayName}</text>
<text x="80" y="424">│  ├─ creator  {name, displayName}</text>
<text x="80" y="448">│  ├─ assignee {name, displayName}</text>
<text x="80" y="472">│  ├─ components[].name</text>
<text x="80" y="496">│  └─ comment</text>
<text x="80" y="520">│     ├─ total</text>
<text class="dropped" x="80" y="544">│     └─ comments[]</text>
<text class="dropped" x="80" y="568">│        ├─ id · created · updated</text>
<text class="dropped" x="80" y="592">│        ├─ author · updateAuthor {name, displayName}</text>
<text class="dropped" x="80" y="616">│        └─ body</text>
<text x="80" y="640">└─ changelog</text>
<text x="80" y="664">   ├─ total</text>
<text x="80" y="688">   └─ histories[]</text>
<text x="80" y="712">      ├─ id · created</text>
<text x="80" y="736">      ├─ author {name, displayName}</text>
<text x="80" y="760">      └─ items[]</text>
<text x="80" y="784">         └─ field · from · fromString · to · toString</text>
</g>
<g class="smallnote">
<text class="start" x="250" y="544">returned by Jira, not kept</text>
<text class="start" x="60" y="830">lighter text: returned by Jira, dropped by the extract</text>
<text class="start" x="60" y="850">fields = FIELDS only · expand=changelog</text>
<text class="start" x="60" y="870">changelog.total &gt; len(histories) → fetched again with issue/&lt;key&gt;</text>
<text class="start" x="60" y="890">timestamps like 2026-10-15T06:23:48.000+0000 → converted to UTC</text>
</g>

<!-- jira_issues table -->
<rect class="wash" x="802" y="92" width="536" height="40" rx="8"/>
<rect class="sketch" x="800" y="90" width="540" height="465" rx="10"/>
<text class="name" x="1070" y="118">jira_issues · one row per issue</text>
<g class="mono">
<text class="pk" x="820" y="160">key  PK</text>
<text x="820" y="184">summary</text>
<text x="820" y="208">issue_type</text>
<text x="820" y="232">status</text>
<text x="820" y="256">resolution</text>
<text x="820" y="280">resolution_date</text>
<text x="820" y="304">priority</text>
<text x="820" y="328">created_at</text>
<text x="820" y="352">updated_at</text>
<text x="820" y="376">reporter_name</text>
<text x="820" y="400">reporter_display_name</text>
<text x="820" y="424">creator_name</text>
<text x="820" y="448">creator_display_name</text>
<text x="820" y="472">assignee_name</text>
<text x="820" y="496">assignee_display_name</text>
<text x="820" y="520">components</text>
<text x="820" y="544">comments_count</text>
</g>
<g class="type">
<text x="1320" y="160">string</text>
<text x="1320" y="184">string</text>
<text x="1320" y="208">string</text>
<text x="1320" y="232">string</text>
<text x="1320" y="256">string · null</text>
<text x="1320" y="280">timestamp[ms, UTC] · null</text>
<text x="1320" y="304">string · null</text>
<text x="1320" y="328">timestamp[ms, UTC]</text>
<text x="1320" y="352">timestamp[ms, UTC]</text>
<text x="1320" y="376">string · null</text>
<text x="1320" y="400">string · null</text>
<text x="1320" y="424">string · null</text>
<text x="1320" y="448">string · null</text>
<text x="1320" y="472">string · null</text>
<text x="1320" y="496">string · null</text>
<text x="1320" y="520">string · null</text>
<text x="1320" y="544">int32</text>
</g>

<!-- jira_events table -->
<rect class="wash" x="802" y="622" width="536" height="40" rx="8"/>
<rect class="sketch" x="800" y="620" width="540" height="300" rx="10"/>
<text class="name" x="1070" y="648">jira_events · one row per change</text>
<g class="mono">
<text x="820" y="690">key  FK</text>
<text class="pk" x="820" y="714">change_id  PK</text>
<text class="pk" x="820" y="738">field  PK</text>
<text x="820" y="762">changed_at</text>
<text x="820" y="786">author_name</text>
<text x="820" y="810">author_display_name</text>
<text x="820" y="834">from_id</text>
<text x="820" y="858">from_value</text>
<text x="820" y="882">to_id</text>
<text x="820" y="906">to_value</text>
</g>
<g class="type">
<text x="1320" y="690">string</text>
<text x="1320" y="714">string</text>
<text x="1320" y="738">string</text>
<text x="1320" y="762">timestamp[ms, UTC]</text>
<text x="1320" y="786">string · null</text>
<text x="1320" y="810">string · null</text>
<text x="1320" y="834">string · null</text>
<text x="1320" y="858">string · null</text>
<text x="1320" y="882">string · null</text>
<text x="1320" y="906">string · null</text>
</g>

<!-- Mappings -->
<path class="light" d="M528 176 H543 V524 H528"/>
<path class="light" d="M528 680 H543 V792 H528"/>
<g class="arrow" marker-end="url(#jd-arrowhead)">
<path d="M548 350 C650 350 680 330 788 330"/>
<path d="M548 736 C650 736 680 780 788 780"/>
<path d="M1342 155 H1372 V685 H1346"/>
</g>
<text class="msg" x="668" y="318">_issue()</text>
<text class="msg" x="668" y="735">_events()</text>
<g class="smallnote">
<text x="668" y="364">one row per issue · comment.total only</text>
<text x="668" y="798">one row per history item</text>
<text x="668" y="816">status · resolution · assignee only</text>
<text x="1372" y="420">1 : n</text>
</g>
</svg>
</div>
<p class="pipeline-caption">Jira issue JSON on the left, the two daily parquet tables on the right · jira_events.key → jira_issues.key, one issue to many events · files: JIRA_metrics/jira_issues/jira_issues_&lt;day&gt;.parquet and JIRA_metrics/jira_events/jira_events_&lt;day&gt;.parquet</p>
</div>

## jira_issues

One row per issue whose `updated_at` falls on the file's day. Key: `key`.

| Column | Type | From the Jira JSON | Notes |
|---|---|---|---|
| `key` | string | `key` | e.g. `ARTEMIS-5123`; the number after the dash sorts the rows |
| `summary` | string | `fields.summary` | |
| `issue_type` | string | `fields.issuetype.name` | Bug, Improvement, Task, … |
| `status` | string | `fields.status.name` | current status |
| `resolution` | string, null | `fields.resolution.name` | null while unresolved |
| `resolution_date` | timestamp[ms, UTC], null | `fields.resolutiondate` | |
| `priority` | string, null | `fields.priority.name` | |
| `created_at` | timestamp[ms, UTC] | `fields.created` | |
| `updated_at` | timestamp[ms, UTC] | `fields.updated` | decides which day's file the issue is in |
| `reporter_name` | string, null | `fields.reporter.name` | Jira user name |
| `reporter_display_name` | string, null | `fields.reporter.displayName` | |
| `creator_name` | string, null | `fields.creator.name` | |
| `creator_display_name` | string, null | `fields.creator.displayName` | |
| `assignee_name` | string, null | `fields.assignee.name` | null when unassigned |
| `assignee_display_name` | string, null | `fields.assignee.displayName` | |
| `components` | string, null | `fields.components[].name` | names joined with `", "`; null when none |
| `comments_count` | int32 | `fields.comment.total` | only the count, no comment text |

## jira_events

One row per changed field in the changelog of the issues in `jira_issues` for the same day. Key: `(change_id, field)`, since one history can change several fields at once (e.g. status and resolution).

| Column | Type | From the Jira JSON | Notes |
|---|---|---|---|
| `key` | string | issue `key` | → `jira_issues.key` |
| `change_id` | string | `changelog.histories[].id` | |
| `field` | string | `histories[].items[].field` | `status`, `resolution` or `assignee` only (`EVENT_FIELDS`) |
| `changed_at` | timestamp[ms, UTC] | `histories[].created` | |
| `author_name` | string, null | `histories[].author.name` | who made the change |
| `author_display_name` | string, null | `histories[].author.displayName` | |
| `from_id` | string, null | `items[].from` | status / resolution id, or user name for assignee |
| `from_value` | string, null | `items[].fromString` | readable value before the change |
| `to_id` | string, null | `items[].to` | |
| `to_value` | string, null | `items[].toString` | readable value after the change |

Rows are sorted by `changed_at`, then `change_id`, then field in the order status, resolution, assignee.

## Notes

- **Snapshots, not history:** each issue is written with its current state into the file of its `updated_at` day. An issue updated again later moves to the later day's file; a rerun of the earlier day no longer contains it.
- **Events carry the full history:** an issue's events are all of its status, resolution and assignee changes, not only those of that day. The same event can appear in several daily files, so downstream deduplicates on `(key, change_id, field)`.
- **Left out:** worklogs, links, labels, description and other edits are not kept as events. Comments are only counted (`comments_count`).
- **Empty days** still get both files, with the schema and no rows.
- **Times** are all converted to UTC and stored with millisecond precision.
