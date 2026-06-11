# Begrippenlijst Mondeling – Stangenmechanisme (Overdekking)

> **Doel:** elk van deze begrippen in je eigen woorden kunnen uitleggen aan de prof — geen formules, wel het fysische "waarom" en concrete cijfers uit onze case (`b=6,0 m`, `n=2` mechanismen, `s: 1,875 → 0,600 m`, `condition_scurve`, met trekveren).
>
> Gebruik dit als aanvulling op `AntwoordenALEX_Mondeling_Stangen.md` (de 10 vragen). Daar vind je het volledige antwoord; hier vind je de bouwstenen die in bijna elk antwoord terugkomen.

## Snelle navigatie

| # | Begrip | Hoort vooral bij |
|---|---|---|
| 1 | `cond(A)` — conditiegetal | Notebook 1, Vraag 6 |
| 2 | Dood punt / singulariteit | Notebook 1, Vraag 6 |
| 3 | `condition_scurve` (bewegingstraject) | Notebook 1, Vraag 4 |
| 4 | Inverse dynamica & Newton-Euler | Notebook 2 |
| 5 | Onbalans vs. schuiverreactie | Notebook 2, Vraag 7 |
| 6 | Trekveren (energiebuffer) | Notebook 3-Trekveren, Vraag 3 |
| 7 | Vliegwiel — en waarom niet | Notebook 3-Trekveren, Vraag 3 |
| 8 | Equivalente massa $m_{K,eq}$ | Notebook 3-Overdekking |
| 9 | Benutting / utilisatie $U$ | Notebook 3-Overdekking, Vraag 1/8 |
| 10 | Dimensionering van de motor | Notebook 4, Vraag 2/9 |
| 11 | Rendementsketen $\eta$ | Notebook 4 |
| 12 | Gemeenschappelijke as & astorsie | Notebook 4, Vraag 9 |
| 13 | Frequentiescheiding & resonantie | Notebook 4, Vraag 5 |

---

## 1. `cond(A)` — conditiegetal

**In één zin:** een getal dat zegt hoe "gevoelig" het mechanisme is — hoe sterk een kleine fout in de schuiverpositie wordt uitvergroot tot fouten in de hoeken/snelheden van de stangen.

**Simpele uitleg:** Bij elke stand van het mechanisme moet je een stelsel vergelijkingen oplossen om te weten hoe snel elke stang draait als de schuiver beweegt. `cond(A)` zegt hoe "wankel" dat stelsel daar is. Laag = robuust, een kleine afwijking blijft klein. Hoog = gevoelig, een kleine afwijking (speling, meetfout, slijtage) geeft een veel grotere fout in de uitkomst. Oneindig = dood punt (zie hieronder) — daar werkt het stelsel helemaal niet meer.

Belangrijk: `cond(A)` is **geen kracht en geen hoek** — het is puur een wiskundige gevoeligheidsmaat van de kinematica, maar ze vertaalt zich rechtstreeks naar iets fysisch: hoe voorspelbaar/robuust het mechanisme reageert.

**Cijfers uit ons project:**
- `cond(A)` varieert over de slag van **4,70 (laagste) tot 68,36 (hoogste)**, gemiddeld ~22,4.
- De piek (68,36) ligt in de **gesloten stand** (`s = 1,875 m`).
- Het "veilige bereik" wordt gedefinieerd als `cond(A) < 10.000`. Onze 68,36 zit daar met een marge van **~146×** ruim onder.
- **Conclusie:** geen singulariteit, maar wel de gevoeligste zone — daarom vertraagt het traject daar bewust (zie `condition_scurve`).

**Hoe je het zegt:** *"cond(A) is geen fysische kracht, maar een gevoeligheidsmaat van de kinematica: hoe sterk een kleine schuiverfout zich vertaalt naar hoekfouten in de stangen. Bij ons blijft dat overal eindig en met grote marge onder de veilige grens — de piek in de gesloten stand is een signaal om daar voorzichtig te bewegen, geen probleem op zich."*

---

## 2. Dood punt / singulariteit

**In één zin:** een stand van het mechanisme waarin twee stangen "in elkaars verlengde" liggen, waardoor de aandrijving daar geen grip meer heeft — `cond(A) → ∞`.

**Simpele uitleg:** Stel je een scharnierend mechanisme voor waarbij twee staven op een bepaald moment een rechte lijn vormen. Op dat moment kan een kleine input (de schuiver een beetje verschuiven) een oneindig grote of een nul-verandering geven aan de uitvoer — de aandrijving "weet niet meer welke kant op". In zo'n stand kan het mechanisme vastlopen óf juist heel onvoorspelbaar/snel bewegen.

Een dood punt is niet altijd slecht: bij een **klem** of **kniehefboom** gebruik je het net bewust om een eindstand mechanisch te vergrendelen (de aandrijfkracht hoeft daar niets meer te leveren om de stand vast te houden).

**Cijfers uit ons project:**
- Wij doorlopen **geen** dood punt: max `cond(A) = 68,36 << 10.000`.
- We kiezen er bewust **niet** voor om een dood punt als vergrendeling te gebruiken, omdat we **tussenstanden** willen kunnen vasthouden (een dood punt vergrendelt alleen één specifieke eindstand). In plaats daarvan gebruiken we een **rem** (remkoppel `11,57 Nm`).
- Als je de slag zou verkleinen (bv. `s_open` dichter naar `s_closed`), stijgt `cond(A)` verder en zou je uiteindelijk wél richting een echt dood punt gaan.

**Hoe je het zegt:** *"Een dood punt is bruikbaar om één eindstand te vergrendelen, maar wij willen elke tussenstand kunnen kiezen en vasthouden — daarom kiezen we voor een gecontroleerde aandrijving met rem in plaats van een singulariteit als 'slot'."*

---

## 3. `condition_scurve` (bewegingstraject)

**In één zin:** het bewegingsprofiel van de schuiver — een vloeiende start/stop-curve die *automatisch extra vertraagt* in de zone waar `cond(A)` hoog is.

**Simpele uitleg:** Je moet de schuiver van `s = 1,875 m` naar `s = 0,600 m` bewegen. Hoe je dat in de tijd doet (snel/traag, met of zonder schokken) is het "traject". Een **trapeziumprofiel** (constante snelheid met blokvormige versnelling) geeft harde overgangen — schokken (jerk). Een **smooth-curve** (`smooth_4567`) lost dat op: ze start en stopt vloeiend, met snelheid én versnelling gelijk aan nul aan beide uiteinden.

`condition_scurve` bouwt daar nog een laag bovenop: ze kijkt naar `cond(A)` langs de baan en **verlaagt de snelheid extra** (tot een factor 0,25 — dus 4× trager) precies in de zone waar het mechanisme gevoelig is (de gesloten stand). Het is dus een vloeiende curve die ook nog "weet" waar ze voorzichtig moet zijn.

**Cijfers uit ons project:**
- Gebaseerd op `smooth_4567`, met `t_move = 20 s`, `t_hold = 4 s`, totaal `24 s`.
- Max snelheid `0,1965 m/s`, max versnelling `0,0511 m/s²` (binnen de gestelde limieten).
- Snelheidsfactor zakt tot **0,25** waar `cond(A)` het hoogst is (gesloten stand, `s = 1,875 m`).
- Bij sneller bewegen (`0,60×`) stijgt het piekvermogen sterk (`92,82 W` i.p.v. `57,06 W`), maar de **arbeid blijft exact `371,68 J`** — want de massa moet sowieso dezelfde hoogte tegen de zwaartekracht in.

**Hoe je het zegt:** *"We gebruiken geen gewone smooth-curve, maar een conditie-bewuste curve: ze start/stopt vloeiend zoals smooth_4567, maar vertraagt bovendien automatisch tot 4× trager in de gesloten stand, waar cond(A) het hoogst is — een uitlegbare ontwerpkeuze, geen black box."*

---

## 4. Inverse dynamica & Newton-Euler

**In één zin:** "we kennen de beweging (uit Notebook 1), en berekenen daaruit welke krachten en momenten nodig zijn" — het omgekeerde van simuleren.

**Simpele uitleg:** Normaal (voorwaartse dynamica) geef je krachten op en bereken je de beweging. Wij doen het omgekeerd: we **leggen de beweging op** (`s(t)` uit Notebook 1) en berekenen welke krachten/momenten in elk scharnier en bij de aandrijving nodig zijn om die beweging te realiseren. Dat doen we per staaf met **Newton-Euler**: voor elke stang geldt "som van krachten = massa × versnelling" en "som van momenten rond het zwaartepunt = traagheidsmoment × hoekversnelling". Met 7 bewegende stangen geeft dat een groot maar lineair stelsel (21 vergelijkingen, 21 onbekenden: alle scharnierkrachten + de aandrijfkracht).

**Cijfers uit ons project:**
- Globale dynamische controle (kracht-/momentbalans sluit) klopt tot machineprecisie (`~10⁻¹³`–`10⁻¹⁶`) — een goede consistentiecheck.
- Notebook 2 = **inertie-only** (geen zwaartekracht/wrijving) → dient om te isoleren hoe klein inertie is bij dit trage traject (`F_drive,s` piek `≈ 2,99 N`).
- Notebook 3-Overdekking = dezelfde aanpak, **plus** zwaartekracht, wrijving, trekveren en de echte massa $m_{K,eq}$.

**Hoe je het zegt:** *"We kennen de beweging al uit de kinematica; inverse dynamica gebruikt Newton-Euler per stang om terug te rekenen welke krachten daarvoor nodig zijn — vandaar 'inverse'. De globale balans sluit tot machineprecisie, dat is onze validatie."*

---

## 5. Onbalans vs. schuiverreactie

**In één zin:** "onbalans" is de **zuiver inertiële** kracht/moment die het bewegende mechanisme op het frame uitoefent — en die is bij ons klein, in tegenstelling tot de **grote maar niet-inertiële** schuiverreactie.

**Simpele uitleg:** Onbalans ontstaat omdat bewegende massa's versnellen — die versnellingskrachten moeten ergens "reageren", en dat gebeurt op het frame (in de scharnieren A en C). Dit is **letterlijk alleen het inertie-effect**: zet je de zwaartekracht en wrijving uit (zoals in Notebook 2), dan blijft alleen onbalans over.

Daarnaast is er de **schuiverreactie** $A_x$: de horizontale kracht die de schuiver op de mast/geleiding uitoefent. Die is bij ons **groot** (`~1,04 kN`), maar komt grotendeels van **zwaartekracht en geometrie** (hefboomwerking), niet van inertie. Verwar deze twee dus niet: een grote reactiekracht ≠ grote onbalans.

**Cijfers uit ons project:**
- Maximale onbalanskracht (zuiver inertieel, op het frame): `‖F_shak‖max ≈ 0,50 N` — verwaarloosbaar.
- Aandrijfkracht inertie-only (los mechanisme): piek `≈ 2,99 N`.
- Schuiverreactie $A_x$ in de overdekking: `≈ 1040,9 N` per mechanisme — **~3× groter dan de aandrijfkracht zelf** (`372,2 N` zonder trekveer).
- Waarom zo klein? Het traject is traag (`~20 s`), dus versnellingen zijn klein → inertiekrachten schalen ongeveer met `1/T²`. **10× sneller bewegen → ~100× grotere inertiekrachten** (van `~2,99 N` naar `~300 N`).

**Hoe je het zegt:** *"Onze inertiële onbalans is verwaarloosbaar omdat het traject traag is — maar dat betekent niet dat er geen grote krachten zijn: de schuiver ziet een lokale dwarsreactie van ~1 kN, veroorzaakt door zwaartekracht en hefboomwerking, niet door onbalans. Die twee zijn ontkoppelde problemen."*

---

## 6. Trekveren (energiebuffer)

**In één zin:** twee gewone lineaire veren, parallel aan de schuiver gemonteerd, die meetrekken bij het openen en zo de motor ontlasten.

**Simpele uitleg:** De overdekking moet tegen de zwaartekracht in geopend worden — dat kost de meeste motorkracht. Een trekveer is opgespannen wanneer de overdekking gesloten is, en trekt mee terwijl de schuiver opent — ze "duwt" als het ware mee in de richting van de beweging en levert een deel van de benodigde kracht. Bij het sluiten moet de veer terug opgespannen worden, maar dat is geen probleem omdat **de zwaartekracht dan zelf al meehelpt** om te sluiten — sluiten wordt dus niet de maatgevende richting.

Belangrijk: een veer is **geen gratis energie**. Ze verschuift en spreidt de belasting (lager piek bij openen, iets meer "werk" bij sluiten), maar de totale energie die het systeem nodig heeft (potentiële energie van de massa) verandert niet.

**Cijfers uit ons project:**
- 2 veren × `10 N/m` (= `0,01 N/mm`) — een **lage** stijfheid, passend bij een lange slag (`1,275 m`).
- Veerkracht: `172,74 N` (open) → `198,24 N` (gesloten) per mechanisme — dus ongeveer `86–99 N` per veer.
- Opgeslagen veerenergie: `236,50 J` per mechanisme.
- **Effect:** piekkracht bij openen daalt van `372,24 N` → `199,47 N` per mechanisme; arbeids-surplus van `240,96 J` → `105,9 J`; motorkoppel van `17,90 Nm` → `9,59 Nm`.
- **Gevolg voor motorklasse:** zonder veren zou je `750 W`-klasse nodig hebben, met veren volstaat `500 W`.

**Hoe je het zegt:** *"De trekveer is een lineaire veer die bij openen meetrekt en de motorpiek bijna halveert — van 593 W naar 219 W vereist vermogen. Bij sluiten wordt ze terug opgespannen, maar dat is niet maatgevend omdat zwaartekracht dan zelf meehelpt. Het is dus geen gratis energie, wel een slimme herverdeling van de belasting."*

---

## 7. Vliegwiel — en waarom niet

**In één zin:** een vliegwiel middelt energiepieken uit binnen een **cyclisch, snel herhalend** proces — onze overdekking is traag en niet-cyclisch, dus een vliegwiel heeft hier geen functie.

**Simpele uitleg:** Een vliegwiel slaat kinetische energie op (een ronddraaiende massa) en geeft die terug binnen dezelfde cyclus — typisch bij persen, compressoren of nokkenmechanismen waar binnen één omwenteling zware en lichte fasen elkaar snel afwisselen. Onze overdekking opent traag (~20 s), **blijft lang stil** (uren tot dagen), en sluit pas veel later. Er is dus geen "ritme" om uit te middelen — een vliegwiel zou tijdens de lange stilstand gewoon nutteloos blijven ronddraaien (en extra massa/veiligheidsrisico toevoegen).

In plaats daarvan kiezen we **trekveren**: die slaan energie op **over de hele slag** (niet per cyclus-ritme) en geven die gericht terug waar de motor het zwaarst belast wordt (bij openen).

**Cijfers uit ons project:**
- `A_max` (arbeids-surplus, "hoeveel energie zou je tijdelijk moeten bufferen") blijft een nuttig getal — `240,96 J` zonder, `105,9 J` met trekveer — maar we vertalen het naar **veerenergie**, niet naar een traagheidsmoment.
- Trager bewegen (`1,50×`) verlaagt het piekvermogen verder (tot `38,32 W`), maar de arbeid blijft `371,68 J` — bevestigt dat het geen cyclisch/vliegwiel-probleem is.

**Hoe je het zegt:** *"Een vliegwiel is logisch bij snel herhalende cycli waar je binnen één omwenteling pieken en dalen uitmiddelt. Onze overdekking beweegt traag en staat lang stil — er is niets om uit te middelen. Een trekveer past beter: ze buffert energie over de volledige slag, niet per cyclus."*

---

## 8. Equivalente massa $m_{K,eq}$

**In één zin:** in plaats van een losse 3 kg puntmassa (zoals in Notebook 2) gebruiken we voor de overdekking een **berekende** massa in punt K, die de helft van de voorbalk + helft van het doek + bevestigingsmateriaal bundelt.

**Simpele uitleg:** Punt K is het uiteinde van de buitenste stang — daar "hangt" in werkelijkheid de voorbalk (die het doek draagt) tussen de twee mechanismen. In plaats van een volledig elastisch model van voorbalk+doek te bouwen, **vereenvoudigen** we: elk mechanisme draagt de helft van de voorbalkmassa en de helft van de doekmassa, plus wat beslag (bouten, klemmen, ...), allemaal samengevoegd tot één puntmassa in K. Dat laat ons **dezelfde** Newton-Euler-aanpak van Notebook 2 hergebruiken, nu met een realistische, breedte-afhankelijke massa.

**Cijfers uit ons project:**
- Voorbalk: `200×100×5 mm` aluminium koker, `46,98 kg` totaal (over `6,0 m`).
- Doek: areal density `0,35 kg/m²` × oppervlakte `13,32 m²` = `4,663 kg`.
- $m_{K,eq}$ = ½ × `46,98` + ½ × `4,663` + `1,5 kg` beslag = **`27,32 kg`** per mechanisme.
- Totale bewegende modelmassa per mechanisme: `31,82 kg` (incl. schuiver en stangen).
- Bewust **conservatief**: een deel van het doek wordt in werkelijkheid door doekspanning/andere stangen gedragen, maar wij rekenen het volledig toe aan K — dat maakt de motor-/structuurcontrole niet te optimistisch.

**Hoe je het zegt:** *"$m_{K,eq}$ vervangt de aanname van Notebook 2 (vaste 3 kg) door een berekende massa: helft van de voorbalk plus helft van het doek plus beslag, samen 27,32 kg per mechanisme. Dat is bewust conservatief — we belasten K met meer massa dan strikt nodig."*

---

## 9. Benutting / utilisatie $U$

**In één zin:** één getal per onderdeel (voorbalk, mast, as, ...) dat zegt hoe dicht je tegen de structurele limiet zit — `U < 1` betekent OK, `U > 1` betekent het onderdeel faalt (theoretisch) op dat criterium.

**Simpele uitleg:** Je kan een balk op meerdere manieren laten "falen": te veel doorbuigen, te veel spanning (materiaal vloeit), of te veel verdraaien (torsie). In plaats van drie aparte getallen bij te houden, nemen we de **grootste** van die drie verhoudingen (werkelijke waarde / toegelaten waarde) en noemen dat de utilisatie $U$. Zo kan je in één oogopslag verschillende belastingsgevallen (eigengewicht, regen, sneeuw, wind) met elkaar vergelijken en zien welk geval **maatgevend** is (= het hoogste $U$, dat bepaalt het ontwerp).

**Cijfers uit ons project:**
- Voorbalk (`200×100×5 mm` Al): 6 belastingsgevallen doorgerekend → **wind uplift** is maatgevend met $U_{max} = 0{,}749$ (alle gevallen `< 1` ✓).
- Mast (`100×100×5 mm` staal): $U = 0{,}070$ (7%) — veel marge.
- **Conclusie:** wind, niet eigengewicht of sneeuw, bepaalt het profiel — een typisch resultaat voor een lichte, brede constructie met een groot doekoppervlak.

**Hoe je het zegt:** *"Utilisatie U is simpelweg: van alle manieren waarop dit onderdeel kan falen (doorbuigen, te veel spanning, te veel torsie), welke is het dichtst bij zijn limiet? Voor onze voorbalk is dat windopwaartse belasting met U=0,75 — ruim onder 1, dus het profiel is OK, met nog wat marge."*

---

## 10. Dimensionering van de motor

**In één zin:** de keten "kracht aan de schuiver → koppel aan de aandrijfas → vermogen → vergelijk met cataloguswaarden van motorklassen → kies de kleinste klasse die overal met marge voldoet."

**Simpele uitleg:** Dit is geen aparte berekening, maar een **vertaalslag** in stappen:
1. Notebook 3-Overdekking levert de kracht die de schuiver nodig heeft ($F_s$), inclusief een veiligheidsfactor (SF=2) → "ontwerplijnkracht".
2. Die kracht werkt via een poelie (straal `r`) op een as → wordt een **koppel** ($T = F \cdot r$, gedeeld door het rendement van de overbrenging).
3. Koppel × toerental (uit de schuiversnelheid) → **vermogen**.
4. Daarnaast: hoeveel koppel moet de **rem** kunnen leveren om de overdekking in een willekeurige tussenstand vast te houden tegen de zwaartekracht (= "houdkracht")?
5. Deze drie eisen (vermogen, koppel, remkoppel) worden vergeleken met **cataloguswaarden** van een paar motorklassen (100 W, 250 W, 500 W, 750 W). Je kiest de **kleinste klasse die op alle drie de criteria > 1 scoort** (marge).

Belangrijk: het is dus geen enkel getal, maar **drie checks tegelijk** — een motor kan voldoende vermogen hebben maar te weinig remkoppel, of omgekeerd.

**Cijfers uit ons project:**
- Ontwerplijnkracht (2 mechanismen, met trekveren, SF=2): `797,91 N`.
- Ontwerp-uitgangskoppel: `21,68 Nm`.
- Vereist remkoppel: `11,57 Nm`.
- Vereist vermogen: `219 W` (zonder veren zou dat `593 W` zijn).
- **Gekozen klasse: 500 W, 48 V BLDC + rem + 25:1 planetaire reductor.**
- Marges: **2,28×** (vermogen) / **2,31×** (koppel) / **1,73×** (rem) — alle > 1, dus de gekozen klasse voldoet met comfortabele speling.
- Poelieradius `r = 25 mm` is een compromis: kleiner → minder koppel maar hoger toerental + meer riembuiging (vermoeiing); groter → meer koppel nodig.

**Hoe je het zegt:** *"Dimensioneren is een keten van vertaalstappen: kracht → koppel via de poelie → vermogen, plus apart een remkoppel-eis voor het vasthouden van tussenstanden. Daarna toets je drie dingen — vermogen, koppel, remkoppel — tegen cataloguswaarden en kies je de kleinste klasse die op alle drie marge heeft. Bij ons is dat 500 W met marges van 1,7 tot 2,3×."*

---

## 11. Rendementsketen $\eta$

**In één zin:** vermogen gaat verloren in drie opeenvolgende stappen tussen motor en schuiver — riem, reductor, en de aandrijving zelf — en die verliezen vermenigvuldig je, je telt ze niet op.

**Simpele uitleg:** Een motor levert nooit 100% van zijn vermogen door aan de last. Onderweg (riem, tandwielen van de reductor, lagers, ...) gaat een deel verloren als wrijving/warmte. Elke stap heeft zijn eigen rendement (een getal tussen 0 en 1), en het totale rendement is het **product** van die stappen — niet de som. Dit betekent: zelfs als elke stap apart redelijk efficiënt is (90%+), kan het totaal toch behoorlijk zakken na 2-3 stappen.

**Cijfers uit ons project:**
- Riem: `0,92`
- Reductor: `0,85`
- Aandrijving (algemeen): `0,78`
- Totaal: `0,92 × 0,85 × 0,78 ≈ 0,61` — dus de motor moet ongeveer **1,6× meer vermogen** leveren dan wat er "netto" bij de schuiver aankomt.
- Dit rendement zit al verwerkt in de koppel-/vermogensberekening van punt 10 hierboven.

**Hoe je het zegt:** *"We gebruiken geen ruwe schatting van één totaal rendement, maar een keten van drie realistische verliezen — riem, reductor, aandrijving — die je vermenigvuldigt. Dat geeft ~61% totaal, dus de motor moet zo'n 1,6× meer leveren dan wat netto nodig is bij de schuiver."*

---

## 12. Gemeenschappelijke as & astorsie

**In één zin:** één motor drijft via één gedeelde as **beide** mechanismen tegelijk aan, zodat ze gegarandeerd synchroon bewegen — de prijs is dat die as torsiestijf genoeg moet zijn.

**Simpele uitleg:** De twee mechanismen moeten **exact dezelfde** beweging maken, anders trekt de voorbalk (die ze verbindt) scheef. Dat kan op twee manieren: (1) twee aparte motoren met **elektronische** synchronisatie (encoders, regelaar — als één motor net iets achterloopt, wringt het systeem), of (2) **één motor** met een **mechanische** as die naar beide kanten loopt — synchronisatie is dan "gratis" door de fysieke verbinding. Wij kiezen optie 2.

De as draagt het **totale** koppel van beide mechanismen en mag onder die belasting niet te veel **verdraaien** (torderen) — anders loopt de ene kant toch een beetje voor op de andere.

**Cijfers uit ons project:**
- Gekozen profiel: `tube_40×5` (holle stalen as, Ø40/30 mm).
- Verdraaiing: `0,549°` over `6,0 m`, tegenover een limiet van `2,0°` — ruime marge.
- Torsiespanning: `2,52 MPa` (ver onder de materiaallimiet).
- Draagt het volledige ontwerpkoppel: `21,68 Nm`.
- **Let op:** de as lost enkel de **verticale** aandrijving + synchronisatie op. De **horizontale** schuiverreactie (`1,04 kN`) blijft een **lokaal** probleem per schuiver/mast — de as helpt daar niet bij.

**Hoe je het zegt:** *"In plaats van twee motoren elektronisch te synchroniseren, gebruiken we één motor met een gemeenschappelijke as die beide mechanismen mechanisch koppelt — synchronisatie zit dan ingebouwd. De prijs is dat die as torsiestijf moet zijn: bij ons verdraait ze maar 0,55° van de toegelaten 2°."*

---

## 13. Frequentiescheiding & resonantie

**In één zin:** we vergelijken hoe "snel" de aandrijfkracht varieert (in Hz) met de eigenfrequentie van de voorbalk — als die te dicht bij elkaar liggen, kan resonantie optreden.

**Simpele uitleg:** Elke constructie (zoals onze voorbalk) heeft een **eigenfrequentie**: de frequentie waarop hij van nature zou trillen als je hem aanstoot. Als de aandrijving toevallig **op diezelfde frequentie** een herhalende kracht uitoefent, kunnen de trillingen **opbouwen** (resonantie) — zelfs een kleine kracht kan dan grote, ongewenste trillingen veroorzaken. Daarom kijk je naar het **frequentiespectrum** van de aandrijfkracht/-versnelling (welke frequenties komen erin voor, en hoe sterk) en vergelijk je de belangrijkste (dominante) frequentie met de eigenfrequentie van de constructie. Hoe groter de verhouding, hoe veiliger.

Dit is bij ons een **nacontrole**, geen ontwerpdoel — het traject is al zo traag dat resonantie sowieso onwaarschijnlijk is, maar je *toont* dat met cijfers in plaats van het aan te nemen.

**Cijfers uit ons project:**
- Dominante aandrijffrequentie: `0,0416 Hz` (= 1 cyclus per `24 s`), met boventonen rond `0,083`, `0,125`, `0,166`, `0,208 Hz`.
- Geschatte 1e buigfrequentie van de voorbalk: `15,60 Hz`.
- Frequentiescheiding: `15,60 / 0,0416 ≈ 375×` — geen resonantierisico.
- **Wat als sneller bewegen?** Aandrijffrequenties schuiven ongeveer evenredig op (`~1/T`) — bij een veel sneller traject zou de marge kleiner worden en zou een echte modale analyse (riem, doek, mast) nodig zijn.

**Hoe je het zegt:** *"We vergelijken het frequentiespectrum van de aandrijfkracht met de eigenfrequentie van de voorbalk. Bij ons trage traject (0,04 Hz) tegenover een voorbalk van 15,6 Hz is de marge 375× — resonantie is hier geen issue, maar het is een controle-instrument: bij een veel sneller ontwerp zou je dit opnieuw moeten checken."*

---

## Cheat sheet: 1-zin per begrip (laatste check vóór het examen)

| Begrip | 1-zinsdefinitie |
|---|---|
| `cond(A)` | Gevoeligheidsmaat van de kinematica — hoog = kleine fouten worden uitvergroot, niet per se een probleem. |
| Dood punt | `cond(A) → ∞`: stangen liggen in elkaars verlengde, aandrijving verliest grip — bruikbaar als vergrendeling, niet bij ons. |
| `condition_scurve` | Vloeiend traject dat extra vertraagt (tot 0,25×) waar `cond(A)` hoog is. |
| Inverse dynamica | Beweging is gekend → krachten/momenten berekenen via Newton-Euler per stang. |
| Onbalans | Zuiver inertiële kracht/moment op het frame — bij ons klein (~0,5 N), los van de grote schuiverreactie (~1 kN). |
| Trekveer | Lineaire veer die meetrekt bij openen, halveert de motorpiek — geen gratis energie, herverdeelt de belasting. |
| Vliegwiel | Middelt pieken binnen een cyclisch proces — niet zinvol bij ons trage, niet-cyclische traject. |
| $m_{K,eq}$ | Berekende puntmassa in K = ½ voorbalk + ½ doek + beslag, vervangt de vaste 3 kg uit Notebook 2. |
| Utilisatie $U$ | Grootste van (doorbuiging, spanning, torsie) t.o.v. limiet — `<1` = OK; bij ons maatgevend = wind uplift, `U=0,75`. |
| Dimensionering motor | Kracht → koppel → vermogen, plus aparte remkoppel-eis, getoetst tegen motorklassen met marge. |
| Rendementsketen $\eta$ | Verliezen in riem × reductor × aandrijving vermenigvuldigen, niet optellen — bij ons `≈0,61`. |
| Gemeenschappelijke as | Eén as koppelt beide mechanismen mechanisch synchroon — moet torsiestijf genoeg zijn. |
| Frequentiescheiding | Verhouding eigenfrequentie constructie / dominante aandrijffrequentie — hoe groter, hoe veiliger tegen resonantie. |
