# Samenvatting: 4 Notebooks – Paraplu-stangenmechanisme (Overdekking)

> **Let op:** dit is een instap-/overzichtssamenvatting. Gebruik voor de finale mondelinge verdediging `Antwoorden_Mondeling_Stangen.md`, `AntwoordenALEX_Mondeling_Stangen.md` en `Bijvragen_Mondeling_Stangen.md`. De hoofdcase is nu een **brede overdekking** (`b = 6,0 m`, `n = 2` identieke mechanismen) met de hellende open stand `s_open = 0,600 m` en **optionele trekveerassistentie**; oude waarden met `s_open = 0,125 m` of een los enkel mechanisme (zonder Overdekking/Trekveren) zijn historiek.

> Doel: inzicht genoeg om code, grafieken en resultaten te kunnen uitleggen en doordenken — niet van buiten leren.
> Alle cijfers komen rechtstreeks uit de notebook-outputs (code runs), niet uit de studieteksten.

---

## Notebook 1 – Kinematica

### 1. Algemeen overzicht

**Wat:** Berekening van posities, snelheden en versnellingen van alle stangen en uitvoerpunt K, puur op basis van de schuiverpositie $s(t)$ langs de mast. Het mechanisme telt 8 links (incl. de vaste mast) en 10 gewrichten B–K; C is het enige vaste rotatiepunt en K (tip van de buitenste rib, link 8) is het outputpunt.

**Topologie:**

| Link | Van → Naar | Type | Rol |
|------|-----------|------|-----|
| 1 | vaste mast | grond | referentie |
| 2 | (schuiver langs mast) | prismatisch | invoer $s(t)$ |
| 3 | B → D → E | ternair | koppelt schuiver aan link 4 |
| 4 | C → E → H | ternair | ruggengraat, vast scharnier in C |
| 5 | D → F → G | ternair | eerste radiale uitbreiding |
| 6 | F → I | binair | koppelstang |
| 7 | G → H → J | ternair | synchronisatiestang |
| 8 | I → J → K | ternair | buitenste rib — K is outputpunt |

**Waarom:** Kinematica is de geometrische basis. Zonder te weten hoe de stangen draaien als de schuiver beweegt, kunnen NB2, NB3-Overdekking en NB4 niet werken. Na `Run All` werkt elke parameterwijziging (geometrie, traject, openingsrichting) automatisch door tot in de animatie, conditionering en de `.npz`-overdracht naar de andere notebooks.

**Aanpak in drie stappen:**
- **Positie** → 3 onafhankelijke vectorlussen → 6 sluitingsvergelijkingen $\vec F(\boldsymbol\theta,s)=\vec 0$ voor $\theta_3,\dots,\theta_8$, opgelost met `fsolve`. Branch tracking (start = vorige oplossing + lineaire predictie) houdt de solver op dezelfde configuratietak.
- **Snelheid** → $A(\boldsymbol\theta)\,\dot{\boldsymbol\theta} = \mathbf b\,\dot s$ via `linalg.solve`
- **Versnelling** → $A(\boldsymbol\theta)\,\ddot{\boldsymbol\theta} = \mathbf b\,\ddot s + \mathbf c(\dot{\boldsymbol\theta},\dot s)$ — **dezelfde matrix $A$** als bij snelheid

$$\text{cond}(A) = \|A\|\cdot\|A^{-1}\| \quad\text{— maat voor gevoeligheid nabij dode punten/singulariteiten}$$

### 2. Belangrijke woordenschat & concepten

| Term | Wat het is | Intuïtief |
|------|-----------|-----------|
| **Schuiver** | De ene invoer: beweegt langs de mast, $s$ groot = gesloten, $s$ klein = open | De "hendel" die de overdekking open/dichtklapt |
| **Punt K** | Uiteinde van de buitenste rib (link 8) | Het punt dat het verste beweegt — draagt de voorbalk (zie NB3-Overdekking) |
| **Sluitingsvergelijking** | Eis dat een gesloten keten terugkomt waar ze begon | Vectorsom over een lus = 0 |
| **Matrix A (6×6)** | Coëfficiëntenmatrix snelheids-/versnellingsstelsel | Hoe hard elke stang draait als de schuiver beweegt — hangt af van de hoeken |
| **Conditiegetal cond(A)** | Gevoeligheid: hoe sterk vertaalt schuiverfout naar hoekfouten | Laag = robuust; hoog = gevoelig; → ∞ = dood punt |
| **Dood punt** | Configuratie waar twee stangen in elkaars verlengde liggen | Motor heeft geen greep meer, hoeksnelheid → ∞ |
| **Mobiliteitsformule (Grübler-Kutzbach)** | $M = 3(n-1) - 2f_1 - f_2 = 1$ | Eén vrijheidsgraad: $s(t)$ is de enige onafhankelijke invoer |
| **Open stand $s_{open}=0{,}600$ m** | Hellende open stand i.p.v. de oude bijna-horizontale $s_{open}=0{,}125$ m | Bewuste keuze: minder "uitval" maar veel lagere zijreactie op de schuiver → realistischer mast-/geleidingontwerp (NB3-Overdekking) |
| **Bewegingsrichting "openen"** | Deze run: $s$ van $1{,}875\to 0{,}600$ m (gesloten → open) | `motion_direction="custom"` met `s_start_custom`/`s_end_custom` is beschikbaar voor tussenstanden |
| **condition_scurve** | Bewegingsprofiel gebouwd op `smooth_4567`, met extra vertraging waar cond(A) groot is | Zachte start/stop **én** preventief trager in de gevoelige gesloten stand |
| **Branch tracking** | Startpunt voor `fsolve` = vorige oplossing | Voorkomt dat de solver naar de "andere" stangenstand springt |
| **Centripetale termen** | $-r\cos\theta\cdot\dot\theta^2$ in het rechterlid $\mathbf c$ | Gevolg van differentiëren van de snelheidsmatrix — staan rechts, niet in $A$ |

**Vergelijking bewegingsprofielen**

| Profiel | Eindpunten $\dot s=\ddot s=0$? | Conditie-aware? | Opmerking |
|---|---|---|---|
| smooth_345 (5e-graads) | Ja | Nee | Eenvoudig referentieprofiel |
| smooth_4567 (7e-graads) | Ja, ook $\dddot s=0$ | Nee | Zachter, geen koppeling met cond(A) |
| **condition_scurve** ✓ | Ja (gebaseerd op smooth_4567) | **Ja** | **Gekozen**: vertraagt tot snelheidsfactor 0,25 waar cond(A) hoog is (gesloten stand) |

### 3. Resultaten & interpretatie

| Grootheid | Symbool | Waarde |
|-----------|---------|--------|
| Schuiverbereik / slag | $s$ | **0,600 m ↔ 1,875 m**, slag = **1,275 m** |
| Bewegingsrichting (deze run) | — | **openen** ($s$: $1{,}875\to 0{,}600$ m) |
| Bewegingsprofiel | — | **condition_scurve** (op basis van smooth_4567) |
| Bewegingstijd | $t_{move}$ / $t_{hold}$ / $t_{eind}$ | 20,0 s / 4,0 s / **24,0 s** |
| Max schuiversnelheid | $\dot s_{max}$ | **0,1965 m/s** (limiet 0,25 m/s) |
| Max schuiverversnelling | $\ddot s_{max}$ | **0,0511 m/s²** (limiet 0,10 m/s²) |
| Max sluitingsfout | $\|\vec F\|$ | **4,7×10⁻¹³** → machineprecisie ✓ |
| Max snelheid punt K | $\lvert\dot{\vec K}\rvert_{max}$ | **0,347 m/s** bij $s=1{,}638$ m |
| Max versnelling punt K | $\lvert\ddot{\vec K}\rvert_{max}$ | **0,108 m/s²** bij $s=1{,}839$ m |
| cond(A): min / gem / max | — | **4,70 / 22,4 / 68,4** |
| Hoogste cond(A) bij | — | $s=1{,}875$ m (gesloten stand, snelheidsfactor 0,25) |
| Kritische zones (cond(A) > 1000) | — | **0** — geen enkel dood punt in het werkbereik |

**Grafieken:** De hoeken $\theta_i(s)$ zijn gladde curven over het volledige bereik. De snelheid van K piekt rond het midden van de slag ($s\approx1{,}638$ m). cond(A) is hoogst in de gesloten stand ($s=1{,}875$ m, het vertrekpunt van deze "open"-beweging) — daar duwt `condition_scurve` de snelheidsfactor terug naar 0,25, zichtbaar als een afgevlakte $\dot s(t)$-curve aan het begin van de beweging.

**Sleutelinzicht:** De keuze $s_{open}=0{,}600$ m (i.p.v. de oude bijna-horizontale $0{,}125$ m) is de belangrijkste geometrische beslissing van het hele project. Ze geeft de overdekking een realistische helling, kost wat "uitval" (diepte), maar verlaagt de zijreactie op de schuiver drastisch — de basis voor een verdedigbaar mast-/geleidingontwerp in NB3-Overdekking. De gesloten stand blijft kinematisch het gevoeligst (cond = 68,4), maar ver van een dood punt (drempel 1000); `condition_scurve` vertraagt daar preventief.

---

## Notebook 2 – Inverse dynamica (inertie only)

### 1. Algemeen overzicht

**Wat:** Alle gewrichtskrachten en de aandrijfkracht $F_s$ berekend op basis van de bekende beweging — zonder zwaartekracht, wrijving of trekveren. Puur inertieel.

**Waarom:** De inertiekrachten afzonderlijk isoleren maakt validatie eenvoudig en legt het belang ervan bloot t.o.v. de zwaartekracht/wrijving (NB3-Overdekking). Daarnaast wordt hier formeel het aandrijfprincipe vastgelegd: **poelie/riem + roterende motor**, niet langer een schroefspindel.

**Aanpak:** 7 links × 3 Newton-Euler vergelijkingen = **21 vergelijkingen, 21 onbekenden** ($R_{Ax}$, $F_{act,y}$, $M_A$ + reactiekrachten in B…J), per tijdstap lineair stelsel $A_{21\times21}\mathbf w = \mathbf b$ oplossen. Rechterlid = inertiële resultanten uit NB1.

### 2. Belangrijke woordenschat & concepten

| Term | Wat het is | Intuïtief |
|------|-----------|-----------|
| **Inverse dynamica** | Beweging gegeven → krachten berekend | Omgekeerde van simuleren |
| **Newton-Euler vergelijkingen** | $\sum F = ma$, $\sum M_{cg} = J\alpha$ per link | Drie vergelijkingen per link, voor het zwaartepunt |
| **A (21×21)** | Coëfficiëntenmatrix van de onbekende krachten | Totaal andere matrix dan de kinematische A — zelfde naam, ander ding! |
| **Gewrichtskrachten** | Krachten in de scharnieren | Intern — vallen weg bij globale controle (actie = -reactie) |
| **Aandrijfkracht $F_{drive,s}$** | Kracht van de actuator op de schuiver | $F_{drive,s} = -F_{actuator,y}$ ($s$ positief naar beneden) |
| **Onbalanskracht / -moment** | Kracht/moment dat het mechanisme op zijn frame overdraagt | $\vec F_{shak} = -(\vec F_A+\vec F_C)$, $M_{shak,C}=-(M_A+\vec r_{CA}\times\vec F_A)$ |
| **Traagheidsmoment $J_{cg}$** | Weerstand van een staaf tegen draaien om zijn middelpunt | $J = mL^2/12$ voor uniforme staaf |

**Vergelijking aandrijfprincipes (inertie-only equivalent)**

| Aandrijfprincipe | Krachtformule | Geschikt voor brede overdekking (NB4)? | Opmerking |
|---|---|---|---|
| Schroefspindel | $F=2\pi\eta T/\text{spoed}$ | Nee | enkel behouden als `screw_lead`-compatibiliteitsalias |
| **Poelie/riem + roterende motor** ✓ | $T_{motor}=\dfrac{F_s\,d_{travel}}{2\pi\eta}$, $d_{travel}=2\pi r_{poelie}$=0,1571 m/omw | **Ja** | **Gekozen**: één centrale motor + as kan via riemen/kabels meerdere ribben/mechanismen aandrijven |

$$\sum F_x=ma_x,\quad \sum F_y=ma_y,\quad \sum M_{cg}=J_{cg}\alpha \quad\Rightarrow\quad A\mathbf w=\mathbf b \ \ \text{(21 onbekenden)}$$

$$P_{act}=F_s\dot s,\qquad T_{motor}=\frac{F_s\,d_{travel}}{2\pi\eta},\qquad A_{max}=\max_t\!\int_0^t(P-\bar P)dt-\min_t\!\int_0^t(P-\bar P)dt$$

### 3. Resultaten & interpretatie

| Grootheid | Symbool | Waarde |
|-----------|---------|--------|
| Totale bewegende modelmassa (incl. K) | $m_{tot}$ | **7,4998 kg** ($m_K$=3,00 kg, schuiver=1,50 kg, links 2-8 samen 4,00 kg) |
| Lijnmassa stangen | $\rho_L$ | 0,5500 kg/m (Al-buis Ø30×2mm, $\rho$=2700 kg/m³, + 0,075 kg/m beslag) |
| Globale dyn. controle (kracht-/momentbalans) | residual | **5,5×10⁻¹⁶** → machineprecisie ✓ |
| cond(A) bereik (21×21 stelsel) | cond(A) | **18,77 – 216,68** |
| Aandrijfkracht (bereik) | $F_{drive,s}$ | **-2,994 N ↔ +0,364 N** |
| Piek / RMS dynamische schuifkracht | $F_{peak}/F_{rms}$ | **2,994 N / 1,108 N** |
| Inertiële kracht in hold-fase | $F_{hold}$ | **0,0 N** (model zonder zwaartekracht/wrijving) |
| Max. onbalanskracht | $\|\vec F_{shak}\|_{max}$ | **0,4996 N** |
| Onbalansmoment rond C (bereik) | $M_{shak,C}$ | **-0,809 ↔ +0,516 Nm** |
| Actuatorvermogen (bereik) | $P_{act}$ | **-0,060 ↔ +0,101 W** |
| Piek aandrijf- / regen.vermogen | $P_{+}/P_{regen}$ | **0,195 W / 0,070 W** |
| Equivalent motorkoppel (piek / RMS) | $T_{motor}$ | **0,1440 / 0,0533 Nm** |
| Piek motortoerental | $n_{motor,max}$ | **75,1 rpm** |
| Arbeids-surplus (energiebuffer) | $A_{max}$ | **0,2541 J** |
| Max. energiebalansfout | $\varepsilon_{E,max}$ | **1,1×10⁻⁴ J** $\ll E_{kin,max}$=0,254 J ✓ |

**Grafieken:** $F_{drive,s}(t)$ blijft over de volledige 24 s binnen $[-2{,}994;\,+0{,}364]$ N — pieken bij de versnellings-/vertragingsfasen van de 20 s-beweging, vrijwel nul tijdens de 4 s hold. Bevestigt dat inertie bij dit trage traject verwaarloosbaar is.

**Sleutelinzicht:** Met de nieuwe "open"-beweging (24 s totaal) blijft de zuiver inertiële aandrijfkracht extreem klein (≤3 N, ≤0,2 W) — dit mechanisme is quasi-statisch. Het aandrijfprincipe **poelie/riem + roterende motor** wordt hier formeel gekozen (schroefspindel blijft enkel als compatibiliteitsalias): dit is precies het principe dat in NB4 één motor + gemeenschappelijke as aan **twee** mechanismen kan koppelen.

---

## Notebook 3 – Overdekking: belastingsanalyse, structuur & trekveren

### 1. Algemeen overzicht

**Wat:** Brede doekoverdekking ($b=6{,}0$ m, $n=2$ identieke mechanismen naast elkaar, mechanisme-afstand = balkspan = 6,0 m). Dezelfde Newton-Euler + zwaartekracht + wrijving-aanpak als de inverse dynamica, aangevuld met:
- een massamodel voor voorbalk + doek + beslag (afhankelijk van de breedte),
- een structurele controle van de voorbalk (eigengewicht, regenwater, sneeuw, wind — 6 belastingscases),
- een eerste-orde mast-/schuivergeleidingscontrole,
- **optionele trekveerassistentie** als energiebuffer.

Resultaten worden opgeslagen als NB4-loadcases (`overdekking` en `overdekking_trekveren`).

**Waarom:** Een los parasolmechanisme (NB1/NB2) is nuttig om kinematica en dynamica op te bouwen, maar een brede overdekking voor een gevel is de eigenlijke eindtoepassing. NB3-Overdekking combineert de volledige belastingsanalyse met de constructieve haalbaarheid (balk, mast, geleiding) en onderzoekt of trekveren de motorvraag kunnen halveren.

**Aanpak:**
- Kinematica (traject, hoeken, snelheden/versnellingen) komt rechtstreeks uit NB1 — **onafhankelijk van NB2**.
- **Massamodel:** $m_{K,eq}$ per mechanisme = ½ voorbalkmassa + ½ doekmassa (areal density × diepte × breedte/$n$) + 1,5 kg beslag — vervangt de vaste 3 kg puntmassa uit NB2.
- **Structurele balkcontrole:** voorbalk als eenvoudig opgelegde aluminium koker over de balkspan; 6 belastingscases (eigengewicht, 3× regenwaterdiepte, sneeuw, wind-down, wind-uplift) → utilisatie $U=\max(\text{doorbuiging},\,\text{von Mises spanning},\,\text{torsie})$ per case.
- **Inverse dynamica** zoals voorheen (Coulombwrijving + vaste-puntsiteratie), nu met $m_{K,eq}$.
- **Mast-/geleidingcontrole:** eerste-orde koppel- en krachtcontrole op basis van de schuiverzijreactie $A_x$.
- **Optionele trekveren:** lineaire veren parallel aan de schuiver (motion ratio = 1), voorspanning automatisch gedimensioneerd op de houdkracht.

### 2. Belangrijke woordenschat & concepten

| Term | Wat het is | Intuïtief |
|------|-----------|-----------|
| **Overdekking ($b$=6,0 m, $n$=2)** | Brede doekconstructie met 2 identieke mechanismen naast elkaar | Van losse parasol naar bruikbare gevelluifel |
| **Voorbalk** | Balk tussen de twee K-punten, draagt het doek | De "neus" van de overdekking, overspant de breedte |
| **Areal density (doek)** | Oppervlaktedichtheid van het doek (0,35 kg/m²) | Doekmassa = dichtheid × oppervlakte |
| **Equivalente puntmassa $m_{K,eq}$** | ½ voorbalk + ½ doek + beslag, toegekend aan elk mechanisme | Vervangt de vaste 3 kg uit NB2 door een breedte-afhankelijke massa |
| **Belastingscases (6)** | Eigengewicht, regenwater (0/10/25 mm), sneeuw, wind-down, wind-uplift | "Worst-case weer" voor de balk; maatgevend = **wind uplift** |
| **Utilisatie $U$** | $\max(\delta/\delta_{lim},\ \sigma_{vM}/\sigma_{toel},\ \theta/\theta_{lim})$ | Eén getal per case; $U<1$ = constructief OK |
| **Von Mises spanning $\sigma_{vM}$** | $\sqrt{\sigma_b^2+3\tau_{eq}^2}$ | Combineert buig- en schuifspanning tot één toetswaarde |
| **Mastmoment $M_{mast}$** | Koppel dat de schuiver op de mast uitoefent | Hoe sterk de mast/bevestiging "duwt" |
| **Geleidingontwerpkracht** | $F_{guide}=k_{SF}\max\|R_{Ax}\|$, $F_{rol}=F_{guide}/n_{rollen}$ | Wat elke rol/glijblok van de schuivergeleiding moet kunnen verdragen |
| **Trekveer (motion ratio = 1)** | Lineaire veer parallel aan schuiver, $F_{veer}=F_{voorspanning}+k\Delta x$ | Energiebuffer i.p.v. vliegwiel — past bij een traag, niet-cyclisch traject |
| **Steunreacties A en C** | Lokale krachten in scharnieren van schuiver en frame | Veel groter dan netto framekracht — altijd per scharnier dimensioneren! |

### 3. Resultaten & interpretatie

**Overdekking & massamodel**

| Grootheid | Symbool | Waarde |
|-----------|---------|--------|
| Breedte / aantal mechanismen | $b$ / $n$ | **6,0 m / 2** (mechanisme-afstand = balkspan = 6,0 m) |
| Diepte (uit NB1) / oppervlakte | $d$ / $A_{doek}$ | 2,221 m / 13,32 m² |
| Voorbalkprofiel (gekozen) | — | **200×100×5** Al, $m'$=7,83 kg/m → 46,98 kg totaal |
| Doekmassa (areal density 0,35 kg/m²) | $m_{doek}$ | 4,663 kg |
| Equiv. puntmassa K per mechanisme | $m_{K,eq}$ | **27,32 kg** = ½ balk + ½ doek + 1,5 kg beslag |
| Totale modelmassa (1 / 2 mechanismen) | $m_{tot}$ | 31,82 kg / **63,64 kg** ≙ 624,3 N |

**Structurele balkcontrole (voorbalk, eenvoudig opgelegd)**

| Grootheid | Symbool | Waarde |
|-----------|---------|--------|
| Maatgevende belastingscase (van 6) | — | **wind uplift** ($p$=-912,6 Pa) |
| Max. doorbuiging | $\delta_{max}$ | **1,36 mm** (limiet $L/300$=20 mm) |
| Max. utilisatie (doorb. / spanning / torsie) | $U_{max}$ | **0,749 / 0,277 / 0,017** → alle < 1 ✓ |
| Status | — | **acceptabel binnen deze aannames** |

**Mast- en schuivergeleiding**

| Grootheid | Symbool | Waarde |
|-----------|---------|--------|
| Mastprofiel (gekozen) | — | **100×100×5** staal S235, utilisatie=6,96% |
| Piekkoppel mast t.o.v. schuiver | $M_{mast,peak}$ | **624,5 Nm** bij $s$=0,6 m |
| Max. zijreactie schuiver $A_x$ | $F_{A,side,peak}$ | **1040,9 N** (= 2,80 × $F_{drive,peak}$) |
| Geleiding-ontwerpkracht (SF=2, 4 rollen) | $F_{guide}/F_{rol}$ | 2081,8 N / 520,5 N |

**Dynamica & actuator (baseline, zonder trekveer)**

| Grootheid | Symbool | Waarde |
|-----------|---------|--------|
| Aandrijfkracht (bereik) | $F_{drive,s}$ | **-372,2 N ↔ -287,9 N** |
| Piek / RMS schuifkracht | $F_{peak}/F_{rms}$ | 372,2 N / 329,6 N |
| Arbeids-surplus | $A_{max}$ | **240,96 J** |
| Piekvermogen | $P_{peak}$ | 64,29 W |
| Equiv. motorkoppel (piek / RMS) | $T_{motor}$ | **17,90 / 15,84 Nm** |
| Globale dyn. controle | residual | 3,30×10⁻¹³ → machineprecisie ✓ |

**Optionele trekveren**

| Grootheid | Symbool | Waarde |
|-----------|---------|--------|
| Aantal / stijfheid per veer | $n$ / $k$ | 2 × 10 N/m (0,01 N/mm) |
| Veerkracht open / gesloten | $F_{veer}$ | 172,7 N / 198,2 N |
| Opgeslagen veerenergie (max) | $E_{veer}$ | 236,5 J |

**Vergelijking: baseline vs. trekveerassistentie**

| Case | $F_{drive,peak}$ | $A_{max}$ | $T_{motor,peak}$ | Opmerking |
|---|---|---|---|---|
| Baseline (zonder trekveer) | 372,2 N | 240,96 J | 17,90 Nm | hoofdcase, motor draagt volledige last |
| **Met trekveer (2×10 N/m)** ✓ | **199,5 N** | **105,9 J** | **9,59 Nm** | **Aanbevolen**: halveert motorvraag; definitieve keuze in NB4 |

**Kernvergelijkingen**

$$F_{drive,s,total}=F_{drive,s,inertie}+F_{drive,s,zwaartekracht}+F_{drive,s,wrijving}$$

$$\delta_{max}=\frac{5qL^4}{384EI},\qquad U=\max\!\left(\frac{\delta_{max}}{\delta_{lim}},\ \frac{\sigma_{vM}}{\sigma_{toelaatbaar}},\ \frac{\theta_{max}}{\theta_{lim}}\right),\qquad \sigma_{vM}=\sqrt{\sigma_b^2+3\tau_{eq}^2}$$

$$F_{veer}=F_{voorspanning}+k\,\Delta x \quad(\Delta x=s-s_{open},\ \text{motion ratio}=1)$$

$$F_{guide}=k_{SF}\cdot\max|R_{Ax}|,\qquad F_{rol}=F_{guide}/n_{rollen}$$

**Grafieken:** `fig_mass` (massa-opbouw: voorbalk + doek → $m_{K,eq}$), `fig_weather` (utilisatie per belastingscase, wind uplift maatgevend), `fig_mast` (mast-/schuiverbelasting), `fig_spring` (veerwet + krachtreductie open/sluit), `fig_act` (actuatorvermogen), `fig_amax` (arbeids-surplus, baseline vs. trekveer), 3D-animatie van de overdekking (cel 36: twee mechanismen + voorbalk + doek).

**Sleutelinzichten:**
- **Wind uplift** is de maatgevende structurele case (niet eigengewicht of sneeuw); het gekozen profiel **200×100×5** Al blijft met $U_{max}=0{,}749$ ruim onder 1.
- De **zijreactie op de schuiver** (1040,9 N) is bijna 3× groter dan de aandrijfkracht (372,2 N) — de mast/geleiding wordt dus door deze dwarskracht gedimensioneerd, niet door de aandrijving zelf.
- **Trekveren (2×10 N/m) halveren** de aandrijfkracht (372,2 → 199,5 N), de arbeids-surplus (240,96 → 105,9 J) en het motorkoppel (17,90 → 9,59 Nm) — rechtstreeks bepalend voor de motorklasse in NB4.
- Een **vliegwiel** past niet bij dit trage, niet-cyclische positioneermechanisme (lange stilstand tussen openen/sluiten); trekveren dienen hier als energiebuffer in de plaats.

---

## Notebook 4 – Aandrijving, motor en optimalisatie

### 1. Algemeen overzicht

**Wat:** Vertaalt de mechanische belasting uit een gekozen NB3-Overdekking-loadcase (`load_case = overdekking_trekveren`, $n=2$ mechanismen op één **gemeenschappelijke as**) naar een aandrijfconcept: riem/kabel + poelie ($r$=25 mm) + 25:1 planetaire reductor + 48 V BLDC-motor met encoder en rem.

**Waarom:** NB3-Overdekking levert de mechanische belasting (kracht, houdkracht, zijreacties); NB4 vertaalt dat naar componentkeuze (motor, as, rem), inclusief precisie, frequentie-inhoud en kost.

**Aanpak:**
- Geen herberekening van de dynamica — leest $F_{drive,s}$, $F_{hold,s}$, $\dot s$ en steunreacties uit de gekozen `.npz` (`baseline`/`trekveren`/`overdekking`/`overdekking_trekveren`/`custom`).
- **Globale rendementsketen** $\eta=0{,}92\text{(riem)}\times0{,}85\text{(reductor)}\times0{,}78\text{(aandrijving)}\approx0{,}61$.
- Poelieradius automatisch gekozen ($r$=25 mm); motorklasse-vergelijking op vermogen/koppel/remkoppel met marges.
- **Gemeenschappelijke as** voor $n=2$ mechanismen: profielkeuze + torsiecontrole (verdraaiing vs. limiet 2°).
- Precisie: encoderresolutie (motor- vs. uitgangsas) + elastische vervorming.
- Frequentie-inhoud: aandrijffrequentie vs. eerste buigfrequentie van de voorbalk.
- Kostraming: aandrijfhardware + volledige constructie.

### 2. Belangrijke woordenschat & concepten

| Term | Wat het is | Intuïtief |
|------|-----------|-----------|
| **Load case (`overdekking_trekveren`)** | Gekozen NB3-Overdekking-resultaat als brondata | "Welke belasting moet de motor aankunnen?" |
| **Globale rendementsketen $\eta\approx0{,}61$** | $0{,}92\text{(riem)}\times0{,}85\text{(reductor)}\times0{,}78\text{(aandrijving)}$ | Drie opeenvolgende verliezen i.p.v. één globale factor |
| **Gemeenschappelijke as** | Eén as drijft beide mechanismen synchroon aan | Voorkomt scheeftrekken van het doekvlak |
| **Astorsie $\theta_{as}=TL/(GJ_p)$** | Verdraaiing van de aandrijfas over de overspanning | Bij te grote verdraaiing lopen de twee schuivers niet meer synchroon |
| **Motorklasse-vergelijking** | 4 klassen (100 W…750 W) getoetst op vermogen/koppel/rem | Kiest de kleinste klasse die mét marge voldoet |
| **Encoderresolutie (motor/uitgangsas)** | Theoretische positieresolutie na reductie | Veel fijner dan nodig — riemrek/speling/stijfheid domineren de echte precisie |
| **Frequentiescheiding $f_{balk}/f_{drive}$** | Verhouding eerste buigfrequentie voorbalk t.o.v. aandrijffrequentie | Hoe ver de aandrijving van balkresonantie zit |
| **Kostraming** | Aandrijfhardware + constructie (materiaal/componenten) | Orde-van-grootte, geen offerte |

### 3. Resultaten & interpretatie

**Aandrijfconcept (load_case = overdekking_trekveren)**

| Grootheid | Symbool | Waarde |
|-----------|---------|--------|
| Load case / brondata | — | **overdekking_trekveren** (`notebook3_overdekking_trekveren_results.npz`) |
| Aandrijfprincipe | — | riem/kabel + poelie ($r$=25 mm) + 25:1 planetaire reductor + 48 V BLDC + rem |
| Aantal mechanismen op gemeenschappelijke as | $n$ | 2 |
| Globale rendementsketen | $\eta$ | 0,92 (riem) × 0,85 (reductor) × 0,78 (aandrijving) ≈ 0,61 |

**Kracht, koppel, vermogen en houdkracht (totaal, 2 mechanismen)**

| Grootheid | Symbool | Waarde |
|-----------|---------|--------|
| Lijnkracht (bedrijf / ontwerp SF=2) | $F_{line}$ | 399,0 N / **797,9 N** |
| Poelie koppel (piek actief / ontwerp) | $T_{poelie}$ | 10,84 / **21,68 Nm** |
| Motorvermogen (piek / RMS) | $P_{motor}$ | **146,3 / 71,8 W** |
| Motorstroom @48 V (piek / RMS) | $I$ | 4,57 / 2,24 A |
| Houdkracht (bereik) | $F_{hold}$ | -231,3 ↔ -196,2 N |
| Remkoppel (ontwerp, SF=2) | $T_{rem}$ | **9,81 – 11,57 Nm** |
| Arbeids-surplus aandrijfas | $A_{max}$ | 211,8 J |

**Motorselectie — vergelijking motorklassen**

| Motorklasse | $P$ [W] | $T_{uit}$ [Nm] | $T_{rem}$ [Nm] | Geschikt? | Kost [€] |
|---|---|---|---|---|---|
| 100 W DC gearmotor | 100 | 8 | 0 (geen rem) | Nee | 180 – 350 |
| 250 W BLDC gearmotor + encoder | 250 | 18 | 6 | Nee | 350 – 700 |
| **500 W 48V BLDC + rem + gearbox** ✓ | **500** | **50** | **20** | **Ja** | **1359 – 1699** |
| 750 W servo + rem | 750 | 75 | 35 | Ja (overgedim.) | 1700 – 2600 |

**Gekozen**: 500 W-klasse — marges 2,28× (vermogen) / 2,31× (koppel) / 1,73× (rem) t.o.v. de berekende eis. Zónder trekveren (NB3-Overdekking baseline) zou dezelfde analyse naar de **750 W servo + rem**-klasse schuiven — de trekveerkeuze in NB3 bepaalt dus rechtstreeks de motorklasse hier.

**Precisie, elastische vervorming en gemeenschappelijke as**

| Grootheid | Symbool | Waarde |
|-----------|---------|--------|
| Lineaire resolutie motor-encoder / uitgangsas-encoder | — | 1,53 µm / 38,3 µm |
| Elastische vervorming (piek / ontwerp) | $\delta_{elastisch}$ | 2,66 / 5,32 mm |
| Asoverspanning / ontwerpkoppel | $L_{as}$ / $T_{as}$ | 6,0 m / 21,68 Nm |
| Gekozen asprofiel | — | **tube_40×5** (Ø40/30 mm), $m'$=4,32 kg/m |
| Verdraaiing / toelaatbaar | $\theta_{as}$ | **0,549° / 2,0°** ✓ |

**Frequentie en kostraming**

| Grootheid | Symbool | Waarde |
|-----------|---------|--------|
| Dominante belastingsfrequentie | $f_{drive}$ | 0,0416 Hz (cyclus 24 s) |
| Eerste buigfrequentie voorbalk | $f_{balk,1}$ | 15,60 Hz |
| Frequentiescheiding | $f_{balk}/f_{drive}$ | **375×** → geen resonantierisico ✓ |
| Energieverbruik per cyclus / per jaar | $E_{cyclus}$ | 476,5 J / 0,029 kWh ≈ €0,01/jaar |
| Totale hardwarekost (raming) | — | **6631 – 11938 €** |

**Kernvergelijkingen**

$$T_{poelie}=\frac{F_s\,r}{\eta},\qquad P_s=F_s\,\dot s$$

$$T_{rem}\ge SF_{rem}\,|F_{hold}|\,r$$

$$\theta_{as}=\frac{T\,L}{G\,J_p}$$

**Grafieken:** `fig_load`/`fig_hold`/`fig_radius` (poelie- en motorselectie), `fig_motor_class` (marges 2,28/2,31/1,73), `fig_precision` (encoder vs. mechanische foutbronnen), `fig_scale` (tijdschaalanalyse), `fig_shaft` (gemeenschappelijke as: torsie/synchronisatie), `fig_count` (effect aantal mechanismen), `fig_scheme` (aandrijfschets, kost en energie).

**Sleutelinzichten:**
- **500 W 48V BLDC + rem + 25:1 gearbox** is de gekozen klasse, met marges 2,28×/2,31×/1,73× op vermogen/koppel/rem — direct het gevolg van de trekveerkeuze in NB3 (zonder veren: 750 W).
- De **gemeenschappelijke as** (`tube_40×5`) synchroniseert beide mechanismen met een verdraaiing van slechts 0,549° (limiet 2°), maar lost de **lokale** zijreactie op de schuiver (1040,9 N, uit NB3) niet op — dat blijft een geleidingsprobleem per mechanisme.
- **Frequentiescheiding van 375×** tussen aandrijving (0,0416 Hz) en eerste balkresonantie (15,60 Hz) sluit resonantie uit voor dit trage positioneermechanisme.
- De **energiekost** van de beweging zelf (≈€0,01/jaar) is volledig verwaarloosbaar t.o.v. de hardwarekost (€6631–11938) — de kost zit in componenten en montage, niet in elektriciteit.

---

## Workflow en afhankelijkheden: NB1 → {NB2, NB3-Overdekking} → NB4

NB1 is de enige bron van kinematica/traject/geometrie. **NB2** (inertie-only check) en **NB3-Overdekking** (volledige belasting + constructie + trekveren) lezen **elk onafhankelijk** `notebook1_kinematica_results.npz` — NB3-Overdekking heeft NB2 niet nodig. **NB4** herberekent geen dynamica en leest enkel de gekozen `.npz` van NB3-Overdekking (`load_case = overdekking_trekveren`). Bij een geometrie- of trajectwijziging in NB1: NB1 → (NB2 én NB3-Overdekking) → NB4 opnieuw runnen, anders werken latere stappen op verouderde kinematica.

| Parameterwijziging | NB1 | NB2 | NB3-Overdekking | NB4 |
|-------------------|:---:|:---:|:---:|:---:|
| Wrijving µ | ✗ | ✗ | ✓ | ✓ (via NB3-npz) |
| Massa K / $m_{K,eq}$ (overdekkingsmassa) | ✗ | ✓ (vast 3 kg) | ✓ (breedte-afhankelijk) | ✓ |
| Bewegingsprofiel / tijdschaal $t_{move}$ | ✓ | ✓ | ✓ | ✓ |
| Breedte $b$ / aantal mechanismen $n$ | ✗ | ✗ | ✓ | ✓ |
| Trekveerstijfheid $k$ (aan/uit) | ✗ | ✗ | ✓ | ✓ (via load_case) |
| Poelieradius $r$ / reductie / $\eta$-keten | ✗ | ✓ (equivalent enkel) | ✗ | ✓ |
| Stanglengtes (geometrie) | ✓ | ✓ | ✓ | ✓ |
