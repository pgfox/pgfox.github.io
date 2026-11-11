## Extract Jira

- `inspect-jira` is the console script that pulls Jira issues and their change history out of the Apache Jira REST API and writes them as daily parquet files.
- Entry point: `inspect-jira` → `ingestion.jira_cli:main` (`ingestion/src/ingestion/jira_cli.py`). Extraction is in `jira_issues.py`, writing in `writer.write_jira_day`, and the day range comes from `commit_cli.resolve_range` (shared with `inspect-repo`).
- Usage: `inspect-jira [--project ARTEMIS] [--out LOC] [--date D | --start D --end D] [--lookback-days N]`
- With no dates it covers 3 days: **today** (UTC) and the 2 days before it. `inspect-repo` starts from yesterday instead.

## JQL (JIRA Query Language) 

This is a query language used to search JIRA. It is similiar to the 'SQL where clauses', It takes the form of 

```
    project = "ARTEMIS" AND updated >= "2026-11-10 00:00" AND updated < "2026-11-11 00:00" ORDER BY key
```

This type of JQL is sent to JIRA's 'search' uri - `https://issues.apache.org/jira/rest/api/2/search`.



## Extract without Login

To ensure the data is **Public** I do the extraction without logging in. 



<div class="pipeline">
<style>
.pipeline{--ink:#788d79;--dark:#536b56;--paper:#fafaf2;--wash:#dce8d5;margin:30px 0;color:var(--dark)}
.pipeline svg{display:block;width:100%;height:auto;overflow:visible}
.pipeline svg path,.pipeline svg ellipse,.pipeline svg circle,.pipeline svg rect{vector-effect:non-scaling-stroke}
.pipeline .sketch{stroke:var(--ink);stroke-width:2.5;stroke-linecap:round;stroke-linejoin:round;fill:none}
.pipeline .light{stroke:#acbcaa;stroke-width:1.5;stroke-linecap:round;fill:none}
.pipeline .lifeline{stroke:#c3cdbf;stroke-width:1.5;stroke-dasharray:2 6;stroke-linecap:round;fill:none}
.pipeline .zone{stroke:#acbcaa;stroke-width:1.5;stroke-dasharray:6 8;fill:none}
.pipeline .frame{stroke:#c3cdbf;stroke-width:1.2;fill:none}
.pipeline .section-bg{fill:var(--paper);stroke:none}
.pipeline .zone-title{font-size:14px;letter-spacing:.12em;fill:#81917c;font-family:system-ui,sans-serif}
.pipeline .guard{font-size:13px;font-style:italic;fill:#81917c;font-family:system-ui,sans-serif}
.pipeline .name{font-size:17px;fill:var(--dark);text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .msg{font-size:15px;fill:var(--dark);text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .msg,.pipeline .smallnote{paint-order:stroke;stroke:var(--paper);stroke-width:6px;stroke-linejoin:round}
.pipeline .badge circle{fill:var(--paper);stroke:var(--ink);stroke-width:1.8}
.pipeline .badge text{font:700 12px system-ui,sans-serif;fill:var(--ink);text-anchor:middle}
.pipeline .arrow{stroke:var(--ink);stroke-width:2.2;fill:none;stroke-linecap:round;stroke-linejoin:round}
.pipeline .smallnote{font-size:12px;fill:#80917d;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .start{text-anchor:start}
.pipeline .heads{position:sticky;top:0;z-index:2;background:var(--paper);box-shadow:0 6px 8px -6px #536b5640}
.pipeline .heads rect{fill:var(--paper)}
.pipeline .dotted{stroke-dasharray:4 8}
.pipeline .pipeline-caption{text-align:center;max-width:none;margin-top:12px;font:14px system-ui,sans-serif;color:#80917d}
@media (max-width:760px){.pipeline .scroller{overflow-x:auto;padding-bottom:8px}.pipeline .scroller svg{min-width:1200px}}
</style>
<div class="scroller">
<div class="heads"><svg viewBox="80 44 1370 58" aria-hidden="true">
<g class="sketch"><rect x="90" y="50" width="140" height="46" rx="8"/><rect x="290" y="50" width="140" height="46" rx="8"/><rect x="490" y="50" width="140" height="46" rx="8"/><rect x="690" y="50" width="140" height="46" rx="8"/><rect x="890" y="50" width="140" height="46" rx="8"/><rect x="1090" y="50" width="140" height="46" rx="8"/><rect x="1290" y="50" width="140" height="46" rx="8"/></g>
<g class="name"><text x="160" y="79">shell</text><text x="360" y="79">jira_cli.main</text><text x="560" y="79">jira_cli.run</text><text x="760" y="79">jira_issues</text><text x="960" y="79">Jira</text><text x="1160" y="79">writer</text><text x="1360" y="79">storage</text></g>
</svg></div>
<svg viewBox="80 102 1370 2458" role="img" aria-label="Sequence diagram of the inspect-jira command. Start: the shell runs inspect-jira; jira_cli.main builds the parser and parses the arguments, then calls jira_cli.run with today's UTC date; run resolves the date range with today as the default target day, raising ValueError for a bad range before any request, and takes the get function from jira_issues.rest_get unless a test injects one. Read issues: run calls jira_issues.read_issues. For every day from start minus 1 to end plus 1 it builds one JQL search for issues of the project updated that day; for every page of results it calls the Jira search endpoint with the fields and the changelog, and gets back issues and a total; for every issue in the page whose changelog was cut short it fetches the issue on its own to get the full changelog, then builds the Issue and its status, resolution and assignee Events, and keeps the issue only if its UTC updated day is inside the exact range. It returns a list of issues, deduplicated by key and sorted by number, and run buckets them by UTC updated day. Write each day, for every day from start to end: run calls writer.write_jira_day, which writes jira_issues_day.parquet and jira_events_day.parquet through storage, atomically to a local path or S3, and run prints a line with the issue, comment and event counts. Done: run returns and main exits 0. On error: ValueError exits 2 with usage, JiraError, StorageError or OSError exit 1 with an error message.">
<defs><marker id="ej-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- Lifelines -->
<g class="lifeline"><path d="M160 102 V2540"/><path d="M360 102 V2540"/><path d="M560 102 V2540"/><path d="M760 102 V2540"/><path d="M960 102 V2540"/><path d="M1160 102 V2540"/><path d="M1360 102 V2540"/></g>

<!-- Phases -->
<rect class="zone" x="95" y="160" width="1340" height="385" rx="18"/>
<rect class="zone" x="95" y="565" width="1340" height="980" rx="18"/>
<rect class="zone" x="95" y="1565" width="1340" height="550" rx="18"/>
<rect class="zone" x="95" y="2135" width="1340" height="165" rx="18"/>
<rect class="zone" x="95" y="2320" width="1340" height="220" rx="18"/>
<rect class="section-bg" x="108" y="174" width="72" height="26" rx="6"/>
<rect class="section-bg" x="108" y="579" width="124" height="26" rx="6"/>
<rect class="section-bg" x="108" y="1579" width="154" height="26" rx="6"/>
<rect class="section-bg" x="108" y="2149" width="62" height="26" rx="6"/>
<rect class="section-bg" x="108" y="2334" width="100" height="26" rx="6"/>
<g class="zone-title"><text x="118" y="192">START</text><text x="118" y="597">READ ISSUES</text><text x="118" y="1597">WRITE EACH DAY</text><text x="118" y="2167">DONE</text><text x="118" y="2352">ON ERROR</text></g>

<!-- loop: one JQL search per day, padded by a day on each side -->
<rect class="frame" x="125" y="690" width="1280" height="700" rx="10"/>
<rect class="section-bg" x="132" y="697" width="420" height="22" rx="5"/>
<g class="guard"><text x="140" y="713">[loop: day = start-1 .. end+1] · one JQL search per day, padded</text></g>

<!-- loop: one pass per page of search results -->
<rect class="frame" x="140" y="815" width="1250" height="555" rx="10"/>
<rect class="section-bg" x="147" y="822" width="325" height="22" rx="5"/>
<g class="guard"><text x="155" y="838">[loop: page · startAt += len(issues) until total]</text></g>

<!-- loop: one pass per issue in the page -->
<rect class="frame" x="155" y="985" width="1220" height="365" rx="10"/>
<rect class="section-bg" x="162" y="992" width="205" height="22" rx="5"/>
<g class="guard"><text x="170" y="1008">[loop: each issue in the page]</text></g>

<!-- opt: changelog cut short in the search -->
<rect class="frame" x="170" y="1030" width="1190" height="150" rx="10"/>
<rect class="section-bg" x="177" y="1037" width="485" height="22" rx="5"/>
<g class="guard"><text x="185" y="1053">[opt: len(histories) &lt; changelog.total] · changelog cut short by the search</text></g>

<!-- loop: one pass per day written -->
<rect class="frame" x="125" y="1620" width="1280" height="475" rx="10"/>
<rect class="section-bg" x="132" y="1627" width="475" height="22" rx="5"/>
<g class="guard"><text x="140" y="1643">[loop: day = start .. end] · every day is written, even one with no issues</text></g>

<!-- Messages -->
<g class="arrow" marker-end="url(#ej-arrowhead)">
<path d="M168 235 H350"/>
<path d="M368 285 H420 V315 H372"/>
<path d="M368 375 H550"/>
<path d="M568 415 H620 V445 H572"/>
<path d="M568 485 H620 V515 H572"/>
<path d="M568 640 H750"/>
<path d="M768 750 H820 V780 H772"/>
<path d="M768 880 H950"/>
<path class="dotted" d="M952 940 H770"/>
<path d="M768 1095 H950"/>
<path class="dotted" d="M952 1155 H770"/>
<path d="M768 1215 H820 V1245 H772"/>
<path d="M768 1290 H820 V1320 H772"/>
<path class="dotted" d="M752 1430 H570"/>
<path d="M568 1480 H620 V1510 H572"/>
<path d="M568 1695 H1150"/>
<path d="M1168 1755 H1350"/>
<path class="dotted" d="M1352 1815 H1170"/>
<path d="M1168 1875 H1350"/>
<path class="dotted" d="M1352 1935 H1170"/>
<path class="dotted" d="M1152 1995 H570"/>
<path class="dotted" d="M552 2055 H170"/>
<path class="dotted" d="M552 2210 H370"/>
<path class="dotted" d="M352 2270 H170"/>
<path class="dotted" d="M352 2395 H170"/>
<path class="dotted" d="M352 2465 H170"/>
</g>
<text class="msg" x="260" y="225">inspect-jira args</text>
<text class="msg start" x="434" y="305">build_parser(), parse_args()</text>
<text class="msg" x="460" y="365">run(args, today)</text>
<text class="msg start" x="634" y="435">resolve_range(default_days_back=0) → (start, end)</text>
<text class="msg start" x="634" y="505">get = jira_issues.rest_get()</text>
<text class="msg" x="660" y="630">read_issues(…)</text>
<text class="msg start" x="834" y="770">build the day's JQL</text>
<text class="msg" x="860" y="870">GET search</text>
<text class="msg" x="860" y="930">issues[], total</text>
<text class="msg" x="860" y="1085">GET issue/&lt;key&gt;</text>
<text class="msg" x="860" y="1145">full changelog</text>
<text class="msg start" x="834" y="1235">_issue() + _events(EVENT_FIELDS)</text>
<text class="msg start" x="834" y="1310">keep if start ≤ updated_at.date() ≤ end</text>
<text class="msg" x="660" y="1420">list[Issue]</text>
<text class="msg start" x="634" y="1500">bucket by UTC updated day</text>
<text class="msg" x="860" y="1685">write_jira_day(out, day, issues)</text>
<text class="msg" x="1260" y="1745">write_bytes</text>
<text class="msg" x="1260" y="1805">saved</text>
<text class="msg" x="1260" y="1865">write_bytes</text>
<text class="msg" x="1260" y="1925">saved</text>
<text class="msg" x="860" y="1985">(paths)</text>
<text class="msg" x="360" y="2045">&lt;day&gt;: N issues, C comments, E events</text>
<text class="msg" x="460" y="2200">None</text>
<text class="msg" x="260" y="2260">exit 0</text>
<text class="msg" x="260" y="2385">exit 2</text>
<text class="msg start" x="380" y="2400">ValueError → parser.error: usage + message</text>
<text class="msg" x="260" y="2455">exit 1</text>
<text class="msg start" x="380" y="2470">JiraError / StorageError / OSError → error: &lt;msg&gt;</text>
<g class="badge">
<circle cx="188" cy="235" r="11"/><text x="188" y="239">1</text>
<circle cx="388" cy="285" r="11"/><text x="388" y="289">2</text>
<circle cx="388" cy="375" r="11"/><text x="388" y="379">3</text>
<circle cx="588" cy="415" r="11"/><text x="588" y="419">4</text>
<circle cx="588" cy="485" r="11"/><text x="588" y="489">5</text>
<circle cx="588" cy="640" r="11"/><text x="588" y="644">6</text>
<circle cx="788" cy="750" r="11"/><text x="788" y="754">7</text>
<circle cx="788" cy="880" r="11"/><text x="788" y="884">8</text>
<circle cx="932" cy="940" r="11"/><text x="932" y="944">9</text>
<circle cx="788" cy="1095" r="11"/><text x="788" y="1099">10</text>
<circle cx="932" cy="1155" r="11"/><text x="932" y="1159">11</text>
<circle cx="788" cy="1215" r="11"/><text x="788" y="1219">12</text>
<circle cx="788" cy="1290" r="11"/><text x="788" y="1294">13</text>
<circle cx="732" cy="1430" r="11"/><text x="732" y="1434">14</text>
<circle cx="588" cy="1480" r="11"/><text x="588" y="1484">15</text>
<circle cx="588" cy="1695" r="11"/><text x="588" y="1699">16</text>
<circle cx="1188" cy="1755" r="11"/><text x="1188" y="1759">17</text>
<circle cx="1332" cy="1815" r="11"/><text x="1332" y="1819">18</text>
<circle cx="1188" cy="1875" r="11"/><text x="1188" y="1879">19</text>
<circle cx="1332" cy="1935" r="11"/><text x="1332" y="1939">20</text>
<circle cx="1132" cy="1995" r="11"/><text x="1132" y="1999">21</text>
<circle cx="532" cy="2055" r="11"/><text x="532" y="2059">22</text>
<circle cx="532" cy="2210" r="11"/><text x="532" y="2214">23</text>
<circle cx="332" cy="2270" r="11"/><text x="332" y="2274">24</text>
</g>
<g class="smallnote">
<text x="260" y="255">--project · --out · --date or range</text>
<text class="start" x="434" y="325">bad syntax → argparse usage, exit 2</text>
<text x="460" y="395">today = UTC date</text>
<text class="start" x="634" y="455">default: today + 2 lookback days · bad range → ValueError, before any request</text>
<text class="start" x="634" y="525">unless a test injects its own get, so no request is sent</text>
<text x="660" y="660">get, project, start, end</text>
<text class="start" x="834" y="790">project · updated in [day, day+1) · ORDER BY key</text>
<text x="860" y="900">jql · startAt · maxResults=100 · FIELDS · expand=changelog</text>
<text x="860" y="960">fields + changelog for each issue</text>
<text x="860" y="1115">expand=changelog</text>
<text class="start" x="834" y="1255">status · resolution · assignee changes → Event</text>
<text class="start" x="834" y="1330">exact UTC cut · an issue found on two days is kept once</text>
<text x="660" y="1450">by key, sorted by issue number</text>
<text class="start" x="634" y="1520">by_day[updated_at.date()]</text>
<text x="860" y="1715">issues = by_day[day], [] for a quiet day</text>
<text x="1260" y="1775">jira_issues_&lt;day&gt;.parquet</text>
<text x="1260" y="1835">local: temp + rename · S3: PUT</text>
<text x="1260" y="1895">jira_events_&lt;day&gt;.parquet</text>
<text x="360" y="2075">printed, one line per day</text>
<text x="760" y="2515">raised anywhere above · run() and resolve_range() only raise, jira_cli.main alone turns errors into exit codes</text>
</g>
</svg>
</div>
<p class="pipeline-caption">inspect-jira, from command line to daily parquet files · Jira is the Apache Jira REST API, reached through the get from jira_issues.rest_get() · storage is the Location of --out (local path or s3://)</p>
</div>

## Notes

- Validation happens before any request: a bad range never calls Jira.
- The searches cover `start-1 .. end+1`, one UTC day each, because JQL compares at minute precision in the server's time zone. The exact cut is done in Python on `updated_at` (UTC). An issue found on two padded days is kept once (by key).
- The search asks only for `FIELDS` and `expand=changelog`. Jira can cut a long changelog short in a search; such an issue is fetched again on its own (`issue/<key>`) to get the full history.
- `_events` keeps only the `EVENT_FIELDS` changes (status, resolution, assignee), one `Event` per `(change_id, field)`.
- Each issue lands in the file of its `updated_at` day: a snapshot of its current state, not of the state it had that day.
- Every day in `[start, end]` is written, so a rerun fully replaces those days' files. An empty day still gets two empty files.
- Only `main()` turns errors into exit codes. `run()` and `resolve_range()` raise, which is how the `jira-metrics` Prefect flow reuses them (`build_parser` / `run`).

## Jira API

- `Jira` is the Apache Jira REST API at `https://issues.apache.org/jira/rest/api/2`, used anonymously.
- Every GET waits until at least `REQUEST_INTERVAL_SECONDS` (1 s) after the previous one.
- A GET that gets 429, 502 or 503 is retried once, after `Retry-After` or 10 s. Any other HTTP error, or no connection, becomes a `JiraError`.
- Tests pass their own `get` to `run`, so no request is sent.

## Output files

Each `write_jira_day` writes two files, in this order:

| File | Contents |
|---|---|
| `JIRA_metrics/jira_issues/jira_issues_<day>.parquet` | one row per issue whose `updated_at` falls on that day |
| `JIRA_metrics/jira_events/jira_events_<day>.parquet` | the status, resolution and assignee changes of those issues |
