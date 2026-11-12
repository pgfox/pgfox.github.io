## Pull Request Data Model

The Pull Request PR data is pulled from github interface using GraphQL. The PR data is written to 3 parquet files.
How the pull requests are fetched is discussed in  [Extract Pull Requests](extract_pr.md).

## PR data returned

Each pull request comes back as its plain fields plus four parts:

```
pull request ─ fields          the PR as it is NOW (title, state, author, dates, counts, …)
             ├ reviews         0..n   each review submitted: who, verdict, when, how many inline comments
             ├ events          0..n   the timeline: everything that happened, oldest first
             ├ ready           0..1   the first "ready for review" event      (extra timeline query)
             └ closed          0..1   the last "closed" event: who, by which commit (extra timeline query)
```

- **Pull request**: the PR. Its plain fields are its current state; a later change overwrites them.
- **Review**: one submitted review (`APPROVED`, `CHANGES_REQUESTED`, `COMMENTED`, `DISMISSED`). Each review holds its inline code comments; we only take their count.
- **Timeline item (event)**: one thing that happened to the PR: a push, a comment, a review, a merge, a close, a label… Only the 16 types in `EVENT_TYPES` are asked for. Each has a `__typename` and a node `id`, and different types carry different fields.
- `ready` and `closed` are the same timeline again (GraphQL aliases), narrowed to one type, so the PR row can carry `ready_for_review_at`, `closed_by_login` and `closing_commit_sha` without scanning the events.

The fields are where the PR ended up; the timeline is how it got there.

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
<svg viewBox="20 20 1380 1405" role="img" aria-label="Data model of the GitHub pull request extract. On the left, a pull request node returned by the GraphQL search: its node id, used only for follow-up queries and not kept; number, title, url, state, isDraft, author login and association, created, updated, closed and merged times, merge commit, base and head ref names, isCrossRepository, label names, comment and review thread counts, the ready and closed aliases; reviews with paging info and nodes holding databaseId, state, submittedAt, author, association and the inline comment count; and events, the timeline items of the EVENT_TYPES, with paging info and nodes holding type name, id, a time, a login and type-specific fields. The fields map through _pull_request() to the pull_requests table, one row per PR, keyed by number. The review nodes map through _review() to pr_reviews, keyed by review_id. The timeline nodes map through _event() to pr_events, keyed by event_id. Each child table's pr_number refers to pull_requests.number, one PR to many rows.">
<defs><marker id="pd-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- Zones -->
<rect class="zone" x="30" y="30" width="560" height="1375" rx="18"/>
<rect class="zone" x="770" y="30" width="620" height="1375" rx="18"/>
<rect class="section-bg" x="43" y="44" width="182" height="26" rx="6"/>
<rect class="section-bg" x="783" y="44" width="160" height="26" rx="6"/>
<g class="zone-title"><text x="53" y="62">GITHUB GRAPHQL API</text><text x="793" y="62">PARQUET TABLES</text></g>

<!-- GitHub pull request JSON -->
<rect class="wash" x="62" y="92" width="496" height="40" rx="8"/>
<rect class="sketch" x="60" y="90" width="500" height="662" rx="10"/>
<text class="name" x="310" y="118">pull request (search.nodes[])</text>
<g class="mono">
<text x="80" y="160">pullRequest</text>
<text class="dropped" x="80" y="184">├─ id</text>
<text x="80" y="208">├─ number · title · url</text>
<text x="80" y="232">├─ state · isDraft</text>
<text x="80" y="256">├─ author.login · authorAssociation</text>
<text x="80" y="280">├─ createdAt · updatedAt · closedAt · mergedAt</text>
<text x="80" y="304">├─ mergeCommit.oid</text>
<text x="80" y="328">├─ baseRefName · headRefName · isCrossRepository</text>
<text x="80" y="352">├─ labels.nodes[].name</text>
<text x="80" y="376">├─ comments.totalCount · reviewThreads.totalCount</text>
<text x="80" y="400">├─ ready.nodes[0].createdAt</text>
<text x="80" y="424">├─ closed.nodes[0] {actor.login, closer.oid}</text>
<text x="80" y="448">├─ reviews</text>
<text class="dropped" x="80" y="472">│  ├─ pageInfo {hasNextPage, endCursor}</text>
<text x="80" y="496">│  └─ nodes[]</text>
<text x="80" y="520">│     ├─ databaseId · state · submittedAt</text>
<text x="80" y="544">│     ├─ author.login · authorAssociation</text>
<text x="80" y="568">│     └─ comments.totalCount</text>
<text x="80" y="592">└─ events  (timelineItems, EVENT_TYPES)</text>
<text class="dropped" x="80" y="616">   ├─ pageInfo {hasNextPage, endCursor}</text>
<text x="80" y="640">   └─ nodes[]</text>
<text x="80" y="664">      ├─ __typename · id</text>
<text x="80" y="688">      ├─ createdAt | submittedAt | commit.committedDate</text>
<text x="80" y="712">      ├─ actor | author | commit.author.user → .login</text>
<text x="80" y="736">      └─ requestedReviewer · label · commit · state · …</text>
</g>
<g class="smallnote">
<text class="start" x="150" y="184">node id: only for follow-up queries, not kept</text>
<text class="start" x="420" y="472">paging only</text>
<text class="start" x="420" y="616">paging only</text>
<text class="start" x="60" y="782">lighter text: returned by GitHub, used but not stored</text>
<text class="start" x="60" y="802">SEARCH_QUERY: up to 50 PRs a page · 100 reviews · 100 events per PR</text>
<text class="start" x="60" y="822">hasNextPage → REVIEWS_QUERY / TIMELINE_QUERY by node id</text>
<text class="start" x="60" y="842">labels: 50, not paged</text>
<text class="start" x="60" y="862">timestamps like 2026-08-24T02:27:24Z → converted to UTC</text>
</g>

<!-- pull_requests table -->
<rect class="wash" x="802" y="92" width="536" height="40" rx="8"/>
<rect class="sketch" x="800" y="90" width="540" height="611" rx="10"/>
<text class="name" x="1070" y="118">pull_requests · one row per PR</text>
<g class="mono">
<text class="pk" x="820" y="160">number  PK</text>
<text x="820" y="184">title</text>
<text x="820" y="208">url</text>
<text x="820" y="232">state</text>
<text x="820" y="256">is_draft</text>
<text x="820" y="280">author_login</text>
<text x="820" y="304">author_association</text>
<text x="820" y="328">created_at</text>
<text x="820" y="352">updated_at</text>
<text x="820" y="376">closed_at</text>
<text x="820" y="400">merged_at</text>
<text x="820" y="424">ready_for_review_at</text>
<text x="820" y="448">closed_by_login</text>
<text x="820" y="472">closing_commit_sha</text>
<text x="820" y="496">merge_commit_sha</text>
<text x="820" y="520">base_ref</text>
<text x="820" y="544">head_ref</text>
<text x="820" y="568">is_cross_repository</text>
<text x="820" y="592">labels</text>
<text x="820" y="616">comments_count</text>
<text x="820" y="640">review_threads_count</text>
<text x="820" y="664">review_comments_count</text>
<text x="820" y="688">reviews_count</text>
</g>
<g class="type">
<text x="1320" y="160">int32</text>
<text x="1320" y="184">string</text>
<text x="1320" y="208">string</text>
<text x="1320" y="232">string</text>
<text x="1320" y="256">bool</text>
<text x="1320" y="280">string · null</text>
<text x="1320" y="304">string</text>
<text x="1320" y="328">timestamp[ms, UTC]</text>
<text x="1320" y="352">timestamp[ms, UTC]</text>
<text x="1320" y="376">timestamp[ms, UTC] · null</text>
<text x="1320" y="400">timestamp[ms, UTC] · null</text>
<text x="1320" y="424">timestamp[ms, UTC] · null</text>
<text x="1320" y="448">string · null</text>
<text x="1320" y="472">string · null</text>
<text x="1320" y="496">string · null</text>
<text x="1320" y="520">string</text>
<text x="1320" y="544">string</text>
<text x="1320" y="568">bool</text>
<text x="1320" y="592">string · null</text>
<text x="1320" y="616">int32</text>
<text x="1320" y="640">int32</text>
<text x="1320" y="664">int32</text>
<text x="1320" y="688">int32</text>
</g>

<!-- pr_reviews table -->
<rect class="wash" x="802" y="772" width="536" height="40" rx="8"/>
<rect class="sketch" x="800" y="770" width="540" height="230" rx="10"/>
<text class="name" x="1070" y="798">pr_reviews · one row per review</text>
<g class="mono">
<text x="820" y="840">pr_number  FK</text>
<text class="pk" x="820" y="864">review_id  PK</text>
<text x="820" y="888">author_login</text>
<text x="820" y="912">author_association</text>
<text x="820" y="936">state</text>
<text x="820" y="960">submitted_at</text>
<text x="820" y="984">comments_count</text>
</g>
<g class="type">
<text x="1320" y="840">int32</text>
<text x="1320" y="864">int64</text>
<text x="1320" y="888">string</text>
<text x="1320" y="912">string</text>
<text x="1320" y="936">string</text>
<text x="1320" y="960">timestamp[ms, UTC] · null</text>
<text x="1320" y="984">int32</text>
</g>

<!-- pr_events table -->
<rect class="wash" x="802" y="1052" width="536" height="40" rx="8"/>
<rect class="sketch" x="800" y="1050" width="540" height="325" rx="10"/>
<text class="name" x="1070" y="1078">pr_events · one row per timeline event</text>
<g class="mono">
<text x="820" y="1120">pr_number  FK</text>
<text class="pk" x="820" y="1144">event_id  PK</text>
<text x="820" y="1168">event_type</text>
<text x="820" y="1192">event_at</text>
<text x="820" y="1216">actor_login</text>
<text x="820" y="1240">requested_reviewer</text>
<text x="820" y="1264">label</text>
<text x="820" y="1288">commit_sha</text>
<text x="820" y="1312">review_state</text>
<text x="820" y="1336">old_value</text>
<text x="820" y="1360">new_value</text>
</g>
<g class="type">
<text x="1320" y="1120">int32</text>
<text x="1320" y="1144">string</text>
<text x="1320" y="1168">string</text>
<text x="1320" y="1192">timestamp[ms, UTC]</text>
<text x="1320" y="1216">string · null</text>
<text x="1320" y="1240">string · null</text>
<text x="1320" y="1264">string · null</text>
<text x="1320" y="1288">string · null</text>
<text x="1320" y="1312">string · null</text>
<text x="1320" y="1336">string · null</text>
<text x="1320" y="1360">string · null</text>
</g>

<!-- Mappings -->
<path class="light" d="M528 196 H543 V428 H528"/>
<path class="light" d="M528 508 H543 V572 H528"/>
<path class="light" d="M528 652 H543 V740 H528"/>
<g class="arrow" marker-end="url(#pd-arrowhead)">
<path d="M548 312 C650 312 680 330 788 330"/>
<path d="M548 540 C660 540 680 900 788 900"/>
<path d="M548 696 C660 696 680 1215 788 1215"/>
<path d="M1342 155 H1372 V835 H1346"/>
<path d="M1372 835 V1115 H1346"/>
</g>
<text class="msg" x="668" y="300">_pull_request()</text>
<text class="msg" x="668" y="712">_review()</text>
<text class="msg" x="668" y="946">_event()</text>
<g class="smallnote">
<text x="668" y="344">one row per PR · counts only</text>
<text x="668" y="730">one row per review, all pages</text>
<text x="668" y="964">one row per timeline item</text>
<text x="668" y="982">EVENT_TYPES only</text>
<text x="1372" y="480">1 : n</text>
</g>
</svg>
</div>
<p class="pipeline-caption">GitHub pull request JSON on the left, the three daily parquet tables on the right · each child's pr_number → pull_requests.number, one PR to many rows · files: PR_metrics/&lt;table&gt;/&lt;table&gt;_&lt;day&gt;.parquet</p>
</div>

## Example: apache/artemis #6637

One POST per page of search results: `SEARCH_QUERY` with `{"q": "repo:apache/artemis is:pr updated:<day>T00:00:00Z..<day>T23:59:59Z", "cursor": null}`, up to 50 PRs per page. Below is a real response for one PR (fetched 2026-11-12 with the pipeline's `SEARCH_QUERY`), as returned except for the cursors shortened and `…` where the other 49 PRs of the page were. `//` comments are ours.

```jsonc
{
  "data": {
    "search": {
      "pageInfo": {"hasNextPage": true, "endCursor": "Y3Vyc29yOjUw"},   // more results → ask again with this cursor
      "nodes": [
        {
          "id": "PR_kwDOAiYwrM8AAAABAvlcxA",           // GitHub node id; used for follow-up page queries (not stored)
          "number": 6637,                               // our key
          "title": "ARTEMIS-6199 Fix race in WildcardAddressManager",
          "url": "https://github.com/apache/artemis/pull/6637",
          "state": "MERGED",                            // OPEN / CLOSED / MERGED
          "isDraft": false,
          "author": {"login": "JohnDoe"},              // null for a deleted account ("ghost")
          "authorAssociation": "CONTRIBUTOR",           // useless here: Apache members show as CONTRIBUTOR
          "createdAt": "2026-08-24T02:27:24Z",
          "updatedAt": "2026-11-04T20:17:52Z",          // decides which day's file it goes in
          "closedAt":  "2026-08-24T15:57:05Z",
          "mergedAt":  "2026-08-24T15:57:05Z",
          "mergeCommit": {"oid": "18a9d8716b212aaec4707e86d3833bbdfbec14cb"},   // joins to commits.sha
          "baseRefName": "main",
          "headRefName": "ARTEMIS-6199",
          "isCrossRepository": true,                    // from a fork
          "labels": {"nodes": []},
          "comments": {"totalCount": 3},                // conversation comments: count only
          "reviewThreads": {"totalCount": 0},           // inline discussion threads: count only

          "reviews": {
            "pageInfo": {"hasNextPage": false, "endCursor": "Y3Vyc29yOnYyOpO0…"},  // true → REVIEWS_QUERY for the rest
            "nodes": [
              {"databaseId": 5009840099, "author": {"login": "maxwaler"},
               "authorAssociation": "CONTRIBUTOR", "state": "APPROVED",
               "submittedAt": "2026-08-24T15:51:40Z", "comments": {"totalCount": 0}}
            ]
          },

          "events": {                                   // alias for timelineItems(itemTypes: EVENT_TYPES)
            "pageInfo": {"hasNextPage": false, "endCursor": "Y3Vyc29yOnYyOpPP…"},  // true → TIMELINE_QUERY for the rest
            "nodes": [
              {"__typename": "HeadRefForcePushedEvent", "id": "HRFPE_lADOAiYwrM8AAAABN8KLRM8AAAAG9XuCRA",
               "createdAt": "2026-08-24T02:28:19Z", "actor": {"login": "JohnDoe"},
               "beforeCommit": {"oid": "b014dbdfe5ef4d63feacaca50f662c8c4a4b0350"},
               "afterCommit":  {"oid": "7b4dec3d1179bb9df3a910641de1790df09b1404"}},
              {"__typename": "IssueComment", "id": "IC_kwDOAiYwrM8AAAABQUUycg",
               "createdAt": "2026-08-24T02:28:55Z", "author": {"login": "JohnDoe"}},          // author, not actor
              {"__typename": "HeadRefForcePushedEvent", "id": "HRFPE_lADOAiYwrM8AAAABN8KLRM8AAAAG9XxvCg",
               "createdAt": "2026-08-24T02:30:46Z", "actor": {"login": "JohnDoe"},
               "beforeCommit": {"oid": "7b4dec3d1179bb9df3a910641de1790df09b1404"},
               "afterCommit":  {"oid": "9826828c05b345d93644e695196089d18712f91d"}},
              {"__typename": "ReviewRequestedEvent", "id": "RRE_lADOAiYwrM8AAAABN8KLRM8AAAAG91l-fg",
               "createdAt": "2026-08-24T14:38:47Z", "actor": {"login": "JohnDoe"},
               "requestedReviewer": {"__typename": "User", "login": "maxwaler"}},       // User/Bot/Mannequin: login; Team: slug
              {"__typename": "PullRequestCommit", "id": "PURC_lADOAiYwrM8AAAABAvlcxNoAKGYyZGFkNGI3…",
               "commit": {"oid": "f2dad4b70735237d5bf6e2377c6e18b9fd612b2a",
                          "committedDate": "2026-08-24T14:39:16Z",                           // no createdAt: when written, not pushed
                          "author": {"user": {"login": "JohnDoe"}}}},
              {"__typename": "HeadRefForcePushedEvent", "id": "HRFPE_lADOAiYwrM8AAAABN8KLRM8AAAAG91oPOA",
               "createdAt": "2026-08-24T14:39:26Z", "actor": {"login": "JohnDoe"},
               "beforeCommit": {"oid": "9826828c05b345d93644e695196089d18712f91d"},
               "afterCommit":  {"oid": "f2dad4b70735237d5bf6e2377c6e18b9fd612b2a"}},
              {"__typename": "PullRequestReview", "id": "PRR_kwDOAiYwrM8AAAABKpwX4w",
               "submittedAt": "2026-08-24T15:51:40Z", "author": {"login": "maxwaler"},  // submittedAt, author
               "state": "APPROVED"},
              {"__typename": "IssueComment", "id": "IC_kwDOAiYwrM8AAAABQbtDlw",
               "createdAt": "2026-08-24T15:51:54Z", "author": {"login": "maxwaler"}},
              {"__typename": "IssueComment", "id": "IC_kwDOAiYwrM8AAAABQbtb5A",
               "createdAt": "2026-08-24T15:52:27Z", "author": {"login": "maxwaler"}},
              {"__typename": "MergedEvent", "id": "ME_lADOAiYwrM8AAAABN8KLRM8AAAAG96F8ug",
               "createdAt": "2026-08-24T15:57:05Z", "actor": {"login": "JohnDoe"},
               "commit": {"oid": "18a9d8716b212aaec4707e86d3833bbdfbec14cb"}},
              {"__typename": "ClosedEvent", "id": "CE_lADOAiYwrM8AAAABN8KLRM8AAAAG96F89A",
               "createdAt": "2026-08-24T15:57:05Z", "actor": {"login": "JohnDoe"},
               "closer": null}                          // a merge closes too; closer is set only when a commit closed it
            ]
          },

          "ready":  {"nodes": []},                      // never a draft, so no ReadyForReviewEvent
          "closed": {"nodes": [{"actor": {"login": "JohnDoe"}, "closer": null}]}
        },
        …                                               // up to 49 more PRs on this page
      ]
    }
  }
}
```

Things this example shows:

- **`updatedAt` moves.** The PR was merged on 2026-08-24 but its `updatedAt` is 2026-11-04: something touched it later (GitHub bumps `updatedAt` for many reasons, not only the events we keep). It is in the 2026-11-04 file now (and in the 2026-08-24 file too, if that day was extracted back then).
- **Counts vs. lists.** `comments.totalCount` is 3, and there are three `IssueComment` events: the count comes from the PR, the list from the timeline; we keep both but not the comment text.
- **A merge is also a close.** `MergedEvent` and `ClosedEvent` come at the same second; `closer` is null because GitHub's merge closed it, not a pushed commit. A PR that lands by a push to `main` instead has `state: CLOSED`, no `mergeCommit`, and `closer: {"oid": "<sha>"}` on its `ClosedEvent`.
- **Force pushes.** The author rewrote the branch three times; each `HeadRefForcePushedEvent` has the head before and after. Only one `PullRequestCommit` is left (the timeline shows the commits on the final branch).
- **Each event type has its own field names:** `createdAt` + `actor` for most, `submittedAt` + `author` for a review, `author` for a comment, and only a `commit` (no time of its own) for `PullRequestCommit`.

Error responses come back as HTTP 200 with an `errors` list instead of (or next to) `data`; `graphql_post` turns that into a `GitHubError`.


## pull_requests

One row per PR whose `updated_at` falls on the file's day. Key: `number` within a file; `(number, updated_at)` across files.

| Column | Type | From the GitHub JSON | #6637 |
|---|---|---|---|
| `number` | int32 | `number` | 6637 |
| `title` | string | `title` | ARTEMIS-6199 Fix race in WildcardAddressManager |
| `url` | string | `url` | https://github.com/apache/artemis/pull/6637 |
| `state` | string | `state` | MERGED |
| `is_draft` | bool | `isDraft` | false |
| `author_login` | string | `author.login` (null for a deleted account) | JohnDoe |
| `author_association` | string | `authorAssociation` | CONTRIBUTOR |
| `created_at` | timestamp | `createdAt` | 2026-08-24 02:27:24 |
| `updated_at` | timestamp | `updatedAt`; decides which day's file the PR is in | 2026-11-04 20:17:52 |
| `closed_at` | timestamp | `closedAt` | 2026-08-24 15:57:05 |
| `merged_at` | timestamp | `mergedAt` | 2026-08-24 15:57:05 |
| `ready_for_review_at` | timestamp | `ready.nodes[0].createdAt` (first `ReadyForReviewEvent`) | null |
| `closed_by_login` | string | `closed.nodes[0].actor.login` (last `ClosedEvent`) | JohnDoe |
| `closing_commit_sha` | string | `closed.nodes[0].closer.oid` (only when a commit closed it) | null |
| `merge_commit_sha` | string | `mergeCommit.oid` | 18a9d871… |
| `base_ref` | string | `baseRefName` | main |
| `head_ref` | string | `headRefName` | ARTEMIS-6199 |
| `is_cross_repository` | bool | `isCrossRepository` | true |
| `labels` | string | `labels.nodes[].name` joined with `, `; null if none | null |
| `comments_count` | int32 | `comments.totalCount` | 3 |
| `review_threads_count` | int32 | `reviewThreads.totalCount` | 0 |
| `review_comments_count` | int32 | computed: sum of the reviews' `comments.totalCount` | 0 |
| `reviews_count` | int32 | computed: number of reviews (all pages) | 1 |

Not stored: the node `id`, and the full `reviews` / `events` lists (they go to the other tables).

## pr_reviews

One row per review of the PRs in `pull_requests` for the same day. Key: `review_id`.

| Column | Type | From the GitHub JSON | #6637 |
|---|---|---|---|
| `pr_number` | int32 | the PR's `number` | 6637 |
| `review_id` | int64 | `databaseId` | 5009840099 |
| `author_login` | string | `author.login` | maxwaler |
| `author_association` | string | `authorAssociation` | CONTRIBUTOR |
| `state` | string | `state` | APPROVED |
| `submitted_at` | timestamp | `submittedAt` (null for a pending review) | 2026-08-24 15:51:40 |
| `comments_count` | int32 | `comments.totalCount` (inline comments in this review) | 0 |

This is the complete list of reviews. The timeline's `PullRequestReview` events leave out single-comment replies in a review thread, which GitHub stores as separate `COMMENTED` reviews.

## pr_events

One row per timeline event of the PRs in `pull_requests` for the same day. Key: `event_id`. Common columns, then type-specific ones; a column that does not apply to the type is null.

| Column | Type | From the GitHub JSON |
|---|---|---|
| `pr_number` | int32 | the PR's `number` |
| `event_id` | string | `id` (node id) |
| `event_type` | string | `__typename` |
| `event_at` | timestamp | `createdAt`; `submittedAt` for `PullRequestReview`; `commit.committedDate` for `PullRequestCommit` |
| `actor_login` | string | `actor.login`; `author.login` for `PullRequestReview` / `IssueComment`; `commit.author.user.login` for `PullRequestCommit` |
| `requested_reviewer` | string | `ReviewRequested/RemovedEvent`: `requestedReviewer.login` or `.slug` |
| `label` | string | `Labeled/UnlabeledEvent`: `label.name` |
| `commit_sha` | string | `PullRequestCommit`: the commit; `MergedEvent`: the merge commit; `ClosedEvent`: the closing commit; `HeadRefForcePushedEvent`: the new head |
| `review_state` | string | `PullRequestReview`: `state`; `ReviewDismissedEvent`: `previousReviewState` |
| `old_value` | string | `HeadRefForcePushedEvent`: old head; `RenamedTitleEvent`: old title; `BaseRefChangedEvent`: old base |
| `new_value` | string | the same, new value |

The eleven events of #6637 become these rows (`pr_number` 6637 on all, ids shortened):

| event_id | event_type | event_at | actor_login | other columns |
|---|---|---|---|---|
| HRFPE_…9XuCRA | HeadRefForcePushedEvent | 2026-08-24 02:28:19 | JohnDoe | commit_sha 7b4dec3d, old_value b014dbdf, new_value 7b4dec3d |
| IC_…QUUycg | IssueComment | 2026-08-24 02:28:55 | JohnDoe | |
| HRFPE_…9XxvCg | HeadRefForcePushedEvent | 2026-08-24 02:30:46 | JohnDoe | commit_sha 9826828c, old_value 7b4dec3d, new_value 9826828c |
| RRE_…91l-fg | ReviewRequestedEvent | 2026-08-24 14:38:47 | JohnDoe | requested_reviewer maxwaler |
| PURC_…KGYyZGFk… | PullRequestCommit | 2026-08-24 14:39:16 | JohnDoe | commit_sha f2dad4b7 |
| HRFPE_…91oPOA | HeadRefForcePushedEvent | 2026-08-24 14:39:26 | JohnDoe | commit_sha f2dad4b7, old_value 9826828c, new_value f2dad4b7 |
| PRR_…KpwX4w | PullRequestReview | 2026-08-24 15:51:40 | maxwaler | review_state APPROVED |
| IC_…QbtDlw | IssueComment | 2026-08-24 15:51:54 | maxwaler | |
| IC_…Qbtb5A | IssueComment | 2026-08-24 15:52:27 | maxwaler | |
| ME_…96F8ug | MergedEvent | 2026-08-24 15:57:05 | JohnDoe | commit_sha 18a9d871 |
| CE_…96F89A | ClosedEvent | 2026-08-24 15:57:05 | JohnDoe | commit_sha null (closed by the merge) |

Opening the PR is not an event: use `pull_requests.created_at`.


## How are PRs 'finished' in the Artemis project

- Merged means the request was accepted, and GitHub itself put the change into main.

- Closed means the request is no longer open. That can happen because it was accepted, or because it was turned down.

In the Artemis project, it seems to end the PR in one of the following ways:

1. Merged on GitHub. It gets a MergedEvent and a ClosedEvent. The code is in main.

2. The code was pushed to main by hand, and that commit closed the PR. It gets only a ClosedEvent, and that event names the commit that closed it (GitHub never "merged" it).

3. Closed without being used. For example, dependabot opened a duplicate bump and someone closed it. It gets only a ClosedEvent, with no commit. The code is not in main/ not added.


So a ClosedEvent alone doesn't tell you the code landed. The PR landed if it has a MergedEvent, or if its ClosedEvent names a commit.

GitHub can get the Closed commit in several way. The main ways appear to be 
- same hash on main as in PR
- 'close PR ####' in the commit message 



## Event types

These are the events we query for in GitHub.

```
EVENT_TYPES = (
    "CLOSED_EVENT",
    "MERGED_EVENT",
    "REOPENED_EVENT",
    "READY_FOR_REVIEW_EVENT",
    "CONVERT_TO_DRAFT_EVENT",
    "PULL_REQUEST_REVIEW",
    "REVIEW_REQUESTED_EVENT",
    "REVIEW_REQUEST_REMOVED_EVENT",
    "REVIEW_DISMISSED_EVENT",
    "PULL_REQUEST_COMMIT",
    "HEAD_REF_FORCE_PUSHED_EVENT",
    "ISSUE_COMMENT",
    "RENAMED_TITLE_EVENT",
    "LABELED_EVENT",
    "UNLABELED_EVENT",
    "BASE_REF_CHANGED_EVENT",
)

```

## Snapshot behaviour

If a PR is update the PR with be inclued in the parquet file for that day. **All** the reviews and timeline events will for the PR will also be included their corresponding parquet file - even if they have not changed. 

```

1. On 23 Oct someone comments on the PR, so its updatedAt becomes 23 Oct.
2. The extract finds the PR in the 23 Oct search.
3. It fetches the PR's full timeline, with no "since" filter (we chose this on purpose, so a rerun doesn't depend on what was fetched before).
4. All of those events go into the 23 Oct file. Each event keeps its real time in event_at.

```

## Notes

- **Snapshots, not history:** each PR is written with its state at extraction time into the file of its `updated_at` UTC day. Each run rewrites every day in its range (target day + lookback), empty days included.
- **A PR is in many files:** when it changes again it is written into the later day's file too; the earlier file keeps the older snapshot (a rerun of that day no longer finds the PR, but files outside the run's range are not touched). The current state is the row with the greatest `updated_at`.
- **Children are written in full with every snapshot:** reviews and the whole timeline are refetched each time (no "since"), so the same `review_id` and `event_id` repeat across files. Downstream deduplicates on `review_id` and on `event_id`; events never change, so any copy will do.
- **Children have no `updated_at`:** they belong to the PR row in the same day's file through `pr_number`.
- **Loading:** `copy-github-prs` copies each file into `raw.pull_requests`, `raw.pr_reviews` and `raw.pr_events` as is, adding `source_file` and `loaded_at`. dbt then keeps every snapshot of a PR (`stg_pull_requests_dedup`, key `(number, updated_at)`) and one row per review (`stg_pr_reviews_dedup`, key `review_id`).
- **Joins:** `merge_commit_sha`, `closing_commit_sha` and the events' `commit_sha` join to `commits.sha`; the Jira id (`ARTEMIS-6199`) comes from the title (`jira_id` macro). Logins are GitHub logins, not names or emails, so they do not join `dim_person` (see `dim_person_github`).
- **Times** are all converted to UTC and stored with millisecond precision (`timestamp(ms, tz=UTC)`).
