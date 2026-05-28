# Samenvatting: 3 Notebooks – Nokkenmechanisme

> Doel: inzicht genoeg om code, grafieken en resultaten te kunnen uitleggen en doordenken — niet van buiten leren.
> Alle cijfers komen rechtstreeks uit de notebook-outputs (code runs) of de examenvoorbereiding.

---

## Notebook 1 – Kinematica & Geometrie

### 1. Algemeen overzicht

**Wat:** Ontwerp van het nokprofiel dat de gewenste volgerbeweging realiseert. Berekening van drukhoek, nokcontour, en kromtestraal. Keuze van steekcirkelstraal $R_0$ en rolvolgerstraal $R_r$ via de Kloomok-Muffley methode.

**Waarom:** Drie eisen moeten gelijktijdig voldaan zijn:
1. **Drukhoek** $\alpha < 30°$ → beperkt zijwaartse wrijvingskracht in de geleiding
2. **Geen ondersnijding** $\rho_{pitch} > R_r$ → de rol kan het profiel geometrisch volgen
3. **Geen zweefgedrag** $F_{normaal} > 0$ → veer houdt de volger altijd in contact (dit in NB2)

**Aanpak:**
- Bewegingsprofiel $s(\theta)$ opbouwen uit 5 segmenten via het 5e-graads polynoom
- Per kandidaat-$R_0$: maximale drukhoek per segment berekenen → kleinste $R_0$ waarbij $\alpha_{max} < 30°$
- Per kandidaat-$R_r$: minimale kromtestraal $\rho_{pitch}$ controleren → $R_r < \rho_{min}$

### 2. Belangrijke woordenschat & concepten

| Term | Wat het is | Intuïtief |
|------|-----------|-----------|
| **Nok / cam** | Roterende schijf met variabele straal | Duwt de volger omhoog als de dikke kant passeert |
| **Rolvolger** | Rol die over de nokcontour rolt | Rolt soepeler dan een puntvolgder — minder slijtage |
| **Steekcirkelstraal $R_0$** | Referentiestraal voor de kinematica | Afstand van rotatie-as tot middelpunt van de rol wanneer s=0 |
| **Nokbasiscirkel** | Cirkel met straal $R_0 - R_r$ | De kleinste cirkel van de nok zelf (niet de steekkromme) |
| **Volgerrolstraal $R_r$** | Straal van de rolvolger | 15 mm: groot genoeg voor slijtage, klein genoeg voor geen ondersnijding |
| **Pitch curve** | Pad beschreven door het middelpunt van de rol | De werkelijke nokcontour zit $R_r$ binnenwaarts hiervan |
| **Drukhoek $\alpha$** | Hoek tussen krachtvector en bewegingsrichting volger | Groter → meer zijdelingse kracht in geleiding; eis: $< 30°$ |
| **Ondersnijding** | $\rho_{pitch} < R_r$: rol past niet in het dal | Mechanisme loopt vast — te vermijden |
| **Heffing $h$** | Totale verplaatsing van de volger over één segment | Segment 4 (terugkeer): $h = 50$ mm — maatgevend |
| **5e-graads polynoom** | Bewegingswet: $y = 10\tau^3 - 15\tau^4 + 6\tau^5$ | C²-continu: versnelling springt niet aan de segmentranden |
| **$C_v$, $C_a$, $C_j$** | Dimensieloze coëfficiënten voor max snelheid, versnelling, ruk | Werkelijke waarden: $v_{max} = C_v \cdot h\omega/\beta$, $a_{max} = C_a \cdot h\omega^2/\beta^2$ |
| **Kloomok-Muffley** | Grafische methode om minimale $R_0$ en maximale $R_r$ te bepalen | Plot $\alpha_{max}$ vs. $R_0$ — kies waar de lijn onder 30° duikt |
| **C²-continuïteit** | Versnelling $\ddot{s}$ is continu over segmentgrenzen | Geen schokken in de normaalkracht bij de overgang naar dwell |

**Werkingsprincipe kettingregel:**
$$s = h \cdot y(\tau), \quad \dot{s} = \frac{h}{\beta}\cdot y'(\tau)\cdot\omega, \quad \ddot{s} = \frac{h}{\beta^2}\cdot y''(\tau)\cdot\omega^2$$
waarbij $\tau = (\theta - \theta_0)/\beta \in [0,1]$ de genormaliseerde hoek is.

**Drukhoek (e = 0):**
$$\tan\alpha = \frac{ds/d\theta}{\sqrt{(R_0+R_r)^2} + s(\theta)}$$

**Kromtestraal pitch curve:**
$$\rho_{pitch} = \frac{\left[(R_0+s)^2 + (ds/d\theta)^2\right]^{3/2}}{(R_0+s)^2 + 2(ds/d\theta)^2 - (R_0+s)\cdot d^2s/d\theta^2}$$

### 3. Resultaten & interpretatie

**Bewegingssegmenten:**

| Segment | Nokhoek | Heffing | Wet | Maatgevend voor |
|---------|---------|---------|-----|----------------|
| Heffing 1 | 10°→65° (55°) | 0→10 mm | Poly5 | — |
| Stilstand 1 | 65°→95° (30°) | 10 mm | Dwell | — |
| Heffing 2 | 95°→210° (115°) | 10→50 mm | Poly5 | — |
| **Terugkeer** | **210°→350° (140°)** | **50→0 mm** | **Poly5** | **Drukhoek & ondersnijding** |
| Stilstand 2 | 350°→10° (20°) | 0 mm | Dwell | — |

**Vergelijkingstabel bewegingswetten (uit notebook output):**

| Wet | $C_v$ | $C_a$ | RMS acc | $C_j$ | C²-continu |
|-----|-------|-------|---------|-------|------------|
| Harmonisch | 1,57 | 4,93 | 3,49 | 15,5 | **Nee** |
| Cycloïde | 2,00 | 6,28 | 4,44 | 39,5 | Ja |
| 3e-gr. polynoom | 1,50 | 6,00 | 3,47 | 12,0 | **Nee** |
| **5e-gr. polynoom** | **1,87** | **5,77** | 4,14 | **60,0** | **Ja** |

**Geometrie-uitkomsten:**

| Grootheid | Waarde |
|-----------|--------|
| Steekcirkelstraal | **$R_0 = 50$ mm** (min. vereist 46 mm voor terugkeer) |
| Volgerrolstraal | **$R_r = 15$ mm** |
| Nokbasiscirkelstraal | **$R_0 - R_r = 35$ mm** |
| Max heffing | **50 mm** bij θ = 210° |
| Max snelheid volger | **0,482 m/s** |
| Max versnelling volger | **±9,89 m/s²** ≈ g |
| Max drukhoek | **+21,86°** (< 30° ✓) bij θ ≈ 147° |
| Min drukhoek | **−23,83°** |
| Min kromtestraal pitch | **40,6 mm** > $R_r = 15$ mm ✓ |
| Ondersnijding | **Geen** (bevestigd door code) |

**Grafieken:** s(θ), v(θ), a(θ), j(θ) over 360° — de segmentgrenzen zijn glad (C²). Drukhoek-grafiek: piekt bij het steilste deel van de terugkeer, blijft ruim onder 30°. Nokcontour: blauw gevuld, met pitch curve in rood, rolvolger in groen.

**Sleutelinzichten:**
- Het **terugkeersegment** (210°→350°, 50 mm in 140°) is altijd maatgevend: grootste $ds/d\theta$ → hoogste drukhoek → bepaalt $R_0$; kleinste $\rho_{pitch}$ → bepaalt $R_r$
- $R_0 = 50$ mm is bewust iets groter dan het minimum (46 mm) — kleine veiligheidsmarge op de drukhoek
- Het harmonisch profiel is niet gekozen ondanks de laagste $C_a$ (4,93): het is niet C²-continu, wat schokken geeft bij elke segmentgrens

---

## Notebook 2 – Dynamica, Veer & Vliegwiel

### 1. Algemeen overzicht

**Wat:** Berekening van de normaalkracht op de nok over de volledige omwenteling, inclusief veerwerking, externe belasting, inertie, demping en wrijving. Daarna: dimensionering van de veervoorspanning en het vliegwiel.

**Waarom:** Het mechanisme moet drie eisen bewaren tijdens het draaien:
1. **Geen zweefgedrag** ($F_{normaal} > 0$): veervoorspanning moet voldoende zijn
2. **Motorspecificatie**: gemiddeld vermogen en koppel bepalen de motorselectie
3. **Vliegwiel**: energiefluctuatie ΔE bepaalt de benodigde vliegwieltraagheid

**Aanpak:**
- Krachtenbalans via d'Alembert: veer + extern + inertie + demping + wrijving
- Preload bepalen: kleinste waarde zodat $F_{normaal} \geq 0$ overal
- Aandrijfkoppel: $M(\theta) = F_{nok,vert} \cdot ds/d\theta$
- Energiefluctuatie: $\Delta E = \max(A_\theta) - \min(A_\theta)$ waarbij $A_\theta = \int(M - M_{gem})d\theta$

### 2. Belangrijke woordenschat & concepten

| Term | Wat het is | Intuïtief |
|------|-----------|-----------|
| **Normaalkracht $F_{normaal}$** | Contactkracht loodrecht op het nokoppervlak | De kracht waarmee de nok op de rol duwt |
| **Veervoorspanning $F_{preload}$** | Initiële veerrekking vóór beweging | Zorgt voor minimale contactkracht ook bij stilstand |
| **Zweefgedrag** | $F_{normaal} \leq 0$: volger verliest contact | Gevaarlijk: slijtage door herhaald hercontact |
| **Zelfblokkering** | $\mu \cdot |\tan\alpha| \geq 1$: noemer = 0 | Volger kan niet meer bewegen — mechanisme blokkeert |
| **Wrijvingsfactor** | $1/(1 - \mu|\tan\alpha|\cdot\text{sgn}(\dot{s}))$ | Vergroot de normaalkracht door geleiding-wrijving |
| **Externe belasting** | Opgelegde kracht op de volger door het werkstuk | Hier: 0→1250 N stijgend, dan constant, dan −480 N |
| **Eigenfrequentie $\omega_n$** | $\sqrt{k/m}$ — resonantiefrequentie veer-massa | Systeem moet sub-resonant draaien ($\omega < \omega_n$) |
| **Relatieve demping $\zeta$** | Fractie van kritische demping | $\zeta = 0,094$: licht gedempt, resonantie mogelijk bij doorloop |
| **Aandrijfkoppel $M(\theta)$** | $M = F_{nok,vert} \cdot ds/d\theta$ | Koppel dat de motor op de nokas moet leveren per hoek |
| **Energiefluctuatie $\Delta E$** | $\max(A_\theta) - \min(A_\theta)$ | De energiebuffer die het vliegwiel moet opvangen |
| **Vliegwieltraagheid $I$** | $I = \Delta E / (K\cdot\omega^2)$ | Hoe groot het vliegwiel moet zijn voor een gewenste snelheidsschommeling K |
| **Fluctuatiecoëfficiënt $K$** | Toegestane relatieve snelheidsschommeling | $K = 0,05$: toerental mag ±2,5% variëren |
| **$F_{nok,vert}$** | Verticale component van normaalkracht | $= F_{normaal} \cdot \cos\alpha$ — de nuttige kracht voor het koppel |

**Krachtenbalans normaalkracht (vóór wrijving):**
$$F_{normaal} = \frac{k \cdot s + F_{preload} + F_{extern} + m\ddot{s} + c\dot{s}}{\cos\alpha}$$

Na wrijvingscorrectie:
$$F_{normaal,gecorr} = F_{normaal} \cdot \frac{1}{1 - \mu|\tan\alpha|\cdot\text{sgn}(\dot{s})}$$

**Vliegwielformule:**
$$I_{vliegwiel} = \frac{\Delta E}{K \cdot \omega^2} = \frac{65{,}9}{0{,}05 \times (4\pi)^2} = 8{,}35\,\text{kg·m}^2$$

**Eigenfrequentie:**
$$\omega_n = \sqrt{k/m} = \sqrt{25\,000/29} = 29{,}4\,\text{rad/s} = 4{,}67\,\text{Hz}$$

### 3. Resultaten & interpretatie

**Systeemparameters:**

| Parameter | Waarde |
|-----------|--------|
| Cyclustijd | **0,500 s** |
| Toerental nokas | **120 rpm** = $4\pi$ rad/s ≈ 12,57 rad/s |
| Equivalente massa | **$m = 29$ kg** |
| Veerstijfheid | **$k = 25\,000$ N/m** (25 N/mm) |
| Relatieve demping | **$\zeta = 0,094$** |
| Absolute demping | **$c = 160$ N·s/m** ($= \zeta \cdot 2\sqrt{km}$) |
| Wrijvingscoëfficiënt | **$\mu = 0,10$** |

**Externe belasting:**

| Hoekbereik | Belasting |
|-----------|-----------|
| 10°→120° | 0→1250 N (lineair stijgend) |
| 120°→170° | 1250 N (constant) |
| 170°→270° | −480 N (constant negatief) |
| Overig | 0 N |

**Veerontwerp:**

| Grootheid | Waarde |
|-----------|--------|
| Minimale veervoorspanning | **284,5 N** |
| Gekozen veervoorspanning | **300 N** (afgerond naar boven op 100 N) |
| Min normaalkracht (incl. wrijving) | **300 N** — gelijk aan preload, op dwell-segment |
| Energiebuffering door veer | ½ × 25000 × 0,05² = **31,25 J per cyclus** |

**Vermogen en vliegwiel:**

| Grootheid | Waarde |
|-----------|--------|
| Gemiddeld motorvermogen | **110 W** |
| Gemiddeld aandrijfkoppel | **8,78 Nm** |
| Energiefluctuatie $\Delta E$ | **65,9 J** |
| Vliegwieltraagheid ($K = 0,05$) | **8,35 kg·m²** |
| Aanbevolen motorkoppel (× 1,5) | **~13,2 Nm nominaal** |

**Frequentie-analyse:**

| Grootheid | Waarde |
|-----------|--------|
| Eigenfrequentie systeem | **$\omega_n = 29{,}4$ rad/s = 4,67 Hz** |
| Fundamentele aandrijffrequentie | 120 rpm / 60 = **2 Hz** |
| Snelheidsverhouding $r_1$ | 2 / 4,67 = **0,43** (sub-resonant ✓) |
| 2e harmonisch | **4 Hz** |
| Snelheidsverhouding $r_2$ | 4 / 4,67 = **0,86** (kritisch!) |
| DMF bij $r_2 = 0,86$, $\zeta = 0,094$ | **~3,3** (resonantieversterking) |
| Zelfblokkeringsfactor | $\mu \cdot |\tan\alpha| = 0,10 \times 0,40 =$ **0,040 ≪ 1** ✓ |
| Max inertiekracht | $m \cdot a_{max} = 29 \times 9,89 =$ **287 N** |

**Krachtendecompositie bij piekbelasting (θ ≈ 130°):**

| Bijdrage | Grootte | % |
|----------|---------|---|
| Externe belasting | **1250 N** | ~55% |
| Veerkracht ($k \cdot s + F_{preload}$) | **~1300–1550 N** | ~40% |
| Inertiakracht ($m\ddot{s}$) | max **287 N** | ~10% (tijdelijk) |
| Dempingskracht ($c\dot{s}$) | max **~77 N** | ~3% |

**Grafieken:** Normaalkracht $F_{normaal}(\theta)$ met aparte curves per bijdrage. Koppel $M(\theta)$ schommelt sterk — piek bij externe belastingsfase (θ ≈ 120°–170°). Cumulatieve energiefluctuatie $A_\theta(\theta)$ met max en min gemarkeerd.

**Sleutelinzichten:**
- De **preload van 300 N** is exact de minimale waarde — de volger zit op de drempel van zweefgedrag. Verhoog de preload voor elke toerental-stijging
- Het **vliegwiel van 8,35 kg·m²** is enorm (stalen schijf r=0,3m → ~185 kg). Reden: bij 120 rpm is $\omega$ klein → $E_{kin} = \frac{1}{2}I\omega^2$ klein → grote I nodig voor zelfde energiebuffer. Moderne oplossing: servomotor zonder vliegwiel
- De **2e harmonisch** (4 Hz) ligt dicht bij $\omega_n$ (4,67 Hz) met $r_2 = 0,86$ → versterkingsfactor ~3,3. Kritisch bij het opstarten: het systeem passeert door resonantie

---

## Notebook 3 – Animatie

### 1. Algemeen overzicht

**Wat:** Visualisatie en animatie van het nokkenmechanisme in beweging. Geen nieuwe berekeningen — hergebruik van alle parameters en kinematica uit NB1 en NB2.

**Waarom:** De animatie laat zien hoe de nokcontour, de rolvolger en het heffingsprofiel samenwerken. Bijzonder nuttig om de drukhoek visueel te controleren: de krachtvector is zichtbaar en kantel op het nokoppervlak.

**Inhoud:** 250 frames, 2 volledige omwentelingen (0 → 4π rad). Per frame worden getoond: huidige nokhoek θ, heffing s, nokcontour (blauw), pitch curve (rood gestippeld), rolvolger (groen), geleiding en blok (groen bovenaan), drukhoeklijn (rood).

### 2. Concepten die visueel duidelijk worden

| Concept | Wat je ziet in de animatie |
|---------|--------------------------|
| **Drukhoek** | De hoekverschuiving tussen de krachtvector (rood gestippeld) en de vertikale geleiding |
| **Dwell-segmenten** | De volger staat stil terwijl de nok doordraaait — vlak deel van het nokprofiel |
| **Heffing en terugkeer** | De volger beweegt omhoog/omlaag terwijl de nokstraal verandert |
| **Pitch curve** | De gestippelde rode curve — het pad van het rolmiddelpunt |
| **Nokcontour** | De blauwe gevulde vorm — de werkelijke nok, $R_r$ binnenwaarts van de pitch curve |

### 3. Resultaten & interpretatie

Geen nieuwe numerieke outputs. De animatie bevestigt visueel:
- De volger volgt het 5e-graads polynoom profiel — geen schokken aan de segmentgrenzen
- De drukhoek is overal klein (< 30°) — de rol neigt niet sterk af van de vertikale
- Dwell-segmenten: nok draait terwijl de volger stilstaat (vlak deel van contour)

---

## Overzicht sleutelnummers (alle notebooks)

| Grootheid | Waarde |
|-----------|--------|
| Cyclustijd / toerental | 0,500 s / **120 rpm** = 12,57 rad/s |
| Steekcirkelstraal $R_0$ | **50 mm** |
| Volgerrolstraal $R_r$ | **15 mm** |
| Nokbasiscirkelstraal | **35 mm** |
| Max heffing | **50 mm** (terugkeer, segment 4) |
| Max drukhoek | **+21,86°** / −23,83° (< 30° ✓) |
| Min $\rho_{pitch}$ | **40,6 mm** > 15 mm ✓ (geen ondersnijding) |
| Equivalente massa | **29 kg** |
| Veerstijfheid $k$ | **25 000 N/m** |
| Veervoorspanning (gekozen) | **300 N** (min = 284,5 N) |
| Min normaalkracht | **300 N** (geen zweefgedrag ✓) |
| Max ext. belasting | **1250 N** |
| Max versnelling volger | **±9,89 m/s²** |
| Max inertiekracht | **287 N** ($= 29 \times 9,89$) |
| Eigenfrequentie | **29,4 rad/s = 4,67 Hz** |
| Gemiddeld motorvermogen | **110 W** |
| Gemiddeld aandrijfkoppel | **8,78 Nm** |
| Energiefluctuatie $\Delta E$ | **65,9 J** |
| Vliegwieltraagheid ($K = 0,05$) | **8,35 kg·m²** |
| 2e harmonisch / $r_2$ | **4 Hz** / $r_2 = 0,86$ (kritisch, DMF ≈ 3,3) |
| Zelfblokkeringsfactor | **0,040 ≪ 1** ✓ |

## Kernformules

| Stap | Formule | Grens |
|------|---------|-------|
| Profiel | $s = h\cdot y(\tau),\;\tau = (\theta-\theta_0)/\beta$ | — |
| Drukhoek | $\tan\alpha = (ds/d\theta)\,/\,(R_0 + R_r + s)$ | $\alpha < 30°$ |
| Geen ondersnijding | $\rho_{pitch} > R_r$ overal | $\rho_{min} = 40{,}6$ mm ✓ |
| Normaalkracht | $F_n = (ks + F_{pre} + F_{ext} + m\ddot{s} + c\dot{s})\,/\,\cos\alpha$ | $F_n > 0$ ✓ |
| Wrijvingscorrectie | $F_{n,gecorr} = F_n\,/\,(1 - \mu|\tan\alpha|\cdot\text{sgn}(\dot{s}))$ | $\mu|\tan\alpha| < 1$ ✓ |
| Aandrijfkoppel | $M(\theta) = F_{nok,vert}\cdot ds/d\theta$ | — |
| Eigenfrequentie | $\omega_n = \sqrt{k/m} = 29{,}4$ rad/s | $\omega \ll \omega_n$ |
| Vliegwiel | $I = \Delta E\,/\,(K\omega^2) = 8{,}35$ kg·m² | $K = 0{,}05$ |
| Zelfblokkering | $\mu|\tan\alpha| < 1$ | $0{,}04 \ll 1$ ✓ |

## Verplichte volgorde: NB1 → NB2 → NB3

NB2 herberekent het nokprofiel intern maar erft de geometrieparameters ($R_0$, $R_r$, segmenten) rechtstreeks uit de setup-cel — geen bestandsoverdracht zoals bij de stangen. NB3 herberekent ook volledig intern.

| Parameterwijziging | NB1 | NB2 | NB3 |
|-------------------|:---:|:---:|:---:|
| Toerental / $T_{cyclus}$ | ✗ | ✓ | ✓ |
| Massa $m$, stijfheid $k$, $\zeta$ | ✗ | ✓ | ✓ |
| Externe belasting | ✗ | ✓ | ✓ |
| Wrijving $\mu$ | ✗ | ✓ | ✓ |
| Segmenten (hoeken, heffingen) | ✓ | ✓ | ✓ |
| $R_0$, $R_r$ (geometrie) | ✓ | ✓ | ✓ |
