# Examenvoorbereiding Mondeling – Nokkenmechanisme

> **Gebaseerd op de actuele code-outputs van Nok_1, Nok_2 en Nok_3.**
> De prof kiest 4 van 10 vragen (elk 2,5/10). Vaste bijvraag: *"Hoeveel kost de motor, en hoeveel kost het energieverbruik?"*

---

## Sleutelnummers uit de notebooks

| Grootheid | Waarde | Notebook / cel |
|-----------|--------|---------------|
| Cyclustijd | 0,500 s | Nok_1 setup |
| Toerental | **120 rpm** = 12,57 rad/s | Nok_1 setup |
| Steekcirkelstraal | **R₀ = 50 mm** | Nok_1 cel 12 |
| Volgerrolstraal | **R_r = 15 mm** | Nok_1 cel 16 |
| Nokbasiscirkelstraal | **R₀ − R_r = 35 mm** | Nok_1 cel 16 |
| Excentriciteit | e = 0 mm | Nok_1 setup |
| Maximale heffing | **50 mm** (bij θ = 210°) | Nok_1 profiel |
| Max snelheid volger | **0,482 m/s** bij θ = 280° | Nok_1 berekend |
| Max versnelling volger | **±9,89 m/s²** ≈ g bij θ = 21,6° | Nok_1 berekend |
| Max drukhoek | **21,86°** bij θ = 146,8° (< 30° ✓) | Nok_1 cel 18 |
| Min drukhoek | **−23,83°** | Nok_1 cel 18 |
| Min kromtestraal pitch | **40,6 mm** > R_r = 15 mm ✓ | Nok_1 berekend |
| Equivalente massa | **m = 29 kg** | Nok_2 setup |
| Veerstijfheid | **k = 25 000 N/m** (25 N/mm) | Nok_2 setup |
| Relatieve demping | **ζ = 0,094** | Nok_2 setup |
| Wrijvingscoëfficiënt geleiding | **μ = 0,10** | Nok_2 setup |
| Veervoorspanning (berekend minimum) | **284,5 N** | Nok_2 cel 5 |
| Veervoorspanning (gekozen) | **300 N** | Nok_2 cel 5 |
| Minimale normaalkracht | **300 N** (= preload, bij dwell) | Nok_2 cel 5 |
| Externe belasting max | **1 250 N** (θ = 120°→170°) | Nok_2 cel 5 |
| Eigenfrequentie systeem | **ω_n = 29,4 rad/s = 4,67 Hz** | berekend: √(k/m) |
| Snelheidsverhouding ω/ω_n | **0,43** (sub-resonant ✓) | berekend |
| Gemiddeld motorvermogen | **110 W** | Nok_2 cel 7 |
| Gemiddeld aandrijfkoppel | **8,78 Nm** | Nok_2 cel 7 |
| Energiefluctuatie ΔE | **65,9 J** | Nok_2 cel 7 |
| Vliegwieltraagheid (K = 0,05) | **8,35 kg·m²** | Nok_2 cel 7 |

### Bewegingssegmenten

| Segment | Nokhoek | Heffing | Wet | Maatgevend voor |
|---------|---------|---------|-----|----------------|
| Heffing 1 | 10°→65° (55°) | 0→10 mm | 5e-gr. polynoom | — |
| Stilstand 1 | 65°→95° (30°) | 10 mm | Dwell | — |
| Heffing 2 | 95°→210° (115°) | 10→50 mm | 5e-gr. polynoom | — |
| **Terugkeer** | **210°→350° (140°)** | **50→0 mm** | **5e-gr. polynoom** | **Drukhoek & ondersnijding** |
| Stilstand 2 | 350°→10° (20°) | 0 mm | Dwell | — |

### Vergelijking bewegingswetten

| Wet | Cv | Ca | RMS acc | Cj | C²-continu |
|-----|----|----|---------|-----|------------|
| Harmonisch | 1,57 | 4,93 | 3,49 | 15,5 | **Nee** |
| Cycloïde | 2,00 | 6,28 | 4,44 | 39,5 | Ja |
| 3e-gr. polynoom | 1,50 | 6,00 | 3,47 | 12,0 | **Nee** |
| **5e-gr. polynoom** | **1,87** | **5,77** | 4,14 | **60,0** | **Ja** |

---

## Vraag 1 – Industrieel gebruik & ontwerpaanpassingen

### ① Volledig antwoord (Ingenieursinzicht)

**Waarvoor gebruikt de industrie een roterend nokkenmechanisme?**

Een nokkenmechanisme zet continue rotatie om in een precieze, geprogram­meerde translatie- of zwenkbeweging. Het is het go-to mechanisme voor elke toepassing waar één motor tientallen of honderden cycli per minuut exact hetzelfde bewegingsprofiel moet herhalen. De industriële toepassingen omvatten:

- **Zuigermotor kleptrein**: de meest iconische toepassing. De nok opent en sluit klep­pen met exact het gewenste hefsegment, stilstand (klep open voor gaswisseling) en terugkeer — identiek aan onze cyclus. Toerental: 2 000–8 000 rpm, heffing 8–12 mm, massa ~0,5 kg. De veer dient hier om de klep te sluiten én contactverlies te voorkomen bij hoge toerentallen.
- **Verpakkingslijn / sealstation**: de nok stuurt de verticale beweging van een warmzegel­balk die om de 0,5 s neer­daalt, 1 s drukt (stilstand = ons dwell-segment!) en terugkeert. De externe belasting tijdens het drukken is vergelijkbaar met onze 1 250 N.
- **Textiel­machine (naaimachine)**: de nok aandrijft de naald omhoog en omlaag, met een stilstand aan de bovenzijde om de draad te voeren — exact de structuur van ons profiel.
- **Drukpers / zeefdruk**: de nok drukt een inktrol met een precies tijdprofiel op het substraat.

**Aanpassing voor een andere toepassing: cilindervuller in een farmaceutische lijn**

Een syringe-vuller moet een plunjer 40 mm naar beneden drukken (vullen), 200 ms pauzeren (dosering stabiliseert) en daarna snel terugkeren zodat de volgende spuit ingevoerd kan worden. Dit is exact de structuur van ons 3-segment profiel (heffing – stilstand – terugkeer).

Wat verandert ten opzichte van ons ontwerp:

| Parameter | Ons ontwerp | Syringe-vuller | Reden |
|-----------|-------------|----------------|-------|
| Cyclustijd | 0,5 s (120 rpm) | 0,25 s (240 rpm) | Hogere productiviteit vereist |
| Heffing | 50 mm | 40 mm | Kleiner doseringsvolume |
| Externe belasting | 1 250 N | ~50 N (vloeistof) | Vloeistofweerstand is gering |
| Veervoorspanning | 300 N | ~80 N | Lagere massa/externe kracht |
| R₀ | 50 mm | 35 mm | Lagere kracht → kleinere R₀ kan |
| Motorpositie | Op nokas | **Servo-aangedreven, direct op as** | Farmaceutisch: geen olie, schone ruimte |

Bij 240 rpm stijgen de versnellingen kwadratisch: a_max = 9,89 × (240/120)² = **39,6 m/s²**. De benodigde veervoorspanning en het motorvermogen stijgen navenant — dit is de kritische ontwerpconsequentie.

### ② "Wat als..."-bijvragen

**"Wat als de machine twee keer zo snel moet draaien (240 rpm ipv 120 rpm)?"**
> De geometrie van de nok verandert niet — het profiel s(θ) is puur hoekgebaseerd. Maar ω verdubbelt, dus versnellingen stijgen kwadratisch: van 9,89 naar ~40 m/s². De inertiakracht m·a_max springt van 287 N naar ~1 150 N. Dit overstijgt de externe belasting, waardoor de motor en veer drastisch zwaarder gedimensioneerd moeten worden. De huidige preload van 300 N is volledig onvoldoende: de minimale preload stijgt proportioneel mee.

**"Wat als we de motor verplaatsen van de nokas naar een reductor?"**
> Met een reductor verlaag je het toerental op de nokas, maar moet de motor sneller draaien. Voordeel: kleinere motor (koppel daalt met factor reductor), lichter vliegwiel op de motortas. Nadeel: de nok draait trager → de vliegwieltraagheid op de nokas stijgt als 1/ω², want I = ΔE / (K·ω²). Bij halvering van het noktoerental vervierdubbelt de benodigde I. De reductor helpt de motor maar verzwaart het vliegwiel op de nokas. Moderne keuze: directe servomotor op de nokas zonder vliegwiel.

**"Wat als we de stilstandsduur verlengen van 30° naar 60°?"**
> De stilstand neemt meer hoekbereik in. De overige segmenten (heffing en terugkeer) moeten dezelfde heffing in minder hoekbereik realiseren. Dit verhoogt ds/dθ, wat de drukhoek vergroot en de vereiste R₀ opdrijft. Concreet: de maatgevende terugkeer van 140° zou inkrimpen naar ~110°, en de Kloomok-Muffley analyse geeft een hogere minimale R₀ — het ontwerp moet opnieuw geïtereerd worden.

### ③ Verificatie in de notebooks

- **Nok_1 setup-cel:** profiel_segmenten — alle 5 segmenten met hoeken en heffingen
- **Nok_1 cel 10:** Kloomok-Muffley drukhoekanalyse per segment → R₀ = 50 mm is het minimum voor α < 30°
- **Nok_2 cel 5:** externe belasting tot 1 250 N → dit is het "industriële werkstuk" profiel

---

## Vraag 2 – Optimalisatie: precisie / energieverbruik / krachtgeneratie

### ① Volledig antwoord (Ingenieursinzicht)

#### Optimalisatie voor precisie

De nokcontour is een gefreesd of geslepen stalen oppervlak — de geometrische fout zit in de manufacturing, niet in de berekening. In de praktijk zijn drie foutbronnen relevant:

**1. Rolvolger-concentriciteit (dominant):** De rolvolger loopt op een naald- of kogellager. Een radiale speling van 10–30 µm in het lager vertaalt zich direct naar een positiefout op de volger. Een naaldlager (speling < 5 µm) is nauwkeuriger dan een glijlager maar duurder en gevoeliger voor vuil.

**2. Excentriciteit e = 0 mm (onze keuze):** Met e = 0 is het ontwerp symmetrisch en eenvoudigst te produceren. Excentriciteit (e ≠ 0) verlaagt de drukhoek tijdens de hefsslag maar vereist een precisieoffset-geleiding — extra productiekost voor marginale winst bij onze lage drukhoek van 22°.

**3. Drukhoek en geleiding:** Bij max α = 21,86° is de zijdelingse wrijvingskracht op de geleiding F_z = F_normaal × sin(α). Bij een normaalkracht van ~1 800 N (veer + externe last): F_z ≈ 1 800 × sin(22°) ≈ 675 N. Een slijtende geleiding vergroot het spel en vermindert de precisie. Rollen in de geleiding in plaats van een glijvlak verbeteren dit dramatisch.

#### Optimalisatie voor energieverbruik

Het gemiddeld motorvermogen is **110 W**. Dit splitst in bijdragen:

| Bijdrage | Grootte-orde | Optimalisatie |
|----------|-------------|---------------|
| Extern werk (1 250 N over 40 mm) | ~80% | Reduceer werkstukbelasting (procesgebonden) |
| Veerverliezen (wrijving in geleiding door preload) | ~15% | Kleinere veerstijfheid k of lagere μ |
| Inertieverliezen (demping) | ~5% | Lagere demping ζ |

De dominante energieverbruiker is de **externe belasting** (het werkstuk dat de nok duwt). Dit is procesgebonden en nauwelijks te optimaliseren. De veerkracht levert een gunstig effect bij de terugkeer (energie-opslag en -teruggave), maar het veercontact verergert de wrijving in de geleiding: F_wrijving = μ × F_normaal = 0,10 × F_normaal.

Een lagere veerstijfheid k verlaagt de normaalkracht en dus de wrijvingsverliezen — maar verhoogt het risico op zweefgedrag. Dit is de fundamentele trade-off van het veerontwerp.

#### Optimalisatie voor krachtgeneratie

De nok genereert kracht via de relatie:
$$M_{motor} = F_{nok,vert} \cdot \frac{ds}{d\theta}$$

**Hefboom-effect:** Waar ds/dθ klein is (bijna-stilstand), levert hetzelfde motorkoppel een hoge kracht op de volger. Waar ds/dθ groot is (pieksnelheid), is de kracht per eenheid koppel laag. Dit is het fundamentele kracht-snelheid-compromis van de nok.

**Drukhoek als verliesfactor:** Bij α = 22° draagt slechts cos(22°) = 0,927 van de normaalkracht bij aan de nuttige vertikale kracht. De rest (sin(22°) = 0,374) belast de geleiding zijdelings. Om meer kracht te genereren: vergroot R₀ (verlaagt α) of reduceer de heffing per hoekgraad (minder steil profiel).

**Externe load = 1 250 N:** De nok genereert op het piekmoment van de externe belasting (θ ≈ 120°–170°) een normaalkracht die hoger is dan de veerbelasting + inertiëkracht gecombineerd. Dit is het dimensionerend geval voor de as, lagers en nokgeometrie.

### ② "Wat als..."-bijvragen

**"Wat als we de veerstijfheid halveren (k = 12 500 N/m)?"**
> De minimale normaalkracht daalt van 300 N (preload) naar lager, want de veer levert minder kracht bij dezelfde heffing. De wrijvingsverliezen in de geleiding dalen proportioneel. Maar: de preload moet opnieuw bepaald worden zodat F_contact > 0 overal geldt. Bij een steilere versnellingsgrens (hoge snelheidsmachine) kan met de helft van k zweefgedrag optreden. Voor onze 120 rpm-machine is dit wel haalbaar, maar het vergt een nieuwe KM-analyse.

**"Wat als we de rolstraal R_r vergroten van 15 mm naar 25 mm voor meer levensduur?"**
> Grotere R_r verdeelt de contactkracht over een groter oppervlak → lagere Hertz-contactdruk → betere levensduur. Maar de ondersnijdingseis ρ_pitch > R_r wordt strenger. Bij R_r = 25 mm geldt: het maatgevend segment (terugkeer) heeft ρ_min = 50 mm > 25 mm, dus net mogelijk. De drukhoek verandert niet. De lagere contactdruk spaart roloppervlak, maar de grotere rol is zwaarder → hogere inertiakrachten bij hoge snelheden.

**"Hoe zou je de normaalkracht verlagen zonder de externe belasting te veranderen?"**
> Vergroot R₀: dit verlaagt de drukhoek α, waardoor de verhouding nuttige kracht/normaalkracht verbetert (cos α stijgt). Of: verklein de hoek van het maatgevende segment (langere terugkeer over meer degrees), waardoor ds/dθ daalt en de drukhoek daalt. Beide opties vragen meer inbouwruimte.

### ③ Verificatie in de notebooks

- **Nok_1 cel 10:** K-M drukhoekanalyse → minimale R₀ = 46 mm voor terugkeersegment
- **Nok_1 cel 18:** plotPressureAngle → max α = 21,86° zichtbaar in grafiek
- **Nok_2 cel 5:** krachtenbalans met externe belasting en preload-berekening
- **Nok_2 cel 7:** P_gem = 110 W, M_gem = 8,78 Nm

---

## Vraag 3 – Vliegwiel: afwegingen

### ① Volledig antwoord (Ingenieursinzicht)

**Wat zegt de energiefluctuatie ΔE = 65,9 J ons?**

Het aandrijfkoppel M(θ) schommelt sterk over één omwenteling: het piekt tijdens de externe belastingsfase (θ ≈ 120°–170°) en daalt tijdens de dwells. Het gemiddeld koppel is 8,78 Nm, maar de piek is aanzienlijk hoger. De **energiefluctuatie ΔE = 65,9 J** is het maximale energieverschil tussen het koppel dat de motor levert (constant = gemiddeld) en wat het mechanisme vraagt (sterk variabel).

**Wat is de benodigde vliegwieltraagheid?**

$$I_{vliegwiel} = \frac{\Delta E}{K \cdot \omega^2} = \frac{65{,}9}{0{,}05 \times (12{,}57)^2} = \frac{65{,}9}{7{,}90} = \mathbf{8{,}35 \; \text{kg}\cdot\text{m}^2}$$

**Dit getal is enorm.** Ter vergelijking: een stalen schijf met straal 0,3 m heeft massa m = 2I/r² = 185 kg. Voor een straal van 0,5 m nog altijd 67 kg. De reden is het lage toerental: bij slechts 120 rpm is de roterende kinetische energie (E = ½Iω²) per kilogram-meter² klein, waardoor je veel massa nodig hebt om dezelfde energiebuffer te realiseren.

**Trade-offs bij vliegwielontwerp:**

| Keuze | Voordeel | Nadeel |
|-------|----------|--------|
| Kleiner K (strakkere regelaar, bijv. K = 0,02) | Stabielere snelheid | I = 3× groter = 25 kg·m² → 560 kg stalen schijf bij r = 0,3 m |
| Groter K (meer snelheidsschommeling, bijv. K = 0,10) | I = 4,2 kg·m² → hanteerbaar | Toerental schommelt ±5% → minder uniforme beweging en productiefouten |
| Vliegwiel op de nokas | Pieken direct opgevangen | Groot en zwaar aan het langzame einde |
| Vliegwiel op motorkas (met reductor) | Kleiner door ω² effect | Reductor nodig, extra kosten en verliezen |

**De moderne keuze: servomotor zonder vliegwiel**

Met een servo-aandrijving (gesloten lus, hoge bandbreedte) kan de motor zelf het variabele koppelprofiel volgen. De condensatoren in de frequentieregelaar bufferen de piekenergie elektrisch. Geen mechanisch vliegwiel nodig. Dit is de standaard aanpak voor moderne machines, want een 8,35 kg·m² vliegwiel is praktisch onbruikbaar voor een 120 rpm machine in de industrie.

**Wanneer is een vliegwiel dan wél de juiste keuze?**

Bij hoge toerentallen: als dezelfde machine 1 200 rpm draait (ω = 125,7 rad/s), wordt I = 65,9 / (0,05 × 15 800) = 0,083 kg·m² — een hanteerbare schijf van ~2 kg bij r = 0,2 m. Vliegwielen zijn inherent zinvoller op snelle machines. Op een traag, zwaar belaste machine (120 rpm, 29 kg massa) zijn ze contra-productief.

### ② "Wat als..."-bijvragen

**"Wat als we de fluctuatiecoëfficiënt K niet 0,05 maar 0,20 kiezen?"**
> I = ΔE / (K·ω²) daalt met factor 4 naar 2,09 kg·m². De schijfmassa bij r = 0,3 m daalt naar ~46 kg — beter maar nog altijd zwaar. Tegelijk schommelt het toerental nu ±10% per cyclus: een machinist merkt dat het mechanisme "pulst". In een farmaceutische of printapplicatie is dit onaanvaardbaar; in een eenvoudige pers misschien wel te tolereren.

**"Wat als we twee identieke nok­machines in fase koppelen op dezelfde as?"**
> Als beide cams in fase zijn: ΔE verdubbelt naar 131,8 J en het vliegwiel wordt nog groter. Als beide cams **180° uit fase** zijn (tegengestelde pieken): de koppelpieken compenseren deels elkaar → ΔE kan significant dalen (afhankelijk van het koppelprofiel). Dit is de elegante oplossing voor cyclische machines met meerdere werkstations: faseverschuiving vermindert het vliegwielprobleem.

**"Wat als de externe belasting wegvalt (geen werkstuk)?"**
> Zonder externe belasting van 1 250 N daalt het gemiddeld vermogen drastisch. De energiefluctuatie ΔE daalt navenant (voornamelijk vet veerl en inertie), en I wordt veel kleiner. Dit toont dat de externe belasting de maatgevende factor is voor het vliegwiel — niet de kinemtica van de nok op zich.

### ③ Verificatie in de notebooks

- **Nok_2 cel 7 output:** `Energiefluctuatie: 65.9 J`, `Vliegwieltraagheid: 8.35 kg m^2`
- **Nok_2 cel 7:** plot van cumulatieve energiefluctuatie E(θ) — zichtbaar de hoekpositie van het maximum (externe belastingsfase) en minimum
- **Nok_2 cel 7:** plot van aandrijfkoppel M(θ) — duidelijk de koppelpiek bij θ ≈ 120°–170°

---

## Vraag 4 – Keuze bewegings-traject: afwegingen

### ① Volledig antwoord (Ingenieursinzicht)

**Welk profiel gekozen en waarom?**

Wij kozen het **5e-graads polynoom** voor alle drie de bewegingssegmenten (heffing 1, heffing 2, terugkeer). De reden is tweeledig:

1. **C²-continuïteit**: De versnelling $\ddot{s}(\theta)$ is continu over de segmentgrenzen. Er treden geen schokken op in de normaalkracht op de nok. Dit is essentieel voor de levensduur van het nokoppervlak en de lagers.

2. **Lagere Ca dan de cycloïde**: Ca = 5,77 versus 6,28 voor de cycloïde. Een lagere piekversnelling betekent een lagere inertiakracht (m·a_max = 29 × 9,89 = **287 N** voor ons 5e-gr. polynoom). Met de cycloïde zou dat 29 × (9,89 × 6,28/5,77) = **312 N** zijn — 9% meer.

**De fundamentele trade-off: Ca vs. Cj**

Het 5e-graads polynoom heeft een hoge ruk: Cj = 60 (de hoogste van alle beschouwde wetten). De cycloïde heeft Cj = 39,5. Een hogere ruk betekent dat de normaalkracht sneller verandert bij de segment­overgangen. In stijve, goed gesmeerde systemen is dit aanvaardbaar. In systemen met speling of flexibiliteit (slijtende geleiding, lange dunne stang) kan een hoge ruk impactkrachten veroorzaken.

**Waarom niet het harmonisch profiel?**

Het harmonisch profiel heeft de laagste Ca (4,93) — de beste piekversnelling van allemaal. Maar het is **niet C²-continu**: de versnelling springt abrupt aan de beginrand van elk segment. Dat is een directe schokbelasting op de nok. Bij 120 rpm (2 Hz) is elke overgang een hamerslag op het nokoppervlak, tweemaal per omwenteling. Dit verkort de levensduur drastisch en veroorzaakt akoestische emissie (geluid/trillingen).

**Waarom niet het 3e-graads polynoom?**

Laagste RMS-versnelling (3,47 vs. 4,14 voor 5e-gr.) → het zuinigst qua gemiddelde normaalkracht, voordelig voor wrijvingsverliezen. Maar ook niet C²-continu: springt in versnelling aan de randen. Zelfde bezwaar als harmonisch.

**Wat het 5e-graads polynoom precies doet aan de nokgrenzen:**

Het profiel start en eindigt met y'(0) = y'(1) = 0 én y''(0) = y''(1) = 0. Dit betekent dat zowel de snelheid als de versnelling nul zijn aan de randen van het segment — een naadloze aansluiting op het dwell-segment. De nokconstructeur hoeft geen speciale overgangsprofielen te slijpen.

### ② "Wat als..."-bijvragen

**"Wat als we een 7e-graads polynoom kiezen voor C³-continuïteit?"**
> Een 7e-gr. polynoom maakt ook de ruk continu aan de segmentranden (Cj = 0 aan de randen). Dit elimineert de laatste abrupte overgang. De Ca van een 7e-gr. polynoom is echter hoger dan het 5e-gr.: Ca ≈ 7,5. Dit betekent grotere inertiakrachten. De afweging: zachter maar krachtiger. Zinvol bij machines met flexibele elementen (resonantiegevoelig) of bij hogere toerentallen waar de ruk problematisch wordt.

**"Wat als we de terugkeer­beweging met de cycloïde uitvoeren in plaats van het 5e-graads polynoom?"**
> De cycloïde heeft een hogere Ca (6,28 vs. 5,77) maar lagere ruk (Cj = 39,5 vs. 60). Concreet: de max versnelling stijgt van 9,89 naar 10,79 m/s² bij terugkeer, de max inertiakracht van 287 naar 313 N. De drukhoek verandert nauwelijks (dominante factor is ds/dθ, niet a(θ)). Het verschil is marginaal bij 120 rpm maar wordt relevant bij hogere toerentallen.

**"Waarom is de terugkeer het maatgevende segment?"**
> De terugkeer legt 50 mm af over 140° — de grootste heffing in de kortste hoekverdeling. Dit geeft de hoogste ds/dθ = v/ω, wat direct de drukhoek bepaalt: tan(α) = (ds/dθ) / (R₀ + s). De Kloomok-Muffley analyse bevestigt dit: voor segment 4 is de minimale R₀ voor α < 30° gelijk aan 46 mm, het hoogste van alle segmenten. Vandaar dat R₀ = 50 mm bepaald wordt door het terugkeersegment.

### ③ Verificatie in de notebooks

- **Nok_1 cel 20 (tabel):** vergelijking Ca / Cj / C²-continuïteit per wet
- **Nok_1 cel 10 output:** `Seg4 (210°→350°, 50→0 mm): minimale R₀ = 46.0 mm` → maatgevend
- **Nok_1 cel 7:** grafiek van s(θ), v(θ), a(θ), j(θ) over 360° — zichtbare segmentovergangen zijn glad

---

## Vraag 5 – Frequentie-inhoud van het ontwerp

### ① Volledig antwoord (Ingenieursinzicht)

**Wat zijn de relevante frequenties?**

De nok draait aan **120 rpm = 2 Hz**. Dit is de **fundamentele frequentie** van het aandrijfsignaal. Door de niet-sinusoïdale nokgeometrie bevat het krachtsignaal harmonischen: 2 Hz, 4 Hz, 6 Hz, 8 Hz, enz.

Het **volger-veersysteem** heeft een eigen frequentie:
$$\omega_n = \sqrt{\frac{k}{m}} = \sqrt{\frac{25\,000}{29}} = 29{,}4 \; \text{rad/s} = \mathbf{4{,}67 \; \text{Hz}}$$

De snelheidsverhouding op de fundamentele:
$$r_1 = \frac{2 \; \text{Hz}}{4{,}67 \; \text{Hz}} = 0{,}43 \quad \Rightarrow \text{sub-resonant ✓}$$

**Waarom de 2e harmonisch kritisch is:**

De aandrijffrequentie op de 2e harmonisch is 2 × 2 Hz = **4 Hz**, waarvoor:
$$r_2 = \frac{4 \; \text{Hz}}{4{,}67 \; \text{Hz}} = 0{,}86$$

Bij r = 0,86 en ζ = 0,094 is de dynamische versterkingsfactor:
$$\text{DMF} = \frac{1}{\sqrt{(1-r^2)^2 + (2\zeta r)^2}} = \frac{1}{\sqrt{(0{,}26)^2 + (0{,}16)^2}} = \frac{1}{\sqrt{0{,}092}} \approx \mathbf{3{,}3}$$

De 2e harmonisch van het krachtsignaal wordt dus met factor ~3,3 versterkt door de veermassadynamica. Dit is **de reden waarom ζ = 0,094 bewust gekozen werd**: zonder demping zou r = 0,86 een versterkingsfactor van 1/(1-0,74) ≈ 3,8 geven. Met ζ = 0,094 wordt dit teruggebracht naar 3,3 — een aanvaardbare versterking.

**Praktische consequentie — zweefgedrag:**

Zweefgedrag treedt op als de dynamische normaalkracht tijdelijk negatief wordt. De versterking van de 2e harmonisch kan de veer­kracht tijdelijk overwinnen. De preload van **300 N** fungeert hier als buffer: zolang de dynamische amplitude kleiner dan 300 N is, verliest de volger het contact niet.

**Wat de frequentie-inhoud ons leert voor het ontwerp:**

- Het systeem werkt veilig sub-resonant op de fundamentele (r₁ = 0,43).
- De 2e harmonisch is kritisch: r₂ = 0,86. Bij het **opstarten** (nok van 0 naar 120 rpm) passeert het systeem door r₂ = 1 (resonantie). Dit is de gevaarlijkste fase: korte maar hevige amplificatie.
- De gekozen ζ = 0,094 beperkt de resonantiedoorloop, maar bij een snelle opstart is de normaalkracht tijdelijk >600 N door de dinamische versterking.
- Als het toerental zou stijgen naar 280 rpm: r₁ = 280 rpm / (4,67 Hz × 60) = 4,67/4,67 = **1,0** — volledige resonantie op de fundamentele. **De machine mag nooit boven 260 rpm draaien.**

### ② "Wat als..."-bijvragen

**"Wat als we de veer stijver maken (k = 100 000 N/m)?"**
> ω_n = √(100 000/29) = 58,7 rad/s = 9,34 Hz. Nu is r₁ = 2/9,34 = 0,21 (diefer sub-resonant), r₂ = 4/9,34 = 0,43 (ook comfortabel). De dynamische versterking bij de 2e harmonisch daalt naar ~1,2 — een veiligere marge. Nadeel: hogere veerstijfheid → hogere normaalkracht → meer wrijvingsverliezen en hoger motorvermogen. De veervoorspanning moet ook opnieuw bepaald worden.

**"Hoe detecteer je zweefgedrag (contactverlies) in het ontwerp?"**
> Zweefgedrag treedt op als de minimale normaalkracht in de simulatie ≤ 0 N wordt. In Nok_2 print de code `Minimale normaalkracht: 300 N` — gelijk aan de preload, wat aangeeft dat de volger op het dwell-segment zit (geen beweging, geen inertiabijdrage). Als de externe belasting negatief piekt of de versnelling groot genoeg is om 300 N te overwinnen, wordt dit negatief. Controleer dit door de preload tijdelijk te halveren en de min-normaalkracht te herberekenen.

**"Waarom is de 3e harmonisch (6 Hz) minder gevaarlijk dan de 2e (4 Hz)?"**
> r₃ = 6/4,67 = 1,28 → dit is bóven resonantie. De DMF bij r > 1 daalt naar 1/(r²-1) × correctie, en bij r = 1,28 is DMF ≈ 1,4. De 3e harmonisch wordt dus minder versterkt (1,4×) dan de 2e (3,3×). Bovendien bevat het nokprofiel typisch minder energie in de 3e harmonisch dan in de 2e, door de gladde 5e-graads polynoom vorm.

### ③ Verificatie in de notebooks

- **Nok_2 setup-cel:** k = 25 000 N/m, m = 29 kg, ζ = 0,094 — van hieruit: ω_n = 29,4 rad/s
- **Nok_2 cel 7:** plot van aandrijfkoppel M(θ) — frequentie-inhoud zichtbaar als variatie over 360°
- **Nok_2 cel 5:** `Minimale normaalkracht: 300 N` — geen zweefgedrag bevestigd
- *Extra verificatie: `np.fft.fft` toepassen op normalforce_tot vs. theta_rad om harmonischen kwantitatief te bepalen*

---

## Vraag 6 – Dode punten (singulariteiten)

### ① Volledig antwoord (Ingenieursinzicht)

**Heeft een nokkenmechanisme "dode punten"?**

Niet in de klassieke zin (zoals een kniehefboom bij TDC/BDC). Maar er zijn vier analoge faalmodi die als "singulariteiten" beschouwd kunnen worden:

**1. Zelfblokkeringsgrens: µ·|tan α| ≥ 1**

De noemer van de normaalkrachtformule wordt nul — de volger blokkeert in de geleiding. Met μ = 0,10 vereist dit |tan α| ≥ 10, dus α ≥ 84°. Onze maximale drukhoek is 21,86°, en tan(21,86°) = 0,40. De zelfblokkering­sfactor µ·|tan α| = 0,10 × 0,40 = **0,040 ≪ 1**. Veilige marge van factor 25.

**2. Drukhoekgrens: α → 90° (krachtoverdracht onmogelijk)**

Bij α = 90° werkt de normaalkracht loodrecht op de heffingsrichting — geen vertikale component meer. In de praktijk geldt de richtwaarde α < 30°. Onze max α = **21,86° < 30° ✓** met een comfortabele marge van 8°.

**3. Ondersnijding: ρ_pitch ≤ R_r**

De rol kan het dal van het nokprofiel geometrisch niet meer volgen: het nokoppervlak "snijdt" de pitch-kromme in zichzelf. Onze minimale ρ_pitch = **40,6 mm** tegenover R_r = 15 mm — een factor 2,7 marge. **Geen ondersnijding** over de volledige omwenteling, bevestigd door code.

**4. Zweefgedrag (lift-off): F_normaal ≤ 0**

De volger verliest contact met de nok. Ons ontwerp garandeert F_normaal ≥ **300 N** (de preload) voor alle θ — geen zweefgedrag.

**Is het erg? Wat is de drempel?**

Van alle vier de singulariteiten is **ondersnijding** de ernstigste: de nok loopt fysiek vast en kan mechanisch breken. Drukhoek en zelfblokkering degraderen geleidelijk (meer wrijving, hogere krachten), maar het mechanisme blijft werken — weliswaar slechter. Zweefgedrag veroorzaakt slijtage door herhaald contact-verlies en -herstel.

**Is een drukhoek van 0° het beste ontwerp?**

Nee. Een drukhoek van nul betekent ds/dθ = 0 — geen beweging. Een kleine drukhoek is onvermijdelijk zodra de volger beweegt. Het ontwerp­doel is α < 30° te realiseren met de kleinste R₀ (compactheid). Ons R₀ = 50 mm is exact de grens: het maatgevende terugkeersegment vereist minimaal 46 mm, en we kiezen 50 mm voor een kleine veiligheidsmarge.

### ② "Wat als..."-bijvragen

**"Wat als R₀ verkleind wordt naar 40 mm om het ontwerp compacter te maken?"**
> Het maatgevende segment (terugkeer) heeft dan α_max ≈ 32–33° — net boven de 30°-richtwaarde. Dit is niet catastrofaal maar ongewenst: de zijwaartse wrijvingskracht in de geleiding stijgt, de motor moet meer koppel leveren, en de geleiding slijt sneller. De Kloomok-Muffley analyse uit Nok_1 toont precies hoe snel α stijgt bij verlaging van R₀ — de grafiek is steil bij R₀ < 46 mm.

**"Wat als de rolstraal R_r vergroot wordt tot 30 mm (bijna de grens voor ondersnijding)?"**
> ρ_min_pitch = 40,6 mm bij de gekozen R₀ = 50 mm. Keuze R_r = 30 mm laat nog maar 10,6 mm marge op het ondersnijdingscriterium. Een kleine productiefout (ruwe nokcontour) kan dan lokaal ρ < R_r veroorzaken. Bovendien: een grotere rol is zwaarder, wat de equivalente massa m verhoogt en de veerpreload opdrijft. De winst (lagere contactdruk) wegen niet op tegen het risico.

**"Is een dood punt bij de bovenste dwellpositie (s = 50 mm) nuttig?"**
> In ons ontwerp is het dwell-segment een vlakke nokstrook — geen singulariteit. Maar in een kniehefboom-nok (toggle-nok) kan men het profiel zo ontwerpen dat de geometrische vergrendeling werkt voor werkstukbevestiging. Bij ons mechanisme heeft die aanpak geen zin omdat de externe belasting via de nok inwerkt en niet via de volger­vergrendeling.

### ③ Verificatie in de notebooks

- **Nok_1 cel 18 output:** `Geen ondersnijding (rho_pitch > R_r voor alle theta)` ✓
- **Nok_1 cel 18:** plotRadiusCurvature — minimale positieve ρ = 40,6 mm zichtbaar
- **Nok_1 cel 18:** plotPressureAngle — max 21,86°, duidelijk onder de 30°-grens
- **Nok_2 cel 5 output:** `Minimale normaalkracht: 300 N` — geen zweefgedrag ✓

---

## Vraag 7 – Onbalans-eigenschappen

### ① Volledig antwoord (Ingenieursinzicht)

**Twee onbalans-mechanismen spelen een rol:**

**A. Roterende onbalans van de nok zelf**

De nok is geometrisch asymmetrisch: aan de uitstekende kant (heffingsgebied, straal ~100 mm) zit meer materiaal dan aan de terugkeerzone (straal ~35 mm). Het massamiddelpunt van de nok ligt niet op de rotatieas. Dit geeft een **centrifugale onbalanskracht**:

$$F_{centrifugaal} = m_{nok} \cdot e_{cm} \cdot \omega^2$$

Als de nok een stalen schijf is (geschat ~1,5 kg) met CM-offset ~12 mm:
$$F_{centrifugaal} \approx 1{,}5 \times 0{,}012 \times (12{,}57)^2 = \mathbf{2{,}8 \; \text{N}}$$

Bij 120 rpm is dit verwaarloosbaar. Maar bij 1 200 rpm (ω = 125,7 rad/s) stijgt dit naar 280 N — dan worden statische balancering en tegengewichten noodzakelijk.

**B. Oscillerende inertiakracht van de translerende volger**

De volger (m = 29 kg) versnelt verticaal tot ±9,89 m/s². De maximale inertiakracht bedraagt:
$$F_{inertie,max} = m \cdot a_{max} = 29 \times 9{,}89 = \mathbf{287 \; \text{N}}$$

Deze kracht reageert op het frame als een **oscillerende vertikale kracht**. De bijbehorende reactie op de nokas is de vertikale component van de normaalkracht. Dit is de dominante onbalanskracht van het systeem: 287 N vertikaal oscillerend bij 2 Hz en zijn harmonischen.

**Vormt dit een probleem?**

Bij 120 rpm en een goed gefundeerd frame: de 2 Hz oscillatie is te laag om resonantie te veroorzaken in een typische machinebedding (eigenfrequentie frame: 20–100 Hz). De 287 N zijn de maximale waarden maar treden slechts kortstondig op (bij de piekversnellings-hoeken). De gemiddelde schokbelasting op het frame is veel lager.

**Wanneer wordt onbalans kritisch?**

Als de machine op een lichte, onvoldoende gestijfde staander staat. Een 29 kg massa die met 10 m/s² oscilleert veroorzaakt bij resonantie met de staander amplitudes die lagers en bevestigingsboulons snel beschadigen. Maatregelen: fundament verzwaren, trillingsdempers onder de machine, of actieve balansmassa toevoegen (counterweight dat 180° uit fase oscilleert).

### ② "Wat als..."-bijvragen

**"Hoe balanceer je de roterende nok?"**
> Materiaalverwijdering aan de uitstekende zijde (frezen van een "pocket") of toevoeging van een tegengewicht aan de diametraal tegenovergestelde zijde van de as. Het doel: het CM van de nok op de rotatieas brengen. Dit heet **statisch balanc­eren** (bij een dunne nok voldoende). Bij een brede cilindrische nok is ook **dynamisch balanceren** nodig om het koppel van het krachtentweespan te elimineren.

**"Hoe reduceert een tegengewicht de kracht op het frame?"**
> Voeg aan de nokas een tegengewicht toe dat 180° tegenover de nokuitstulping zit en een gelijk groot CM-offset heeft. De centrifugale krachten heffen elkaar op → de roterende onbalanskracht op de lagers verdwijnt. Dit verandert niets aan de oscillerende kracht van de volger (die is lineair, niet rotatoir).

**"Wat als de volger veel zwaarder gemaakt wordt (m = 100 kg)?"**
> F_inertie_max = 100 × 9,89 = 989 N — bijna de externe belasting evenaren. Dit heeft twee effecten: de preload moet drastisch hoger (de veer moet 989 N inertiakracht opvangen), én de krachten op het frame stijgen naar ~1 000 N oscillerend bij 2 Hz. Dit veroorzaakt ernstige trillingen. Concl.: massa reduceren is de slimste maatregel voor het beheersen van zowel de onbalans als het motorvermogen.

### ③ Verificatie in de notebooks

- **Nok_2 setup-cel:** m = 29 kg, omega = 12,57 rad/s — van hieruit: F_inertie berekend
- **Nok_1 cel 7:** plot van a(θ) — max ±9,89 m/s² bij de steilste segmentovergangen
- **Nok_2 cel 5:** normaalkrachtplot — de inertiabijdrage (normalforce_acc) is apart getekend

---

## Vraag 8 – Relatieve invloeden van inertie, wrijving en stijfheid

### ① Volledig antwoord (Ingenieursinzicht)

**Kwantitatieve krachtendecompositie:**

Bij de piekbelasting van het systeem (θ ≈ 130°, externe last op maximum) tellen volgende krachten mee in de normaalkracht:

| Bijdrage | Grootte | % van totaal |
|----------|---------|-------------|
| Externe belasting | **1 250 N** | ~55% |
| Veerkracht (k·s + F_preload) | **1 300–1 550 N** (bij s = 40–50 mm) | ~40% |
| Inertiakracht (m·a) | max **287 N** | ~10% (tijdelijk) |
| Dempingskracht (c·v) | max **~77 N** | ~3% |

De externe belasting en de veerkracht domineren. De inertiekracht is significant (287 N = bijna het gewicht van 29 kg), maar tijdelijk. De demping is klein.

**Stijfheid:**

De veerstijfheid k = 25 000 N/m (25 N/mm) vervult twee functies:
1. **Contact-bewaarder**: preload 300 N + k·s houdt de volger op de nok bij alle hoeken.
2. **Energiebuffer**: bij de hefsslag laadt de veer op (energie in: ½k·s²_max = ½×25000×0,05² = 31,25 J), bij de terugkeer geeft ze energie terug aan de as. Dit verlaagt het piekmoment op de motor bij de terugkeerslag.

**Wrijving:**

Met μ = 0,10 en de typische normaalkracht van ~1 800 N bij piekbelasting:
- Wrijvingskracht in geleiding: 0,10 × 1 800 × sin(22°) = **67 N** (langs geleiding, loodrecht op beweging)
- De dempingskracht c·v is een aparte bijdrage: c = ζ × 2√(km) = 0,094 × 2 × 851 = 160 N·s/m
- F_demping_max = 160 × 0,482 = **77 N**

Beide wrijvingsbijdragen zijn samen ~144 N of ~6% van de piekbelasting. Wrijving is hier **niet dominant** omdat de externe belasting zo groot is (1 250 N). In een systeem zonder externe last zou wrijving proporitioneel een grotere rol spelen.

**Stijfheid van de nok zelf (nokstijfheid):**

In ons model nemen we de nok als star aan. In werkelijkheid heeft staal een contactstijfheid bij Hertz-contact: bij een normaalkracht van 1 800 N en een rolstraal van 15 mm geeft de Hertz-theorie een elastische deformatie van ~1–5 µm. Dit is verwaarloosbaar voor de kinematica maar relevant voor de levensduurberekening (contactspanning en vermoeiing).

### ② "Wat als..."-bijvragen

**"Wanneer wordt inertie dominant ten opzichte van de veerkracht?"**
> De inertiekracht m·a overtreft de gemiddelde veerkracht als:
> m·a_max > k·s_gem + F_preload
> 29 × a_max > 25 000 × 0,025 + 300 = 925 N → a_max > 31,9 m/s²
> Dit vereist ω > √(31,9/9,89) × 12,57 ≈ 22,7 rad/s × 12,57 = **22,7 rad/s ≈ 217 rpm**.
> Boven ~220 rpm wordt inertie de dominante kracht en moeten motor, veer en as opnieuw gedimensioneerd worden.

**"Wat als de wrijvingscoëfficiënt stijgt naar μ = 0,30 (versleten, ongesmeerde geleiding)?"**
> De wrijvingsfactor in de normaalkrachtformule wordt: 1/(1 − µ·|tan α|). Bij α = 22° en μ = 0,30:
> factor = 1/(1 − 0,30 × 0,40) = 1/(0,88) = 1,14.
> De normaalkracht stijgt met 14%. Dit verhoogt het motorvermogen en versnelt de slijtage van de geleiding verder — een positieve terugkoppellus. Onderhoud (smering) is hier kritisch.

**"Is de veerstijfheid k een ontwerp­vrijheidsgraad of een vaste keuze?"**
> k is een vrije keuze. De eis is: (k·s_max + F_preload) moet groot genoeg zijn om bij de maximale negatieve normaalkrachtbijdrage (extreme versnelling) nog positief te blijven. Voor ons systeem is de minimale preload 284,5 N. Een hogere k verbeeldt de dynamische veiligheid maar verhoogt de wrijvingsverliezen. k = 25 N/mm is een ingenieurskompromis: de veer is niet te zwaar (wrijvingsverlies) maar voldoende stijf voor het draaispectrum tot 120 rpm.

### ③ Verificatie in de notebooks

- **Nok_2 cel 5 output:** `spring_preload_min = 284.5 N` — de grenswaarde voor geen zweefgedrag
- **Nok_2 cel 5:** plotForces → aparte curves voor acc-bijdrage, external load, spring, damping
- **Nok_2 setup-cel:** µ = 0,10, k_veer = 25 000 N/m, c_demping berekend uit ζ

---

## Vraag 9 – Meerdere kopieën parallel aan dezelfde motor

### ① Volledig antwoord (Ingenieursinzicht)

**Scenario A: N identieke nokken op dezelfde as (meest realistisch)**

De meest gangbare industriële implementatie is N nokken op één gemeenschappelijke as, elk met eigen volger en veer. Alle nokken draaien synchroon. De motor ziet de som van alle N aandrijfkoppels.

Voor N kopieën:
- Totaal motorvermogen: N × 110 = **N × 110 W**
- Totaal aandrijfkoppel (gem): N × 8,78 = **N × 8,78 Nm**
- Energiefluctuatie: ΔE_totaal hangt af van de **fasering**

**Effect van fasering op de vliegwieleis:**

Als alle N nokken **in fase** zijn: ΔE_totaal = N × 65,9 J. Het vliegwiel moet N keer groter. De koppelpieken vallen samen — de motor heeft een factor N hogere piekkoppelvraag.

Als de N nokken **gelijkmatig over 360° gefaseerd** zijn (fasestap = 360°/N): de koppelpieken van de individuele nokken overlappen minimaal. ΔE_totaal daalt — soms tot bijna nul voor een goed verdeeld profiel. Dit is precies het principe van een meercilindermotor: gefaseerde nokken geven een vlakkere koppelkromme.

Voor ons mechanisme met een koppelpatroon dat sterk geconcentreerd is bij θ = 120°–170° (externe belasting):
- Bij N = 2, fasestap 180°: de tweede nok pikt op wanneer de eerste klaarzet — ΔE kan met ~50% dalen.
- Bij N = 4, fasestap 90°: nog vlakker, ΔE daalt verder.

**Dimensionering as en lagers:**

De as draagt N × torsiemoment + buiging door de N normaalkrachten. De as-diameter moet proportioneel met N mee­schatten. De lagerbelasting stijgt lineair met N.

**Scenario B: N parallelle volgers op dezelfde nok (beperkt toepasbaar)**

Alleen mogelijk bij een brede cilindrische nok of bij volgers op exact dezelfde hoogte (dezelfde s(θ)). In de praktijk ongebruikelijk voor translerende volgers, maar standaard bij nokkenasontwerp voor meercilindermotor.

### ② "Wat als..."-bijvragen

**"Hoeveel winst geeft fasering van 2 nokken bij 180°?"**
> Het koppelpatroon van onze nok heeft een piek bij θ = 120°–170°. De tweede nok bij 180°-fasering heeft zijn piek bij θ + 180° = 300°–350°. Afhankelijk van het koppelverloop in de terugkeerfase (waar de veer energie teruggeeft) kunnen beide pieken elkaar gedeeltelijk compenseren. De kwantitatieve winst vereist de som M₁(θ) + M₂(θ+180°) te plotten — dit kan met minimale aanpassing van Nok_2 (tweede curve optellen).

**"Wat verandert er NIET als je van 1 naar N nokken gaat?"**
> De kinematica (s(θ), v(θ), a(θ)) van elke individuele nok is identiek — Nok_1 hoeft niet opnieuw te worden uitgevoerd. De drukhoek, ondersnijding, en het veer-ontwerp per volger veranderen niet. Alleen de motorspecificatie, de asdimensionering en de vliegwielberekening schalen mee.

**"Als je 6 identieke nokken wilt aandrijven, welk aspect faalt het eerste?"**
> Meest waarschijnlijk de as. 6 × 8,78 Nm = 52,7 Nm totaal aandrijfkoppel + buiging door de 6 normaalkrachten. Een dunne stalen as van 20 mm diameter heeft een torsie-grens van ~100 Nm voor normaal staal — dit volstaat, maar de buigbelasting door de normaalkrachten kan de as overbelasten. De diameter moet navenant groter gekozen worden.

### ③ Verificatie in de notebooks

- **Nok_2 cel 7:** basiswaarden per nok: P_gem = 110 W, M_gem = 8,78 Nm, ΔE = 65,9 J
- **Nok_2 cel 7:** plot van M(θ) — hieruit af te lezen hoe fasering van een tweede nok het totaalkoppel zou afvlakken

---

## Vraag 10 – Parameterwijziging: voorspelling en verificatie

### ① Volledig antwoord (Ingenieursinzicht)

**Gekozen parameterwijziging: toerental verhogen van 120 rpm naar 180 rpm (T = 0,333 s)**

Dit simuleert een productiviteitsverhoging van 50% — een typische vraag uit de industrie.

**Voorspelling (kwalitatief):**

Het nokprofiel s(θ) is hoekgebaseerd en verandert **niet**. Wat verandert:
1. ω stijgt van 12,57 naar 18,85 rad/s (factor 1,5)
2. Versnellingen schalen met ω²: a_max gaat van 9,89 naar 9,89 × (1,5)² = **22,3 m/s²**
3. Inertiakracht: van 287 naar **646 N** (factor 2,25)
4. De minimale preload: spring_preload_min stijgt proportioneel met de inertiakracht
5. Energiefluctuatie ΔE ≈ constant (de koppelfluctuatie stijgt maar ω² stijgt ook) → I_vliegwiel daalt
6. Gemiddeld motorvermogen: P = M·ω — het koppel stijgt (meer inertie), ω stijgt → P stijgt superlineair

**Kwantitatieve voorspelling:**

Nieuwe preload-min: de inertiabijdrage stijgt met factor 2,25, overige termen ongewijzigd.
Spring_preload_min_orig = 284,5 N bevatte: inertie-bijdrage + externe last + veerkracht – zwaartekracht.

De inertiebijdrage was: m × a_mm × ω_orig² / 1000 ≈ weg te lezen uit notebook. Ruw geschat: inertie ~100 N bij 120 rpm → bij 180 rpm: ~225 N. Spring_preload_min stijgt van ~285 naar ~410 N → afronden naar **500 N** met veiligheidsmarge.

Vliegwieltraagheid: I ∝ ΔE/ω² → als ΔE gelijk blijft: I_new = 8,35 × (120/180)² = **3,71 kg·m²** — het vliegwiel kan kleiner.

**Snelheidsverhouding na parameterwijziging:**

r₁_new = (180/60 Hz) / 4,67 Hz = 3 Hz / 4,67 Hz = **0,64** — nog steeds sub-resonant ✓
r₂_new = 6 Hz / 4,67 Hz = **1,28** — de 2e harmonisch is nu bóven resonantie → minder gevaarlijk dan bij 120 rpm

**Verificatie (aanpassing Nok_2):**

Pas in Nok_2 de setup-cel aan: `T_cyclus = 1/3` (= 0,333 s), herbereken alle cellen, en lees de nieuwe spring_preload_min en vliegwieltraagheid af.

**Wat verandert niet:**

De geometrie van de nok (R₀, R_r, profiel s(θ)) is volledig ongewijzigd. De drukhoek en ondersnijdingscheck in Nok_1 hoeven niet opnieuw bepaald te worden. Alleen Nok_2 herberekenen volstaat.

### ② "Wat als..."-bijvragen

**"Moet je Nok_1 opnieuw uitvoeren na deze parameterwijziging?"**
> Nee. Het nokprofiel s(θ) is een geometrische functie van de nokhoek θ. De snelheid waarmee de nok draait (rpm) verandert de profilering niet. Alle kinematische plots (drukhoek, kromtestraal, nokcontour) in Nok_1 zijn nog geldig. Alleen de tijdgebaseerde grootheden (snelheid in m/s, versnelling in m/s²) veranderen — maar die zijn afleidbaar via ω.

**"Welke parameter zou de drukhoek het meest veranderen?"**
> R₀ verkleinen. De drukhoek is tan(α) = (ds/dθ) / (R₀ + s). Een kleinere R₀ verhoogt α sterk (noemertje kleiner). Toerental heeft géén effect op de drukhoek: die is uitsluitend geometrisch bepaald door s(θ) en R₀. Dat toont het elegante principe van het nokontwerp: de geometrie en de snelheid zijn volkomen ontkoppeld.

**"Wat is de maximale veilige snelheid voor dit mechanisme?"**
> De grens wordt bepaald door zweefgedrag: de preload moet groter blijven dan de maximum inertiakracht: F_preload > m × a_max = m × (h/β²) × C_a × ω². Oplossend: ω_max = √(F_preload / (m × (h/β²) × C_a)) ≈ √(300 / (29 × 9,89/158)) = √(300 / 1,815) = **12,9 rad/s ≈ 123 rpm** bij de huidige preload van 300 N. De machine draait dus exact aan de veiligheidsgrens. Verhoog de preload naar 500 N vooraleer het toerental te verhogen.

### ③ Verificatie in de notebooks

- **Nok_2 setup-cel:** aanpassen `T_cyclus = 1/3` → herbereken alle cellen
- **Nok_2 cel 5 output:** nieuwe spring_preload_min vergelijken met voorspelling
- **Nok_2 cel 7 output:** nieuw P_gem, ΔE, I_vliegwiel vergelijken met de kwadratische schaling

---

## Vaste bijvraag – Motorkosten en energieverbruik

### ① Volledig antwoord

**Motor-specificaties (uit Nok_2 cel 7):**

```
Gemiddeld vermogen:    110 W
Gemiddeld koppel:      8,78 Nm
Piek koppel:           ~15–20 Nm (geschat uit koppelprofiel, zie grafiek)
Toerental nokas:       120 rpm
Energiefluctuatie ΔE:  65,9 J
```

**Motortype en indicatieve prijs:**

Een inductiemotor 150–200 W is technisch voldoende voor het gemiddeld vermogen, maar een vliegwiel van 8,35 kg·m² is dan verplicht voor de koppelpieken. Totaal kost:
- Inductiemotor 200 W: **€80–180**
- Vliegwiel (staal, ~100 kg bij r = 0,3 m): **€300–800** (bewerkt staal)
- Totaal klassieke aanpak: **~€400–1 000**

Moderne aanpak: **servomotor met drive** (geen vliegwiel nodig):
- Servomotor 200 W, 10 Nm nominaal koppel: **€300–800** (bijv. SEW, Siemens)
- Servo-drive/controller: **€400–1 000**
- Totaal servo-aanpak: **€700–1 800**

De servo-aanpak is duurder maar elimineert het 100 kg-vliegwiel, bespaar ruimte, verbetert de stopprecisie en laat snelheidsaanpassing toe zonder mechanische ingrepen.

**Motorkoppel vanuit nokgeometrie:**

$$T_{motor} = \frac{M_{aandrijf}}{i} = M_{aandrijf} \quad (\text{directe aandrijving, geen reductor})$$

Gemiddeld koppel = 8,78 Nm, met veiligheidsfactor 1,5: **T_{ontwerp} = 13,2 Nm nominaal**.

**Energieverbruik:**

- Netto mechanisch vermogen: 110 W gemiddeld
- Motor-efficiëntie η ≈ 0,85: elektrisch vermogen = **130 W**
- Fabrieksomgeving (1 shift = 8 h/dag, 250 werkdagen/jaar): werkuren = 2 000 h/jaar
- Jaarlijks energieverbruik: E = 0,130 kW × 2 000 h = **260 kWh/jaar**
- Kostprijs bij €0,30/kWh: **€78/jaar**

Voor 2-shifts productie (4 000 h/jaar): E = 520 kWh/jaar ≈ **€156/jaar**

### ② Bijvragen

**"Hoe berekent u het motorkoppel vanuit de aandrijfkracht?"**
$$M_{motor} = F_{nok,vert} \cdot \frac{ds}{d\theta}$$
Op het piek van de externe belasting (ds/dθ ≈ 0,25 mm/° = 14,3 mm/rad bij terugkeer):
$M \approx 1\,800 \times 0{,}014 \approx 25 \; \text{Nm}$ (piekkoppel, bevestigd door Nok_2 cel 7 grafiek)

**"Waarom is het energieverbruik zo relatief hoog vergeleken met de parasol?"**
> Twee redenen: (1) het mechanisme draait continu in een machine (niet één slag per dag), en (2) de externe belasting van 1 250 N over een 50 mm heffing levert per cyclus: E_ext ≈ 1 250 × 0,04 = 50 J, terwijl de cyclustijd slechts 0,5 s is → P_ext ≈ 100 W. De machine doet nuttig werk per cyclus, niet alleen een parasol openen. Bij 120 rpm × 3 600 s/h = 432 000 werkstroken per uur.

**"Is het efficiënt de veer te gebruiken voor energieterugwinning?"**
> Ja, de veer doet dit automatisch. Bij heffing laadt de veer op (motor levert energie aan veer + werkstuk). Bij terugkeer geeft de veer energie terug aan de nokas. Dit verlaagt het piek-motorkoppel in de terugkeerslag. Hoe hoger k, hoe meer buffering — maar ook hogere wrijvingsverliezen. Voor ons k = 25 N/mm bij 50 mm heffing: E_veer = ½ × 25 000 × (0,05)² = **31,25 J per cyclus** buffering — bijna de helft van de externe werkenergie per cyclus.

---

## Examensamenvatting: kernformules nokkenmechanisme

| Stap | Formule / Principe | Kritische grens |
|------|-------------------|-----------------|
| Heffingsprofiel | $s(\theta) = h \cdot y(\tau)$, $\tau = (\theta-\theta_0)/\beta$ | — |
| Drukhoek | $\tan\alpha = \frac{ds/d\theta - e}{\sqrt{(R_0+R_r)^2-e^2}+s}$ | **α < 30°** |
| Geen ondersnijding | $\rho_{pitch} > R_r$ voor alle $\theta$ | **ρ_min = 40,6 mm > 15 mm ✓** |
| Geen zweefgedrag | $F_{normaal} > 0 \;\forall\theta$ | **F_min = 300 N (preload) ✓** |
| Preload minimum | $F_{preload} \geq \max(-k\cdot s - m\ddot{s} - F_{ext})$ | **284,5 N → 300 N** |
| Aandrijfkoppel | $M(\theta) = F_{nok,vert} \cdot ds/d\theta$ | — |
| Eigenfrequentie | $\omega_n = \sqrt{k/m} = 29{,}4 \; \text{rad/s}$ | r = ω/ω_n < 1 |
| Vliegwieltraagheid | $I = \Delta E / (K\omega^2)$ | **8,35 kg·m² (K=0,05)** |
| Zelfblokkering-grens | $\mu \cdot \lvert\tan\alpha\rvert < 1$ | **0,040 ≪ 1 ✓** |

**Top-3 inzichten voor het mondeling:**
1. **De terugkeerslag is alles­bepalend**: de 50 mm over 140° geeft de hoogste ds/dθ → maatgevend voor drukhoek (R₀ = 50 mm), ondersnijding (R_r = 15 mm) én de koppelpiek in de vliegwielberekening.
2. **2e harmonisch = 4 Hz ≈ ω_n/1,17**: de eigenfrequentie van het systeem ligt gevaarlijk dicht bij de 2e harmonisch van de aandrijving. ζ = 0,094 is bewust gekozen om de resonantiedoorloop bij het opstarten te beheersen.
3. **Vliegwiel van 8,35 kg·m² is onpraktisch bij 120 rpm**: de lage snelheid maakt een vliegwiel enorm zwaar. De moderne oplossing is een servomotor met condensatorbank — geen mechanisch vliegwiel nodig.
