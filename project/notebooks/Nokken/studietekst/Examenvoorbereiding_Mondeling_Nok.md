# Examenvoorbereiding Mondeling – Nokkenmechanisme

> De prof kiest 4 van 10 vragen (elk 2,5/10). Vaste bijvraag: *"Hoeveel kost de motor, en hoeveel kost het energieverbruik?"*
> Elke vraag hieronder heeft: een kort antwoord met **ingenieursinzicht**, "wat als"-varianten, en een tabel met de **grafiek(en)** die je in de notebook moet openen om je antwoord te bewijzen.

---

## Sleutelnummers — cheat sheet

| Grootheid | Waarde |
|-----------|--------|
| Cyclustijd / toerental | 0,500 s = **120 rpm = 12,57 rad/s** |
| Steekcirkelstraal R₀ | **50 mm** |
| Volgerrolstraal R_r | **15 mm** (nokbasiscirkel = 35 mm) |
| Excentriciteit | e = 0 mm |
| Maximale heffing | **50 mm** bij θ = 210° |
| Max snelheid volger | 0,482 m/s bij θ = 280° |
| Max versnelling volger | **±9,89 m/s²** bij θ ≈ 21,6° |
| Drukhoek | **21,86°** (max, θ≈147°) / −23,83° (min) — beide < 30° ✓ |
| Min kromtestraal pitch | **40,6 mm** > R_r = 15 mm ✓ |
| Equivalente massa volger | **m = 29 kg** |
| Veerstijfheid | **k = 25 000 N/m** (25 N/mm) |
| Relatieve demping | **ζ = 0,094** |
| Wrijvingscoëfficiënt geleiding | **µ = 0,10** |
| Veervoorspanning (min. nodig / gekozen) | **284,5 N → 300 N** |
| Minimale normaalkracht | **300 N** (= preload, tijdens dwell) |
| Externe belasting (max) | **1 250 N** (θ = 120°→170°) |
| Eigenfrequentie systeem | **ω_n = 29,4 rad/s = 4,67 Hz** |
| Snelheidsverhouding ω/ω_n | **0,43** (sub-resonant ✓) |
| Gemiddeld motorvermogen | **110 W** |
| Gemiddeld aandrijfkoppel | **8,78 Nm** |
| Energiefluctuatie ΔE | **65,9 J** |
| Vliegwieltraagheid (K = 0,05) | **8,35 kg·m²** |

### Bewegingssegmenten

| Segment | Nokhoek | Heffing | Wet |
|---------|---------|---------|-----|
| Heffing 1 | 10°→65° (55°) | 0→10 mm | 5e-gr. polynoom |
| Stilstand 1 | 65°→95° (30°) | 10 mm | Dwell |
| Heffing 2 | 95°→210° (115°) | 10→50 mm | 5e-gr. polynoom |
| **Terugkeer** | **210°→350° (140°)** | **50→0 mm** | **5e-gr. polynoom — maatgevend** |
| Stilstand 2 | 350°→10° (20°) | 0 mm | Dwell |

### Vergelijking bewegingswetten

| Wet | Cv | Ca | RMS acc | Cj | C²-continu |
|-----|----|----|---------|-----|------------|
| Harmonisch | 1,57 | 4,93 | 3,49 | 15,5 | **Nee** |
| Cycloïde | 2,00 | 6,28 | 4,44 | 39,5 | Ja |
| 3e-gr. polynoom | 1,50 | 6,00 | 3,47 | 12,0 | **Nee** |
| **5e-gr. polynoom (gekozen)** | **1,87** | **5,77** | 4,14 | **60,0** | **Ja** |

---

## Vraag 1 – Industrieel gebruik & ontwerpaanpassingen

### Kernantwoord

**Direct antwoord:** een nokkenmechanisme wordt industrieel gebruikt voor machines die met één motor honderden identieke heffing–stilstand–terugkeer-cycli per minuut moeten uitvoeren; voor een andere toepassing (cilindervuller, farma) pas je vooral toerental, heffing en externe last aan, terwijl de basisstructuur (heffing–pauze–terugkeer) hetzelfde blijft.

Een nokkenmechanisme zet continue rotatie om in een **exact herhaalbaar** verplaatsings- of zwenkprofiel — ideaal voor machines die honderden identieke cycli per minuut moeten draaien met één motor. Onze cyclus (heffing – stilstand – heffing – terugkeer – stilstand) is letterlijk de structuur van:

- **Kleptrein verbrandingsmotor**: heffing opent de klep, stilstand = gaswisseling, terugkeer sluit ze.
- **Verpakkingslijn/sealstation**: stilstand = persfase op het werkstuk (vergelijkbaar met onze 1 250 N externe last).
- **Naaimachine**: stilstand = draadvoeding aan de bovenzijde.

**Aanpassing — cilindervuller (farma)**: zelfde 3-fasen-structuur (vullen – pauze – terugkeer), maar:

| Parameter | Ons ontwerp | Vuller | Reden |
|-----------|-------------|--------|-------|
| Cyclustijd | 0,5 s (120 rpm) | 0,25 s (240 rpm) | hogere productiviteit |
| Heffing | 50 mm | 40 mm | kleiner doseringsvolume |
| Externe last | 1 250 N | ~50 N | vloeistof i.p.v. werkstuk |
| Veerpreload | 300 N | ~80 N | lagere massa/last |
| Aandrijving | op nokas | servomotor, direct | farma: schoon, geen olie |

![alt text](image-1.png)

**Kernconsequentie**: bij 2× toerental schalen versnellingen **kwadratisch**: 9,89 → ~40 m/s². Dat is de hefboom die veer en motor zwaarder maakt — niet de geometrie.

### Wat als...

- **2× toerental (240 rpm)**: a_max → ~40 m/s², F_inertie 287 N → ~1 150 N. De huidige preload van 300 N is dan volstrekt onvoldoende.
- **Motor via reductor i.p.v. direct op de nokas**: kleinere motor (koppel ÷ reductie), maar de vliegwieltraagheid op de *nokas* stijgt als 1/ω² — bij halvering van het noktoerental verviervoudigt I. Moderne keuze: servomotor direct op de nokas, geen vliegwiel.
- **Stilstand 30°→60°**: de overige segmenten moeten dezelfde heffing in minder graden realiseren → ds/dθ stijgt → drukhoek en vereiste R₀ stijgen, terugkeer krimpt naar ~110°.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---------|---------------------------|
| **Kinematisch volgerprofiel** s/v/a/j(θ) — *Nok_1* | Wijs de 3 fasen (heffing – stilstand – terugkeer – stilstand) aan en koppel ze aan de kleptrein/sealstation-analogie. |
| **Nokcontour** (vorm) — *Nok_1* | Toon de fysieke schijfvorm — exact wat in een machine gefreesd zou worden. |
| Animatie *(optioneel)* — *Nok_3* | Laat de nok één cyclus draaien zien om de drie fasen visueel te tonen. |

---

## Vraag 2 – Optimalisatie: precisie / energieverbruik / krachtgeneratie

### Kernantwoord

**Direct antwoord:** optimalisatie voor **precisie** zit in de fabricage (lagerspeling), niet in de berekening; voor **energieverbruik** zit 80% van de 110 W in de externe last (procesgebonden, niet optimaliseerbaar) en 15% in veerwrijving (wél optimaliseerbaar via k); voor **krachtgeneratie** is de drukhoek (22°) de beperkende factor en is de externe last van 1 250 N het dimensionerend geval.

**Precisie** wordt bepaald door fabricage, niet door berekening. Grootste foutbron: speling in het rolvolgerlager (10–30 µm). Onze keuze e = 0 (geen excentriciteit) maakt het ontwerp symmetrisch en goedkoop — bij onze lage drukhoek (22°) levert excentriciteit nauwelijks winst voor de extra productiekost.

**Energieverbruik** (P_gem = 110 W) splitst ruwweg in:

| Bijdrage | Aandeel | Optimaliseerbaar? |
|----------|---------|--------------------|
| Extern werk (1 250 N werkstuk) | ~80% | Nee — procesgebonden |
| Veerwrijving in geleiding | ~15% | Ja — kleinere k |
| Inertieverliezen | ~5% | Ja — lagere ζ |

Lagere veerstijfheid k verlaagt de wrijving, maar verhoogt het risico op zweefgedrag — **dé** trade-off van het veerontwerp.

**Krachtgeneratie**: hefboomeffect — waar ds/dθ klein is (bijna stilstand) levert hetzelfde motorkoppel veel kracht; waar ds/dθ groot is (pieksnelheid) net weinig. Bij α = 22° gaat slechts cos(22°) = 0,93 van de normaalkracht naar nuttige verticale kracht; de rest (sin 22° = 0,37) belast de geleiding zijdelings. De externe last van 1 250 N (θ ≈ 120°–170°) is het dimensionerend geval voor as en lagers.

### Wat als...

- **k halveren**: minder wrijvingsverlies, maar de preload (nu 300 N) moet herrekend worden voor zweefveiligheid.
- **R_r van 15→25 mm**: lagere contactdruk (langere levensduur), ondersnijdingsmarge daalt maar blijft positief (40,6 mm > 25 mm), zwaardere rol → meer inertie.
- **Normaalkracht verlagen zonder externe last te wijzigen**: vergroot R₀ (verlaagt α, cos α stijgt) of verleng het maatgevend segment.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---------|---------------------------|
| **Drukhoek α(θ)** — *Nok_1* | Wijs de piek α_max = 21,86° bij θ ≈ 147° aan — bepaalt zowel de wrijving als het kracht-rendement (cos α). |
| **Normaalkracht met componenten** — *Nok_2* | Wijs de 'extload'-curve (piek 1 250 N, θ ≈ 120°–170°) aan als dimensionerend voor as/lagers. |
| **Aandrijfkoppel & vermogen** — *Nok_2* | Toon de gemiddelde-vermogenlijn (110 W) als basis voor de 80/15/5%-energieverdeling. |

---

## Vraag 3 – Vliegwiel: afwegingen

### Kernantwoord

**Direct antwoord:** de vliegwielafweging is dat het *benodigde* vliegwiel (I = 8,35 kg·m², ~185 kg schijf) bij 120 rpm onpraktisch zwaar is — de echte keuze is dus niet "welke K", maar "mechanisch vliegwiel vs. servomotor zonder vliegwiel", waarbij de servo-optie over de levensduur (TCO) goedkoper uitkomt.

ΔE = 65,9 J is het maximale verschil tussen wat de motor constant levert en wat het mechanisme variabel vraagt. Dat geeft I_vliegwiel = **8,35 kg·m²** — bij 120 rpm enorm: een stalen schijf van r = 0,3 m zou **185 kg** wegen (67 kg bij r = 0,5 m). Reden: bij laag toerental is de roterende kinetische energie per kg·m² klein, dus is veel massa nodig voor dezelfde energiebuffer.

| Keuze | Voordeel | Nadeel |
|-------|----------|--------|
| Kleiner K (0,02) | stabieler toerental | I = 25 kg·m² → 560 kg schijf |
| Groter K (0,10) | I = 4,2 kg·m², hanteerbaar | toerental schommelt ±5% |

**Moderne oplossing: servomotor zonder vliegwiel** — condensatoren in de drive bufferen de piekenergie elektrisch. Bij hoog toerental (1 200 rpm) zou I dalen naar 0,083 kg·m² (~2 kg schijf): vliegwielen zijn inherent zinvoller op snelle machines.

**TCO-argument**: klassiek (motor + ~100 kg vliegwiel) kost €400–1 000 eenmalig, maar €200/jaar onderhoud × 20 jaar = €4 500. Servo (€700–1 800) heeft geen vliegwielonderhoud → terugverdiend in 2–5 jaar, sneller bij continue (24/7) productie.

### Wat als...

- **K = 0,20 i.p.v. 0,05**: I daalt 4× naar 2,09 kg·m² (~46 kg schijf), maar toerental schommelt nu ±10% — onaanvaardbaar in farma/print.
- **2 identieke nokken op één as, in fase**: ΔE verdubbelt (131,8 J) → groter vliegwiel. **180° uit fase**: koppelpieken compenseren elkaar deels → ΔE daalt.
- **Externe belasting valt weg**: P_gem en ΔE dalen drastisch → de externe last is de échte driver van het vliegwielprobleem, niet de nok-kinematica zelf.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---------|---------------------------|
| **Cumulatieve energiefluctuatie E(θ)** — *Nok_2* | Wijs het verschil tussen het maximum en minimum van de curve aan = ΔE = 65,9 J. |
| **Aandrijfkoppel M(θ)** — *Nok_2* | Wijs de koppelpiek bij θ ≈ 120°–170° aan — de oorzaak van de energiefluctuatie. |

---

## Vraag 4 – Keuze bewegingstraject: afwegingen

### Kernantwoord

**Direct antwoord:** we kiezen het 5e-graads polynoom omdat het — samen met de cycloïde — als enige voldoet aan C²-continuïteit (geen sprong in a(θ), dus geen hamerslag); het overblijvend verschil met de cycloïde (Ca/Cj) is bij ons huidige, trage toerental verwaarloosbaar en dient vooral als marge voor toekomstige snelheidsverhoging.

**Waarom willen we géén sprong in a(θ)?** Niet als abstract netheidscriterium — via F ≈ ... + m·a/cosα is een sprong in a(θ) **direct** een sprong in de normaalkracht op het rolcontact. Bij 120 rpm passeert elke van de 5 segmentovergangen (θ = 10°, 65°, 95°, 210°, 350°) tweemaal per seconde. Een eindige krachtssprong daar is de "hamerslag": een micro-impact op lager en nokoppervlak, 10×/s. Dat onze curve dat NIET doet, is rechtstreeks zichtbaar in de normaalkrachtgrafiek van Nok_2: die loopt overal vloeiend, ook precies op de segmentgrenzen. In de kleptrein-analogie van Vraag 1 zou een sprong in a(θ) zich uiten als tikgeluid en versnelde klepzittingsslijtage bij elke cyclus — precies het probleem dat C²-continue bewegingswetten (zoals onze keuze) in de automotive industrie hebben weggewerkt t.o.v. oudere cirkelboog-/rechte-flank-nokken.

**Waarom dan niet harmonisch (Ca = 4,93 — zelfs lager dan onze 5,77)?** Omdat y''(0) ≠ y''(1) ≠ 0 voor het harmonisch profiel: de versnelling start/eindigt niet op nul. Aan de grens met een dwell (a = 0) ontstaat dus een eindige sprong, ondanks de lágere piekwaarde. Dezelfde reden velt het 3e-graads polynoom (laagste RMS = 3,47, ook y''(rand) ≠ 0). **Een lage piekversnelling is waardeloos als de curve niet glad aansluit op de buursegmenten** — dat, en niet "Ca zo laag mogelijk", is het echte criterium.

**5e-graads polynoom vs. cycloïde — eerlijke heroverweging**: bij beide geldt y''(0) = y''(1) = 0, dus beide zijn C²-continu — de "geen-hamerslag"-eis is voor ALLEBEI vervuld. Het overblijvende verschil is puur Ca (5,77 vs. 6,28) en Cj (60 vs. 39,5 — hier juist hóger voor het 5e-gr. polynoom). In absolute cijfers voor ons systeem (m = 29 kg, ω = 12,57 rad/s):

| | 5e-graads (gekozen) | Cycloïde |
|---|---|---|
| a_max | 9,89 m/s² | 10,79 m/s² |
| F_inertie,max | 287 N | 313 N (+9%) |

Dat verschil van **26 N** is amper 2% van de piekbelasting (externe last 1 250 N + veerkracht ~1 400 N, zie Vraag 8). Bij ons **trage** toerental (120 rpm = ω = 12,57 rad/s) verandert dit niets aan de dimensionering van veer, motor of as — **de cycloïde was hier evengoed geweest**. Wat het 5e-graads polynoom dan wél verdedigbaar maakt: a ∝ ω², dus dit verschil schaalt kwadratisch. Bij 240 rpm (Vraag 1) wordt het al ~100 N. De keuze is dus vooral een **marge voor toekomstige snelheidsverhoging**, geen harde eis bij de huidige 120 rpm. Het échte selectiecriterium — C²-continuïteit tegen hamerslag — scheidt enkel {5e-gr., cycloïde} van {harmonisch, 3e-gr.}.

### Wat als...

- **7e-graads polynoom (C³-continu)**: ook de ruk continu aan de randen, maar Ca ≈ 7,5 → F_inertie ≈ 29 × 9,89 × (7,5/5,77) ≈ 374 N bij ons toerental. Pas de moeite waard als de ruk-discontinuïteit zelf een probleem geeft — bv. bij hoger toerental of een flexibele aandrijflijn.
- **Cycloïde voor de terugkeer**: a_max 9,89 → 10,79 m/s², F_inertie 287 → 313 N — bij 120 rpm verwaarloosbaar (zie boven). De drukhoek verandert nauwelijks, want die volgt uit ds/dθ, niet uit a(θ).
- **Waarom is de terugkeer maatgevend?** 50 mm over 140° = hoogste ds/dθ → hoogste drukhoek → bepaalt R₀ = 50 mm (minimaal 46 mm vereist) — dit geldt voor élke C²-continue bewegingswet, niet specifiek voor het 5e-graads polynoom.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---------|---------------------------|
| **Kinematisch volgerprofiel** a(θ) — *Nok_1* | Wijs aan dat a(θ) bij elke segmentovergang (θ = 10°, 65°, 95°, 210°, 350°) vloeiend naar 0 loopt — geen sprongen. |
| **Normaalkracht met componenten** — *Nok_2* | Wijs aan dat de normaalkrachtcurve op diezelfde hoeken géén verticale sprong vertoont — het tastbare gevolg van C²-continuïteit (geen hamerslag). |
| **Kloomok-Muffley drukhoekanalyse, Seg4 (terugkeer)** — *Nok_1* | Wijs aan waar α_max de 30°-lijn kruist bij R₀ ≈ 46 mm — verklaart R₀ = 50 mm, onafhankelijk van de gekozen bewegingswet. |

---

## Vraag 5 – Frequentie-inhoud van het ontwerp

### Kernantwoord

**Direct antwoord:** de relevante frequenties zijn de aandrijffrequentie (2 Hz = 120 rpm) en de eigenfrequentie van het volger-veersysteem (4,67 Hz). De fundamentele zit veilig sub-resonant (r₁ = 0,43), maar de 2e harmonische van de aandrijving (4 Hz) ligt met r₂ = 0,86 gevaarlijk dicht bij resonantie — dát is de kritische frequentie.

De nok draait aan **2 Hz** (120 rpm) — de fundamentele aandrijffrequentie. Het volger-veersysteem heeft eigenfrequentie ω_n = 4,67 Hz. Verhouding op de fundamentele: r₁ = 0,43 → **sub-resonant ✓**.

**De 2e harmonisch (4 Hz) is kritisch**: r₂ = 0,86 → dynamische versterking DMF ≈ 3,3 (zonder demping zou dit 3,8 zijn). Daarom is **ζ = 0,094 bewust gekozen** — het beperkt deze versterking.

**Praktisch**: zweefgedrag treedt op zodra de versterkte dynamische kracht de veerpreload (300 N) overwint — de preload is de buffer.

**Snelheidsgrens**: bij 280 rpm zou r₁ = 1,0 (volledige resonantie op de fundamentele) → **de machine mag nooit boven ~260 rpm draaien**.

### Wat als...

- **Stijvere veer (k = 100 000 N/m)**: ω_n = 9,34 Hz → r₁ = 0,21, r₂ = 0,43 (beide veiliger), DMF daalt naar ~1,2. Maar: hogere normaalkracht → meer wrijving/vermogen, preload moet herrekend.
- **Zweefgedrag detecteren**: check of de minimale normaalkracht in Nok_2 onder 0 zakt; test door de preload tijdelijk te halveren.
- **3e harmonisch (6 Hz) minder gevaarlijk dan 2e**: r₃ = 1,28 (boven resonantie, DMF ≈ 1,4 vs. 3,3) én bevat het profiel van nature minder energie op de 3e harmonisch dankzij de gladde 5e-gr. polynoomvorm.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---------|---------------------------|
| **Aandrijfkoppel M(θ)** — *Nok_2* | Wijs op de niet-sinusoïdale vorm — dit bevat de hogere harmonischen (2 Hz, 4 Hz, ...) van het krachtsignaal. |
| **Normaalkracht met componenten** — *Nok_2* | Wijs het minimum van de totale curve aan (= preload = 300 N) als veiligheidsbuffer tegen de versterkte 2e harmonisch. |

---

## Vraag 6 – Dode punten (singulariteiten)

### Kernantwoord

**Direct antwoord:** nee — geen klassieke dode punten zoals TDC/BDC bij een kniehefboom. Wel bestaan er 4 analoge faalmodi, en in ons ontwerp hebben ze allemaal een ruime veiligheidsmarge:

| Faalmodus | Grens | Onze waarde | Marge |
|-----------|-------|-------------|-------|
| Zelfblokkering: µ\|tan α\| ≥ 1 | < 1 | 0,040 | factor 25 |
| Drukhoek: α → 90° | < 30° | 21,86° | 8° |
| Ondersnijding: ρ_pitch ≤ R_r | > 15 mm | 40,6 mm | factor 2,7 |
| Zweefgedrag: F_normaal ≤ 0 | > 0 N | 300 N (preload) | — |

**Ernstigste**: ondersnijding — de nok loopt fysiek vast en kan breken. Drukhoek/zelfblokkering degraderen geleidelijk (meer wrijving), zweefgedrag veroorzaakt slijtage door herhaald contactverlies.

**Bewust zweefgedrag als energiebesparing**: de 300 N preload genereert continue wrijving — 100% van de cyclus, ook tijdens de onbelaste retourslag (140° van 360°). Bewust contactverlies tijdens de retour zou ~7 W besparen → over 10⁸ cycli ~3 900 kWh, meer dan de prijs van de motor zelf. Voorwaarden: geen externe last tijdens retour, schokdemper bij hercontact, reproduceerbare hercontacthoek. In ons ontwerp niet toepasbaar (last kan ook tijdens retour actief zijn), maar het toont de afweging **contactzekerheid vs. wrijvingsenergie**.

**α = 0° is geen ideaal**: dat betekent ds/dθ = 0 = geen beweging. R₀ = 50 mm is exact de grens (46 mm vereist + kleine veiligheidsmarge).

### Wat als...

- **R₀ naar 40 mm (compacter)**: α_max ≈ 32–33° (net boven 30°) → meer zijwrijving, meer motorkoppel, snellere slijtage — niet catastrofaal maar ongewenst.
- **R_r naar 30 mm**: ondersnijdingsmarge daalt naar 10,6 mm (kleine productiefout kan lokaal ρ < R_r geven), en de zwaardere rol verhoogt massa/preload.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---------|---------------------------|
| **Drukhoek α(θ)** — *Nok_1* | Wijs α_max = 21,86° aan t.o.v. de 30°-grenslijn. |
| **Kromtestraal ρ_pitch & ρ_cam(θ)** — *Nok_1* | Wijs het minimum van ρ_pitch (40,6 mm) aan t.o.v. R_r = 15 mm — geen ondersnijding. |
| **Normaalkracht met componenten** — *Nok_2* | Wijs het minimum van de totale normaalkracht (= 300 N = preload) aan — blijft positief, geen zweefgedrag. |

---

## Vraag 7 – Onbalans-eigenschappen

### Kernantwoord

**Direct antwoord:** de onbalans-eigenschappen van dit mechanisme zijn tweeledig: (A) een kleine **roterende onbalans** van de nok zelf (~2,8 N bij 120 rpm, verwaarloosbaar) en (B) een dominante **oscillerende inertiakracht** van de volger (287 N bij 2 Hz) die op het frame inwerkt. Bij 120 rpm en een stijf gefundeerd frame vormt dit geen probleem.

Twee mechanismen spelen mee:

**A. Roterende onbalans van de nok**: de massa is asymmetrisch verdeeld (CM niet op de as) → centrifugaalkracht. Geschat ~2,8 N bij 120 rpm — verwaarloosbaar. Bij 1 200 rpm: ~280 N — dan wél balanceren nodig.

**B. Oscillerende inertiakracht van de volger** (dominant): F_inertie,max = m·a_max = 29 × 9,89 = **287 N**, oscillerend bij 2 Hz + harmonischen. Dit is de reactiekracht die op het frame inwerkt.

**Probleem?** Bij 120 rpm (2 Hz) en een goed gefundeerd frame (eigenfrequentie 20–100 Hz) geen resonantiegevaar. 287 N is een piekwaarde, kortstondig. Wordt kritisch bij een lichte of onvoldoende gestijfde opstelling.

### Wat als...

- **Nok balanceren**: materiaal wegfrezen aan de zware zijde of een tegengewicht toevoegen (CM op de as) = statisch balanceren; bij een brede cilindrische nok ook dynamisch balanceren.
- **Tegengewicht**: heft de roterende centrifugaalkracht op de lagers op, maar verandert niets aan de oscillerende volgerkracht (lineair, niet rotatoir).
- **Volger 29 → 100 kg**: F_inertie 287 → 989 N (bijna gelijk aan de externe last van 1 250 N) → preload moet drastisch omhoog én het frame krijgt ~1 000 N oscillerende belasting bij 2 Hz → ernstige trillingen. Massa reduceren is de slimste hefboom.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---------|---------------------------|
| **Kinematisch volgerprofiel** a(θ) — *Nok_1* | Wijs de piekwaarden ±9,89 m/s² aan bij de steilste segmentovergangen — hieruit volgt F_inertie = 287 N. |
| **Normaalkracht met componenten** — *Nok_2* | Wijs de aparte 'acc'-curve (inertiebijdrage) aan als de oscillerende belasting op het frame. |

---

## Vraag 8 – Relatieve invloeden van inertie, wrijving en stijfheid

### Kernantwoord

**Direct antwoord:** bij piekbelasting domineren externe last (~55%) en veerkracht (~40%) samen ~95% van de normaalkracht; inertie is significant maar tijdelijk (~10%), wrijving/demping is klein (~3%). De stijfheid (veer) bepaalt bovendien — via de zwaartekracht — de ondergrens van de preload, niet de piek-inertie.

Bij piekbelasting (θ ≈ 130°):

| Bijdrage | Grootte | Aandeel |
|----------|---------|---------|
| Externe belasting | 1 250 N | ~55% |
| Veerkracht (k·s + preload) | 1 300–1 550 N | ~40% |
| Inertiekracht (m·a) | max 287 N | ~10% (tijdelijk) |
| Demping (c·v) | max 77 N | ~3% |

Externe last + veerkracht domineren; inertie is fors maar kortstondig; demping is klein.

**De veer (k = 25 N/mm) heeft een dubbele rol**: (1) houdt het contact bij elke hoek (preload + k·s), (2) energiebuffer — laadt 31,25 J op tijdens de heffing en geeft dit terug bij de terugkeer, wat het piekkoppel daar verlaagt.

**Verborgen drivers van de preload**: zwaartekracht (F_g = 29 × 9,81 = 284 N, continu aanwezig) — de berekende minimale preload van 284,5 N ≈ m·g is **geen toeval**: de veer compenseert in de eerste plaats de zwaartekracht, niet de piek-inertie. Daarbovenop: continue Coulomb-wrijving (~24 N → ~5 W, altijd aanwezig). Samen bepalen ze de ondergrens van preload én basisverbruik — iets wat een puur inertieel model volledig mist.

### Wat als...

- **Wanneer wordt inertie dominant?** Boven ω ≈ 22,7 rad/s ≈ **217 rpm** overtreft m·a_max de gemiddelde veerkracht (925 N) → motor, veer en as moeten opnieuw gedimensioneerd.
- **µ van 0,10 → 0,30** (versleten geleiding): normaalkracht stijgt met 14% (factor 1/(1−µ\|tanα\|) = 1,14) → meer vermogen, snellere slijtage — een positieve terugkoppellus. Smering wordt kritisch.
- **Grote afwijking simulatie vs. meting?** Check eerst (1) speling/productietoleranties, (2) encoderresolutie (1 000 pulsen/omw → 0,36°, ruis versterkt sterk bij 2× differentiëren — een 14-bit encoder helpt), (3) directe krachtmeting via rekstrookjes/load cell.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---------|---------------------------|
| **Normaalkracht met componenten** — *Nok_2* | Wijs bij θ ≈ 130° de relatieve hoogte van de 'spring', 'extload', 'acc' en 'damping'-curves t.o.v. elkaar aan. |
| **Normaalkracht met componenten** — *Nok_2* | Wijs het minimum van de totale curve aan (tijdens dwell, waar acc = 0 en extload = 0) — dit ≈ m·g, en toont dat zwaartekracht de preload bepaalt. |

---

## Vraag 9 – Meerdere kopieën parallel aan dezelfde motor

### Kernantwoord

**Direct antwoord:** bij N parallelle kopieën op één gemeenschappelijke as schalen vermogen en koppel lineair (N × 110 W, N × 8,78 Nm); de fasering tussen de nokken bepaalt of de energiefluctuatie (en dus het vliegwiel) N× groter wordt (in fase) of net afvlakt (gefaseerd over 360°/N).

Meest realistisch: **N nokken op één gemeenschappelijke as**, elk met eigen volger/veer, synchroon draaiend. Vermogen en koppel schalen lineair: N × 110 W, N × 8,78 Nm.

**Fasering bepaalt het vliegwiel**:
- **In fase**: ΔE_totaal = N × 65,9 J → vliegwiel N× groter, koppelpieken vallen samen → motor moet N× de piekvraag aankunnen.
- **Gefaseerd over 360°/N**: koppelpieken vullen elkaars dalen op → ΔE daalt drastisch (zelfde principe als een meercilindermotor met gefaseerde nokken). Bij N = 2 (180°) kan ΔE met ~50% dalen.

As en lagers moeten meeschalen met N (torsie + buiging door N normaalkrachten).

### Wat als...

- **2 nokken, 180° gefaseerd**: de piek van nok 2 valt in de terugkeerfase van nok 1 (waar de veer juist energie teruggeeft) → gedeeltelijke compensatie. Kwantitatief: M₁(θ) + M₂(θ+180°) optellen.
- **Wat blijft onveranderd?** Kinematica (s, v, a per nok), drukhoek, ondersnijding en het veerontwerp per volger blijven identiek — alleen motor, as en vliegwiel schalen mee.
- **6 nokken**: as faalt eerst — 6 × 8,78 = 52,7 Nm torsie + buiging door 6 normaalkrachten → diameter moet groter.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---------|---------------------------|
| **Aandrijfkoppel M(θ)** — *Nok_2* | Wijs de geconcentreerde piek bij θ ≈ 120°–170° aan en leg uit hoe een 180°-verschoven kopie van deze curve de dalen zou opvullen. |

---

## Vraag 10 – Parameterwijziging: voorspelling en verificatie

### Kernantwoord

**Direct antwoord:** de aanpak is steeds dezelfde drie stappen: **(1)** een parameter wijzigen, **(2)** op basis van fysisch inzicht voorspellen wat er met drukhoek/krachten/vermogen/resonantie gebeurt, en **(3)** dat numeriek bevestigen in Nok_1/Nok_2. De tabel hieronder geeft zes representatieve parameters — inclusief R₀, R_r, de drukhoekgrens (α) en het toerental — telkens met wat **verandert**, wat **(ongeveer) constant blijft**, en een korte verklaring.

| Parameter die je wijzigt | Wat verandert | Wat blijft (ongeveer) constant | Korte uitleg |
|---|---|---|---|
| **Steekcirkelstraal R₀**<br>(`50 mm → 40 mm`, compacter) | • Drukhoek α_max: `21,86° → ~32-33°` (boven `30°`-grens)<br>• Zijwrijving en motorkoppel stijgen, snellere slijtage | • Heffing (`50 mm`) en bewegingswet (5e-gr. polynoom)<br>• Toerental, m, k | Kleinere R₀ = minder hefboomarm voor dezelfde heffing → steilere drukhoek; `R₀ ≈ 46 mm` is de ondergrens voor `α < 30°`. → *Nok_1*, Kloomok-Muffley-analyse herrekenen. |
| **Volgerrolstraal R_r**<br>(`15 mm → 25/30 mm`) | • Contactdruk daalt (langere levensduur)<br>• Ondersnijdingsmarge daalt: `25,6 mm → 15,6 mm` (`R_r=25`) / `→ 10,6 mm` (`R_r=30`)<br>• Rolmassa/inertie stijgt licht | • Drukhoek α(θ) — hangt af van R₀ en `ds/dθ`, niet van R_r<br>• Heffing, bewegingswet, toerental | Grotere rol = lagere contactspanning, maar `ρ_pitch,min = 40,6 mm` blijft vast → minder ondersnijdingsmarge en iets meer massa. → *Nok_1*, ondersnijdingscontrole `ρ_pitch > R_r`. |
| **Toerental**<br>(`120 → 180 rpm`) | • a_max: `9,89 → 22,3 m/s²` (`ω² = 2,25`)<br>• F_inertie,max: `287 → 646 N` (`×2,25`)<br>• I_vliegwiel: `8,35 → 3,71 kg·m²` (`×0,44 = 1/ω²`)<br>• r₁: `0,43 → 0,64`; r₂: `0,86 → 1,28` | • s(θ), drukhoek α(θ), kromtestraal ρ(θ) — pure geometrie | `a ∝ ω²` — geometrie (Nok_1) en snelheid (Nok_2) zijn volledig ontkoppeld; enkel `T_cyclus` aanpassen. → *Nok_2*, `T_cyclus = 1/3`. |
| **Drukhoekgrens (α)**<br>(`30° → 35°` of `→ 25°`) | • Minimaal toelaatbare R₀ verschuift (`≈ 46 mm` bij `30°`; lager bij `35°`, hoger bij `25°`)<br>• Geeft ontwerpvrijheid om R₀ te verkleinen (of dwingt R₀ te vergroten) | • Heffing, bewegingswet, toerental — puur een ontwerpgrens, geen fysische wijziging | `30°` is een vuistregel tegen overmatige zijwrijving/zelfblokkering (huidige marge: `21,86°` vs `30°`); een ruimere grens staat een kleinere nok toe, ten koste van wrijving/slijtage. → *Nok_1*, grenslijn in Kloomok-Muffley-grafiek verschuiven. |
| **Veerstijfheid k**<br>(`25 000 → 100 000 N/m`) | • ω_n: `4,67 → 9,34 Hz`; r₁: `0,43 → 0,21`; r₂: `0,86 → 0,43` (beide veiliger)<br>• DMF (2e harmonisch): `~3,3 → ~1,2`<br>• Normaalkracht/wrijving/motorvermogen stijgen | • s(θ), v(θ), a(θ), drukhoek, ondersnijding — kinematica blijft identiek | Stijvere veer duwt beide resonantieverhoudingen weg van `1` (veiliger tegen zweefgedrag), maar verhoogt continue veerkracht/wrijvingsvermogen. → *Nok_2*, `k` aanpassen en normaalkracht/vermogen herrekenen. |
| **Aantal nokken N parallel**<br>(`1 → 2`, in fase vs. `180°` gefaseerd) | • P_gem, M_gem: `×N` (`110→220 W`, `8,78→17,56 Nm`)<br>• ΔE/vliegwiel: `×N` in fase (`65,9→131,8 J`) of `~50%` minder bij `180°` gefaseerd<br>• As/lagers moeten meeschalen | • s(θ), v(θ), a(θ), drukhoek, ondersnijding en veerontwerp per volger — per-nok kinematica blijft identiek | Extra nokken schalen vermogen/koppel lineair; fasering bepaalt of de energiefluctuatie meeschaalt of net afvlakt — analoog aan een meercilindermotor. → *Nok_2*, `M₁(θ) + M₂(θ+180°)` optellen. |

**Detail bij parameter 3 (toerental) — exacte cijfers:**

| Grootheid | 120 rpm | 180 rpm | Factor |
|---|---:|---:|---:|
| a_max | `9,89 m/s²` | `22,3 m/s²` | `ω² = 2,25` |
| F_inertie,max | `287 N` | `646 N` | `2,25` |
| Preload (geschat) | `300 N` | `~500 N` | — |
| I_vliegwiel | `8,35 kg·m²` | `3,71 kg·m²` | `1/ω² = 0,44` |
| r₁ (sub-resonant?) | `0,43` | `0,64` (nog OK) | — |
| r₂ (2e harmonisch) | `0,86` (kritisch) | `1,28` (boven resonantie, minder gevaarlijk) | — |

### Wat als...

- **Moet Nok_1 herrekend worden bij toerentalwijziging?** Nee — s(θ), drukhoek en kromtestraal zijn puur geometrisch; rpm beïnvloedt enkel v en a (via ω).
- **Welke parameter verandert de drukhoek het meest?** R₀ (kleiner → α stijgt sterk, zie tabel rij 1). Toerental heeft géén effect — geometrie en snelheid zijn volledig ontkoppeld.
- **Maximale veilige snelheid bij de huidige preload (300 N)?** ω_max ≈ 12,9 rad/s ≈ **123 rpm** — we zitten al exact op de grens. Eerst preload naar 500 N verhogen vóór sneller draaien.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---------|---------------------------|
| **Kinematisch volgerprofiel** s/v/a/j(θ) — *Nok_1* | Wijs aan dat s(θ) (vorm) ongewijzigd blijft, terwijl a(θ) en j(θ) na herberekening in Nok_2 met factor 2,25 resp. 3,375 schalen bij toerentalwijziging. |
| **Drukhoek α(θ) en Kloomok-Muffley-grenslijn** — *Nok_1* | Wijs aan hoe α_max verschuift bij een andere R₀ of een andere drukhoekgrens, en hoe dit de minimale R₀ bepaalt. |
| **Normaalkracht met componenten** — *Nok_2* | Wijs aan hoe de veerkracht-component (en dus de totale normaalkracht) verandert bij een andere `k`. |

---

## Vaste bijvraag – Motorkosten en energieverbruik

### Kernantwoord

**Direct antwoord:** een eenvoudige inductiemotor + vliegwiel kost €400–1 000 eenmalig maar heeft hoge onderhoudskosten; een servomotor zonder vliegwiel kost €700–1 800 maar is over de levensduur goedkoper. Het energieverbruik bedraagt ~€78–156/jaar (1 resp. 2 ploegen) bij 110 W gemiddeld vermogen.

| Grootheid | Waarde |
|-----------|--------|
| Gemiddeld vermogen | 110 W |
| Gemiddeld koppel | 8,78 Nm |
| Piekkoppel | ~15–20 Nm |
| Energiefluctuatie ΔE | 65,9 J |

**Klassiek** (inductiemotor 200 W + ~100 kg vliegwiel): **€400–1 000** eenmalig.
**Modern** (servomotor + drive, geen vliegwiel): **€700–1 800** — duurder, maar elimineert het vliegwiel, bespaart ruimte, geeft betere stopprecisie en vrije snelheidskeuze.

**Energieverbruik** (η = 0,85 → 130 W elektrisch): 1 ploeg (2 000 h/jaar) = 260 kWh/jaar ≈ **€78/jaar**; 2 ploegen (4 000 h/jaar) = 520 kWh/jaar ≈ **€156/jaar**.

**Veer als energiebuffer**: 31,25 J/cyclus opgeslagen tijdens heffing en teruggegeven bij terugkeer — bijna de helft van de externe werkenergie per cyclus, wat het piekkoppel in de terugkeerslag verlaagt.

### Bijvragen (kort)

- **Motorkoppel uit aandrijfkracht**: M = F_nok,vert × ds/dθ. Bij piekbelasting: ≈ 1 800 N × 0,014 m/rad ≈ **25 Nm** (piekkoppel).
- **Waarom relatief hoog energieverbruik?** Continu draaiende machine (432 000 cycli/uur bij 120 rpm) + externe last 1 250 N over 50 mm → P_ext ≈ 100 W. Dit is nuttig arbeidsvermogen, geen verspilling.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---------|---------------------------|
| **Aandrijfkoppel & vermogen** — *Nok_2* | Wijs de gemiddelde lijnen (M_gem = 8,78 Nm, P_gem = 110 W) en de piekwaarde van M(θ) aan. |

---

## Examensamenvatting: kritische grenzen

| Check | Grens | Resultaat |
|-------|-------|-----------|
| Drukhoek | α < 30° | 21,86° ✓ |
| Ondersnijding | ρ_pitch > R_r | 40,6 mm > 15 mm ✓ |
| Zweefgedrag | F_normaal > 0 | F_min = 300 N (preload) ✓ |
| Zelfblokkering | µ\|tan α\| < 1 | 0,040 ✓ |
| Resonantie | ω/ω_n < 1 | r₁ = 0,43, r₂ = 0,86 ✓ |
| Preload | ≥ benodigd minimum | 300 N ≥ 284,5 N ✓ |

**Top-3 inzichten voor het mondeling:**
1. **De terugkeerslag is allesbepalend**: 50 mm over 140° geeft de hoogste ds/dθ → bepaalt drukhoek (R₀ = 50 mm), ondersnijding (R_r = 15 mm) én de koppelpiek voor de vliegwielberekening.
2. **2e harmonisch (4 Hz) ligt gevaarlijk dicht bij ω_n (4,67 Hz)**: r₂ = 0,86. ζ = 0,094 is bewust gekozen om de resonantieversterking (DMF) te beperken van 3,8 naar 3,3.
3. **Een vliegwiel van 8,35 kg·m² is onpraktisch bij 120 rpm**: lage snelheid → enorme massa nodig. De moderne oplossing is een servomotor met condensatorbank — geen mechanisch vliegwiel.

---

## Opening van het examen: eigen inzichtsvraag & trade-off

> Volgens examenervaringen begint Herman vaak met: *"Heb je zelf een inzichtsvraag of opmerking over de taak?"* en *"Wat is een interessante trade-off die je gemaakt hebt?"* — bereid dit proactief voor en breng het zelf naar voren, vóór de prof het vraagt.

### Trade-off om zelf aan te kaarten: veergesloten vs. positief-gestuurde (desmodromische) nok

**Onze keuze**: een veer (k = 25 N/mm, preload 300 N) duwt de volger continu tegen de nok — de nok kan enkel "duwen", de veer "trekt terug". Dit is precies de kleptrein-analogie uit Vraag 1.

**Het alternatief — desmodromisch** (bv. Ducati-kleppen): twee nokprofielen (één voor heffing, één voor terugkeer) sturen de volger in BEIDE richtingen mechanisch aan, zonder veer.

| | Onze veeroplossing | Desmodromisch |
|---|---|---|
| Zweefgedrag (Vraag 5/6) | Risico bij hoog toerental — preload (300 N) is de buffer | **Onmogelijk** — volger is altijd positief gestuurd |
| Energieverlies (Vraag 8) | Continue veerwrijving, ~5 W, 100% van de cyclus | Geen veerwrijving, maar 2× nokoppervlakken → 2× contactwrijving |
| Snelheidslimiet (Vraag 5) | ~260 rpm (r₁ = 1) door veerresonantie | Veel hoger — geen massa-veersysteem, dus geen ω_n-grens |
| Complexiteit/kost | 1 nok + veer — goedkoop, eenvoudig | 2 nokken + dubbele speling-afstelling — duur, precisiewerk |

**Kernpunt**: onze veeroplossing is de juiste keuze **voor déze toepassing** (120 rpm, matige last) — desmodromisch wordt pas de moeite waard bij zeer hoog toerental (racemotoren, >10 000 rpm) waar veerresonantie de échte limiet wordt. De "beste" oplossing is dus toepassingsafhankelijk — exact het soort afweging die de prof wil horen.

### Inzichtsvraag om zelf te stellen: hoe krap mag de preload-marge zijn?

We kozen preload = 300 N, terwijl het berekende minimum 284,5 N (≈ m·g) is — een marge van slechts **~5%**. Bewust krap: een hogere preload betekent meer continue wrijving (Vraag 2/8) over de volledige cyclus, dus meer energieverbruik.

Maar fabricagetoleranties, veerverzwakking door vermoeiing en temperatuurseffecten kunnen die 5% makkelijk opeten → zweefgedrag (Vraag 6) tijdens bedrijf, niet tijdens de berekening.

**Vraag aan de prof**: is een marge van 5% in de praktijk verantwoord, of hoort hier een vaste veiligheidsfactor (bv. 1,2–1,5×) op de minimale preload, ten koste van extra wrijvingsverlies? Dit is de spanning tussen **theoretische optimalisatie** en **praktische robuustheid** — een goed startpunt om zelf aan te kaarten.
