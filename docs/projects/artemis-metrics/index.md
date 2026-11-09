# Artemis Metric Pipeline

A test page for the Git metrics project: an automated pipeline that takes a checked-out repository all the way to an analytics dashboard. The animation follows one document as it pauses inside each step.

<div class="tabs">
<div class="tab-list" role="tablist" aria-label="Pipeline diagrams">
<button type="button" class="tab" role="tab" id="gm-tab-main" aria-controls="gm-panel-main" aria-selected="true">Flow Overview</button>
<button type="button" class="tab" role="tab" id="gm-tab-full" aria-controls="gm-panel-full" aria-selected="false">Flow Details</button>
</div>
<div class="tab-panel" role="tabpanel" id="gm-panel-main" aria-labelledby="gm-tab-main">
<div class="pipeline">
<style>
.pipeline{--ink:#788d79;--dark:#536b56;--paper:#fafaf2;--wash:#dce8d5;margin:30px 0;color:var(--dark)}
.pipeline svg{display:block;width:100%;height:auto;overflow:visible}
.pipeline .sketch{stroke:var(--ink);stroke-width:2.5;stroke-linecap:round;stroke-linejoin:round;fill:none}
.pipeline .light{stroke:#acbcaa;stroke-width:1.5;stroke-linecap:round;fill:none}
.pipeline .label{font-size:21px;fill:var(--dark);text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .desc{font-size:14px;fill:#81917c;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .arrow{stroke:var(--ink);stroke-width:2.6;fill:none;stroke-linecap:round;stroke-linejoin:round}
.pipeline .smallnote{font-size:12px;fill:#80917d;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .dotted{stroke-dasharray:4 8}
#gm-panel-main .dotted{stroke-width:1.3}
.pipeline .pipeline-caption{text-align:center;max-width:none;margin-top:12px;font:14px system-ui,sans-serif;color:#80917d}
@media (max-width:760px){.pipeline .scroller{overflow-x:auto;padding-bottom:8px}.pipeline .scroller svg{min-width:1090px}}
</style>
<div class="scroller">
<svg viewBox="0 -490 1700 880" role="img" aria-label="Hand-drawn pipeline overview: Git repo, GitHub and Jira feed SeaweedFS, which lands in PostgreSQL raw tables; dbt builds stage and core models inside it; Superset shows the dashboards.">
<defs><marker id="gm-arrowhead-main" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>
<!-- Straight loose-pencil arrows through the gaps -->
<g class="arrow" marker-end="url(#gm-arrowhead-main)">
<path d="M178 190 Q 352 188 526 190"/><path d="M668 190 Q 839 188 1010 190"/><path class="dotted" d="M1308 190 Q 1390 191 1471 190"/>
</g>
<!-- Stage 1: hand-sketched open Git folder + branching -->
<g transform="translate(110 -372)" class="sketch">
<path d="M-54 -23 Q-54 -29 -46 -29 H-13 L-5 -20 H48 Q54 -20 54 -13 L50 50 Q48 55 39 55 H-47 Q-54 55 -54 46 Z" />
<path d="M-54 -17 Q-3 -19 54 -13" class="light"/>
<path d="M-24 18 L-2 0 L24 22 M-2 0 L-2 37"/>
<circle cx="-24" cy="18" r="5" fill="var(--paper)"/><circle cx="-2" cy="0" r="5" fill="var(--paper)"/><circle cx="24" cy="22" r="5" fill="var(--paper)"/><circle cx="-2" cy="37" r="5" fill="var(--paper)"/>
</g>
<!-- Stage 3: simple database cylinder with a little leaf like the reference -->
<g transform="translate(620 148)" class="sketch">
<ellipse cx="0" cy="-31" rx="49" ry="13"/><path d="M-49 -31 V43 Q-47 56 0 56 Q47 56 49 43 V-31"/>
<path d="M-49 -4 Q-41 9 0 9 Q39 9 49 -4 M-49 22 Q-40 35 0 35 Q41 35 49 22" class="light"/>
<path d="M12 -4 Q11 -29 38 -32 Q41 -10 24 -1 Q17 3 12 -4Z" fill="var(--wash)"/><path d="M12 -4 L33 -25"/>
</g>
<!-- Stage 4: tall PostgreSQL unit with the dbt flow inside (grows upwards; bottom stays on the main row) -->
<g transform="translate(1160 148)" class="sketch">
<ellipse cx="0" cy="-148" rx="150" ry="16"/><path d="M-150 -148 V52 Q-146 72 0 72 Q146 72 150 52 V-148"/>
<path d="M-150 -95 Q-126 -78 0 -78 Q124 -78 150 -95 M-150 -15 Q-124 2 0 2 Q126 2 150 -15" class="light"/>
<g transform="translate(20 0)">
<path d="M-120 -117 H-30 V-73 H-120 Z M-20 -47 H80 V-3 H-20 Z M-120 13 H-30 V57 H-120 Z" fill="var(--paper)"/>
<path d="M-30 -95 Q 28 -95 30 -52 M21 -61 L30 -50 L39 -61 M-20 -25 Q -75 -25 -75 8 M-84 -2 L-75 10 L-66 -2"/>
<text x="-75" y="-86" font-size="24" text-anchor="middle" fill="var(--dark)" stroke="none">raw</text>
<text x="30" y="-16" font-size="24" text-anchor="middle" fill="var(--dark)" stroke="none">stage</text>
<text x="-75" y="44" font-size="24" text-anchor="middle" fill="var(--dark)" stroke="none">core</text>
</g>
</g>
<!-- Stage 5: hand-drawn dashboard and chart -->
<g transform="translate(1550 148)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-38 35V-5 M-38 35H42 M-31 26 L-15 5 L-1 18 L17 -12 L30 1 L41 -18"/>
</g>
<!-- Top row: Git repo feeding Python metrics extraction, piped down to SeaweedFS beside the GitHub line -->
<g class="arrow" marker-end="url(#gm-arrowhead-main)"><path d="M178 -370 Q 415 -372 652 -370 V96"/></g>
<!-- Stage 1: hand-sketched Jira badge (stacked diamonds), bottom row -->
<g transform="translate(110 147)" class="sketch">
<circle cx="0" cy="0" r="48"/>
<path d="M0 -30 L30 0 L0 30 L-30 0 Z"/>
<path d="M0 -13 L13 0 L0 13 L-13 0 Z" fill="var(--wash)"/>
</g>
<g class="label"><text x="110" y="-238">Git repo</text></g>
<g class="desc"><text x="110" y="-216">checked out</text></g>
<!-- Upper row: GitHub feeding PR metrics extraction -->
<g class="arrow" marker-end="url(#gm-arrowhead-main)"><path d="M178 -110 Q 400 -112 620 -110 V92"/></g>
<!-- Stage 1b: hand-sketched GitHub octocat badge -->
<g transform="translate(110 -112)" class="sketch">
<circle cx="0" cy="0" r="48"/>
<path d="M-22 6 Q-25 -10 -18 -18 L-20 -32 L-6 -24 Q0 -26 6 -24 L20 -32 L18 -18 Q25 -10 22 6 Q18 20 0 20 Q-18 20 -22 6Z"/>
<path d="M-8 20 V40 M8 20 V40 M-8 30 Q-18 32 -22 23"/>
</g>
<g class="label"><text x="110" y="22">GitHub</text></g>

<!-- Labels below the icons -->
<g class="label"><text x="110" y="282">Jira</text><text x="620" y="282">SeaweedFS</text><text x="1160" y="282">PostgreSQL</text><text x="1550" y="282">Superset</text></g>
<g class="desc"><text x="620" y="304">Parquet object</text><text x="1160" y="304">raw landing + dbt transforms</text><text x="1550" y="304">dashboards</text></g>
<g class="smallnote"><text x="1160" y="322">dbt flow inside PostgreSQL unit</text></g>
<!-- subtle hand-drawn orchestration bracket -->
<path d="M110 332 V346 Q110 351 115 351 H1405 Q1410 351 1410 346 V332" class="light"/>
<text x="760" y="374" class="desc" style="font-size:15px">Orchestrated by Prefect · scheduled runs, retries &amp; checks</text>
</svg>
</div>
<p class="pipeline-caption">Illustrative flow · Git, GitHub &amp; Jira → object storage → PostgreSQL + dbt → BI</p>
</div>
</div>
<div class="tab-panel" role="tabpanel" id="gm-panel-full" aria-labelledby="gm-tab-full" hidden>
<div class="pipeline">
<style>
.pipeline{--ink:#788d79;--dark:#536b56;--paper:#fafaf2;--wash:#dce8d5;margin:30px 0;color:var(--dark)}
.pipeline svg{display:block;width:100%;height:auto;overflow:visible}
.pipeline .sketch{stroke:var(--ink);stroke-width:2.5;stroke-linecap:round;stroke-linejoin:round;fill:none}
.pipeline .light{stroke:#acbcaa;stroke-width:1.5;stroke-linecap:round;fill:none}
.pipeline .label{font-size:21px;fill:var(--dark);text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .desc{font-size:14px;fill:#81917c;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .arrow{stroke:var(--ink);stroke-width:2.6;fill:none;stroke-linecap:round;stroke-linejoin:round}
.pipeline .smallnote{font-size:12px;fill:#80917d;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .dotted{stroke-dasharray:4 8}
#gm-panel-full .dotted{stroke-width:1.3}
.pipeline .pipeline-caption{text-align:center;max-width:none;margin-top:12px;font:14px system-ui,sans-serif;color:#80917d}
@media (max-width:760px){.pipeline .scroller{overflow-x:auto;padding-bottom:8px}.pipeline .scroller svg{min-width:1090px}}
</style>
<div class="scroller">
<svg viewBox="0 -490 1700 880" role="img" aria-label="Hand-drawn pipeline: Git repo to Python metrics extraction and GitHub to PR metrics extraction, both writing to SeaweedFS, above Jira to issue metrics extraction, SeaweedFS, a load step into PostgreSQL raw tables, dbt transformations, and Apache Superset">
<defs><marker id="gm-arrowhead-full" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>
<!-- Straight loose-pencil arrows through the gaps -->
<g class="arrow" marker-end="url(#gm-arrowhead-full)">
<path d="M178 190 Q 230 188 282 190"/><path d="M428 190 Q 477 192 526 190"/><path d="M668 190 Q 720 189 772 190"/><path d="M908 190 Q 959 188 1010 190"/><path class="dotted" d="M1308 190 Q 1390 191 1471 190"/>
</g>
<!-- Stage 1: hand-sketched open Git folder + branching -->
<g transform="translate(110 -372)" class="sketch">
<path d="M-54 -23 Q-54 -29 -46 -29 H-13 L-5 -20 H48 Q54 -20 54 -13 L50 50 Q48 55 39 55 H-47 Q-54 55 -54 46 Z" />
<path d="M-54 -17 Q-3 -19 54 -13" class="light"/>
<path d="M-24 18 L-2 0 L24 22 M-2 0 L-2 37"/>
<circle cx="-24" cy="18" r="5" fill="var(--paper)"/><circle cx="-2" cy="0" r="5" fill="var(--paper)"/><circle cx="24" cy="22" r="5" fill="var(--paper)"/><circle cx="-2" cy="37" r="5" fill="var(--paper)"/>
</g>
<!-- Stage 2: handwritten Python document + tiny cog -->
<g transform="translate(360 -375)" class="sketch">
<path d="M-42 -39 H15 L40 -12 V48 Q40 54 33 54 H-42 Q-48 54 -48 47 V-32 Q-48 -39 -42 -39Z"/>
<path d="M15 -39 V-13 H40"/><path d="M-27 -15 L-37 -6 L-27 3 M-5 -15 L5 -6 L-5 3 M-11 -22 L-19 10"/>
<path d="M-27 23 H24 M-27 36 H7" class="light"/>
<circle cx="34" cy="38" r="14" fill="var(--paper)"/><path d="M34 19V24 M34 52V57 M15 38H20 M48 38H53 M20 24L24 28 M44 48L48 52 M48 24L44 28 M24 48L20 52"/>
</g>
<!-- Stage 3: simple database cylinder with a little leaf like the reference -->
<g transform="translate(620 148)" class="sketch">
<ellipse cx="0" cy="-31" rx="49" ry="13"/><path d="M-49 -31 V43 Q-47 56 0 56 Q47 56 49 43 V-31"/>
<path d="M-49 -4 Q-41 9 0 9 Q39 9 49 -4 M-49 22 Q-40 35 0 35 Q41 35 49 22" class="light"/>
<path d="M12 -4 Q11 -29 38 -32 Q41 -10 24 -1 Q17 3 12 -4Z" fill="var(--wash)"/><path d="M12 -4 L33 -25"/>
</g>
<!-- Stage 3b: Load — handwritten document with a down-arrow into a tray + tiny cog -->
<g transform="translate(840 145)" class="sketch">
<path d="M-42 -39 H15 L40 -12 V48 Q40 54 33 54 H-42 Q-48 54 -48 47 V-32 Q-48 -39 -42 -39Z"/>
<path d="M15 -39 V-13 H40"/>
<path d="M-14 -26 V2 M-24 -8 L-14 2 L-4 -8 M-32 8 V16 H4 V8"/>
<path d="M-30 30 H18" class="light"/>
<circle cx="34" cy="38" r="14" fill="var(--paper)"/><path d="M34 19V24 M34 52V57 M15 38H20 M48 38H53 M20 24L24 28 M44 48L48 52 M48 24L44 28 M24 48L20 52"/>
</g>
<!-- Stage 4: tall PostgreSQL unit with the dbt flow inside (grows upwards; bottom stays on the main row) -->
<g transform="translate(1160 148)" class="sketch">
<ellipse cx="0" cy="-148" rx="150" ry="16"/><path d="M-150 -148 V52 Q-146 72 0 72 Q146 72 150 52 V-148"/>
<path d="M-150 -95 Q-126 -78 0 -78 Q124 -78 150 -95 M-150 -15 Q-124 2 0 2 Q126 2 150 -15" class="light"/>
<g transform="translate(20 0)">
<path d="M-120 -117 H-30 V-73 H-120 Z M-20 -47 H80 V-3 H-20 Z M-120 13 H-30 V57 H-120 Z" fill="var(--paper)"/>
<path d="M-30 -95 Q 28 -95 30 -52 M21 -61 L30 -50 L39 -61 M-20 -25 Q -75 -25 -75 8 M-84 -2 L-75 10 L-66 -2"/>
<text x="-75" y="-86" font-size="24" text-anchor="middle" fill="var(--dark)" stroke="none">raw</text>
<text x="30" y="-16" font-size="24" text-anchor="middle" fill="var(--dark)" stroke="none">stage</text>
<text x="-75" y="44" font-size="24" text-anchor="middle" fill="var(--dark)" stroke="none">core</text>
</g>
</g>
<!-- dbt runs in its own Docker container above the PostgreSQL unit and builds into it -->
<g transform="translate(1160 -170)" class="sketch">
<path d="M-92 -40 Q-92 -46 -86 -46 H86 Q92 -46 92 -40 V40 Q92 46 86 46 H-86 Q-92 46 -92 40 Z"/>
<path d="M-74 -46 V46 M-60 -46 V46 M60 -46 V46 M74 -46 V46" class="light"/>
<path d="M-92 -34 H-86 M86 -34 H92 M-92 34 H-86 M86 34 H92" class="light"/>
<text x="0" y="12" font-size="34" font-weight="700" text-anchor="middle" fill="var(--dark)" stroke="none" font-family="system-ui,sans-serif">dbt</text>
</g>
<g class="arrow" marker-end="url(#gm-arrowhead-full)"><path d="M1160 -118 Q 1162 -77 1160 -38"/></g>
<g class="label"><text x="1268" y="-176" style="text-anchor:start">dbt</text></g>
<g class="desc"><text x="1268" y="-154" style="text-anchor:start">in its own Docker container</text></g>
<!-- Prefect above dbt: a dotted arrow shows it triggers the dbt run -->
<g transform="translate(1160 -380)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-24 0 L0 26 M24 0 L0 26"/>
<circle cx="-24" cy="0" r="8" fill="var(--paper)"/><circle cx="24" cy="0" r="8" fill="var(--paper)"/><circle cx="0" cy="26" r="8" fill="var(--paper)"/>
</g>
<g class="arrow" marker-end="url(#gm-arrowhead-full)"><path class="dotted" d="M1160 -324 Q 1162 -274 1160 -224"/></g>
<g class="label"><text x="1230" y="-382" style="text-anchor:start">Prefect</text></g>
<g class="desc"><text x="1230" y="-360" style="text-anchor:start">orchestrator</text></g>
<g class="smallnote"><text x="1172" y="-278" style="text-anchor:start">triggers the dbt run</text></g>
<!-- Prefect triggers the three extract steps: dotted line over the top, down a rail right of the Extract column (crossing the extract→SeaweedFS arrows), branching into each Extract from above -->
<g class="arrow"><path class="dotted" d="M1160 -428 V-450 Q1160 -470 1140 -470 H490 Q470 -470 470 -450 V55"/></g>
<g class="arrow" marker-end="url(#gm-arrowhead-full)">
<path class="dotted" d="M470 -470 H380 Q360 -470 360 -450 V-422"/>
<path class="dotted" d="M470 -185 H380 Q360 -185 360 -165 V-162"/>
<path class="dotted" d="M470 55 Q470 75 450 75 H380 Q360 75 360 95 V98"/>
</g>
<g class="smallnote"><text x="760" y="-452">triggers the extract steps</text></g>
<!-- Prefect also triggers the load step: dotted line from Prefect to Load -->
<g class="arrow" marker-end="url(#gm-arrowhead-full)"><path class="dotted" d="M1103 -380 H880 Q840 -380 840 -340 V94"/></g>
<g class="smallnote"><text x="852" y="-110" style="text-anchor:start">triggers the load</text></g>
<g class="smallnote"><text x="1172" y="-74" style="text-anchor:start">dbt run · builds stage + core</text></g>
<!-- Stage 5: hand-drawn dashboard and chart -->
<g transform="translate(1550 148)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-38 35V-5 M-38 35H42 M-31 26 L-15 5 L-1 18 L17 -12 L30 1 L41 -18"/>
</g>
<!-- Top row: Git repo feeding Python metrics extraction, piped down to SeaweedFS beside the GitHub line -->
<g class="arrow" marker-end="url(#gm-arrowhead-full)"><path d="M178 -370 Q 230 -372 282 -370"/><path d="M428 -370 Q 540 -372 652 -370 V96"/></g>
<!-- Stage 1: hand-sketched Jira badge (stacked diamonds), bottom row -->
<g transform="translate(110 147)" class="sketch">
<circle cx="0" cy="0" r="48"/>
<path d="M0 -30 L30 0 L0 30 L-30 0 Z"/>
<path d="M0 -13 L13 0 L0 13 L-13 0 Z" fill="var(--wash)"/>
</g>
<!-- Stage 2: handwritten document with a ticket glyph + tiny cog, bottom row -->
<g transform="translate(360 145)" class="sketch">
<path d="M-42 -39 H15 L40 -12 V48 Q40 54 33 54 H-42 Q-48 54 -48 47 V-32 Q-48 -39 -42 -39Z"/>
<path d="M15 -39 V-13 H40"/>
<path d="M-32 -20 H8 V-12 Q3 -8 8 -4 V4 H-32 V-4 Q-27 -8 -32 -12 Z"/>
<path d="M-22 -12 H-2 M-22 -4 H-8" class="light"/>
<path d="M-32 22 H18 M-32 34 H2" class="light"/>
<circle cx="34" cy="38" r="14" fill="var(--paper)"/><path d="M34 19V24 M34 52V57 M15 38H20 M48 38H53 M20 24L24 28 M44 48L48 52 M48 24L44 28 M24 48L20 52"/>
</g>
<g class="label"><text x="110" y="-238">Git repo</text><text x="360" y="-238">Extract</text></g>
<g class="desc"><text x="110" y="-216">checked out</text><text x="360" y="-216">Python metrics</text></g>
<!-- Upper row: GitHub feeding PR metrics extraction -->
<g class="arrow" marker-end="url(#gm-arrowhead-full)"><path d="M178 -110 Q 230 -112 282 -110"/><path d="M428 -110 Q 524 -112 620 -110 V92"/></g>
<!-- Stage 1b: hand-sketched GitHub octocat badge -->
<g transform="translate(110 -112)" class="sketch">
<circle cx="0" cy="0" r="48"/>
<path d="M-22 6 Q-25 -10 -18 -18 L-20 -32 L-6 -24 Q0 -26 6 -24 L20 -32 L18 -18 Q25 -10 22 6 Q18 20 0 20 Q-18 20 -22 6Z"/>
<path d="M-8 20 V40 M8 20 V40 M-8 30 Q-18 32 -22 23"/>
</g>
<!-- Stage 2b: handwritten document with a pull-request glyph + tiny cog -->
<g transform="translate(360 -115)" class="sketch">
<path d="M-42 -39 H15 L40 -12 V48 Q40 54 33 54 H-42 Q-48 54 -48 47 V-32 Q-48 -39 -42 -39Z"/>
<path d="M15 -39 V-13 H40"/>
<circle cx="-26" cy="-18" r="5" fill="var(--paper)"/><circle cx="-26" cy="22" r="5" fill="var(--paper)"/><circle cx="6" cy="22" r="5" fill="var(--paper)"/>
<path d="M-26 -13 V17 M6 17 V-4 Q6 -18 -8 -18 M-3 -23 L-8 -18 L-3 -13"/>
<circle cx="34" cy="38" r="14" fill="var(--paper)"/><path d="M34 19V24 M34 52V57 M15 38H20 M48 38H53 M20 24L24 28 M44 48L48 52 M48 24L44 28 M24 48L20 52"/>
</g>
<g class="label"><text x="110" y="22">GitHub</text><text x="360" y="22">Extract</text></g>
<g class="desc"><text x="360" y="44">PR metrics</text></g>
<!-- Labels below the icons -->
<g class="label"><text x="110" y="282">Jira</text><text x="360" y="282">Extract</text><text x="620" y="282">SeaweedFS</text><text x="840" y="282">Load</text><text x="1160" y="282">PostgreSQL</text><text x="1550" y="282">Superset</text></g>
<g class="desc"><text x="360" y="304">issue metrics</text><text x="620" y="304">Parquet object</text><text x="840" y="304">into raw tables</text><text x="1160" y="304">raw landing + dbt transforms</text><text x="1550" y="304">dashboards</text></g>
<g class="smallnote"><text x="1160" y="322">dbt flow inside PostgreSQL unit</text></g>
<!-- subtle hand-drawn orchestration bracket -->
<path d="M110 332 V346 Q110 351 115 351 H1405 Q1410 351 1410 346 V332" class="light"/>
<text x="760" y="374" class="desc" style="font-size:15px">Orchestrated by Prefect · scheduled runs, retries &amp; checks</text>
</svg>
</div>
<p class="pipeline-caption">Illustrative flow · Git, GitHub &amp; Jira extraction → object storage → load → PostgreSQL + dbt → BI</p>
</div>
</div>
<script>
document.querySelectorAll('.tabs').forEach(function (tabs) {
  var buttons = tabs.querySelectorAll('[role="tab"]');
  buttons.forEach(function (button) {
    button.addEventListener('click', function () {
      buttons.forEach(function (b) {
        var active = b === button;
        b.setAttribute('aria-selected', String(active));
        document.getElementById(b.getAttribute('aria-controls')).hidden = !active;
      });
    });
  });
});
</script>
</div>


## superset BI

<div style="padding:75% 0 0 0;position:relative;margin:36px 0 40px;"><iframe src="https://player.vimeo.com/video/1244314464?badge=0&amp;autopause=0&amp;player_id=0&amp;app_id=58479&amp;muted=1&amp;loop=1" frameborder="0" allow="autoplay; fullscreen; picture-in-picture; clipboard-write; encrypted-media; web-share" referrerpolicy="strict-origin-when-cross-origin" style="position:absolute;top:0;left:0;width:100%;height:100%;" title="BI_screens"></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>



## observablity

<div style="padding:79.48% 0 0 0;position:relative;margin:36px 0 40px;"><iframe src="https://player.vimeo.com/video/1244317385?badge=0&amp;autopause=0&amp;player_id=0&amp;app_id=58479&amp;muted=1&amp;loop=1" frameborder="0" allow="autoplay; fullscreen; picture-in-picture; clipboard-write; encrypted-media; web-share" referrerpolicy="strict-origin-when-cross-origin" style="position:absolute;top:0;left:0;width:100%;height:100%;" title="Data_Freshness"></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>

## Orchestration

<div style="padding:56.25% 0 0 0;position:relative;margin:36px 0 40px;"><iframe src="https://player.vimeo.com/video/1244319262?badge=0&amp;autopause=0&amp;player_id=0&amp;app_id=58479" frameborder="0" allow="autoplay; fullscreen; picture-in-picture; clipboard-write; encrypted-media; web-share" referrerpolicy="strict-origin-when-cross-origin" style="position:absolute;top:0;left:0;width:100%;height:100%;" title="Prefect"></iframe></div><script src="https://player.vimeo.com/api/player.js"></script>

## where is it running

cloud (see Self-Hosted Data Stack project)


## release/deploy

Docker 

./release (dev box)
./smoke_test
./deploy

GHCR 
