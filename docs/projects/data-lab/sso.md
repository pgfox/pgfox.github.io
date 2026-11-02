# Single Sign-On

 
- In the prod lab all incoming requests go through Caddy . Once you sign into one GUI you will be able to access other GUIs
- we use a combination of ForwardAuth and OIDC to secure the GUI
- Whey? Some support OIDC and other do not.
- how each work is outlined below.




### ForwardAuth 

- Caddy 'route' checks incoming request to see if they are whoe they said they are.

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
.pipeline .msg .num{font-weight:700;fill:var(--ink)}
.pipeline .arrow{stroke:var(--ink);stroke-width:2.2;fill:none;stroke-linecap:round;stroke-linejoin:round}
.pipeline .smallnote{font-size:12px;fill:#80917d;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .support{opacity:.35}
.pipeline .dotted{stroke-dasharray:4 8}
.pipeline .pipeline-caption{text-align:center;max-width:none;margin-top:12px;font:14px system-ui,sans-serif;color:#80917d}
@media (max-width:760px){.pipeline .scroller{overflow-x:auto;padding-bottom:8px}.pipeline .scroller svg{min-width:1090px}}
</style>
<div class="scroller">
<svg viewBox="80 0 1260 1245" role="img" aria-label="Sequence diagram of a first visit to pgAdmin through Authelia single sign-on. First request: the browser requests pgadmin-dev.pgfox.dev with no session cookie; Caddy asks Authelia via forward-auth; Authelia answers 401 and Caddy redirects the browser to the login page. Logging in: the browser sends username and password to Authelia; Authelia binds to LLDAP as its read-only authelia account to find the user, then binds as the user with the password; LLDAP accepts, and Authelia sets the authelia_session cookie on pgfox.dev and redirects back. Back to pgAdmin: the browser repeats the request with the cookie; Authelia answers 200 with Remote-User and Remote-Groups headers; Caddy forwards the request to pgAdmin with those headers and a shared secret, and pgAdmin shows the page logged in without its own login screen. Steps 10 to 14 repeat on every later request.">
<defs><marker id="sso-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- Participants -->
<g transform="translate(170 70) scale(.62)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<circle cx="0" cy="14" r="22"/><path d="M-22 14 H22 M0 -8 Q-13 14 0 36 Q13 14 0 -8" class="light"/>
</g>
<g transform="translate(450 70) scale(.8)" class="sketch">
<path d="M0 -46 L40 -31 V4 Q40 34 0 50 Q-40 34 -40 4 V-31 Z"/>
<path d="M-20 2 H20 M9 -9 L20 2 L9 13"/>
</g>
<g transform="translate(730 72)" class="sketch">
<circle cx="-18" cy="0" r="16"/><circle cx="-18" cy="0" r="5" class="light"/>
<path d="M-2 0 H36 M24 0 V12 M32 0 V9"/>
</g>
<g transform="translate(1000 70)" class="sketch">
<path d="M-26 -32 H26 Q32 -32 32 -26 V32 H-26 Z"/>
<path d="M-34 -18 H-20 M-34 0 H-20 M-34 18 H-20"/>
<circle cx="4" cy="-8" r="9"/><path d="M-12 20 Q4 2 20 20"/>
</g>
<g transform="translate(1250 70) scale(.62)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-38 -12 H38 V38 H-38 Z"/><path d="M-38 5 H38 M-38 22 H38 M-10 -12 V38" class="light"/>
</g>
<g class="name"><text x="170" y="130">Browser</text><text x="450" y="130">Caddy</text><text x="730" y="130">Authelia</text><text x="1000" y="130">LLDAP</text><text x="1250" y="130">pgAdmin</text></g>

<!-- Lifelines -->
<g class="lifeline"><path d="M170 145 V1195"/><path d="M450 145 V1195"/><path d="M730 145 V1195"/><path d="M1000 145 V1195"/><path d="M1250 145 V1195"/></g>

<!-- Phases -->
<rect class="zone" x="95" y="160" width="1230" height="285" rx="18"/>
<rect class="zone" x="95" y="465" width="1230" height="345" rx="18"/>
<rect class="zone" x="95" y="830" width="1230" height="365" rx="18"/>
<rect class="section-bg" x="108" y="174" width="134" height="26" rx="6"/>
<rect class="section-bg" x="108" y="479" width="110" height="26" rx="6"/>
<rect class="section-bg" x="108" y="844" width="160" height="26" rx="6"/>
<g class="zone-title"><text x="118" y="192">FIRST REQUEST</text><text x="118" y="497">LOGGING IN</text><text x="118" y="862">BACK TO PGADMIN</text></g>

<!-- Messages -->
<g class="arrow" marker-end="url(#sso-arrowhead)">
<path d="M178 235 H440"/>
<path d="M458 295 H720"/>
<path class="dotted" d="M722 355 H460"/>
<path class="dotted" d="M442 415 H180"/>
<path d="M178 540 H720"/>
<path d="M738 600 H990"/>
<path d="M738 660 H990"/>
<path class="dotted" d="M992 720 H740"/>
<path class="dotted" d="M722 780 H180"/>
<path d="M178 905 H440"/>
<path d="M458 965 H720"/>
<path class="dotted" d="M722 1035 H460"/>
<path d="M458 1105 H1240"/>
<path class="dotted" d="M1242 1165 H180"/>
</g>
<text class="msg" x="310" y="225"><tspan class="num">1</tspan>  GET pgadmin-dev.pgfox.dev</text>
<text class="msg" x="590" y="285"><tspan class="num">2</tspan>  forward-auth: allowed in?</text>
<text class="msg" x="590" y="345"><tspan class="num">3</tspan>  401 · no authelia_session cookie</text>
<text class="msg" x="310" y="405"><tspan class="num">4</tspan>  302 → login page (auth-dev)</text>
<text class="msg" x="450" y="530"><tspan class="num">5</tspan>  username + password (on auth-dev, via Caddy)</text>
<text class="msg" x="865" y="590"><tspan class="num">6</tspan>  bind as read-only authelia, find admin_user</text>
<text class="msg" x="865" y="650"><tspan class="num">7</tspan>  bind as admin_user with the password</text>
<text class="msg" x="865" y="710"><tspan class="num">8</tspan>  accepted → password is correct</text>
<text class="msg" x="450" y="770"><tspan class="num">9</tspan>  set authelia_session cookie on pgfox.dev, redirect back</text>
<text class="msg" x="310" y="895"><tspan class="num">10</tspan>  GET pgadmin-dev.pgfox.dev again</text>
<text class="msg" x="590" y="955"><tspan class="num">11</tspan>  forward-auth: allowed in?</text>
<text class="msg" x="590" y="1025"><tspan class="num">12</tspan>  200 + Remote-* headers</text>
<text class="msg" x="850" y="1095"><tspan class="num">13</tspan>  request + Remote-* headers + shared secret</text>
<text class="msg" x="710" y="1155"><tspan class="num">14</tspan>  page, logged in as admin_user · no pgAdmin login screen</text>
<g class="smallnote"><text x="310" y="255">no session cookie yet</text><text x="590" y="315">browser cookies passed along</text><text x="310" y="925">now with the session cookie</text><text x="590" y="985">cookie passed along</text><text x="590" y="1055">Remote-User: admin_user · Remote-Groups: admins</text><text x="850" y="1125">pgAdmin trusts Remote-User only from Caddy&#x27;s IP</text><text x="710" y="1228">later requests skip straight to steps 10–14 (see below)</text></g>
</svg>
</div>
<p class="pipeline-caption">Authelia forward-auth, shown for pgAdmin · Prefect, LLDAP and the S3 admin work the same way · Grafana and Superset use OIDC instead</p>
</div>


#### Sebsequent Calls (after login)
- 

<div class="pipeline">
<div class="scroller">
<svg viewBox="80 0 1260 600" role="img" aria-label="Sequence diagram of a request from a user who is already logged in. The browser requests pgadmin-dev.pgfox.dev with the authelia_session cookie; Caddy asks Authelia via forward-auth, passing the cookie; Authelia finds a valid session and answers 200 with Remote-User and Remote-Groups headers; Caddy forwards the request to pgAdmin with those headers and a shared secret; pgAdmin returns the page logged in as admin_user. LLDAP is not contacted.">
<defs><marker id="sso-arrowhead-2" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- Participants -->
<g transform="translate(170 70) scale(.62)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<circle cx="0" cy="14" r="22"/><path d="M-22 14 H22 M0 -8 Q-13 14 0 36 Q13 14 0 -8" class="light"/>
</g>
<g transform="translate(450 70) scale(.8)" class="sketch">
<path d="M0 -46 L40 -31 V4 Q40 34 0 50 Q-40 34 -40 4 V-31 Z"/>
<path d="M-20 2 H20 M9 -9 L20 2 L9 13"/>
</g>
<g transform="translate(730 72)" class="sketch">
<circle cx="-18" cy="0" r="16"/><circle cx="-18" cy="0" r="5" class="light"/>
<path d="M-2 0 H36 M24 0 V12 M32 0 V9"/>
</g>
<g transform="translate(1250 70) scale(.62)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-38 -12 H38 V38 H-38 Z"/><path d="M-38 5 H38 M-38 22 H38 M-10 -12 V38" class="light"/>
</g>
<g class="name"><text x="170" y="130">Browser</text><text x="450" y="130">Caddy</text><text x="730" y="130">Authelia</text><text x="1250" y="130">pgAdmin</text></g>

<g class="support">
<g transform="translate(1000 70)" class="sketch">
<path d="M-26 -32 H26 Q32 -32 32 -26 V32 H-26 Z"/>
<path d="M-34 -18 H-20 M-34 0 H-20 M-34 18 H-20"/>
<circle cx="4" cy="-8" r="9"/><path d="M-12 20 Q4 2 20 20"/>
</g>
<text class="name" x="1000" y="130">LLDAP</text>
</g>

<!-- Lifelines -->
<g class="lifeline"><path d="M170 145 V545"/><path d="M450 145 V545"/><path d="M730 145 V545"/><path d="M1250 145 V545"/></g>
<g class="lifeline support"><path d="M1000 145 V545"/></g>

<!-- Phase -->
<rect class="zone" x="95" y="160" width="1230" height="385" rx="18"/>
<rect class="section-bg" x="108" y="174" width="176" height="26" rx="6"/>
<g class="zone-title"><text x="118" y="192">ALREADY LOGGED IN</text></g>
<text class="smallnote" x="1000" y="290">not contacted</text>

<!-- Messages -->
<g class="arrow" marker-end="url(#sso-arrowhead-2)">
<path d="M178 235 H440"/>
<path d="M458 305 H720"/>
<path class="dotted" d="M722 375 H460"/>
<path d="M458 445 H1240"/>
<path class="dotted" d="M1242 515 H180"/>
</g>
<text class="msg" x="310" y="225"><tspan class="num">1</tspan>  GET pgadmin-dev.pgfox.dev</text>
<text class="msg" x="590" y="295"><tspan class="num">2</tspan>  forward-auth: allowed in?</text>
<text class="msg" x="590" y="365"><tspan class="num">3</tspan>  200 + Remote-* headers</text>
<text class="msg" x="850" y="435"><tspan class="num">4</tspan>  request + Remote-* headers + shared secret</text>
<text class="msg" x="710" y="505"><tspan class="num">5</tspan>  page, logged in as admin_user</text>
<g class="smallnote"><text x="310" y="255">with the authelia_session cookie</text><text x="590" y="325">cookie passed along</text><text x="590" y="395">session valid · Remote-User: admin_user · Remote-Groups: admins</text><text x="850" y="465">pgAdmin trusts Remote-User only from Caddy&#x27;s IP</text><text x="710" y="578">no login page and no LLDAP lookup: Authelia only checks the session cookie</text></g>
</svg>
</div>
<p class="pipeline-caption">Already logged in · every request after the first, while the session lasts</p>
</div>


### limitations of ForwardAuth.

- caddy need to check with Authelia for each request
- the cookie is for all servics using ForwardAuth
- 

## OIDC

## OIDC

<div class="pipeline">
<div class="scroller">
<svg viewBox="80 0 1260 1350" role="img" aria-label="Sequence diagram of an OIDC login to Grafana with Authelia. The browser requests grafana-dev.pgfox.dev and Caddy passes it straight through. Grafana redirects the browser to Authelia's authorization endpoint with client_id grafana, the openid profile email groups scopes, a state and a code challenge. If there is no authelia_session cookie, Authelia shows the login page and checks the password against LLDAP; with a cookie this is skipped, which is the single sign-on. Authelia redirects back to Grafana with a code, without a consent screen. Grafana's server exchanges the code and its client secret at Authelia's token endpoint for a signed ID token and an access token, verifies the signature, creates or updates admin_user, maps the admins group to the Admin role, and starts its own session.">
<defs><marker id="sso-arrowhead-3" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>

<!-- Participants -->
<g transform="translate(170 70) scale(.62)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<circle cx="0" cy="14" r="22"/><path d="M-22 14 H22 M0 -8 Q-13 14 0 36 Q13 14 0 -8" class="light"/>
</g>
<g transform="translate(420 70) scale(.8)" class="sketch">
<path d="M0 -46 L40 -31 V4 Q40 34 0 50 Q-40 34 -40 4 V-31 Z"/>
<path d="M-20 2 H20 M9 -9 L20 2 L9 13"/>
</g>
<g transform="translate(680 70) scale(.62)" class="sketch">
<path d="M-55 -39 Q-55 -45 -48 -45 H48 Q55 -45 55 -38 V46 Q55 53 48 53 H-48 Q-55 53 -55 46Z"/>
<path d="M-55 -25 H55" class="light"/><circle cx="-42" cy="-35" r="3"/><circle cx="-31" cy="-35" r="3"/>
<path d="M-36 38 V14 M-22 38 V-2 M-8 38 V20 M8 34 A 19 19 0 0 1 46 34 M27 34 L37 16"/>
</g>
<g transform="translate(960 72)" class="sketch">
<circle cx="-18" cy="0" r="16"/><circle cx="-18" cy="0" r="5" class="light"/>
<path d="M-2 0 H36 M24 0 V12 M32 0 V9"/>
</g>
<g transform="translate(1230 70)" class="sketch">
<path d="M-26 -32 H26 Q32 -32 32 -26 V32 H-26 Z"/>
<path d="M-34 -18 H-20 M-34 0 H-20 M-34 18 H-20"/>
<circle cx="4" cy="-8" r="9"/><path d="M-12 20 Q4 2 20 20"/>
</g>
<g class="name"><text x="170" y="130">Browser</text><text x="420" y="130">Caddy</text><text x="680" y="130">Grafana</text><text x="960" y="130">Authelia</text><text x="1230" y="130">LLDAP</text></g>

<!-- Lifelines -->
<g class="lifeline"><path d="M170 145 V1300"/><path d="M420 145 V1300"/><path d="M680 145 V1300"/><path d="M960 145 V1300"/><path d="M1230 145 V1300"/></g>

<!-- Phases -->
<rect class="zone" x="95" y="160" width="1230" height="240" rx="18"/>
<rect class="zone" x="95" y="420" width="1230" height="465" rx="18"/>
<rect class="zone" x="95" y="905" width="1230" height="395" rx="18"/>
<rect class="section-bg" x="108" y="174" width="134" height="26" rx="6"/>
<rect class="section-bg" x="108" y="434" width="124" height="26" rx="6"/>
<rect class="section-bg" x="108" y="919" width="160" height="26" rx="6"/>
<g class="zone-title"><text x="118" y="192">OPEN GRAFANA</text><text x="118" y="452">AT AUTHELIA</text><text x="118" y="937">BACK AT GRAFANA</text></g>

<!-- opt: only without an Authelia session -->
<rect class="frame" x="125" y="530" width="1170" height="275" rx="10"/>
<rect class="section-bg" x="132" y="537" width="560" height="22" rx="5"/>
<g class="guard"><text x="140" y="553">[no authelia_session cookie] · with one, all of this is skipped: that is the single sign-on</text></g>

<!-- Messages -->
<g class="arrow" marker-end="url(#sso-arrowhead-3)">
<path d="M178 235 H410"/>
<path d="M428 295 H670"/>
<path class="dotted" d="M672 365 H180"/>
<path d="M178 495 H950"/>
<path class="dotted" d="M952 600 H180"/>
<path d="M178 660 H950"/>
<path d="M968 720 H1220"/>
<path class="dotted" d="M1222 780 H970"/>
<path class="dotted" d="M952 855 H180"/>
<path d="M178 975 H670"/>
<path d="M688 1045 H950"/>
<path class="dotted" d="M952 1115 H690"/>
<path d="M688 1172 H740 V1202 H692"/>
<path class="dotted" d="M672 1270 H180"/>
</g>
<text class="msg" x="295" y="225"><tspan class="num">1</tspan>  GET grafana-dev.pgfox.dev</text>
<text class="msg" x="550" y="285"><tspan class="num">2</tspan>  passed straight through</text>
<text class="msg" x="425" y="355"><tspan class="num">3</tspan>  302 → auth-dev …/api/oidc/authorization</text>
<text class="msg" x="565" y="485"><tspan class="num">4</tspan>  GET /api/oidc/authorization</text>
<text class="msg" x="565" y="590"><tspan class="num">5</tspan>  login page</text>
<text class="msg" x="565" y="650"><tspan class="num">6</tspan>  username + password</text>
<text class="msg" x="1095" y="710"><tspan class="num">7</tspan>  check password</text>
<text class="msg" x="1095" y="770"><tspan class="num">8</tspan>  accepted</text>
<text class="msg" x="565" y="845"><tspan class="num">9</tspan>  302 → grafana-dev …/login/generic_oauth?code=…&amp;state=…</text>
<text class="msg" x="425" y="965"><tspan class="num">10</tspan>  GET /login/generic_oauth?code=…</text>
<text class="msg" x="820" y="1035"><tspan class="num">11</tspan>  POST /api/oidc/token: code + client_secret</text>
<text class="msg" x="820" y="1105"><tspan class="num">12</tspan>  ID token + access token</text>
<text class="msg" x="754" y="1192" style="text-anchor:start"><tspan class="num">13</tspan>  verify signature, create / update admin_user</text>
<text class="msg" x="425" y="1260"><tspan class="num">14</tspan>  set Grafana session cookie · page, logged in as admin_user</text>
<g class="smallnote"><text x="550" y="315">no forward-auth on this site</text><text x="425" y="385">client_id=grafana · scope=openid profile email groups · state · code_challenge</text><text x="565" y="515">via Caddy</text><text x="1095" y="740">same LLDAP binds as forward-auth</text><text x="565" y="875">no consent screen: consent_mode is implicit</text><text x="425" y="995">via Caddy</text><text x="820" y="1065">server to server, not through the browser (via Caddy)</text><text x="820" y="1135">ID token: signed admin_user, email, name, groups · access token: for /api/oidc/userinfo</text><text x="754" y="1212" style="text-anchor:start">groups contain admins → Admin role, otherwise Viewer</text><text x="710" y="1333">from now on Grafana uses its own session and doesn't ask Authelia again until that session ends</text></g>
</svg>
</div>
<p class="pipeline-caption">Authelia as OIDC provider, shown for Grafana · Superset works the same way</p>
</div>
