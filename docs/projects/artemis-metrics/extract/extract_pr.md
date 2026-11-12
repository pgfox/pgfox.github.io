## Extract Pull Requests

- `inspect-prs` is the console script that pulls pull requests out of the GitHub GraphQL API and writes them as daily parquet files. It is configured to write the files directly to the S3 location.
- Entry point: `inspect-prs` → `ingestion.pr_cli:main` (`ingestion/src/ingestion/pr_cli.py`). Extraction is in `github_prs.py`, writing in `writer.write_pr_day`, and the day range in `commit_cli.resolve_range` (shared with `inspect-repo` and `inspect-jira`).
- Usage: `inspect-prs [--github-repo apache/artemis] [--out LOC] [--date D | --start D --end D] [--lookback-days N] [--env-file .env]`
- With no dates it covers 3 days: **today** (UTC) and the 2 days before it. Today, not yesterday as in `inspect-repo`, because PRs change during the day.


## GraphQL

GraphQL is a query language for APIs. Instead of many fixed URLs that each return a set shape of data, there is one endpoint, and you send a query that describes exactly the fields you want. So you get only the data you want with calling potentially many REST APIs.


```

```

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
<g class="name"><text x="160" y="79">shell</text><text x="360" y="79">pr_cli.main</text><text x="560" y="79">pr_cli.run</text><text x="760" y="79">github_prs</text><text x="960" y="79">GitHub</text><text x="1160" y="79">writer</text><text x="1360" y="79">storage</text></g>
</svg></div>
<svg viewBox="80 102 1370 3043" role="img" aria-label="Sequence diagram of the inspect-prs command. Start: the shell runs inspect-prs; pr_cli.main builds the parser and parses the arguments, then calls pr_cli.run with today's UTC date; run resolves the date range, today plus the lookback days, raising ValueError for a bad range before any request. Token, only when no post function was passed in: run loads GITHUB_TOKEN from the env file or the environment, raising ConfigError when it is missing, and builds the GraphQL post function. Read pull requests: run calls github_prs.read_pull_requests, which, for each UTC day from start minus one to end plus one, searches GitHub for PRs of the repo updated that day, following search pages while hasNextPage; for each PR node it fetches further review pages and further timeline pages while hasNextPage, builds the PullRequest with its reviews and events, and keeps it only if its updated day is inside the range. It returns the PRs sorted by number and run buckets them by UTC updated day. Write each day, for every day from start to end: run calls writer.write_pr_day, which writes three parquet files through storage, pull_requests, pr_reviews and pr_events, and run prints a line with the counts. Done: run returns and main exits 0. On error: ValueError exits 2 with usage; ConfigError, GitHubError, StorageError or OSError exit 1 with an error message.">
<defs><marker id="ep-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- Lifelines -->
<g class="lifeline"><path d="M160 102 V3125"/><path d="M360 102 V3125"/><path d="M560 102 V3125"/><path d="M760 102 V3125"/><path d="M960 102 V3125"/><path d="M1160 102 V3125"/><path d="M1360 102 V3125"/></g>

<!-- Phases -->
<rect class="zone" x="95" y="160" width="1340" height="320" rx="18"/>
<rect class="zone" x="95" y="500" width="1340" height="300" rx="18"/>
<rect class="zone" x="95" y="820" width="1340" height="1220" rx="18"/>
<rect class="zone" x="95" y="2060" width="1340" height="640" rx="18"/>
<rect class="zone" x="95" y="2720" width="1340" height="165" rx="18"/>
<rect class="zone" x="95" y="2905" width="1340" height="220" rx="18"/>
<rect class="section-bg" x="108" y="174" width="72" height="26" rx="6"/>
<rect class="section-bg" x="108" y="514" width="72" height="26" rx="6"/>
<rect class="section-bg" x="108" y="834" width="196" height="26" rx="6"/>
<rect class="section-bg" x="108" y="2074" width="154" height="26" rx="6"/>
<rect class="section-bg" x="108" y="2734" width="62" height="26" rx="6"/>
<rect class="section-bg" x="108" y="2919" width="100" height="26" rx="6"/>
<g class="zone-title"><text x="118" y="192">START</text><text x="118" y="532">TOKEN</text><text x="118" y="852">READ PULL REQUESTS</text><text x="118" y="2092">WRITE EACH DAY</text><text x="118" y="2752">DONE</text><text x="118" y="2937">ON ERROR</text></g>

<!-- opt: only when no post was passed in -->
<rect class="frame" x="125" y="555" width="1280" height="225" rx="10"/>
<rect class="section-bg" x="132" y="562" width="460" height="22" rx="5"/>
<g class="guard"><text x="140" y="578">[opt: post is None] · tests pass their own post, so this step is skipped</text></g>

<!-- loop: one search per padded day -->
<rect class="frame" x="125" y="945" width="1280" height="915" rx="10"/>
<rect class="section-bg" x="132" y="952" width="515" height="22" rx="5"/>
<g class="guard"><text x="140" y="968">[loop: day = start-1 .. end+1] · one search per UTC day, padded by a day each side</text></g>

<!-- loop: search pages -->
<rect class="frame" x="145" y="1075" width="1240" height="765" rx="10"/>
<rect class="section-bg" x="152" y="1082" width="250" height="22" rx="5"/>
<g class="guard"><text x="160" y="1098">[loop: search page · while hasNextPage]</text></g>

<!-- loop: each PR node -->
<rect class="frame" x="165" y="1250" width="1200" height="570" rx="10"/>
<rect class="section-bg" x="172" y="1257" width="235" height="22" rx="5"/>
<g class="guard"><text x="180" y="1273">[loop: each PR node · _pull_request]</text></g>

<!-- loop: more reviews -->
<rect class="frame" x="185" y="1300" width="1160" height="150" rx="10"/>
<rect class="section-bg" x="192" y="1307" width="380" height="22" rx="5"/>
<g class="guard"><text x="200" y="1323">[loop: reviews.pageInfo.hasNextPage] · more than 100 reviews</text></g>

<!-- loop: more timeline items -->
<rect class="frame" x="185" y="1470" width="1160" height="160" rx="10"/>
<rect class="section-bg" x="192" y="1477" width="410" height="22" rx="5"/>
<g class="guard"><text x="200" y="1493">[loop: events.pageInfo.hasNextPage] · hasNextPage, not totalCount</text></g>

<!-- loop: one pass per day -->
<rect class="frame" x="125" y="2115" width="1280" height="565" rx="10"/>
<rect class="section-bg" x="132" y="2122" width="530" height="22" rx="5"/>
<g class="guard"><text x="140" y="2138">[loop: day = start .. end] · every day is written, an empty day gets three empty files</text></g>

<!-- Messages -->
<g class="arrow" marker-end="url(#ep-arrowhead)">
<path d="M168 235 H350"/>
<path d="M368 285 H420 V315 H372"/>
<path d="M368 375 H550"/>
<path d="M568 415 H620 V445 H572"/>
<path d="M568 610 H620 V640 H572"/>
<path d="M568 700 H620 V730 H572"/>
<path d="M568 895 H750"/>
<path d="M768 1010 H820 V1040 H772"/>
<path d="M768 1140 H950"/>
<path class="dotted" d="M952 1200 H770"/>
<path d="M768 1360 H950"/>
<path class="dotted" d="M952 1420 H770"/>
<path d="M768 1530 H950"/>
<path class="dotted" d="M952 1590 H770"/>
<path d="M768 1670 H820 V1700 H772"/>
<path d="M768 1750 H820 V1780 H772"/>
<path class="dotted" d="M752 1910 H570"/>
<path d="M568 1970 H620 V2000 H572"/>
<path d="M568 2190 H1150"/>
<path d="M1168 2250 H1350"/>
<path class="dotted" d="M1352 2300 H1170"/>
<path d="M1168 2360 H1350"/>
<path class="dotted" d="M1352 2410 H1170"/>
<path d="M1168 2460 H1350"/>
<path class="dotted" d="M1352 2510 H1170"/>
<path class="dotted" d="M1152 2570 H570"/>
<path class="dotted" d="M552 2630 H170"/>
<path class="dotted" d="M552 2795 H370"/>
<path class="dotted" d="M352 2855 H170"/>
<path class="dotted" d="M352 2980 H170"/>
<path class="dotted" d="M352 3050 H170"/>
</g>
<text class="msg" x="260" y="225">inspect-prs args</text>
<text class="msg start" x="434" y="305">build_parser(), parse_args()</text>
<text class="msg" x="460" y="365">run(args, today)</text>
<text class="msg start" x="634" y="435">resolve_range(default_days_back=0) → (start, end)</text>
<text class="msg start" x="634" y="630">load_token(--env-file)</text>
<text class="msg start" x="634" y="720">post = github_prs.graphql_post(token)</text>
<text class="msg" x="660" y="885">read_pull_requests(…)</text>
<text class="msg start" x="834" y="1030">search q for one UTC day</text>
<text class="msg" x="860" y="1130">POST SEARCH_QUERY</text>
<text class="msg" x="860" y="1190">nodes[]</text>
<text class="msg" x="860" y="1350">POST REVIEWS_QUERY</text>
<text class="msg" x="860" y="1410">next 100 reviews</text>
<text class="msg" x="860" y="1520">POST TIMELINE_QUERY</text>
<text class="msg" x="860" y="1580">next 100 timeline items</text>
<text class="msg start" x="834" y="1690">build PullRequest</text>
<text class="msg start" x="834" y="1770">keep if start ≤ updated_at.date() ≤ end</text>
<text class="msg" x="660" y="1900">list[PullRequest]</text>
<text class="msg start" x="634" y="1990">bucket by UTC updated day</text>
<text class="msg" x="860" y="2180">write_pr_day(out, day, prs)</text>
<text class="msg" x="1260" y="2240">write_bytes</text>
<text class="msg" x="1260" y="2290">saved</text>
<text class="msg" x="1260" y="2350">write_bytes</text>
<text class="msg" x="1260" y="2400">saved</text>
<text class="msg" x="1260" y="2450">write_bytes</text>
<text class="msg" x="1260" y="2500">saved</text>
<text class="msg" x="860" y="2560">(paths)</text>
<text class="msg" x="345" y="2620">&lt;day&gt;: N PRs, R reviews, E events</text>
<text class="msg" x="460" y="2785">None</text>
<text class="msg" x="260" y="2845">exit 0</text>
<text class="msg" x="260" y="2970">exit 2</text>
<text class="msg start" x="380" y="2985">ValueError → parser.error: usage + message</text>
<text class="msg" x="260" y="3040">exit 1</text>
<text class="msg start" x="380" y="3055">ConfigError / GitHubError / StorageError / OSError → error: &lt;msg&gt;</text>
<g class="badge">
<circle cx="188" cy="235" r="11"/><text x="188" y="239">1</text>
<circle cx="388" cy="285" r="11"/><text x="388" y="289">2</text>
<circle cx="388" cy="375" r="11"/><text x="388" y="379">3</text>
<circle cx="588" cy="415" r="11"/><text x="588" y="419">4</text>
<circle cx="588" cy="610" r="11"/><text x="588" y="614">5</text>
<circle cx="588" cy="700" r="11"/><text x="588" y="704">6</text>
<circle cx="588" cy="895" r="11"/><text x="588" y="899">7</text>
<circle cx="788" cy="1010" r="11"/><text x="788" y="1014">8</text>
<circle cx="788" cy="1140" r="11"/><text x="788" y="1144">9</text>
<circle cx="932" cy="1200" r="11"/><text x="932" y="1204">10</text>
<circle cx="788" cy="1360" r="11"/><text x="788" y="1364">11</text>
<circle cx="932" cy="1420" r="11"/><text x="932" y="1424">12</text>
<circle cx="788" cy="1530" r="11"/><text x="788" y="1534">13</text>
<circle cx="932" cy="1590" r="11"/><text x="932" y="1594">14</text>
<circle cx="788" cy="1670" r="11"/><text x="788" y="1674">15</text>
<circle cx="788" cy="1750" r="11"/><text x="788" y="1754">16</text>
<circle cx="732" cy="1910" r="11"/><text x="732" y="1914">17</text>
<circle cx="588" cy="1970" r="11"/><text x="588" y="1974">18</text>
<circle cx="588" cy="2190" r="11"/><text x="588" y="2194">19</text>
<circle cx="1188" cy="2250" r="11"/><text x="1188" y="2254">20</text>
<circle cx="1332" cy="2300" r="11"/><text x="1332" y="2304">21</text>
<circle cx="1188" cy="2360" r="11"/><text x="1188" y="2364">22</text>
<circle cx="1332" cy="2410" r="11"/><text x="1332" y="2414">23</text>
<circle cx="1188" cy="2460" r="11"/><text x="1188" y="2464">24</text>
<circle cx="1332" cy="2510" r="11"/><text x="1332" y="2514">25</text>
<circle cx="1132" cy="2570" r="11"/><text x="1132" y="2574">26</text>
<circle cx="532" cy="2630" r="11"/><text x="532" y="2634">27</text>
<circle cx="532" cy="2795" r="11"/><text x="532" y="2799">28</text>
<circle cx="332" cy="2855" r="11"/><text x="332" y="2859">29</text>
</g>
<g class="smallnote">
<text x="260" y="255">--github-repo · --out · --date or range</text>
<text class="start" x="434" y="325">bad syntax → argparse usage, exit 2</text>
<text x="460" y="395">today = UTC date</text>
<text class="start" x="634" y="455">today + --lookback-days (default 2) · bad range → ValueError, before any request</text>
<text class="start" x="634" y="650">GITHUB_TOKEN from the file, else the environment · missing → ConfigError</text>
<text class="start" x="634" y="740">Authorization: bearer &lt;GITHUB_TOKEN&gt;</text>
<text x="660" y="915">post, repo, start, end</text>
<text class="start" x="834" y="1050">repo:&lt;repo&gt; is:pr updated:&lt;day&gt;T00:00:00Z..&lt;day&gt;T23:59:59Z</text>
<text x="860" y="1160">{q, cursor} · first 50 PRs</text>
<text x="860" y="1220">PR fields + first reviews and events</text>
<text x="860" y="1380">{id, cursor}</text>
<text x="860" y="1550">{id, cursor}</text>
<text x="860" y="1610">EVENT_TYPES only</text>
<text class="start" x="834" y="1710">_review · _event</text>
<text class="start" x="834" y="1790">by_number · a PR seen on two days is kept once</text>
<text x="660" y="1930">sorted by number</text>
<text class="start" x="634" y="2010">by_day[updated_at.date()]</text>
<text x="860" y="2210">prs = by_day[day], [] for a quiet day</text>
<text x="1260" y="2270">pull_requests_&lt;day&gt;.parquet</text>
<text x="1260" y="2320">local: temp + rename · S3: PUT</text>
<text x="1260" y="2380">pr_reviews_&lt;day&gt;.parquet</text>
<text x="1260" y="2480">pr_events_&lt;day&gt;.parquet</text>
<text x="345" y="2650">printed, one line per day</text>
<text x="760" y="3100">raised anywhere above · run() and resolve_range() only raise, pr_cli.main alone turns errors into exit codes</text>
</g>
</svg>
</div>
<p class="pipeline-caption">inspect-prs, from command line to daily parquet files · GitHub is the GraphQL API reached through post · storage is the Location of --out (local path or s3://) · files land in PR_metrics/&lt;table&gt;/</p>
</div>

## Notes

- Validation happens before the token is read or any request is sent: a bad range never calls GitHub.
- The searches cover `start-1 .. end+1`, one UTC day each, which keeps each search far below GitHub's 1,000-result search cap. The exact cut is done in Python on `updated_at` (UTC). A PR found on two padded days is kept once (`by_number`, the later fetch wins).
- Per PR the search already brings up to 100 reviews and 100 timeline items. Only when `hasNextPage` is true are more pages fetched by node id (`REVIEWS_QUERY`, `TIMELINE_QUERY`). Timeline pages follow `hasNextPage` only, because `timelineItems.totalCount` ignores `itemTypes`. Labels (50) are not paged.
- `ready` (first `READY_FOR_REVIEW_EVENT`) and `closed` (last `CLOSED_EVENT`) are extra timeline queries inside the search, giving `ready_for_review_at`, `closed_by_login` and `closing_commit_sha`.
- Each PR lands in the file of its `updated_at` day: a snapshot of its current state, with its full timeline every time (so `event_id` repeats across files).
- Every day in `[start, end]` is written, so a rerun fully replaces those days' files. An empty day still gets three empty files.
- `GITHUB_TOKEN` from `--env-file` wins over the process environment (as in `db.load_settings`). In the prod container there is no `.env`, so it comes from the environment.
- Only `main()` turns errors into exit codes. `run()` and `resolve_range()` raise, which is how the `github-pr-metrics` Prefect flow reuses them (`build_parser` / `run`).

## GitHub API

All requests go to `https://api.github.com/graphql` through the `post` that `github_prs.graphql_post(token)` returns.

| Situation | What happens |
|---|---|
| 502 / 503, or a 403 that says "rate limit" | retried once, after `Retry-After` or 10 s |
| 401 | the token was rejected → `GitHubError` |
| any other HTTP error, no connection, or an `errors` list in the response | `GitHubError` |

Tests pass their own `post` to `run`, so no token is read and no request is sent.

## Output files

Each `write_pr_day` writes three files, in this order, under `PR_metrics/` in `--out`:

| File | Content |
|---|---|
| `pull_requests/pull_requests_<day>.parquet` | one row per PR updated that day |
| `pr_reviews/pr_reviews_<day>.parquet` | the reviews of those PRs |
| `pr_events/pr_events_<day>.parquet` | the timeline events of those PRs |

The column-by-column layout is in [Pull Request Data Model](PR_data.md).
