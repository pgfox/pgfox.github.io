# Git Metrics Pipeline

A test page for the Git metrics project: an automated pipeline that takes a checked-out repository all the way to an analytics dashboard. The animation follows one document as it pauses inside each step.

<div class="tabs">
<div class="tab-list" role="tablist" aria-label="Pipeline diagrams">
<button type="button" class="tab" role="tab" id="gm-tab-main" aria-controls="gm-panel-main" aria-selected="true">Main flow</button>
<button type="button" class="tab" role="tab" id="gm-tab-full" aria-controls="gm-panel-full" aria-selected="false">Full Flow</button>
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
.pipeline .item{filter:drop-shadow(0 2px 1px #adbfa03d)}
.pipeline .travel{animation:gm-file-step 19s ease-in-out infinite;transform:translate(110px,190px);opacity:0}
@keyframes gm-file-step{
  0%,4%   {transform:translate(110px,190px);opacity:0}
  6%      {transform:translate(172px,190px);opacity:1}
  20%     {transform:translate(360px,190px);opacity:1}
  22%,27% {transform:translate(360px,190px);opacity:0}
  29%     {transform:translate(426px,190px);opacity:1}
  41%     {transform:translate(620px,190px);opacity:1}
  43%,48% {transform:translate(620px,190px);opacity:0}
  50%     {transform:translate(686px,190px);opacity:1}
  62%     {transform:translate(792px,190px);opacity:1}
  63%,100%{transform:translate(810px,190px);opacity:0}
}
.pipeline .stage-ring{stroke:var(--accent);stroke-width:2;stroke-dasharray:8 10;fill:none;opacity:0;transform-box:fill-box;transform-origin:center;animation:gm-highlight 20s linear infinite}
.pipeline .r2{animation-delay:4s}.pipeline .r3{animation-delay:8s}.pipeline .r4{animation-delay:12s}.pipeline .r5{animation-delay:16s}
@keyframes gm-highlight{0%,100%{opacity:0;transform:scale(.96)}8%,24%{opacity:0;transform:scale(.96)}14%{opacity:.55;transform:scale(1.04)}}
.pipeline .pipeline-caption{text-align:center;max-width:none;margin-top:12px;font:14px system-ui,sans-serif;color:#80917d}
@media (max-width:760px){.pipeline .scroller{overflow-x:auto;padding-bottom:8px}.pipeline .scroller svg{min-width:1090px}}
.pipeline .pipeline-controls{display:flex;justify-content:flex-end;margin-bottom:8px}
.pipeline .pipeline-toggle{cursor:pointer;border:1px solid #d7dacd;background:#fff;border-radius:999px;color:#456b14;font:600 13px var(--sans);padding:7px 16px}
.pipeline .pipeline-toggle:hover{background:var(--tint)}
.pipeline .pipeline-toggle:focus-visible{outline:2px solid var(--accent);outline-offset:2px}
.pipeline.is-paused .travel,.pipeline.is-paused .stage-ring{animation-play-state:paused}
@media (prefers-reduced-motion:reduce){.pipeline .travel{animation-delay:-10s}.pipeline:not(.is-playing) .travel,.pipeline:not(.is-playing) .stage-ring{animation-play-state:paused}}
</style>
<div class="pipeline-controls"><button type="button" class="pipeline-toggle" hidden>Pause animation</button></div>
<div class="scroller">
<svg viewBox="0 0 1500 390" role="img" aria-label="Animated hand-drawn pipeline: Git repo to Python extraction, SeaweedFS, PostgreSQL raw and dbt transformations, and Apache Superset">
<defs><marker id="gm-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>
<!-- Straight loose-pencil arrows through the gaps -->
<g class="arrow" marker-end="url(#gm-arrowhead)">
<path d="M178 190 Q 230 188 282 190"/><path d="M428 190 Q 477 192 526 190"/><path d="M668 190 Q 760 188 820 190"/><path class="dotted" d="M1108 190 Q 1190 191 1271 190"/>
</g>
<!-- Stage 1: hand-sketched open Git folder + branching -->
<g transform="translate(110 147)" class="sketch">
<path d="M-54 -23 Q-54 -29 -46 -29 H-13 L-5 -20 H48 Q54 -20 54 -13 L50 50 Q48 55 39 55 H-47 Q-54 55 -54 46 Z" />
<path d="M-54 -17 Q-3 -19 54 -13" class="light"/>
<path d="M-24 18 L-2 0 L24 22 M-2 0 L-2 37"/>
<circle cx="-24" cy="18" r="5" fill="var(--paper)"/><circle cx="-2" cy="0" r="5" fill="var(--paper)"/><circle cx="24" cy="22" r="5" fill="var(--paper)"/><circle cx="-2" cy="37" r="5" fill="var(--paper)"/>
</g>
<!-- Stage 2: handwritten Python document + tiny cog -->
<g transform="translate(360 145)" class="sketch">
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
<!-- Stage 4: wide PostgreSQL unit with the dbt flow inside -->
<g transform="translate(960 148)" class="sketch">
<ellipse cx="0" cy="-38" rx="150" ry="16"/><path d="M-150 -38 V52 Q-146 72 0 72 Q146 72 150 52 V-38"/>
<path d="M-150 -7 Q-126 10 0 10 Q124 10 150 -7 M-150 21 Q-124 38 0 38 Q126 38 150 21" class="light"/>
<g transform="translate(30 -4)">
<path d="M-80 -33 H-35 V-8 H-80 Z M-20 4 H25 V29 H-20 Z M-80 39 H-35 V64 H-80 Z"/>
<path d="M-35 -20 Q-1 -20 2 3 M-8 -1 L2 4 L7 -6 M-20 18 Q-54 18 -57 39 M-48 34 L-57 39 L-63 30"/>
<text x="-57" y="-23" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">raw</text>
<text x="2" y="21" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">stage</text>
<text x="-57" y="56" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">core</text>
</g>
</g>
<!-- Stage 5: hand-drawn dashboard and chart -->
<g transform="translate(1350 148)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-38 35V-5 M-38 35H42 M-31 26 L-15 5 L-1 18 L17 -12 L30 1 L41 -18"/>
</g>
<!-- Labels below the icons -->
<g class="label"><text x="110" y="282">Git repo</text><text x="360" y="282">Extract</text><text x="620" y="282">SeaweedFS</text><text x="960" y="282">PostgreSQL</text><text x="1350" y="282">Superset</text></g>
<g class="desc"><text x="110" y="304">checked out</text><text x="360" y="304">Python metrics</text><text x="620" y="304">Parquet object</text><text x="960" y="304">raw landing + dbt transforms</text><text x="1350" y="304">dashboards</text></g>
<g class="smallnote"><text x="960" y="322">dbt flow inside PostgreSQL unit</text></g>
<!-- subtle hand-drawn orchestration bracket -->
<path d="M110 332 V346 Q110 351 115 351 H1205 Q1210 351 1210 346 V332" class="light"/>
<text x="660" y="374" class="desc" style="font-size:15px">Orchestrated by Prefect · scheduled runs, retries &amp; checks</text>
<!-- Travelling document: simple hand-drawn file, no solid block behind arrows -->
<g class="travel item">
<path d="M-13 -19 H4 L14 -9 V19 H-13 Q-17 19 -17 15 V-15 Q-17 -19 -13 -19Z" transform="translate(6 -6)" fill="var(--paper)" stroke="var(--accent)" stroke-width="2" stroke-linejoin="round" opacity=".45"/>
<path d="M-13 -19 H4 L14 -9 V19 H-13 Q-17 19 -17 15 V-15 Q-17 -19 -13 -19Z" fill="var(--paper)" stroke="var(--accent)" stroke-width="2" stroke-linejoin="round"/>
<path d="M4 -19V-9H14 M-9 -1H7 M-9 6H7 M-9 13H2" stroke="var(--accent)" stroke-width="1.5" stroke-linecap="round" fill="none"/>
</g>
</svg>
</div>
<p class="pipeline-caption">Illustrative flow · Git extraction → object storage → PostgreSQL + dbt → BI</p>
<script>
(function () {
  var pipeline = document.currentScript.closest('.pipeline');
  var button = pipeline.querySelector('.pipeline-toggle');
  var reduced = window.matchMedia('(prefers-reduced-motion: reduce)');
  function isPlaying() {
    if (pipeline.classList.contains('is-playing')) return true;
    if (pipeline.classList.contains('is-paused')) return false;
    return !reduced.matches;
  }
  function render() {
    var playing = isPlaying();
    button.textContent = playing ? 'Pause animation' : 'Play animation';
    button.setAttribute('aria-pressed', String(!playing));
  }
  button.addEventListener('click', function () {
    var playing = !isPlaying();
    pipeline.classList.toggle('is-playing', playing);
    pipeline.classList.toggle('is-paused', !playing);
    render();
  });
  reduced.addEventListener('change', render);
  button.hidden = false;
  render();
})();
</script>
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
.pipeline .item{filter:drop-shadow(0 2px 1px #adbfa03d)}
.pipeline .travel{animation:gm-file-step 19s ease-in-out infinite;transform:translate(110px,190px);opacity:0}
@keyframes gm-file-step{
  0%,4%   {transform:translate(110px,190px);opacity:0}
  6%      {transform:translate(172px,190px);opacity:1}
  20%     {transform:translate(360px,190px);opacity:1}
  22%,27% {transform:translate(360px,190px);opacity:0}
  29%     {transform:translate(426px,190px);opacity:1}
  41%     {transform:translate(620px,190px);opacity:1}
  43%,48% {transform:translate(620px,190px);opacity:0}
  50%     {transform:translate(686px,190px);opacity:1}
  62%     {transform:translate(792px,190px);opacity:1}
  63%,100%{transform:translate(810px,190px);opacity:0}
}
.pipeline .stage-ring{stroke:var(--accent);stroke-width:2;stroke-dasharray:8 10;fill:none;opacity:0;transform-box:fill-box;transform-origin:center;animation:gm-highlight 20s linear infinite}
.pipeline .r2{animation-delay:4s}.pipeline .r3{animation-delay:8s}.pipeline .r4{animation-delay:12s}.pipeline .r5{animation-delay:16s}
@keyframes gm-highlight{0%,100%{opacity:0;transform:scale(.96)}8%,24%{opacity:0;transform:scale(.96)}14%{opacity:.55;transform:scale(1.04)}}
.pipeline .pipeline-caption{text-align:center;max-width:none;margin-top:12px;font:14px system-ui,sans-serif;color:#80917d}
@media (max-width:760px){.pipeline .scroller{overflow-x:auto;padding-bottom:8px}.pipeline .scroller svg{min-width:1090px}}
.pipeline .pipeline-controls{display:flex;justify-content:flex-end;margin-bottom:8px}
.pipeline .pipeline-toggle{cursor:pointer;border:1px solid #d7dacd;background:#fff;border-radius:999px;color:#456b14;font:600 13px var(--sans);padding:7px 16px}
.pipeline .pipeline-toggle:hover{background:var(--tint)}
.pipeline .pipeline-toggle:focus-visible{outline:2px solid var(--accent);outline-offset:2px}
.pipeline.is-paused .travel,.pipeline.is-paused .stage-ring{animation-play-state:paused}
@media (prefers-reduced-motion:reduce){.pipeline .travel{animation-delay:-10s}.pipeline:not(.is-playing) .travel,.pipeline:not(.is-playing) .stage-ring{animation-play-state:paused}}
</style>
<div class="pipeline-controls"><button type="button" class="pipeline-toggle" hidden>Pause animation</button></div>
<div class="scroller">
<svg viewBox="0 -180 1500 570" role="img" aria-label="Animated hand-drawn pipeline: GitHub to PR metrics extraction above Git repo to Python extraction, SeaweedFS, PostgreSQL raw and dbt transformations, and Apache Superset">
<defs><marker id="gm-arrowhead-full" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>
<!-- Straight loose-pencil arrows through the gaps -->
<g class="arrow" marker-end="url(#gm-arrowhead-full)">
<path d="M178 190 Q 230 188 282 190"/><path d="M428 190 Q 477 192 526 190"/><path d="M668 190 Q 760 188 820 190"/><path class="dotted" d="M1108 190 Q 1190 191 1271 190"/>
</g>
<!-- Stage 1: hand-sketched open Git folder + branching -->
<g transform="translate(110 147)" class="sketch">
<path d="M-54 -23 Q-54 -29 -46 -29 H-13 L-5 -20 H48 Q54 -20 54 -13 L50 50 Q48 55 39 55 H-47 Q-54 55 -54 46 Z" />
<path d="M-54 -17 Q-3 -19 54 -13" class="light"/>
<path d="M-24 18 L-2 0 L24 22 M-2 0 L-2 37"/>
<circle cx="-24" cy="18" r="5" fill="var(--paper)"/><circle cx="-2" cy="0" r="5" fill="var(--paper)"/><circle cx="24" cy="22" r="5" fill="var(--paper)"/><circle cx="-2" cy="37" r="5" fill="var(--paper)"/>
</g>
<!-- Stage 2: handwritten Python document + tiny cog -->
<g transform="translate(360 145)" class="sketch">
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
<!-- Stage 4: wide PostgreSQL unit with the dbt flow inside -->
<g transform="translate(960 148)" class="sketch">
<ellipse cx="0" cy="-38" rx="150" ry="16"/><path d="M-150 -38 V52 Q-146 72 0 72 Q146 72 150 52 V-38"/>
<path d="M-150 -7 Q-126 10 0 10 Q124 10 150 -7 M-150 21 Q-124 38 0 38 Q126 38 150 21" class="light"/>
<g transform="translate(30 -4)">
<path d="M-80 -33 H-35 V-8 H-80 Z M-20 4 H25 V29 H-20 Z M-80 39 H-35 V64 H-80 Z"/>
<path d="M-35 -20 Q-1 -20 2 3 M-8 -1 L2 4 L7 -6 M-20 18 Q-54 18 -57 39 M-48 34 L-57 39 L-63 30"/>
<text x="-57" y="-23" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">raw</text>
<text x="2" y="21" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">stage</text>
<text x="-57" y="56" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">core</text>
</g>
</g>
<!-- Stage 5: hand-drawn dashboard and chart -->
<g transform="translate(1350 148)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-38 35V-5 M-38 35H42 M-31 26 L-15 5 L-1 18 L17 -12 L30 1 L41 -18"/>
</g>
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
<g class="label"><text x="110" y="282">Git repo</text><text x="360" y="282">Extract</text><text x="620" y="282">SeaweedFS</text><text x="960" y="282">PostgreSQL</text><text x="1350" y="282">Superset</text></g>
<g class="desc"><text x="110" y="304">checked out</text><text x="360" y="304">Python metrics</text><text x="620" y="304">Parquet object</text><text x="960" y="304">raw landing + dbt transforms</text><text x="1350" y="304">dashboards</text></g>
<g class="smallnote"><text x="960" y="322">dbt flow inside PostgreSQL unit</text></g>
<!-- subtle hand-drawn orchestration bracket -->
<path d="M110 332 V346 Q110 351 115 351 H1205 Q1210 351 1210 346 V332" class="light"/>
<text x="660" y="374" class="desc" style="font-size:15px">Orchestrated by Prefect · scheduled runs, retries &amp; checks</text>
<!-- Travelling document: simple hand-drawn file, no solid block behind arrows -->
<g class="travel item">
<path d="M-13 -19 H4 L14 -9 V19 H-13 Q-17 19 -17 15 V-15 Q-17 -19 -13 -19Z" transform="translate(6 -6)" fill="var(--paper)" stroke="var(--accent)" stroke-width="2" stroke-linejoin="round" opacity=".45"/>
<path d="M-13 -19 H4 L14 -9 V19 H-13 Q-17 19 -17 15 V-15 Q-17 -19 -13 -19Z" fill="var(--paper)" stroke="var(--accent)" stroke-width="2" stroke-linejoin="round"/>
<path d="M4 -19V-9H14 M-9 -1H7 M-9 6H7 M-9 13H2" stroke="var(--accent)" stroke-width="1.5" stroke-linecap="round" fill="none"/>
</g>
</svg>
</div>
<p class="pipeline-caption">Illustrative flow · Git extraction → object storage → PostgreSQL + dbt → BI</p>
<script>
(function () {
  var pipeline = document.currentScript.closest('.pipeline');
  var button = pipeline.querySelector('.pipeline-toggle');
  var reduced = window.matchMedia('(prefers-reduced-motion: reduce)');
  function isPlaying() {
    if (pipeline.classList.contains('is-playing')) return true;
    if (pipeline.classList.contains('is-paused')) return false;
    return !reduced.matches;
  }
  function render() {
    var playing = isPlaying();
    button.textContent = playing ? 'Pause animation' : 'Play animation';
    button.setAttribute('aria-pressed', String(!playing));
  }
  button.addEventListener('click', function () {
    var playing = !isPlaying();
    pipeline.classList.toggle('is-playing', playing);
    pipeline.classList.toggle('is-paused', !playing);
    render();
  });
  reduced.addEventListener('change', render);
  button.hidden = false;
  render();
})();
</script>
</div>
</div>
</div>
