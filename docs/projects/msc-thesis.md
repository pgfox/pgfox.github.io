# Text classification with Vector Embeddings

As part of my MSc in Applied Information and Data Science (HSLU), I worked with IMPACT initiatives on text classification. The constraint was that it had to run locally on a consumer-grade laptop with limited resources (standard office machine). 

From a technical perspective, the goal was to understand which embedding spaces are more effective when applied to IMPACT's data.

<div class="pipeline">
<style>
.pipeline{--ink:#788d79;--dark:#536b56;--paper:#fafaf2;--wash:#dce8d5;margin:30px 0;color:var(--dark)}
.pipeline svg{display:block;width:100%;height:auto;overflow:visible}
.pipeline .sketch{stroke:var(--ink);stroke-width:2.5;stroke-linecap:round;stroke-linejoin:round;fill:none}
.pipeline .light{stroke:#acbcaa;stroke-width:1.5;stroke-linecap:round;fill:none}
.pipeline .label{font-size:21px;fill:var(--dark);text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .desc{font-size:14px;fill:#81917c;text-anchor:middle;font-family:system-ui,sans-serif}
.pipeline .arrow{stroke:var(--ink);stroke-width:2.6;fill:none;stroke-linecap:round;stroke-linejoin:round}
.pipeline .vector{font-size:22px;fill:var(--dark);text-anchor:middle;font-family:ui-monospace,SFMono-Regular,Menlo,monospace}
.pipeline .dotted{stroke-dasharray:4 8}
.pipeline .pipeline-caption{text-align:center;max-width:none;margin-top:12px;font:14px system-ui,sans-serif;color:#80917d}
@media (max-width:760px){.pipeline .scroller{overflow-x:auto;padding-bottom:8px}.pipeline .scroller svg{min-width:1090px}}
</style>
<div class="scroller">
<svg viewBox="0 0 1240 340" role="img" aria-label="Hand-drawn flow: text goes into an embedding model, which produces a vector such as [1.0, 0.3, 1.6, …], which feeds a classifier that picks two of five labels; the embedding model and classifier sit inside one box">
<defs><marker id="mt-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>
<!-- Hand-drawn box around the embedding model and classifier -->
<path d="M318 58 Q318 44 332 44 H1158 Q1172 45 1172 58 V306 Q1171 320 1157 320 H333 Q319 319 318 306 Z" class="light dotted"/>
<!-- Straight loose-pencil arrows through the gaps -->
<g class="arrow" marker-end="url(#mt-arrowhead)">
<path d="M178 160 Q 262 158 346 160"/><path d="M494 160 Q 560 162 626 160"/><path d="M866 160 Q 928 158 990 160"/>
</g>
<!-- Stage 1: handwritten text document -->
<g transform="translate(110 152)" class="sketch">
<path d="M-42 -39 H15 L40 -12 V48 Q40 54 33 54 H-42 Q-48 54 -48 47 V-32 Q-48 -39 -42 -39Z"/>
<path d="M15 -39 V-13 H40"/>
<path d="M-32 -18 H2 M-32 -4 H26 M-32 10 H22 M-32 24 H26 M-32 38 H4" class="light"/>
</g>
<!-- Stage 2: embedding model as a small sketched neural network -->
<g transform="translate(420 160)" class="sketch">
<path d="M-60 -50 Q-60 -56 -53 -56 H53 Q60 -56 60 -49 V49 Q60 56 53 56 H-53 Q-60 56 -60 49Z"/>
<path d="M-34 -24 L0 -36 M-34 -24 L0 -12 M-34 -24 L0 12 M-34 0 L0 -36 M-34 0 L0 -12 M-34 0 L0 12 M-34 0 L0 36 M-34 24 L0 12 M-34 24 L0 36 M-34 24 L0 -12 M0 -36 L34 -12 M0 -12 L34 -12 M0 -12 L34 12 M0 12 L34 -12 M0 12 L34 12 M0 36 L34 12" class="light"/>
<circle cx="-34" cy="-24" r="6" fill="var(--paper)"/><circle cx="-34" cy="0" r="6" fill="var(--paper)"/><circle cx="-34" cy="24" r="6" fill="var(--paper)"/>
<circle cx="0" cy="-36" r="6" fill="var(--paper)"/><circle cx="0" cy="-12" r="6" fill="var(--paper)"/><circle cx="0" cy="12" r="6" fill="var(--paper)"/><circle cx="0" cy="36" r="6" fill="var(--paper)"/>
<circle cx="34" cy="-12" r="6" fill="var(--wash)"/><circle cx="34" cy="12" r="6" fill="var(--wash)"/>
</g>
<!-- Stage 3: the embedding vector, with sketched brackets -->
<g transform="translate(746 160)">
<path d="M-102 -28 Q-110 -28 -110 -20 V20 Q-110 28 -102 28 M102 -28 Q110 -28 110 -20 V20 Q110 28 102 28" class="sketch"/>
<text x="0" y="8" class="vector" textLength="180" lengthAdjust="spacingAndGlyphs">1.0, 0.3, 1.6, …</text>
</g>
<!-- Stage 4: classifier splitting into five labels, two chosen -->
<g transform="translate(1070 160)" class="sketch">
<circle cx="-50" cy="0" r="7" fill="var(--paper)"/>
<path d="M-43 0 Q-20 0 -8 -56 M-43 0 Q-20 0 -8 -28 M-43 0 H-8 M-43 0 Q-20 0 -8 28 M-43 0 Q-20 0 -8 56" class="light"/>
<path d="M-8 -66 H40 Q46 -66 46 -61 V-51 Q46 -46 40 -46 H-8 Z"/>
<path d="M-8 -38 H40 Q46 -38 46 -33 V-23 Q46 -18 40 -18 H-8 Z" fill="var(--wash)"/>
<path d="M-8 -10 H40 Q46 -10 46 -5 V5 Q46 10 40 10 H-8 Z"/>
<path d="M-8 18 H40 Q46 18 46 23 V33 Q46 38 40 38 H-8 Z" fill="var(--wash)"/>
<path d="M-8 46 H40 Q46 46 46 51 V61 Q46 66 40 66 H-8 Z"/>
<path d="M9 -28 L15 -23 L28 -33 M9 28 L15 33 L28 23"/>
</g>
<!-- Labels below the icons -->
<g class="label"><text x="110" y="250">Text</text><text x="420" y="250">Embedding model</text><text x="746" y="250">Embedding</text><text x="1070" y="250">Classifier</text></g>
<g class="desc"><text x="110" y="272">raw input</text><text x="420" y="272">text → vector</text><text x="746" y="272">dense vector</text><text x="1070" y="272">predicted labels</text></g>
</svg>
</div>
<p class="pipeline-caption">Illustrative flow · text → embedding model → vector → classifier</p>
</div>

<div class="videos">
<style>
.videos{display:flex;gap:24px;align-items:flex-start;max-width:860px;margin:30px auto}
/* Widths are inversely proportional to each video's height ratio (99.78% vs 113.92%), so both render at the same height. */
.videos .video{min-width:0}
.videos .video:nth-of-type(1){flex:100.22 1 0}
.videos .video:nth-of-type(2){flex:87.78 1 0}
@media (max-width:760px){.videos{flex-direction:column;align-items:stretch}.videos .video{flex:none!important}}
</style>
<div class="video"><div style="padding:99.78% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/1238311530?badge=0&amp;autopause=0&amp;player_id=0&amp;app_id=58479&amp;loop=1&amp;autoplay=1&amp;muted=1" frameborder="0" allow="autoplay; fullscreen; picture-in-picture; clipboard-write; encrypted-media; web-share" referrerpolicy="strict-origin-when-cross-origin" style="position:absolute;top:0;left:0;width:100%;height:100%;" title="Projection of Pretrained BERT base embeddings"></iframe></div></div>
<div class="video"><div style="padding:113.92% 0 0 0;position:relative;"><iframe src="https://player.vimeo.com/video/1238314136?badge=0&amp;autopause=0&amp;player_id=0&amp;app_id=58479&amp;loop=1&amp;autoplay=1&amp;muted=1" frameborder="0" allow="autoplay; fullscreen; picture-in-picture; clipboard-write; encrypted-media; web-share" referrerpolicy="strict-origin-when-cross-origin" style="position:absolute;top:0;left:0;width:100%;height:100%;" title="SetFit_t2"></iframe></div></div>
</div>
<script src="https://player.vimeo.com/api/player.js"></script>
