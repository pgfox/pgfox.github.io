## Commit Data Model

The commit data is read from a local clone of the Artemis repo using `git log`. The commit data is written to 2 parquet files.
How the commits are read is discussed in [Extract Commits](extract_commits.md).

## Commit data returned

Each commit comes back as a header plus its list of changed files:

```
commit ─ header            who, when (author + committer, each with its own offset), parents, subject
       └ files    0..n     one numstat line per file changed vs. the FIRST parent: +lines, -lines, path(s)
```

- **Commit**: one commit on the first-parent line of `origin/main`, i.e. what landed on main, in order. The commits of a merged branch are not read on their own; the merge commit stands for them.
- **Author vs. committer**: the author wrote the change, the committer put it on main (applied, rebased or merged it). They are often different people and different days. Each has a name, email, timestamp and UTC offset.
- **File change**: one changed file of that commit, with lines added and deleted. A rename has an old and a new path; a binary file has no line counts.


The header is the commit; the numstat is what it did to the tree.

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
<svg viewBox="20 20 1380 840" role="img" aria-label="Data model of the git commit extract. On the left, one commit record from git log: sha, parent SHAs (only their count is kept), author name and email, author date as epoch seconds and offset, committer name and email, committer date, subject, then the numstat part with one entry per changed file: lines added, lines deleted and path, a rename with old and new path, or a binary file with dashes for the counts. The header maps through parse_log to a Commit and the commits table, one row per commit keyed by sha; the numstat entries are also counted and summed into the commits row. Each numstat entry maps to a FileChange and the commit_files table, one row per file per commit keyed by sha and path. commit_files.sha refers to commits.sha, one commit to many rows.">
<defs><marker id="cd-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- Zones -->
<rect class="zone" x="30" y="30" width="560" height="810" rx="18"/>
<rect class="zone" x="770" y="30" width="620" height="810" rx="18"/>
<rect class="section-bg" x="43" y="44" width="142" height="26" rx="6"/>
<rect class="section-bg" x="783" y="44" width="160" height="26" rx="6"/>
<g class="zone-title"><text x="53" y="62">GIT LOG OUTPUT</text><text x="793" y="62">PARQUET TABLES</text></g>

<!-- git log record -->
<rect class="wash" x="62" y="92" width="496" height="40" rx="8"/>
<rect class="sketch" x="60" y="90" width="500" height="360" rx="10"/>
<text class="name" x="310" y="118">commit record (one per commit)</text>
<g class="mono">
<text x="80" y="160">record  (\x1e … , fields split on \x1f)</text>
<text x="80" y="184">├─ %H   sha</text>
<text class="dropped" x="80" y="208">├─ %P   parent SHAs</text>
<text x="80" y="232">├─ %an · %ae   author name · email</text>
<text x="80" y="256">├─ %ad  &lt;epoch&gt; &lt;±HHMM&gt;</text>
<text x="80" y="280">├─ %cn · %ce   committer name · email</text>
<text x="80" y="304">├─ %cd  &lt;epoch&gt; &lt;±HHMM&gt;</text>
<text x="80" y="328">├─ %s   subject</text>
<text x="80" y="352">└─ numstat  (NUL separated, -z)</text>
<text x="80" y="376">   ├─ added · deleted · path</text>
<text x="80" y="400">   ├─ added · deleted · ∅ · old · new</text>
<text x="80" y="424">   └─ - · - · path</text>
</g>
<g class="smallnote">
<text class="start" x="270" y="208">only the count is kept</text>
<text class="start" x="390" y="400">rename</text>
<text class="start" x="240" y="424">binary: no counts</text>
</g>

<!-- commits table -->
<rect class="wash" x="802" y="92" width="536" height="40" rx="8"/>
<rect class="sketch" x="800" y="90" width="540" height="430" rx="10"/>
<text class="name" x="1070" y="118">commits · one row per commit</text>
<g class="mono">
<text class="pk" x="820" y="160">sha  PK</text>
<text x="820" y="184">parent_count</text>
<text x="820" y="208">author_name</text>
<text x="820" y="232">author_email</text>
<text x="820" y="256">author_ts</text>
<text x="820" y="280">author_tz_offset_minutes</text>
<text x="820" y="304">committer_name</text>
<text x="820" y="328">committer_email</text>
<text x="820" y="352">committer_ts</text>
<text x="820" y="376">committer_tz_offset_minutes</text>
<text x="820" y="400">subject</text>
<text x="820" y="424">files_changed</text>
<text x="820" y="448">lines_added</text>
<text x="820" y="472">lines_deleted</text>
<text x="820" y="496">binary_files</text>
</g>
<g class="type">
<text x="1320" y="160">string</text>
<text x="1320" y="184">int32</text>
<text x="1320" y="208">string</text>
<text x="1320" y="232">string</text>
<text x="1320" y="256">timestamp[ms, UTC]</text>
<text x="1320" y="280">int32</text>
<text x="1320" y="304">string</text>
<text x="1320" y="328">string</text>
<text x="1320" y="352">timestamp[ms, UTC]</text>
<text x="1320" y="376">int32</text>
<text x="1320" y="400">string</text>
<text x="1320" y="424">int32</text>
<text x="1320" y="448">int32</text>
<text x="1320" y="472">int32</text>
<text x="1320" y="496">int32</text>
</g>

<!-- commit_files table -->
<rect class="wash" x="802" y="572" width="536" height="40" rx="8"/>
<rect class="sketch" x="800" y="570" width="540" height="240" rx="10"/>
<text class="name" x="1070" y="598">commit_files · one row per file per commit</text>
<g class="mono">
<text class="pk" x="820" y="640">sha  PK · FK</text>
<text class="pk" x="820" y="664">path  PK</text>
<text x="820" y="688">old_path</text>
<text x="820" y="712">lines_added</text>
<text x="820" y="736">lines_deleted</text>
<text x="820" y="760">is_binary</text>
<text x="820" y="784">is_rename</text>
</g>
<g class="type">
<text x="1320" y="640">string</text>
<text x="1320" y="664">string</text>
<text x="1320" y="688">string · null</text>
<text x="1320" y="712">int32 · null</text>
<text x="1320" y="736">int32 · null</text>
<text x="1320" y="760">bool</text>
<text x="1320" y="784">bool</text>
</g>

<!-- Mappings -->
<path class="light" d="M528 176 H543 V332 H528"/>
<path class="light" d="M528 368 H543 V428 H528"/>
<g class="arrow" marker-end="url(#cd-arrowhead)">
<path d="M548 254 C650 254 680 280 788 280"/>
<path d="M548 398 C650 398 680 460 788 460"/>
<path d="M548 398 C660 398 680 700 788 700"/>
<path d="M1342 155 H1372 V635 H1346"/>
</g>
<text class="msg" x="668" y="252">Commit</text>
<text class="msg" x="668" y="580">FileChange</text>
<g class="smallnote">
<text x="668" y="290">one row per commit</text>
<text x="668" y="452">counted and summed</text>
<text x="668" y="598">one row per numstat entry</text>
<text x="1372" y="400">1 : n</text>
</g>
</svg>
</div>
<p class="pipeline-caption">git log output on the left, the two daily parquet tables on the right · commit_files.sha → commits.sha, one commit to many rows · files: &lt;DATA_DIR&gt;/&lt;table&gt;/&lt;table&gt;_&lt;day&gt;.parquet</p>
</div>


## commits

One row per commit whose `committer_ts` falls on the file's day. Key: `sha`.

`git_log.parse_log` splits the output on `\x1e` into records, each record on `\x1f` into the nine header fields plus the numstat part, and builds a `Commit` with a list of `FileChange` (`ingestion/src/ingestion/git_log.py`). The schemas are `COMMITS_SCHEMA` and `COMMIT_FILES_SCHEMA` in `writer.py`.

| Column | Type | From the git output | a415917d |
|---|---|---|---|
| `sha` | string | `%H` | a415917d4542fe9c… |
| `parent_count` | int32 | number of SHAs in `%P`; more than 1 is a merge | 1 |
| `author_name` | string | `%an` | John Doe|
| `author_email` | string | `%ae` | jdoe@apache.org |
| `author_ts` | timestamp | `%ad` epoch | 2025-09-15 20:09:55 |
| `author_tz_offset_minutes` | int32 | `%ad` offset, `-0500` → -300 | -300 |
| `committer_name` | string | `%cn` | Tom Doe |
| `committer_email` | string | `%ce` | tdoe@gmail.com |
| `committer_ts` | timestamp | `%cd` epoch; decides which day's file the commit is in | 2025-09-22 19:45:56 |
| `committer_tz_offset_minutes` | int32 | `%cd` offset | -240 |
| `subject` | string | `%s` | ARTEMIS-5659 support masking JDBC URL |
| `files_changed` | int32 | computed: number of numstat entries | 3 |
| `lines_added` | int32 | computed: sum over text files (binary counts as 0) | 59 |
| `lines_deleted` | int32 | computed: sum over text files | 12 |
| `binary_files` | int32 | computed: number of binary files | 0 |

Not stored: the parent SHAs themselves (only their count) and the message body.

## commit_files

One row per file changed by the commits in `commits` for the same day. Key: `(sha, path)`.

| Column | Type | From the git output |
|---|---|---|
| `sha` | string | the commit's `%H` |
| `path` | string | the path; the **new** path for a rename |
| `old_path` | string | the old path for a rename, else null |
| `lines_added` | int32 | numstat added; null for a binary file |
| `lines_deleted` | int32 | numstat deleted; null for a binary file |
| `is_binary` | bool | numstat counts were `-` |
| `is_rename` | bool | `old_path` is set |

The three files of a415917d become these rows (`sha` a415917d on all, paths shortened):

| path | old_path | lines_added | lines_deleted | is_binary | is_rename |
|---|---|---|---|---|---|
| …/deployers/impl/FileConfigurationParser.java | null | 5 | 1 | false | false |
| …/config/impl/FileConfigurationDbEncryptedConfigTest.java | …/config/impl/FileConfigurationDbEncryptedPassTest.java | 50 | 8 | false | true |
| …/resources/ConfigurationTest-db-encrypted-config.xml | …/resources/ConfigurationTest-db-encrypted-pass-config.xml | 4 | 3 | false | true |

and the binary file of 3573b666:

| path | old_path | lines_added | lines_deleted | is_binary | is_rename |
|---|---|---|---|---|---|
| tests/security-resources/server-keystore-keypass.jceks | null | null | null | true | false |

A commit with no files (e.g. an empty merge) has a `commits` row with `files_changed` 0 and no `commit_files` rows.


## Which day's file

A commit is never updated, so it is written to exactly one file: the UTC day of its **committer** date.

```

1. John Doe writes the change on 15 Sep (author date).
2. Tom Doe puts it on main on 22 Sep (committer date).
3. The extract for 22 Sep finds it; it goes into commits_2025-09-22.parquet.
4. Re-extracting 22 Sep gives the same rows (unless new commits for that day have arrived),
   so the file's bytes are unchanged and the loader skips it.

```

`read_commits` keeps commits whose `committer_ts` is in `[start 00:00, end + 1 day 00:00)` UTC. `commit_cli.run` groups them by `committer_ts.date()` and `writer.write_commit_day` writes two files for **every** day in the range, empty or not:

```
<DATA_DIR>/commits/commits_<day>.parquet                  one row per commit
<DATA_DIR>/commit_files/commit_files_<day>.parquet        one row per file changed by a commit
```

## Notes

- **One file per committer day (UTC):** a415917d is in `commits_2025-09-22.parquet`, not the 15th. The author date is only data.
- **Every day in the range is written, even empty:** a rerun fully replaces each day's pair of files, so a day never keeps stale rows. Files outside the run's range are not touched. Writes are atomic (`storage.Location.write_bytes`).
- **Lookback:** the default range is yesterday plus 2 days of lookback (`--lookback-days`), because a commit can reach `origin/main` after its committer date (pushed later). A commit pushed more than the lookback after its committer date is missed until that day is re-extracted with `--start/--end`. (The flow's default target is today instead of yesterday.)
- **No snapshots:** unlike PRs and Jira issues there is nothing to choose between; a commit's committer date never changes.
- **Loading:** `copy-git-commits` copies each new or changed file (SHA-256 of its bytes) into `raw.commits` / `raw.commit_files` as is, adding `source_file` and `loaded_at` (append-only, tracked in `metadata.ingest_commits` / `ingest_commit_files`). dbt keeps the latest load per key: `stg_commits_dedup` on `sha`, `stg_commit_files_dedup` on `(sha, path)`, flagging bad rows (`data_error`).
- **Joins:** `sha` joins to `pull_requests.merge_commit_sha`, `closing_commit_sha` and `pr_events.commit_sha`. The Jira id comes from `subject` (`jira_id` macro: first `ARTEMIS-<n>`, else `NO-JIRA`, else `UNKNOWN`). Names and emails make `dim_person` (author and committer share it); they do not join GitHub logins or Jira usernames.
- **Merges** count everything the branch brought in as the merge's own lines, under the merge's committer and subject; the branch's authors and JIRA ids are not seen.
- **Times** are stored as `timestamp(ms, tz=UTC)` (git has whole seconds).
