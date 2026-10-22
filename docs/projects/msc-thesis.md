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

<div class="pipeline">
<div class="scroller">
<svg viewBox="0 0 1340 430" role="img" aria-label="Hand-drawn labelling flow: discussion group transcripts in Word are parsed into text chunk CSVs, and the saturation grid in Excel is parsed into a CSV; both are combined into a prompt for Llama 3.1 8B running in Ollama, whose response gives text chunks with suggested labels, which are manually reviewed into curated data">
<defs><marker id="lb-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>
<!-- Loose-pencil arrows: two parse steps, the merge into the prompt, the LLM response, and the manual review -->
<g class="arrow" marker-end="url(#lb-arrowhead)">
<path d="M170 100 Q 245 98 318 100"/><path d="M170 310 Q 245 312 318 310"/>
<path d="M442 100 C 540 100 540 190 612 192"/><path d="M442 310 C 540 310 540 220 612 218"/>
<path d="M748 205 Q 823 203 898 205"/><path d="M1022 205 Q 1095 207 1168 205"/>
</g>
<g class="desc"><text x="245" y="84">parse transcripts</text><text x="245" y="294">parse saturation grid</text><text x="505" y="198">combine text + potential</text><text x="505" y="216">labels into prompt</text><text x="823" y="189">response from LLM</text><text x="1095" y="172">manually review text</text><text x="1095" y="189">+ suggested labels</text></g>
<!-- Stage 1a: stack of discussion group transcripts (Word docs) -->
<g transform="translate(115 105)" class="sketch">
<path d="M-40 -50 H2 L22 -30 V26 Q22 30 18 30 H-40 Q-44 30 -44 26 V-46 Q-44 -50 -40 -50Z" fill="var(--paper)"/>
<path d="M-35 -45 H7 L27 -25 V31 Q27 35 23 35 H-35 Q-39 35 -39 31 V-41 Q-39 -45 -35 -45Z" fill="var(--paper)"/>
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z" fill="var(--paper)"/>
<path d="M12 -40 V-20 H32"/>
<path d="M-24 -22 H4 M-24 -8 H22 M-24 6 H18 M-24 20 H22 M-24 32 H2" class="light"/>
</g>
<!-- Stage 1b: saturation grid spreadsheet (Excel) -->
<g transform="translate(110 310)" class="sketch">
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z"/>
<path d="M12 -40 V-20 H32"/>
<path d="M-24 -14 H22 V-2 H-24 Z" fill="var(--wash)"/>
<path d="M-24 -14 V30 H22 V-2 M-24 10 H22 M-24 20 H22 M-8 -14 V30 M8 -14 V30" class="light"/>
</g>
<!-- Stage 2a: stack of text chunk CSVs -->
<g transform="translate(385 105)" class="sketch">
<path d="M-40 -50 H2 L22 -30 V26 Q22 30 18 30 H-40 Q-44 30 -44 26 V-46 Q-44 -50 -40 -50Z" fill="var(--paper)"/>
<path d="M-35 -45 H7 L27 -25 V31 Q27 35 23 35 H-35 Q-39 35 -39 31 V-41 Q-39 -45 -35 -45Z" fill="var(--paper)"/>
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z" fill="var(--paper)"/>
<path d="M12 -40 V-20 H32"/>
<text x="-12" y="-20" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">csv</text>
<path d="M-24 -6 H22 M-24 8 H22 M-24 22 H22 M-24 34 H22 M-6 -6 V34" class="light"/>
</g>
<!-- Stage 2b: saturation grid CSV -->
<g transform="translate(380 310)" class="sketch">
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z"/>
<path d="M12 -40 V-20 H32"/>
<text x="-12" y="-20" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">csv</text>
<path d="M-24 -6 H22 M-24 8 H22 M-24 22 H22 M-24 34 H22 M-8 -6 V34 M8 -6 V34" class="light"/>
</g>
<!-- Stage 3: local LLM as a box holding a speech bubble -->
<g transform="translate(680 205)" class="sketch">
<path d="M-60 -50 Q-60 -56 -53 -56 H53 Q60 -56 60 -49 V49 Q60 56 53 56 H-53 Q-60 56 -60 49Z"/>
<path d="M-30 -26 H30 Q36 -26 36 -20 V8 Q36 14 30 14 H-6 L-18 28 V14 H-30 Q-36 14 -36 8 V-20 Q-36 -26 -30 -26Z" fill="var(--wash)"/>
<circle cx="-14" cy="-6" r="3"/><circle cx="0" cy="-6" r="3"/><circle cx="14" cy="-6" r="3"/>
</g>
<!-- Stage 4: stack of text chunks with suggested labels -->
<g transform="translate(965 210)" class="sketch">
<path d="M-40 -50 H2 L22 -30 V26 Q22 30 18 30 H-40 Q-44 30 -44 26 V-46 Q-44 -50 -40 -50Z" fill="var(--paper)"/>
<path d="M-35 -45 H7 L27 -25 V31 Q27 35 23 35 H-35 Q-39 35 -39 31 V-41 Q-39 -45 -35 -45Z" fill="var(--paper)"/>
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z" fill="var(--paper)"/>
<path d="M12 -40 V-20 H32"/>
<text x="-12" y="-20" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">csv</text>
<path d="M-24 -6 H0 M-24 8 H0 M-24 22 H0 M-24 34 H0" class="light"/>
<path d="M6 -10 H22 V-2 H6 Z M6 4 H22 V12 H6 Z M6 18 H22 V26 H6 Z" fill="var(--wash)" stroke-width="1.8"/>
</g>
<!-- Stage 5: stack of curated, reviewed data -->
<g transform="translate(1235 210)" class="sketch">
<path d="M-40 -50 H2 L22 -30 V26 Q22 30 18 30 H-40 Q-44 30 -44 26 V-46 Q-44 -50 -40 -50Z" fill="var(--paper)"/>
<path d="M-35 -45 H7 L27 -25 V31 Q27 35 23 35 H-35 Q-39 35 -39 31 V-41 Q-39 -45 -35 -45Z" fill="var(--paper)"/>
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z" fill="var(--wash)"/>
<path d="M12 -40 V-20 H32"/>
<path d="M-16 6 L-5 17 L18 -8"/>
</g>
<!-- Labels below the icons -->
<g class="label"><text x="110" y="178">Transcripts</text><text x="380" y="178">Text chunks</text><text x="110" y="388">Saturation grid</text><text x="380" y="388">Saturation grid</text><text x="680" y="290">Llama 3.1 8B</text><text x="960" y="290">Suggested labels</text><text x="1230" y="290">Curated data</text></g>
<g class="desc"><text x="110" y="200">discussion groups · Word</text><text x="380" y="200">CSV</text><text x="110" y="410">Excel</text><text x="380" y="410">CSV</text><text x="680" y="312">via Ollama</text><text x="960" y="312">text chunks + labels · CSV</text><text x="1230" y="312">reviewed by hand</text></g>
</svg>
</div>
<p class="pipeline-caption">Labelling flow · transcripts + saturation grid → local LLM suggests labels → manual review → curated data</p>
</div>

<div class="pipeline">
<div class="scroller">
<svg viewBox="0 0 1340 550" role="img" aria-label="Hand-drawn augmentation flow: curated texts are split into a test dataset of 129 texts and a training dataset of 512 texts; the training dataset is back-translated via French, back-translated via German, copied forward and paraphrased, and the four results form an augmented training dataset of 2048 texts">
<defs><marker id="ag-arrowhead" viewBox="0 0 12 12" refX="10" refY="6" markerWidth="7" markerHeight="7" orient="auto"><path d="M1 1 L10 6 L1 11" class="arrow"/></marker></defs>
<!-- Hand-drawn box around the augmented training dataset -->
<path d="M60 354 Q60 340 74 340 H1266 Q1280 341 1280 354 V512 Q1279 526 1265 526 H75 Q61 525 60 512 Z" class="light dotted"/>
<!-- Loose-pencil arrows: the train/test split, then four augmentation processes fanning out from the training set -->
<g class="arrow" marker-end="url(#ag-arrowhead)">
<path d="M178 150 Q 450 148 712 150"/><path d="M330 150 C 380 150 380 80 434 80"/>
<path d="M748 198 C 748 280 470 260 470 346"/><path d="M754 198 C 754 280 700 270 700 346"/><path d="M766 198 C 766 280 930 270 930 346"/><path d="M772 198 C 772 280 1160 260 1160 346"/>
</g>
<g class="desc"><text x="250" y="134">train / test split</text></g>
<!-- Stage 1: stack of curated texts -->
<g transform="translate(135 155)" class="sketch">
<path d="M-40 -50 H2 L22 -30 V26 Q22 30 18 30 H-40 Q-44 30 -44 26 V-46 Q-44 -50 -40 -50Z" fill="var(--paper)"/>
<path d="M-35 -45 H7 L27 -25 V31 Q27 35 23 35 H-35 Q-39 35 -39 31 V-41 Q-39 -45 -35 -45Z" fill="var(--paper)"/>
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z" fill="var(--wash)"/>
<path d="M12 -40 V-20 H32"/>
<path d="M-16 6 L-5 17 L18 -8"/>
</g>
<!-- Stage 2a: held-out test dataset -->
<g transform="translate(480 80)" class="sketch">
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z"/>
<path d="M12 -40 V-20 H32"/>
<path d="M-24 -18 H4 M-24 -4 H22 M-24 10 H18 M-24 24 H22" class="light"/>
</g>
<!-- Stage 2b: training dataset -->
<g transform="translate(760 150)" class="sketch">
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z"/>
<path d="M12 -40 V-20 H32"/>
<path d="M-24 -18 H4 M-24 -4 H22 M-24 10 H18 M-24 24 H22" class="light"/>
</g>
<!-- Stage 3: four augmented stacks, one per process -->
<g transform="translate(475 410)" class="sketch">
<path d="M-40 -50 H2 L22 -30 V26 Q22 30 18 30 H-40 Q-44 30 -44 26 V-46 Q-44 -50 -40 -50Z" fill="var(--paper)"/>
<path d="M-35 -45 H7 L27 -25 V31 Q27 35 23 35 H-35 Q-39 35 -39 31 V-41 Q-39 -45 -35 -45Z" fill="var(--paper)"/>
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z" fill="var(--paper)"/>
<path d="M12 -40 V-20 H32"/>
<text x="-12" y="-20" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">fr</text>
<path d="M-24 -4 H22 M-24 10 H18 M-24 24 H22" class="light"/>
</g>
<g transform="translate(705 410)" class="sketch">
<path d="M-40 -50 H2 L22 -30 V26 Q22 30 18 30 H-40 Q-44 30 -44 26 V-46 Q-44 -50 -40 -50Z" fill="var(--paper)"/>
<path d="M-35 -45 H7 L27 -25 V31 Q27 35 23 35 H-35 Q-39 35 -39 31 V-41 Q-39 -45 -35 -45Z" fill="var(--paper)"/>
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z" fill="var(--paper)"/>
<path d="M12 -40 V-20 H32"/>
<text x="-12" y="-20" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">de</text>
<path d="M-24 -4 H22 M-24 10 H18 M-24 24 H22" class="light"/>
</g>
<g transform="translate(935 410)" class="sketch">
<path d="M-40 -50 H2 L22 -30 V26 Q22 30 18 30 H-40 Q-44 30 -44 26 V-46 Q-44 -50 -40 -50Z" fill="var(--paper)"/>
<path d="M-35 -45 H7 L27 -25 V31 Q27 35 23 35 H-35 Q-39 35 -39 31 V-41 Q-39 -45 -35 -45Z" fill="var(--paper)"/>
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z" fill="var(--paper)"/>
<path d="M12 -40 V-20 H32"/>
<text x="-12" y="-20" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">1:1</text>
<path d="M-24 -4 H22 M-24 10 H18 M-24 24 H22" class="light"/>
</g>
<g transform="translate(1165 410)" class="sketch">
<path d="M-40 -50 H2 L22 -30 V26 Q22 30 18 30 H-40 Q-44 30 -44 26 V-46 Q-44 -50 -40 -50Z" fill="var(--paper)"/>
<path d="M-35 -45 H7 L27 -25 V31 Q27 35 23 35 H-35 Q-39 35 -39 31 V-41 Q-39 -45 -35 -45Z" fill="var(--paper)"/>
<path d="M-30 -40 H12 L32 -20 V36 Q32 40 28 40 H-30 Q-34 40 -34 36 V-36 Q-34 -40 -30 -40Z" fill="var(--paper)"/>
<path d="M12 -40 V-20 H32"/>
<text x="-12" y="-20" font-size="12" text-anchor="middle" fill="var(--dark)" stroke="none">≈</text>
<path d="M-24 -4 H22 M-24 10 H18 M-24 24 H22" class="light"/>
</g>
<!-- Labels -->
<g class="label"><text x="130" y="228">Curated texts</text><text x="530" y="78" style="text-anchor:start">Test dataset</text><text x="820" y="146" style="text-anchor:start">Training dataset</text><text x="190" y="404">Augmented</text><text x="190" y="430">training dataset</text><text x="470" y="482">Back-translation</text><text x="700" y="482">Back-translation</text><text x="930" y="482">Copy forward</text><text x="1160" y="482">Paraphrase</text></g>
<g class="desc"><text x="530" y="100" style="text-anchor:start">129 texts · held out</text><text x="820" y="168" style="text-anchor:start">512 texts</text><text x="190" y="454">2048 texts</text><text x="470" y="504">via French</text><text x="700" y="504">via German</text><text x="930" y="504">original texts</text><text x="1160" y="504">reworded texts</text></g>
</svg>
</div>
<p class="pipeline-caption">Augmentation flow · curated texts → train / test split → four augmentation processes → augmented training set</p>
</div>

<div class="pipeline models">
<style>
.models table{width:100%;max-width:960px;margin:0 auto;border-collapse:collapse;font:16px system-ui,sans-serif;color:var(--dark);background:var(--paper)}
.models th{text-align:left;font-size:13px;font-weight:600;letter-spacing:.04em;text-transform:uppercase;color:#81917c;background:var(--wash);padding:12px 18px;border-top:2.5px solid var(--ink);border-bottom:2.5px solid var(--ink)}
.models td{padding:13px 18px;border-bottom:1.5px dashed #acbcaa;vertical-align:top}
.models tr:last-child td{border-bottom:2.5px solid var(--ink)}
.models td:first-child{font-weight:600;white-space:nowrap}
.models code{font:14px ui-monospace,SFMono-Regular,Menlo,monospace;color:var(--dark)}
.models .note{color:#81917c}
@media (max-width:760px){.models td:first-child{white-space:normal}.models th,.models td{padding:10px 12px}}
.models tr.dim td{opacity:.45;font-weight:400}
.models tr.pick td{background:var(--wash)}
.models td.mark{width:64px;padding:8px 12px;vertical-align:middle}
.models td.mark svg{display:block;width:44px;height:auto}
</style>
<table>
<thead><tr><th scope="col">Model</th><th scope="col">Implementation</th><th scope="col"><span class="visually-hidden" style="position:absolute;width:1px;height:1px;overflow:hidden;clip:rect(0 0 0 0)">Shown below</span></th></tr></thead>
<tbody>
<tr class="dim"><td>Word2Vec</td><td><code>word2vec-google-news-300</code></td><td class="mark"></td></tr>
<tr class="pick"><td>Pretrained BERT base</td><td><code>bert-base-uncased</code></td><td class="mark"><svg viewBox="0 0 48 20" role="img" aria-label="shown below"><path d="M44 10 Q 26 9 8 10 M15 3 L6 10 L15 17" class="arrow"/></svg></td></tr>
<tr class="dim"><td>Fine-tuned BERT base</td><td><code>bert-base-uncased</code> <span class="note">fine-tuned using HumSet data</span></td><td class="mark"></td></tr>
<tr class="dim"><td>Pretrained sBERT</td><td><code>all-MiniLM-L6-v2</code></td><td class="mark"></td></tr>
<tr class="pick"><td>sBERT fine-tuned with SetFit</td><td><code>all-MiniLM-L6-v2</code> <span class="note">fine-tuned with IMPACT data</span></td><td class="mark"><svg viewBox="0 0 48 20" role="img" aria-label="shown below"><path d="M44 10 Q 26 11 8 10 M15 3 L6 10 L15 17" class="arrow"/></svg></td></tr>
<tr class="dim"><td>Llama 3.1</td><td><code>Llama-3.1 8B</code></td><td class="mark"></td></tr>
</tbody>
</table>
<p class="pipeline-caption">The two embedding spaces projected below · pretrained BERT base and sBERT fine-tuned with SetFit</p>
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

<div class="pipeline models results">
<style>
.results .table-wrap{overflow-x:auto}
.results table{min-width:720px}
.results thead tr+tr th{border-top:none}
.results th.group{text-align:center;border-bottom:1.5px dashed #acbcaa}
.results th.num,.results td.num{text-align:right}
.results td:first-child{white-space:normal}
.results td .sub{display:block;margin-top:3px;font-size:12px;font-weight:400;color:#81917c}
.results td.num{font:15px ui-monospace,SFMono-Regular,Menlo,monospace;font-variant-numeric:tabular-nums}
.results td.num strong{font-weight:700;color:var(--dark)}
.results tr.worst td{background:#ecece4}
.results tr.best td{background:var(--wash)}
.results td.mark{width:110px;padding:8px 12px;vertical-align:middle;white-space:nowrap;font-weight:600}
.results td.mark svg{display:inline-block;vertical-align:middle;width:40px;height:auto;margin-right:6px}
</style>
<div class="table-wrap">
<table>
<thead>
<tr><th scope="col" rowspan="2">Embedding model</th><th scope="colgroup" colspan="3" class="group">Classifiers</th><th scope="col" rowspan="2"><span style="position:absolute;width:1px;height:1px;overflow:hidden;clip:rect(0 0 0 0)">Ranking</span></th></tr>
<tr><th scope="col" class="num">Logistic regression</th><th scope="col" class="num">Random forest</th><th scope="col" class="num">Support vector classification</th></tr>
</thead>
<tbody>
<tr><td>Word2Vec<code class="sub">word2vec-google-news-300</code></td><td class="num">0.1417</td><td class="num">0.1329</td><td class="num">0.1085</td><td class="mark"></td></tr>
<tr class="worst"><td>Pretrained BERT base<code class="sub">bert-base-uncased</code></td><td class="num">0.1528</td><td class="num">0.1484</td><td class="num">0.1329</td><td class="mark"><svg viewBox="0 0 48 20" aria-hidden="true"><path d="M44 10 Q 26 9 8 10 M15 3 L6 10 L15 17" class="arrow"/></svg>Worst</td></tr>
<tr><td>Fine-tuned BERT base<code class="sub">bert-base-uncased fine-tuned using HumSet data</code></td><td class="num">0.1185</td><td class="num">0.1240</td><td class="num">0.0941</td><td class="mark"></td></tr>
<tr><td>Pretrained sBERT<code class="sub">all-MiniLM-L6-v2</code></td><td class="num">0.1030</td><td class="num">0.0997</td><td class="num">0.0908</td><td class="mark"></td></tr>
<tr class="best"><td>sBERT fine-tuned with SetFit<code class="sub">all-MiniLM-L6-v2 fine-tuned with IMPACT data</code></td><td class="num">0.0786</td><td class="num"><strong>0.0775</strong></td><td class="num">0.0875</td><td class="mark"><svg viewBox="0 0 48 20" aria-hidden="true"><path d="M44 10 Q 26 11 8 10 M15 3 L6 10 L15 17" class="arrow"/></svg>Best</td></tr>
<tr><td>Llama 3.1<code class="sub">Llama-3.1 8B</code></td><td class="num">0.1030</td><td class="num">0.1484</td><td class="num">0.1008</td><td class="mark"></td></tr>
</tbody>
</table>
</div>
<p class="pipeline-caption">Classification results per embedding space · Hamming loss (lower is better)</p>
</div>

<div class="pipeline models results">
<div class="table-wrap">
<table>
<thead><tr><th scope="col">Embedding model</th><th scope="col" class="num">CPU-only machine</th><th scope="col" class="num">GPU machine</th><th scope="col"><span style="position:absolute;width:1px;height:1px;overflow:hidden;clip:rect(0 0 0 0)">Ranking</span></th></tr></thead>
<tbody>
<tr><td>Word2Vec</td><td class="num">10 s</td><td class="num">&lt;10 s</td><td class="mark"></td></tr>
<tr><td>Pretrained BERT base</td><td class="num">1800 s</td><td class="num">15 s</td><td class="mark"></td></tr>
<tr><td>Fine-tuned BERT base</td><td class="num">1800 s</td><td class="num">15 s</td><td class="mark"></td></tr>
<tr><td>Pretrained sBERT</td><td class="num">120 s</td><td class="num">10 s</td><td class="mark"></td></tr>
<tr class="best"><td>sBERT fine-tuned with SetFit</td><td class="num">120 s</td><td class="num">10 s</td><td class="mark"><svg viewBox="0 0 48 20" aria-hidden="true"><path d="M44 10 Q 26 11 8 10 M15 3 L6 10 L15 17" class="arrow"/></svg>Best</td></tr>
</tbody>
</table>
</div>
<p class="pipeline-caption">Embedding generation time per model · all times in seconds</p>
</div>
