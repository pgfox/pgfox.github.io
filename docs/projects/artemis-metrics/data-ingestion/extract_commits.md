## Extract Commits

- `inspect-repo` is the console script that pulls commits out of a git clone and writes them as daily parquet files. It is configured to write the file directly to the S3 location. 
- Entry point: `inspect-repo` → `ingestion:main` → `cli.main()` (`ingestion/src/ingestion/cli.py`).
- Usage: `inspect-repo --repo <clone> [--out LOC] [--date D | --start D --end D] [--lookback-days N] [--ref REF] [--no-fetch] [--repo-url URL]`
- With no dates it covers 3 days: yesterday and the 2 days before it.

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
<g class="name"><text x="160" y="79">shell</text><text x="360" y="79">cli.main</text><text x="560" y="79">cli.run</text><text x="760" y="79">git_log</text><text x="960" y="79">git</text><text x="1160" y="79">writer</text><text x="1360" y="79">storage</text></g>
</svg></div>
<svg viewBox="80 102 1370 2373" role="img" aria-label="Sequence diagram of the inspect-repo command. Start: the shell runs inspect-repo; cli.main builds the parser and parses the arguments, then calls cli.run with today's UTC date; run resolves the date range, raising ValueError for a bad range before any git work. Fetch, skipped with --no-fetch: run calls git_log.clone_or_fetch, which runs git fetch origin, or git clone --no-checkout for a new repo. Read commits: run calls git_log.read_commits, which verifies the ref with git rev-parse, runs one padded git log for the whole range, parses it and keeps only commits inside the exact UTC range, and returns a list of commits; run buckets them by UTC committer day. Write each day, for every day from start to end: run calls writer.write_day, which writes commits_day.parquet and commit_files_day.parquet through storage, atomically to a local path or S3, and run prints a line with the commit and file change counts. Done: run returns and main exits 0. On error: ValueError exits 2 with usage, GitError, StorageError or OSError exit 1 with an error message.">
<defs><marker id="ec-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- Lifelines -->
<g class="lifeline"><path d="M160 102 V2455"/><path d="M360 102 V2455"/><path d="M560 102 V2455"/><path d="M760 102 V2455"/><path d="M960 102 V2455"/><path d="M1160 102 V2455"/><path d="M1360 102 V2455"/></g>

<!-- Phases -->
<rect class="zone" x="95" y="160" width="1340" height="320" rx="18"/>
<rect class="zone" x="95" y="500" width="1340" height="360" rx="18"/>
<rect class="zone" x="95" y="880" width="1340" height="580" rx="18"/>
<rect class="zone" x="95" y="1480" width="1340" height="550" rx="18"/>
<rect class="zone" x="95" y="2050" width="1340" height="165" rx="18"/>
<rect class="zone" x="95" y="2235" width="1340" height="220" rx="18"/>
<rect class="section-bg" x="108" y="174" width="72" height="26" rx="6"/>
<rect class="section-bg" x="108" y="514" width="72" height="26" rx="6"/>
<rect class="section-bg" x="108" y="894" width="134" height="26" rx="6"/>
<rect class="section-bg" x="108" y="1494" width="154" height="26" rx="6"/>
<rect class="section-bg" x="108" y="2064" width="62" height="26" rx="6"/>
<rect class="section-bg" x="108" y="2249" width="100" height="26" rx="6"/>
<g class="zone-title"><text x="118" y="192">START</text><text x="118" y="532">FETCH</text><text x="118" y="912">READ COMMITS</text><text x="118" y="1512">WRITE EACH DAY</text><text x="118" y="2082">DONE</text><text x="118" y="2267">ON ERROR</text></g>

<!-- opt: skipped with the no-fetch flag -->
<rect class="frame" x="125" y="555" width="1280" height="285" rx="10"/>
<rect class="section-bg" x="132" y="562" width="400" height="22" rx="5"/>
<g class="guard"><text x="140" y="578">[not --no-fetch] · with --no-fetch this whole step is skipped</text></g>

<!-- loop: one pass per day -->
<rect class="frame" x="125" y="1535" width="1280" height="475" rx="10"/>
<rect class="section-bg" x="132" y="1542" width="470" height="22" rx="5"/>
<g class="guard"><text x="140" y="1558">[loop: day = start .. end] · every day is written, even one with no commits</text></g>

<!-- Messages -->
<g class="arrow" marker-end="url(#ec-arrowhead)">
<path d="M168 235 H350"/>
<path d="M368 285 H420 V315 H372"/>
<path d="M368 375 H550"/>
<path d="M568 415 H620 V445 H572"/>
<path d="M568 630 H750"/>
<path d="M768 690 H950"/>
<path class="dotted" d="M952 750 H770"/>
<path class="dotted" d="M752 810 H570"/>
<path d="M568 955 H750"/>
<path d="M768 1015 H950"/>
<path class="dotted" d="M952 1075 H770"/>
<path d="M768 1135 H950"/>
<path class="dotted" d="M952 1195 H770"/>
<path d="M768 1250 H820 V1280 H772"/>
<path class="dotted" d="M752 1345 H570"/>
<path d="M568 1395 H620 V1425 H572"/>
<path d="M568 1610 H1150"/>
<path d="M1168 1670 H1350"/>
<path class="dotted" d="M1352 1730 H1170"/>
<path d="M1168 1790 H1350"/>
<path class="dotted" d="M1352 1850 H1170"/>
<path class="dotted" d="M1152 1910 H570"/>
<path class="dotted" d="M552 1970 H170"/>
<path class="dotted" d="M552 2125 H370"/>
<path class="dotted" d="M352 2185 H170"/>
<path class="dotted" d="M352 2310 H170"/>
<path class="dotted" d="M352 2380 H170"/>
</g>
<text class="msg" x="260" y="225">inspect-repo args</text>
<text class="msg start" x="434" y="305">build_parser(), parse_args()</text>
<text class="msg" x="460" y="365">run(args, today)</text>
<text class="msg start" x="634" y="435">resolve_range() → (start, end)</text>
<text class="msg" x="660" y="620">clone_or_fetch(…)</text>
<text class="msg" x="860" y="680">fetch origin</text>
<text class="msg" x="860" y="740">ok</text>
<text class="msg" x="660" y="800">None</text>
<text class="msg" x="660" y="945">read_commits(…)</text>
<text class="msg" x="860" y="1005">rev-parse --verify</text>
<text class="msg" x="860" y="1065">ok</text>
<text class="msg" x="860" y="1125">log --first-parent</text>
<text class="msg" x="860" y="1185">raw log text</text>
<text class="msg start" x="834" y="1270">parse_log() + exact UTC cut</text>
<text class="msg" x="660" y="1335">list[Commit]</text>
<text class="msg start" x="634" y="1415">bucket by UTC committer day</text>
<text class="msg" x="860" y="1600">write_day(out, day, commits)</text>
<text class="msg" x="1260" y="1660">write_bytes</text>
<text class="msg" x="1260" y="1720">saved</text>
<text class="msg" x="1260" y="1780">write_bytes</text>
<text class="msg" x="1260" y="1840">saved</text>
<text class="msg" x="860" y="1900">(paths)</text>
<text class="msg" x="360" y="1960">&lt;day&gt;: N commits, M file changes</text>
<text class="msg" x="460" y="2115">None</text>
<text class="msg" x="260" y="2175">exit 0</text>
<text class="msg" x="260" y="2300">exit 2</text>
<text class="msg start" x="380" y="2315">ValueError → parser.error: usage + message</text>
<text class="msg" x="260" y="2370">exit 1</text>
<text class="msg start" x="380" y="2385">GitError / StorageError / OSError → error: &lt;msg&gt;</text>
<g class="badge">
<circle cx="188" cy="235" r="11"/><text x="188" y="239">1</text>
<circle cx="388" cy="285" r="11"/><text x="388" y="289">2</text>
<circle cx="388" cy="375" r="11"/><text x="388" y="379">3</text>
<circle cx="588" cy="415" r="11"/><text x="588" y="419">4</text>
<circle cx="588" cy="630" r="11"/><text x="588" y="634">5</text>
<circle cx="788" cy="690" r="11"/><text x="788" y="694">6</text>
<circle cx="932" cy="750" r="11"/><text x="932" y="754">7</text>
<circle cx="732" cy="810" r="11"/><text x="732" y="814">8</text>
<circle cx="588" cy="955" r="11"/><text x="588" y="959">9</text>
<circle cx="788" cy="1015" r="11"/><text x="788" y="1019">10</text>
<circle cx="932" cy="1075" r="11"/><text x="932" y="1079">11</text>
<circle cx="788" cy="1135" r="11"/><text x="788" y="1139">12</text>
<circle cx="932" cy="1195" r="11"/><text x="932" y="1199">13</text>
<circle cx="788" cy="1250" r="11"/><text x="788" y="1254">14</text>
<circle cx="732" cy="1345" r="11"/><text x="732" y="1349">15</text>
<circle cx="588" cy="1395" r="11"/><text x="588" y="1399">16</text>
<circle cx="588" cy="1610" r="11"/><text x="588" y="1614">17</text>
<circle cx="1188" cy="1670" r="11"/><text x="1188" y="1674">18</text>
<circle cx="1332" cy="1730" r="11"/><text x="1332" y="1734">19</text>
<circle cx="1188" cy="1790" r="11"/><text x="1188" y="1794">20</text>
<circle cx="1332" cy="1850" r="11"/><text x="1332" y="1854">21</text>
<circle cx="1132" cy="1910" r="11"/><text x="1132" y="1914">22</text>
<circle cx="532" cy="1970" r="11"/><text x="532" y="1974">23</text>
<circle cx="532" cy="2125" r="11"/><text x="532" y="2129">24</text>
<circle cx="332" cy="2185" r="11"/><text x="332" y="2189">25</text>
</g>
<g class="smallnote">
<text x="260" y="255">--repo · --out · --date or range</text>
<text class="start" x="434" y="325">bad syntax → argparse usage, exit 2</text>
<text x="460" y="395">today = UTC date</text>
<text class="start" x="634" y="455">bad range → ValueError, before any git work</text>
<text x="660" y="650">repo, repo_url</text>
<text x="860" y="710">or clone --no-checkout (new repo)</text>
<text x="660" y="975">repo, ref, start, end</text>
<text x="860" y="1035">REF^{commit} · fails → GitError</text>
<text x="860" y="1155">--numstat -z · padded ±1 day</text>
<text x="860" y="1215">one git log for the whole range</text>
<text class="start" x="834" y="1290">keep lower ≤ committer_ts &lt; upper</text>
<text x="660" y="1365">each with files = [FileChange…]</text>
<text class="start" x="634" y="1435">by_day[committer_ts.date()]</text>
<text x="860" y="1630">commits = by_day[day], [] for a quiet day</text>
<text x="1260" y="1690">commits_&lt;day&gt;.parquet</text>
<text x="1260" y="1750">local: temp + rename · S3: PUT</text>
<text x="1260" y="1810">commit_files_&lt;day&gt;.parquet</text>
<text x="360" y="1990">printed, one line per day</text>
<text x="760" y="2430">raised anywhere above · run() and resolve_range() only raise, cli.main alone turns errors into exit codes</text>
</g>
</svg>
</div>
<p class="pipeline-caption">inspect-repo, from command line to daily parquet files · storage is the Location of --out (local path or s3://) · git is the subprocess started by git_log.run_git</p>
</div>

## Notes

- Validation happens before any git work: a bad range never fetches.
- One `git log` covers the whole range; the per-day files are only a bucketing step afterwards.
- Every day in `[start, end]` is written, so a rerun fully replaces those days' files (including days that lost commits).
- Only `main()` turns errors into exit codes. `run()` and `resolve_range()` raise, which is how the `git-repo-metrics` Prefect flow reuses them (`build_parser` / `run`).



## git commands

`read_commits` runs two git commands, both through `git_log.run_git`. Here they are for `--ref origin/main --start 2026-11-06 --end 2026-11-08`.

### 1. Check the ref exists

```bash
git -C /artemis rev-parse --verify "origin/main^{commit}"
```

This fails, raising `GitError`, if the branch does not exist or does not point to a commit. The check runs before the log is read.

### 2. Read the log

```bash
git -C /artemis log origin/main \
  --first-parent \
  --diff-merges=first-parent \
  -M \
  --numstat \
  -z \
  --date=raw \
  "--since-as-filter=2026-11-05 00:00:00 +0000" \
  "--until=2026-11-10 00:00:00 +0000" \
  "--format=%x1e%H%x1f%P%x1f%an%x1f%ae%x1f%ad%x1f%cn%x1f%ce%x1f%cd%x1f%s%x1f"
```

| Option | What it does |
|---|---|
| `-C /artemis` | runs git as if it was started inside the clone |
| `origin/main` | the branch to read (`--ref`) |
| `--first-parent` | stays on the branch that was merged into (main) and skips the commits of the merged branches |
| `--diff-merges=first-parent` | gives merge commits line counts too: a merge's file changes are taken by comparing it with its first parent, the commit main was on just before the merge |
| `-M` | turns on rename detection |
| `--numstat` | lists every file each commit changed, with the number of lines added and deleted |
| `-z` | separates fields with NUL bytes, so file names with spaces or odd characters parse safely |
| `--date=raw` | prints dates as seconds since 1 January 1970 (UTC), followed by the person's time zone offset |
| `--since-as-filter` | drops commits older than the date, but keeps walking the history (plain `--since` stops at the first old commit) |
| `--until` | drops commits newer than the date |
| `--format` | one record per commit: `%x1e` (record separator) starts it, then the fields split by `%x1f` (unit separator): hash, parents, author name, email, date, committer name, email, date, subject |

The date window is padded by a day on each side: the range covers 2026-11-06 00:00 up to 2026-11-09 00:00, but git is asked for 2026-11-05 to 2026-11-10. `parse_log` then makes the exact cut in Python, keeping `start 00:00 UTC <= committer time < (end + 1) 00:00 UTC`.
