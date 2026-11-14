# Orchestration

## Overview

Prefect is used to trigger the flows. The primary flow is `artemis-metrics-pipeline` - runs daily on a schedule. All the other boxes in the diagram below is sub flows. 
The flows are thin wrappers. Each extract or load task builds the matching `ingestion` CLI's arguments and calls its `run()`. Each dbt task calls `dbtRunner` in-process.

Python code is used to register the deployments on prefetch and server them. This is done using the `__init__.py` 

### In Dev 

The `__init__.py` file is invoked directly. Use the following command.

TODO: check this is the exact command
```
uv run --env-file .env --env-file .env.<env> --package orchestration

```
The python code connects to python on the HOST's port.

### In Prod

All the code (ingest, transforms) are bundled into a Docker image . A docker container is created based on this image and  is attached to the docker compose network where Pefect container is running and listening. 


## Pipeline flow

<div class="pipeline">
<style>
.pipeline{--ink:#788d79;--dark:#536b56;--paper:#fafaf2;margin:30px 0;color:var(--dark)}
.pipeline svg{display:block;width:100%;height:auto;overflow:visible}
.pipeline svg path,.pipeline svg circle,.pipeline svg rect{vector-effect:non-scaling-stroke}
.pipeline .sketch{stroke:var(--ink);stroke-width:2.5;stroke-linecap:round;stroke-linejoin:round;fill:var(--paper)}
.pipeline .zone{stroke:#acbcaa;stroke-width:1.5;stroke-dasharray:6 8;fill:none}
.pipeline .section-bg{fill:var(--paper);stroke:none}
.pipeline .zone-title{font-size:14px;letter-spacing:.12em;fill:#81917c;font-family:system-ui,sans-serif}
.pipeline .name{font-size:17px;fill:var(--dark);text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .msg{font-size:15px;fill:var(--dark);text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .msg,.pipeline .desc{font-size:14px;font-style:italic;fill:#80917d;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .smallnote{paint-order:stroke;stroke:var(--paper);stroke-width:6px;stroke-linejoin:round}
.pipeline .badge circle{fill:var(--paper);stroke:var(--ink);stroke-width:1.8}
.pipeline .badge text{font:700 12px system-ui,sans-serif;fill:var(--ink);text-anchor:middle}
.pipeline .arrow{stroke:var(--ink);stroke-width:2.2;fill:none;stroke-linecap:round;stroke-linejoin:round}
.pipeline .smallnote{font-size:12px;fill:#80917d;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .pipeline-caption{text-align:center;max-width:none;margin-top:12px;font:14px system-ui,sans-serif;color:#80917d}
@media (max-width:760px){.pipeline .scroller{overflow-x:auto;padding-bottom:8px}.pipeline .scroller svg{min-width:1100px}}
</style>
<div class="scroller">
<svg viewBox="20 20 1360 700" role="img" aria-label="Flow chart of the artemis-metrics-pipeline flow. A Prefect flow run, from the prod cron at 13:42 Berlin or from the UI or CLI, starts artemis-metrics-pipeline, which calls nine subflows one after another. Extract: 1 git-repo-metrics, 2 jira-metrics, 3 github-pr-metrics write parquet to out. Load: 4 load-git-metrics, 5 load-jira-metrics, 6 load-github-pr-metrics copy the parquet into the Postgres raw tables. Transform: 7 dbt-build-git-metrics builds the core models, 8 dbt-build-mart the mart views, 9 dbt-build-data-freshness the freshness model. An exception in any step stops the pipeline.">
<defs><marker id="orch-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- Prefect and the parent flow -->
<rect class="sketch" x="1100" y="40" width="220" height="50" rx="8"/>
<text class="name" x="1210" y="71">Prefect server</text>
<rect class="sketch" x="500" y="30" width="400" height="70" rx="10"/>
<text class="name" x="700" y="60">artemis-metrics-pipeline</text>
<text class="desc" x="700" y="84">Run Artemis metrics pipeline</text>

<!-- Phases -->
<rect class="zone" x="40" y="150" width="400" height="550" rx="18"/>
<rect class="zone" x="500" y="150" width="400" height="550" rx="18"/>
<rect class="zone" x="960" y="150" width="400" height="550" rx="18"/>
<rect class="section-bg" x="53" y="164" width="92" height="26" rx="6"/>
<rect class="section-bg" x="513" y="164" width="62" height="26" rx="6"/>
<rect class="section-bg" x="973" y="164" width="114" height="26" rx="6"/>
<g class="zone-title"><text x="63" y="182">EXTRACT</text><text x="523" y="182">LOAD</text><text x="983" y="182">TRANSFORM</text></g>
<g class="smallnote"><text x="240" y="680">ingestion CLIs · write parquet to out (S3 on dev / prod)</text><text x="700" y="680">read parquet from out · COPY into Postgres raw</text><text x="1160" y="680">dbtRunner in-process · target = DBT_TARGET</text></g>

<!-- Calls -->
<g class="arrow" marker-end="url(#orch-arrowhead)">
<path d="M1100 65 H910"/>
<path d="M500 65 H240 V200"/>
<path d="M240 320 V360"/>
<path d="M240 480 V520"/>
<path d="M400 585 H470 V265 H530"/>
<path d="M700 320 V360"/>
<path d="M700 480 V520"/>
<path d="M860 585 H930 V265 H990"/>
<path d="M1160 320 V360"/>
<path d="M1160 480 V520"/>
</g>
<text class="msg" x="1005" y="55">flow run</text>
<g class="smallnote"><text x="1005" y="85">prod cron 13:42 Berlin · or UI / CLI</text></g>

<!-- Subflows -->
<rect class="sketch" x="80" y="210" width="320" height="110" rx="10"/>
<g class="badge"><circle cx="104" cy="234" r="11"/><text x="104" y="238">1</text></g>
<text class="name" x="240" y="258">git-repo-metrics</text>
<text class="desc" x="240" y="286">Extract Metric From Repo</text>
<rect class="sketch" x="80" y="370" width="320" height="110" rx="10"/>
<g class="badge"><circle cx="104" cy="394" r="11"/><text x="104" y="398">2</text></g>
<text class="name" x="240" y="418">jira-metrics</text>
<text class="desc" x="240" y="446">Extract Jira issues</text>
<rect class="sketch" x="80" y="530" width="320" height="110" rx="10"/>
<g class="badge"><circle cx="104" cy="554" r="11"/><text x="104" y="558">3</text></g>
<text class="name" x="240" y="578">github-pr-metrics</text>
<text class="desc" x="240" y="606">Extract GitHub PRs</text>
<rect class="sketch" x="540" y="210" width="320" height="110" rx="10"/>
<g class="badge"><circle cx="564" cy="234" r="11"/><text x="564" y="238">4</text></g>
<text class="name" x="700" y="258">load-git-metrics</text>
<text class="desc" x="700" y="286">Load metrics to DB</text>
<rect class="sketch" x="540" y="370" width="320" height="110" rx="10"/>
<g class="badge"><circle cx="564" cy="394" r="11"/><text x="564" y="398">5</text></g>
<text class="name" x="700" y="418">load-jira-metrics</text>
<text class="desc" x="700" y="446">Load Jira issues to DB</text>
<rect class="sketch" x="540" y="530" width="320" height="110" rx="10"/>
<g class="badge"><circle cx="564" cy="554" r="11"/><text x="564" y="558">6</text></g>
<text class="name" x="700" y="578">load-github-pr-metrics</text>
<text class="desc" x="700" y="606">Load GitHub PRs to DB</text>
<rect class="sketch" x="1000" y="210" width="320" height="110" rx="10"/>
<g class="badge"><circle cx="1024" cy="234" r="11"/><text x="1024" y="238">7</text></g>
<text class="name" x="1160" y="258">dbt-build-git-metrics</text>
<text class="desc" x="1160" y="286">DBT build core models</text>
<rect class="sketch" x="1000" y="370" width="320" height="110" rx="10"/>
<g class="badge"><circle cx="1024" cy="394" r="11"/><text x="1024" y="398">8</text></g>
<text class="name" x="1160" y="418">dbt-build-mart</text>
<text class="desc" x="1160" y="446">DBT build mart views</text>
<rect class="sketch" x="1000" y="530" width="320" height="110" rx="10"/>
<g class="badge"><circle cx="1024" cy="554" r="11"/><text x="1024" y="558">9</text></g>
<text class="name" x="1160" y="578">dbt-build-data-freshness</text>
<text class="desc" x="1160" y="606">DBT build data freshness</text>
</svg>
</div>
<p class="pipeline-caption">artemis-metrics-pipeline and its subflows · each box is a subflow (its own child flow run in the Prefect UI) with its deployment name · an exception in any step stops the chain · each subflow is also a deployment that can be run alone</p>
</div>

## TODO

- invoke by command.
- describe the backfill functionality
- review the flow order


