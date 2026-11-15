# Release

## Overview

I have a dev-infrastructure deployed on my development machine. This is used by the dev and integration test environments.  I then have have prod-infrastructure deployed in the cloud, this hosts the smoke-test env and the prod env. 
The 'release process' is how I get my changes in 'dev' env all the way to 'prod' 


### release 

The `release.sh  <my_new_tag>` creates a new tagged cantidate release :

- tags the code in github with the new tag
- git buils a docker image using the code from this tag. 
- pushes the image to GHCR (Github container registory) with the tag `<my_new_tag>-rc` (rc - release canditate)

#### release.sh flow

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
.pipeline .msg,.pipeline .desc{font-size:14px;font-style:italic;fill:#80917d;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .badge circle{fill:var(--paper);stroke:var(--ink);stroke-width:1.8}
.pipeline .badge text{font:700 12px system-ui,sans-serif;fill:var(--ink);text-anchor:middle}
.pipeline .arrow{stroke:var(--ink);stroke-width:2.2;fill:none;stroke-linecap:round;stroke-linejoin:round}
.pipeline .arrow .dashed{stroke-dasharray:7 7}
.pipeline .smallnote{font-size:12px;fill:#80917d;text-anchor:middle;font-family:system-ui,sans-serif;paint-order:stroke;stroke:var(--paper);stroke-width:6px;stroke-linejoin:round}
.pipeline .pipeline-caption{text-align:center;max-width:none;margin-top:12px;font:14px system-ui,sans-serif;color:#80917d}
@media (max-width:760px){.pipeline .scroller{overflow-x:auto;padding-bottom:8px}.pipeline .scroller svg{min-width:1100px}}
</style>
<div class="scroller">
<svg viewBox="0 50 1400 370" role="img" aria-label="Flow chart of release.sh. Release: 1 run the unit and dbt integration tests, 2 merge dev into main and create an annotated tag with the release notes. Image: 3 build the image from the tag with git archive piped into docker build, 4 push it to GHCR as vX.Y.Z-rc. Any failure stops the script.">
<defs><marker id="rel-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- The script: frame around everything -->
<rect class="sketch" x="10" y="70" width="1380" height="330" rx="22"/>
<text class="name" x="700" y="115">release.sh</text>

<!-- Phases -->
<rect class="zone" x="40" y="150" width="640" height="220" rx="18"/>
<rect class="zone" x="720" y="150" width="640" height="220" rx="18"/>
<rect class="section-bg" x="53" y="164" width="92" height="26" rx="6"/>
<rect class="section-bg" x="733" y="164" width="72" height="26" rx="6"/>
<g class="zone-title"><text x="63" y="182">RELEASE</text><text x="743" y="182">IMAGE</text></g>
<g class="smallnote"><text x="360" y="350">git · main, dev and the tag are pushed to origin</text><text x="1040" y="350">built from the tagged commit only</text></g>

<!-- Calls -->
<g class="arrow" marker-end="url(#rel-arrowhead)">
<path d="M340 265 H370"/>
<path d="M640 265 H750"/>
<path d="M1020 265 H1050"/>
</g>

<!-- Steps -->
<rect class="sketch" x="80" y="210" width="260" height="110" rx="10"/>
<g class="badge"><circle cx="104" cy="234" r="11"/><text x="104" y="238">1</text></g>
<text class="name" x="210" y="258">run tests</text>
<text class="desc" x="210" y="286">unit tests · dbt integration test</text>
<rect class="sketch" x="380" y="210" width="260" height="110" rx="10"/>
<g class="badge"><circle cx="404" cy="234" r="11"/><text x="404" y="238">2</text></g>
<text class="name" x="510" y="258">merge and tag</text>
<text class="desc" x="510" y="286">dev → main · tag -a with notes</text>
<rect class="sketch" x="760" y="210" width="260" height="110" rx="10"/>
<g class="badge"><circle cx="784" cy="234" r="11"/><text x="784" y="238">3</text></g>
<text class="name" x="890" y="258">build image</text>
<text class="desc" x="890" y="286">git archive vX.Y.Z | docker build</text>
<rect class="sketch" x="1060" y="210" width="260" height="110" rx="10"/>
<g class="badge"><circle cx="1084" cy="234" r="11"/><text x="1084" y="238">4</text></g>
<text class="name" x="1190" y="258">push to GHCR</text>
<text class="desc" x="1190" y="286">metrics-artemis:vX.Y.Z-rc</text>
</svg>
</div>
<p class="pipeline-caption">release.sh step by step · any failed test or command stops the script (set -e) · the tag notes list the changes since the last tag, and "needs full_refresh" in them is picked up by deploy.sh</p>
</div>


### snoke test

The `smoke_candidate.sh <my_new_tag>`:

- takes the candidate releae container and deploys it to the 'prod infrastructure' 
- the smoke test container registers inself as a smoke test deployment.
- then the smoke test invokes prefect deployment and that triggers the pipeline on the smoke_test container 
- one the smoke test passes it retags the container from  `<my_new_tag>-rc` to `<my_new_tag>`

>The smoke test data dummy data is kept separate from the 'prod' environment data. It is intended to check if the rc container is functional and interacts correctly with prod infrastructure.

### deploy

The `deploy.sh <my_new_tag>` deployes this tagged container into the prod-env. 




