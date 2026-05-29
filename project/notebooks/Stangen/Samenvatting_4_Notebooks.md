# Samenvatting: 4 Notebooks – Paraplu-stangenmechanisme

> Doel: inzicht genoeg om code, grafieken en resultaten te kunnen uitleggen en doordenken — niet van buiten leren.
> Alle cijfers komen rechtstreeks uit de notebook-outputs (code runs), niet uit de studieteksten.

---

## Notebook 1 – Kinematica

### 1. Algemeen overzicht

**Wat:** Berekening van posities, snelheden en versnellingen van alle stangen en uitvoerpunt K, puur op basis van de schuiverpositie $s(t)$ langs de mast.

**Waarom:** Kinematica is de geometrische basis. Zonder te weten hoe de stangen draaien als de schuiver beweegt, kunnen NB2, NB3 en NB4 niet werken.

**Aanpak in drie stappen:**
- **Positie** → niet-lineair stelsel $\vec{F}(\boldsymbol{\theta}, s) = \vec{0}$ oplossen via `fsolve` (Newton-Raphson)
- **Snelheid** → differentiëren naar de tijd → lineair stelsel $A\boldsymbol{\omega} = \mathbf{b}\dot{s}$ oplossen via `linalg.solve`
- **Versnelling** → nog eens differentiëren → $A\boldsymbol{\alpha} = \mathbf{C}(\boldsymbol{\omega}, \ddot{s})$ — dezelfde matrix $A$!

### 2. Belangrijke woordenschat & concepten

| Term | Wat het is | Intuïtief |
|------|-----------|-----------|
| **Schuiver** | De ene invoer: beweegt langs de mast | De "hendel" die de paraplu open/dichtklapt |
| **Punt K** | Uiteinde van de buitenste rib (link 8) | Het punt dat het verste beweegt — de "tip" van de paraplu |
| **Sluitingsvergelijking** | Eis dat een gesloten keten terugkomt waar ze begon | Vectorsom over een lus = 0 |
| **Matrix A (6×6)** | Coëfficiëntenmatrix snelheids-/versnellingsstelsel | Hoe hard elke stang draait als de schuiver beweegt — hangt af van de hoeken |
| **Conditiegetal cond(A)** | Gevoeligheid: hoe sterk vertaalt schuiverfout naar hoekfouten | Laag = robuust; hoog = gevoelig; → ∞ = dood punt |
| **Dood punt** | Configuratie waar twee stangen in elkaars verlengde liggen | Motor heeft geen greep meer, hoeksnelheid → ∞ |
| **Mobiliteitsformule (Grübler-Kutzbach)** | $M = 3(n-1) - 2f_1 - f_2$ | Telt hoeveel motoren nodig zijn: M = 1 voor onze paraplu |
| **Smooth-345 / Smooth-4567** | Polynomiale bewegingsprofielen voor de schuiver | Zachte start/stop: geen schokken aan begin en einde |
| **Condition-aware profiel** | Profiel dat trager beweegt waar cond(A) groot is | Preventief vertragen in de gesloten beginstand |
| **Branch tracking** | Startpunt voor `fsolve` = vorige oplossing + lineaire predictie | Voorkomt dat de solver naar de "andere" stangenstand springt |
| **Centripetale termen** | $-r\cos\theta \cdot \dot\theta^2$ in het rechterlid C | Gevolg van differentiëren van de snelheidsmatrix — staan rechts, niet in A |

**Drie vectorlussen** → 6 vergelijkingen voor 6 onbekende hoeken $\theta_3$ t/m $\theta_8$.

### 3. Resultaten & interpretatie

| Uitkomst | Waarde |
|----------|--------|
| Schuiverbereik | 1,8750 m (gesloten) → **0,1250 m** (open) |
| Slag | **1,7500 m** |
| Gewenste openingstijd | 14,00 s |
| Effectieve openingstijd | **30,03 s** (verlengd door condition-aware vertraging) |
| Hold-tijd | 4,00 s |
| Totale cyclustijd | **34,05 s** |
| Max schuiversnelheid | **0,2495 m/s** |
| Max schuifersnelling | **0,0627 m/s²** |
| Max snelheid punt K | **0,3763 m/s** bij s = 1,57 m |
| Max versnelling punt K | **0,1171 m/s²** bij s = 1,84 m |
| cond(A): min / gem / max | **4,70 / 35,02 / 68,36** |
| Hoogste cond(A) bij | s = 1,8750 m (gesloten stand) |
| Kritische zones (cond > 1000) | **0** — geen enkel dood punt in het werkbereik |
| Snelheidsfactor gesloten stand | **0,250** bij s = 1,875 m → 4× trager |
| Max sluitingsfout (fsolve) | **5,779 × 10⁻¹³ m** — machineprecisie |
| Snelheidsresidu $\|A\omega - B\|$ | **8,2 × 10⁻¹⁷** |

**Grafieken:** De hoeken $\theta_i(s)$ zijn gladde curven. De snelheid van K piekt in het midden van de slag. Het conditiegetal is hoogst bij de gesloten beginstand (s = 1,85 m) en daalt naarmate de paraplu opengaat.

**Sleutelinzicht:** De gesloten beginstand is het gevoeligst (cond = 68,4), niet het gevaarlijkst (ver van 1000). Het condition-aware profiel vertraagt de schuiver daar preventief, waardoor de openingstijd verlengde van 14 naar 30,03 s.

---

## Notebook 2 – Inverse dynamica (inertie only)

### 1. Algemeen overzicht

**Wat:** Alle gewrichtskrachten en de aandrijfkracht $F_s$ berekend op basis van de bekende beweging — zonder zwaartekracht of wrijving. Puur inertieel.

**Waarom:** De inertiekrachten afzonderlijk isoleren maakt validatie eenvoudig en legt het belang ervan bloot t.o.v. andere bijdragen.

**Aanpak:** 7 links × 3 Newton-Euler vergelijkingen = **21 vergelijkingen, 21 onbekenden**, per tijdstap lineair stelsel $A_{21\times21}\mathbf{w} = \mathbf{b}$ oplossen. Rechterlid = inertiële resultanten.

### 2. Belangrijke woordenschat & concepten

| Term | Wat het is | Intuïtief |
|------|-----------|-----------|
| **Inverse dynamica** | Beweging gegeven → krachten berekend | Omgekeerde van simuleren |
| **Newton-Euler vergelijkingen** | $\sum F = ma$, $\sum M_{cg} = J\alpha$ per link | Drie vergelijkingen per link, voor het zwaartepunt |
| **A (21×21)** | Coëfficiëntenmatrix van de onbekende krachten | Totaal andere matrix dan de kinematische A — zelfde naam, ander ding! |
| **Gewrichtskrachten** | Krachten in de scharnieren | Intern — vallen weg bij globale controle (actie = -reactie) |
| **Aandrijfkracht $F_s$** | Kracht van de actuator op de schuiver | $F_s = -F_{act,y}$ (minteken door richting van s naar beneden) |
| **Onbalanskracht** | Kracht die het mechanisme op zijn frame overdraagt | $\vec{F}_{shak} = -(\vec{F}_A + \vec{F}_C)$ |
| **Traagheidsmoment $J_{cg}$** | Weerstand van een staaf tegen draaien om zijn middelpunt | $J = mL^2/12$ voor uniforme staaf |
| **Puntmassa K (3 kg)** | Extra massa op uiteinde van link 8 | Stelt het doek + randprofiel voor |
| **Poeliekoppel $T$** | $T = F_s \cdot r / \eta$ | $r$ = poelieradius (25 mm), $\eta$ = rendement tandriem + reductiekast |
| **Globale krachtenbalans** | Som externe krachten = totale $\sum m\vec{a}_{cg}$ | Sterke verificatie: fout < 10⁻¹⁵ N ✓ |

### 3. Resultaten & interpretatie

| Massa | Waarde |
|-------|--------|
| Link 2 (schuiver) | 1,500 kg |
| Links 3–8 (stangen, $\rho_l = 0,55$ kg/m) | samen ~2,00 kg |
| Puntmassa K (doek) | 3,000 kg |
| **Totaal** | **7,500 kg** |

| Uitkomst | Waarde |
|----------|--------|
| Max $F_s$ (inertie only) | **2,632 N** — extreem klein |
| Max onbalanskracht | **0,907 N** |
| Max onbalansmoment | **1,424 Nm** |
| Globale balanscontrole | **1,1 × 10⁻¹⁵ N** ✓ |
| $A_{\max}$ (inertie only) | **0,3879 J** |
| Max poelietoerental | **~95 rpm** ($= \frac{0{,}2495}{2\pi \times 0{,}025} \times 60$) — motor na 60:1 reductie: zie NB4 |

**Grafieken:** $F_s(t)$ schommelt rond nul met kleine pieken bij opstart en stop — bewijst dat inertie verwaarloosbaar is.

**Sleutelinzicht:** De inertiekrachten zijn ~24× kleiner dan de gravitatielast (NB3). Dit is een quasi-statisch mechanisme — zo traag dat massa's bijna in rust zijn.

---

## Notebook 3 – Inverse dynamica met zwaartekracht & wrijving

### 1. Algemeen overzicht

**Wat:** Dezelfde inverse-dynamica aanpak als NB2, nu met zwaartekracht en wrijving. Geeft de realistische aandrijfkracht, vermogensvereisten en motordimensionering.

**Waarom:** Zwaartekracht domineert (92%). Zonder NB3 weet je niet hoe groot de motor moet zijn.

**Aanpak:**
- Bekende krachten (zwaartekracht, wrijving) gaan naar het **rechterlid** — matrix $A$ en onbekenden blijven gelijk
- Wrijving is impliciet → **vaste-puntsiteratie** (max 20 iteraties, tolerantie 10⁻⁸, convergeert in 8–12)
- Drie oplossingen apart: inertie / + zwaartekracht / + wrijving → decompositie per bijdrage
- Arbeid-energiestelling als validatie: $P_{act} = \dot{E}_{kin} + P_{zwk} + P_{wrijving}$

### 2. Belangrijke woordenschat & concepten

| Term | Wat het is | Intuïtief |
|------|-----------|-----------|
| **Coulombwrijving** | $F_{wrij} = -\mu N \cdot \tanh(\dot{q}/v_\epsilon)$ | Constante wrijving evenredig met normaaldruk, altijd tegen beweging |
| **Schuiverwrijving** | Wrijving van schuiver langs mast, $\mu = 0,05$ | Normale kracht = horizontale geleidingsreactie $R_{Ax}$ |
| **Scharnierwrijving** | Wrijvingsmoment op pennen, $\mu_{pin} = 0,05$, $r_{pin} = 6$ mm | Geeft moment op twee verbonden links, geen kracht |
| **Vaste-puntsiteratie** | Los op zonder wrijving → schat wrijving → herhaal | Nodig omdat wrijving afhangt van onbekende normaaldruk |
| **Houdkracht $F_{hold}$** | Kracht nodig om statisch op positie te houden | Enkel zwaartekracht, geen inertie |
| **Tandriem (niet-zelfremmend)** | Riem houdt positie niet vanzelf — aparte rem verplicht | Anders dan een spindel: geen zelfremmende werking bij axiale last |
| **Arbeid-energiestelling** | $P_{act} = \dot{E}_{kin} + P_{zwk} + P_{wrijving}$ | Validatie: motorvermogen = som van alle bijdragen |
| **Arbeids-surplus $A_{max}$** | $\max A_\theta - \min A_\theta$, $A_\theta = \int(P-\bar{P})dt$ | Energiebuffer die motor of condensator moet leveren voor pieken |
| **Steunreacties A en C** | Lokale krachten in scharnieren van schuiver en frame | Veel groter dan netto framekracht — altijd per scharnier dimensioneren! |

### 3. Resultaten & interpretatie

**Krachtdecompositie (bij openen):**

| Bijdrage $F_s$ | Waarde | % |
|----------------|--------|---|
| Zwaartekracht | **63,43 N** | **83%** |
| Schuiverwrijving | **38,32 N** | 50% |
| Inertie | **3,17 N** | 4% |
| Scharnierwrijving | **1,80 N** | 2% |
| **Totaal max** | **76,06 N** | — |

| Andere sleutelwaarden | Waarde |
|-----------------------|--------|
| RMS $F_s$ tijdens beweging | **66,98 N** |
| Piek actuatorvermogen | **38,75 W** |
| Netto mechanische energie | **119,81 J** |
| Arbeids-surplus $A_{max}$ | **74,27 J** |
| Max rem/regeneratief vermogen | **0,00 W** |
| P_avg / P_peak / P_rms | 3,51 W / 16,79 W / 6,44 W |
| Piek poeliekoppel uitgang (× 1,5) | **4,39 Nm** ($= 1{,}5 \times 76{,}06 \times 0{,}025 / 0{,}65$) |
| RMS poeliekoppel uitgang (× 1,5) | **3,85 Nm** |
| Aanbevolen nominaal motorvermogen | **≥ 9,7 W** |
| Aanbevolen piekvermogencapaciteit | **≥ 21,8 W** |
| Piek poelietoerental uitgang | **~95 rpm** (motor na 60:1 reductie: ~5718 rpm → zie NB4) |

**Statische houdanalyse:**

| Positie | s (m) | \|F\_hold\| (N) | Wrijvingsgrens (N) |
|---------|-------|----------------|-------------------|
| Open | 0,125 | **30,75** | **63,78** |
| Half | 0,564 | 61,97 | 13,51 |
| Half | 1,003 | 63,07 | 6,74 |
| Half | 1,439 | 63,33 | 3,57 |
| Gesloten | 1,875 | 63,43 | 0,19 |

**Framebelasting:**

| Grootheid | Waarde |
|-----------|--------|
| Totaal modelgewicht | **73,57 N** |
| Netto framekracht max | **73,95 N** |
| Steunreactie schuiver max (norm) | **797,85 N** |
| Steunreactie C max (norm) | **798,40 N** |

**Grafieken:** $F_s(t)$ loopt grotendeels rond 60-75 N, met een piek bij de hogere wrijvingsreacties. Arbeid-energiestelling: fout rond $2 \times 10^{-4}$ W.

**Sleutelinzichten:**
- Zwaartekracht domineert de nuttige last; schuiverwrijving kan lokaal groot worden door de hoge zijreactie
- Regeneratief vermogen = 0: bij openen moeten massa's omhoog, zwaartekracht werkt altijd tégen
- Wrijving is geen betrouwbaar houdcriterium: in gesloten stand is de theoretische marge bijna nul → aparte rem verplicht (tandriem is niet-zelfremmend)
- Steunreacties (~798 N bij schuiver en C) >> netto framekracht (74 N): altijd per scharnier/geleiding dimensioneren

---

## Notebook 4 – Aandrijfarchitectuur (tandriem + motor + rem)

### 1. Algemeen overzicht

**Wat:** Keuze en dimensionering van de volledige aandrijving: **tandriem/kabel** over een poelie, aangedreven door een **24 V DC/BLDC motor met reductiekast**, en een **aparte rem** voor de houdfunctie.

**Waarom:** NB3 geeft mechanische belasting. NB4 vertaalt die naar componentkeuze. De tandriem heeft geen zelfremmende werking — een aparte rem is verplicht.

**Aanpak:** Varieer poelieradius en reductie om de combinatie te vinden die tegelijk koppelgrens (≤ 12 Nm) en snelheidslimiet (≤ 120 rpm piek) respecteert.

### 2. Belangrijke woordenschat & concepten

| Term | Wat het is | Intuïtief |
|------|-----------|-----------|
| **Poelieradius $r$** | Straal van het wiel waarover de riem loopt | Groter → meer koppel, lager toerental |
| **Reductie 60:1** | Motor draait 60× sneller dan de poelieuitgang | Kleine snelle motor drijft langzame poelie aan via tandwielkast |
| **Efficiëntie $\eta = 0,65$** | Vermogensverlies riem + reductiekast | 35% gaat verloren als warmte |
| **Ontwerplijnkracht 200 N** | Praktische ondergrens voor de riemkracht | Veel hoger dan berekende 70 N — robuustheid voor slijtage, wind, nood |
| **Aparte rem** | Elektromagnetische rem aan reductoruitgang | Houdt schuiver op elke positie vast |
| **Remkoppel** | $T_{rem} = \text{SF} \times |F_{hold}| \times r$ | Veiligheidsgrens × houdkracht × poeliestraal |
| **Tijdschaalfactor $\lambda$** | Hoe snel het traject wordt afgelegd ($\lambda < 1$ = sneller) | Piekvermogen schaalt als $1/\lambda$; zwaartekrachtarbeid onveranderd |

**Poelie-formules:**
$$T_{uitgang} = \frac{F_{ontwerp} \cdot r}{\eta} \qquad n_{uitgang} = \frac{v_{schuiver}}{2\pi r}$$

**Remkoppel uitgang (max):**
$$T_{rem} = \text{SF} \times |F_{hold,max}| \times r = 2{,}0 \times 63{,}43 \times 0{,}025 = \mathbf{3{,}17\,\text{Nm}}$$

### 3. Resultaten & interpretatie

**Poelieradius-tabel:**

| r (mm) | avg rpm | piek rpm | T_drive (Nm) | T_rem (Nm) | Status |
|--------|---------|----------|-------------|-----------|--------|
| 15 | — | ~159 | 3,08 | 1,90 | niet haalbaar (< 20 mm) |
| 20 | ~19 | ~119 | 6,15 | 2,54 | niet haalbaar (piek > 120 rpm) |
| **25** | **~25** | **~95** | **7,69** | **3,17** | **gekozen** |
| 30 | ~19 | ~80 | 9,23 | 3,81 | haalbaar |
| 40 | ~14 | ~60 | 12,31 | 5,08 | haalbaar maar koppel = max |
| 50 | ~11 | ~48 | 15,38 | 6,34 | koppel overschreden |
| 60 | ~9 | ~40 | 18,46 | 7,61 | koppel overschreden |

**Reductieverhouding-tabel:**

| Reductie | Motor avg rpm | Motor piek rpm | Motor koppel (Nm) | Status |
|----------|--------------|----------------|-------------------|--------|
| 25:1 | ~560 | ~2383 | 0,410 | haalbaar |
| 40:1 | ~896 | ~3813 | 0,256 | haalbaar |
| **60:1** | **~1344** | **~5719** | **0,171** | **gekozen** (hoogste haalbare) |
| 75:1 | — | ~7148 | — | te snel (> 6000 rpm) |
| 100:1 | — | ~9530 | — | te snel |

**Finale ontwerpwaarden:**

| Parameter | Waarde |
|-----------|--------|
| Poelieradius | **25 mm** |
| Reductie | **60:1** |
| Efficiëntie $\eta$ | **0,65** |
| Veiligheidsfactor | **2,0** |
| Piek lijnkracht (operationeel) | **76,06 N** |
| Ontwerplijnkracht (met vloer) | **200,0 N** |
| Ontwerp aandrijfkoppel uitgang | **7,69 Nm** → klasse ≥ 8 Nm |
| Max remkoppel uitgang | **3,17 Nm** → klasse ≥ 3,17 Nm |
| Max remkoppel motoras | **3,17 / (60 × 0,75) ≈ 0,070 Nm** |
| Piek-uitgangstoerental | **~95 rpm** |
| Gemiddeld uitgangstoerental | **~25 rpm** |
| Geschatte motorpieksnelheid | **~5718 rpm** |
| Piek motor-ingangsvermogen ontwerp | **51,7 W** |
| Aanbevolen motorvermogen klasse | **~77,5 W** |
| Max lokale zijreactie schuiver | **~797,26 N** (uit NB3) |
| Arbeids-surplus aandrijving | **74,27 J** |
| Resolutie motorencoder na reductie | **~0,00064 mm/count** |
| Dominante precisiefout | **mechanische speling ~1 mm** (encoder veel kleiner) |

**Tijdschaalanalyse:**

| Factor $\lambda$ | Beweegtijd (s) | Piek \|$F_s$\| (N) | Piek P (W) | Pos. arbeid (J) | $A_{max}$ (J) |
|------------------|---------------|---------------------|-----------|-----------------|---------------|
| 0,60 (sneller) | ~17,9 | **75,95** | **27,82** | ~119,8 | **74,03** |
| **1,00 (huidig)** | **29,85** | **76,06** | **16,79** | **119,81** | **74,27** |
| 1,50 (trager) | ~44,8 | **76,10** | **11,21** | ~119,8 | **74,34** |
| 2,00 (trager) | ~59,7 | **76,11** | **8,42** | ~119,8 | **74,37** |

**Houdanalyse NB4:**

| Positie | s (m) | \|F\_hold\| (N) | T\_rem uitgang (Nm) | T\_rem motoras (Nm) |
|---------|-------|----------------|---------------------|---------------------|
| Open | 0,125 | 30,75 | 1,538 | 0,034 |
| Halfopen | ~1,00 | ~63,07 | 3,153 | 0,070 |
| Gesloten | 1,875 | 63,43 | 3,172 | 0,071 |

**Grafieken:** Staafdiagram poelieradii vs. koppel/toerental — r = 25 mm is de enige die zowel ≤ 120 rpm als ≤ 12 Nm haalt. Energieschaalcurve: positieve arbeid ~constant ongeacht $\lambda$; piekvermogen daalt sterk bij trager bewegen.

**Energiekost:**
- Per slag (η = 0,65): 119,81 / 0,65 ≈ 184 J ≈ 0,000051 kWh
- Per jaar (200 d × 2 slagen): ≈ 0,020 kWh ≈ **€0,006/jaar** — volledig verwaarloosbaar
- Standby controller (~2 W continu) verbruikt >400× meer dan de mechanische aandrijving

**Sleutelinzichten:**
- Tandriem heeft geen zelfremmende werking → aparte rem altijd verplicht
- Trager bewegen spaart piekvermogen (× 0,48 bij λ=2) maar niet de totale energie (zwaartekrachtarbeid ≈ constant)
- Aanbevolen motor ~77,5 W is veel groter dan ~10 W continu mechanisch nodig: veiligheid + efficiëntieverlies domineert
- De dominante precisiefout is mechanische speling (1 mm), niet de encoder (0,00064 mm/count)

---

## Verplichte volgorde: NB1 → NB2 → NB3 → NB4

Elke notebook laadt het `.npz`-bestand van de vorige. NB1 niet opnieuw draaien na een geometriewijziging = alle latere resultaten werken op verouderde kinematica.

| Parameterwijziging | NB1 | NB2 | NB3 | NB4 |
|-------------------|:---:|:---:|:---:|:---:|
| Wrijving µ | ✗ | ✗ | ✓ | ✓ |
| Massa K | ✗ | ✓ | ✓ | ✓ |
| Openingstijd $t_{move}$ | ✓ | ✓ | ✓ | ✓ |
| Poelieradius $r$ | ✗ | ✗ | ✗ | ✓ |
| Stanglengtes | ✓ | ✓ | ✓ | ✓ |
