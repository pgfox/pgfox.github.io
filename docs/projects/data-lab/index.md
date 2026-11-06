# Self-Hosted Data Lab

I wanted a data stack I could reuse for my own data projects, that was fully opensource.

It is a docker compose network that wires together open source tools/frameworks that I can use on different projects. 

It provides common functionality for data projects, including:

- database
- object storage (S3 compilant)
- orchestration
- Single Sign On (SSO)
- BI tooling
- Observability (runtine dashboards, central logging)





## How it fits together

<div class="pipeline">
<style>
.pipeline{--ink:#788d79;--dark:#536b56;--paper:#fafaf2;--wash:#dce8d5;margin:30px 0;color:var(--dark)}
.pipeline svg{display:block;width:100%;height:auto;overflow:visible}
.pipeline svg path,.pipeline svg ellipse,.pipeline svg circle,.pipeline svg rect{vector-effect:non-scaling-stroke}
.pipeline .sketch{stroke:var(--ink);stroke-width:2.5;stroke-linecap:round;stroke-linejoin:round;fill:none}
.pipeline .light{stroke:#acbcaa;stroke-width:1.5;stroke-linecap:round;fill:none}
.pipeline .zone{stroke:#acbcaa;stroke-width:1.5;stroke-dasharray:6 8;fill:none}
.pipeline .zone-title{font-size:14px;letter-spacing:.12em;fill:#81917c;font-family:system-ui,sans-serif}
.pipeline .label{font-size:21px;fill:var(--dark);text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .name{font-size:16px;fill:var(--dark);text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .desc{font-size:14px;fill:#81917c;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .arrow{stroke:var(--ink);stroke-width:2.6;fill:none;stroke-linecap:round;stroke-linejoin:round}
.pipeline .smallnote{font-size:12px;fill:#80917d;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .dotted{stroke-dasharray:4 8}
.pipeline .outer{stroke:#c3cdbf;stroke-width:1.5;stroke-dasharray:2 7;stroke-linecap:round;fill:none}
.pipeline .support{opacity:.45}
.pipeline{position:relative}
.pipeline .hit{fill:transparent;cursor:help;outline:none}
.pipeline .hit:hover,.pipeline .hit:focus-visible,.pipeline .hit.is-active{stroke:#acbcaa;stroke-width:1.5;stroke-dasharray:3 5}
.pipeline .dl-tip{position:absolute;z-index:5;max-width:280px;padding:8px 12px;border:1px solid #d7dacd;border-radius:8px;background:#fff;box-shadow:0 4px 14px #536b5626;color:var(--dark);font:13px/1.45 system-ui,sans-serif;pointer-events:none;transform:translateX(-50%)}
.pipeline .dl-tip strong{display:block;margin-bottom:2px;font-family:ui-monospace,monospace;font-size:13px}
.pipeline .pipeline-caption{text-align:center;max-width:none;margin-top:12px;font:14px system-ui,sans-serif;color:#80917d}
.pipeline .pipeline-hint{text-align:center;max-width:none;margin:0 0 8px;font:italic 13px system-ui,sans-serif;color:#80917d}
@media (max-width:760px){.pipeline .scroller{overflow-x:auto;padding-bottom:8px}.pipeline .scroller svg{min-width:1090px}}
</style>
<p class="pipeline-hint">Hover over (or tap) a container for more details</p>
<div class="scroller">
<svg viewBox="165 -6 1340 782" role="img" aria-label="Hand-drawn architecture map of one Docker Compose project on one machine. Caddy routes to three groups of containers. Identity: Authelia checks logins against LLDAP. Data: SeaweedFS provides S3 storage, Prefect uses its support database prefect-db, Superset reads Postgres and its support database superset-db, and pgAdmin manages Postgres. Observability: Blackbox probes feed Prometheus, Alloy ships container logs to Loki, and Grafana reads both.">
<defs><marker id="dl-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- Outer boundary: one Compose project on one machine -->
<rect class="outer" x="175" y="4" width="1320" height="762" rx="26"/>
<text class="smallnote" x="200" y="34" style="text-anchor:start">one Docker Compose project</text>
<text class="smallnote" x="200" y="50" style="text-anchor:start">one machine</text>

<!-- Zones -->
<rect class="zone" x="420" y="20" width="1060" height="170" rx="18"/>
<rect class="zone" x="420" y="215" width="1060" height="330" rx="18"/>
<rect class="zone" x="420" y="570" width="1060" height="180" rx="18"/>
<g class="zone-title"><text x="446" y="48">IDENTITY</text><text x="446" y="243">DATA</text><text x="446" y="598">OBSERVABILITY</text></g>

<!-- Arrows -->
<g class="arrow" marker-end="url(#dl-arrowhead)">
<path class="dotted" d="M335 372 C 370 250, 430 104, 572 100"/>
<path d="M338 398 Q 380 330 416 320"/>
<path d="M300 530 C 310 625, 360 650, 416 650"/>
<path d="M665 100 Q 760 98 852 100"/>
<path d="M760 374 V 408"/>
<path d="M1045 374 L 1007 408"/><path d="M1078 374 L 1162 408"/><path d="M1282 374 L 1200 408"/>
<path d="M565 650 L 678 650"/><path d="M768 650 L 892 650"/><path d="M1115 650 L 988 650"/><path d="M1318 650 L 1205 650"/>
</g>
<g class="smallnote"><text x="336" y="212" style="text-anchor:end">forward-auth</text><text x="760" y="88">checks logins</text></g>

<!-- Caddy: shield with a pass-through arrow -->
<g transform="translate(290 400)" class="sketch">
<path d="M0 -46 L40 -31 V4 Q40 34 0 50 Q-40 34 -40 4 V-31 Z"/>
<path d="M-20 2 H20 M9 -9 L20 2 L9 13"/>
</g>
<g class="label"><text x="290" y="478">Caddy</text></g>
<g class="desc"><text x="290" y="498">caddy-proxy · HTTPS 80/443</text></g>
<g class="smallnote"><text x="290" y="516">routes each hostname to its app</text></g>

<!-- Identity -->
<g transform="translate(620 100)" class="sketch">
<circle cx="-18" cy="0" r="16"/><circle cx="-18" cy="0" r="5" class="light"/>
<path d="M-2 0 H36 M24 0 V12 M32 0 V9"/>
</g>
<g transform="translate(900 100)" class="sketch">
<path d="M-26 -32 H26 Q32 -32 32 -26 V32 H-26 Z"/>
<path d="M-34 -18 H-20 M-34 0 H-20 M-34 18 H-20"/>
<circle cx="4" cy="-8" r="9"/><path d="M-12 20 Q4 2 20 20"/>
</g>
<g class="name"><text x="620" y="152">authelia</text><text x="900" y="160">lldap</text></g>
<g class="desc"><text x="620" y="172">SSO portal</text><text x="900" y="178">user directory</text></g>

<!-- Data: seaweedfs (front) -->
<g transform="translate(520 445) scale(.62)" class="sketch">
<ellipse cx="0" cy="-31" rx="49" ry="13"/><path d="M-49 -31 V43 Q-47 56 0 56 Q47 56 49 43 V-31"/>
<path d="M-49 -4 Q-41 9 0 9 Q39 9 49 -4 M-49 22 Q-40 35 0 35 Q41 35 49 22" class="light"/>
<path d="M12 -4 Q11 -29 38 -32 Q41 -10 24 -1 Q17 3 12 -4Z" fill="var(--wash)"/><path d="M12 -4 L33 -25"/>
</g>
<g class="name"><text x="520" y="502">seaweedfs</text></g>
<g class="desc"><text x="520" y="522">S3 storage</text></g>

<!-- Data: prefect + its support db -->
<g transform="translate(760 305) scale(.7)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-24 0 L0 26 M24 0 L0 26"/>
<circle cx="-24" cy="0" r="8" fill="var(--paper)"/><circle cx="24" cy="0" r="8" fill="var(--paper)"/><circle cx="0" cy="26" r="8" fill="var(--paper)"/>
</g>
<g class="name"><text x="760" y="362">prefect</text></g>
<g class="support">
<g transform="translate(760 445) scale(.62)" class="sketch">
<ellipse cx="0" cy="-31" rx="49" ry="13"/><path d="M-49 -31 V43 Q-47 56 0 56 Q47 56 49 43 V-31"/>
<path d="M-49 -4 Q-41 9 0 9 Q39 9 49 -4 M-49 22 Q-40 35 0 35 Q41 35 49 22" class="light"/>
</g>
<g class="name"><text x="760" y="502">prefect-db</text></g>
<g class="desc"><text x="760" y="522">flows &amp; runs</text></g>
</g>

<!-- Data: superset, pgadmin, postgres (back) -->
<g transform="translate(1060 305) scale(.7)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-38 35V-5 M-38 35H42 M-31 26 L-15 5 L-1 18 L17 -12 L30 1 L41 -18"/>
</g>
<g transform="translate(1300 305) scale(.7)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-38 -12 H38 V38 H-38 Z"/><path d="M-38 5 H38 M-38 22 H38 M-10 -12 V38" class="light"/>
</g>
<g class="smallnote"><text x="1060" y="263">OIDC login</text></g>
<g class="name"><text x="1060" y="362">superset</text><text x="1300" y="362">pgadmin</text></g>
<g class="support">
<g transform="translate(1000 445) scale(.62)" class="sketch">
<ellipse cx="0" cy="-31" rx="49" ry="13"/><path d="M-49 -31 V43 Q-47 56 0 56 Q47 56 49 43 V-31"/>
<path d="M-49 -4 Q-41 9 0 9 Q39 9 49 -4 M-49 22 Q-40 35 0 35 Q41 35 49 22" class="light"/>
</g>
<g class="name"><text x="1000" y="502">superset-db</text></g>
<g class="desc"><text x="1000" y="522">metadata</text></g>
</g>
<g transform="translate(1180 445) scale(.62)" class="sketch">
<ellipse cx="0" cy="-31" rx="49" ry="13"/><path d="M-49 -31 V43 Q-47 56 0 56 Q47 56 49 43 V-31"/>
<path d="M-49 -4 Q-41 9 0 9 Q39 9 49 -4 M-49 22 Q-40 35 0 35 Q41 35 49 22" class="light"/>
</g>
<g class="name"><text x="1180" y="502">postgres</text></g>
<g class="desc"><text x="1180" y="522">appdb</text></g>

<!-- Observability -->
<g transform="translate(525 650)" class="sketch">
<path d="M-30 -15 L0 -30 L30 -15 V22 L0 37 L-30 22 Z M-30 -15 L0 0 L30 -15 M0 0 V37"/>
</g>
<g transform="translate(720 650) scale(.62)" class="sketch">
<ellipse cx="0" cy="-31" rx="49" ry="13"/><path d="M-49 -31 V43 Q-47 56 0 56 Q47 56 49 43 V-31"/>
<path d="M-36 14 L-18 14 L-9 -4 L3 32 L13 14 L36 14"/>
</g>
<g transform="translate(940 650) scale(.7)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-36 38 V14 M-22 38 V-2 M-8 38 V20 M8 34 A 19 19 0 0 1 46 34 M27 34 L37 16"/>
</g>
<g transform="translate(1160 650) scale(.62)" class="sketch">
<ellipse cx="0" cy="-31" rx="49" ry="13"/><path d="M-49 -31 V43 Q-47 56 0 56 Q47 56 49 43 V-31"/>
<path d="M-32 0 H24 M-32 16 H12 M-32 32 H28" class="light"/>
</g>
<g transform="translate(1360 650)" class="sketch">
<path d="M-36 -30 H36 L10 2 V30 L-10 38 V2 Z"/>
</g>
<g class="name"><text x="525" y="708">blackbox</text><text x="720" y="708">prometheus</text><text x="940" y="708">grafana</text><text x="1160" y="708">loki</text><text x="1360" y="708">alloy</text></g>
<g class="desc"><text x="525" y="728">probes every service</text><text x="720" y="728">health metrics</text><text x="940" y="728">dashboards · OIDC login</text><text x="1160" y="728">container logs</text><text x="1360" y="728">ships all logs</text></g>
<!-- Hover areas: one per container, text shown in the pop-up -->
<g class="hits">
<rect class="hit" x="185" y="348" width="210" height="176" rx="12" tabindex="0" data-name="caddy-proxy" data-tip="Reverse proxy. Serves HTTPS on 80/443 and sends each *-dev.pgfox.dev hostname to its app." aria-label="caddy-proxy: Reverse proxy. Serves HTTPS on 80/443 and sends each *-dev.pgfox.dev hostname to its app."/>
<rect class="hit" x="560" y="70" width="120" height="110" rx="12" tabindex="0" data-name="authelia" data-tip="SSO login portal (auth-dev.pgfox.dev). Guards the forward-auth sites (pgAdmin, Prefect, LLDAP, SeaweedFS admin) and is the OIDC provider for Grafana and Superset." aria-label="authelia: SSO login portal (auth-dev.pgfox.dev). Guards the forward-auth sites (pgAdmin, Prefect, LLDAP, SeaweedFS admin) and is the OIDC provider for Grafana and Superset."/>
<rect class="hit" x="840" y="62" width="120" height="124" rx="12" tabindex="0" data-name="lldap" data-tip="User directory (LDAP). Authelia checks logins against it." aria-label="lldap: User directory (LDAP). Authelia checks logins against it."/>
<rect class="hit" x="460" y="416" width="120" height="114" rx="12" tabindex="0" data-name="seaweedfs" data-tip="S3-compatible object storage, plus its admin UI." aria-label="seaweedfs: S3-compatible object storage, plus its admin UI."/>
<rect class="hit" x="715" y="266" width="90" height="104" rx="12" tabindex="0" data-name="prefect" data-tip="Workflow orchestration server and UI." aria-label="prefect: Workflow orchestration server and UI."/>
<rect class="hit" x="700" y="416" width="120" height="114" rx="12" tabindex="0" data-name="prefect-db" data-tip="Postgres for Prefect&#x27;s flows and runs." aria-label="prefect-db: Postgres for Prefect&#x27;s flows and runs."/>
<rect class="hit" x="1015" y="266" width="90" height="104" rx="12" tabindex="0" data-name="superset" data-tip="BI / charting app (also the lab-dev.pgfox.dev site). Charts appdb." aria-label="superset: BI / charting app (also the lab-dev.pgfox.dev site). Charts appdb."/>
<rect class="hit" x="1255" y="266" width="90" height="104" rx="12" tabindex="0" data-name="pgadmin" data-tip="Web admin UI for Postgres." aria-label="pgadmin: Web admin UI for Postgres."/>
<rect class="hit" x="940" y="416" width="120" height="114" rx="12" tabindex="0" data-name="superset-db" data-tip="Postgres for Superset&#x27;s own metadata (users, dashboards)." aria-label="superset-db: Postgres for Superset&#x27;s own metadata (users, dashboards)."/>
<rect class="hit" x="1120" y="416" width="120" height="114" rx="12" tabindex="0" data-name="postgres" data-tip="Main app database (appdb). Host port 15432." aria-label="postgres: Main app database (appdb). Host port 15432."/>
<rect class="hit" x="450" y="610" width="150" height="126" rx="12" tabindex="0" data-name="blackbox" data-tip="Probes each service&#x27;s health endpoint for Prometheus." aria-label="blackbox: Probes each service&#x27;s health endpoint for Prometheus."/>
<rect class="hit" x="660" y="610" width="120" height="126" rx="12" tabindex="0" data-name="prometheus" data-tip="Stores health-probe metrics." aria-label="prometheus: Stores health-probe metrics."/>
<rect class="hit" x="848" y="610" width="184" height="126" rx="12" tabindex="0" data-name="grafana" data-tip="Dashboards for health probes and logs." aria-label="grafana: Dashboards for health probes and logs."/>
<rect class="hit" x="1100" y="610" width="120" height="126" rx="12" tabindex="0" data-name="loki" data-tip="Stores logs collected from the containers." aria-label="loki: Stores logs collected from the containers."/>
<rect class="hit" x="1295" y="610" width="130" height="126" rx="12" tabindex="0" data-name="alloy" data-tip="Ships every container&#x27;s logs to Loki." aria-label="alloy: Ships every container&#x27;s logs to Loki."/>
</g>
</svg>
</div>
<p class="pipeline-caption">Dev stack · one Docker network · prod is the same with a <code>prod-</code> prefix</p>
<div class="dl-tip" role="tooltip" hidden><strong></strong><span></span></div>
<script>
(function () {
  var box = document.currentScript.closest('.pipeline');
  var tip = box.querySelector('.dl-tip');
  var active = null;
  function show(hit) {
    if (active) active.classList.remove('is-active');
    active = hit;
    hit.classList.add('is-active');
    tip.querySelector('strong').textContent = hit.dataset.name;
    tip.querySelector('span').textContent = hit.dataset.tip;
    tip.hidden = false;
    var b = box.getBoundingClientRect(), r = hit.getBoundingClientRect();
    var half = tip.offsetWidth / 2;
    var x = Math.min(Math.max(r.left + r.width / 2 - b.left, half), b.width - half);
    var y = r.top - b.top - tip.offsetHeight - 8;
    if (y < 0) y = r.bottom - b.top + 8;
    tip.style.left = x + 'px';
    tip.style.top = y + 'px';
  }
  function hide() {
    if (active) active.classList.remove('is-active');
    active = null;
    tip.hidden = true;
  }
  box.querySelectorAll('.hit').forEach(function (hit) {
    hit.addEventListener('pointerenter', function () { show(hit); });
    hit.addEventListener('pointerleave', function (e) { if (e.pointerType === 'mouse') hide(); });
    hit.addEventListener('focus', function () { show(hit); });
    hit.addEventListener('blur', hide);
  });
  document.addEventListener('pointerdown', function (e) {
    if (active && !e.target.closest('.hit')) hide();
  });
  document.addEventListener('keydown', function (e) { if (e.key === 'Escape') hide(); });
})();
</script>
</div>


##  Tools used 
It runs within one docker compose file on a single machine. I have a 'dev' version of the data lab running on my home network and a 'simulated prod' version that is running a VPS in the cloud (public internet)






## Limitations

I prioritised ease of use and functionality. I wanted something that is simple to run on one machine and easy to deploy. It does **not** provide any High Availablity or Data redunancy features.   

It does have some security features rolled in such as SSL, Authentication and Single Sign on. But is not production hardened. 

#




## Single sign-on

- see 
## Data tools

## Observability

## Dev vs prod

## What I'd change for production
