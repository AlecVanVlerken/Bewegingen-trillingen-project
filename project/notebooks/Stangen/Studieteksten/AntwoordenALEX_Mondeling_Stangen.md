# Examenvoorbereiding Mondeling – Stangenmechanisme (Overdekking)

> De prof kiest 4 van 10 vragen (elk 2,5/10). Vaste bijvraag: *"Hoeveel kost de motor, en hoeveel kost het energieverbruik?"*
> Elke vraag hieronder heeft: een kort antwoord in bullets met **ingenieursinzicht** (de trade-off / waarom deze keuze), "wat als"-varianten, en een tabel met de **grafiek(en)** die je in de notebook moet openen om je antwoord te bewijzen — inclusief wat je precies moet aanwijzen.

---

## Sleutelnummers — cheat sheet

| Grootheid | Actuele waarde |
|---|---:|
| Breedte overdekking / aantal mechanismen | `6.0 m` / `2` |
| Schuivertraject bij openen | `s = 1.875 -> 0.600 m` |
| Helling open stand / uitval (diepte) | `~15.3 deg` / `2.22 m` |
| Effectieve beweegtijd | `~20.00 s` |
| Max schuiversnelheid / -versnelling | `0.1965 m/s` / `0.0511 m/s^2` |
| Max `cond(A)` | `68.36` (gesloten stand, `s=1.875 m`) |
| "Veilig bereik" voor `cond(A)` | `< 1e4` → marge `~146x` t.o.v. `68.36` |
| Voorbalk | aluminium koker `200x100x5 mm` |
| Voorbalkmassa / doekmassa | `46.98 kg` / `4.66 kg` totaal |
| Equivalente K-massa / bewegende modelmassa | `27.32 kg` / `31.82 kg` per mechanisme |
| Voorbalk benutting (maatgevend: wind uplift) | `0.75` |
| Max doorbuiging voorbalk | `~15.0 mm` |
| `F_s` zonder trekveren (open / sluit) | `372.24 N` / `309.24 N` per mechanisme |
| `F_s` met trekveren (open / sluit) | `199.47 N` / `115.16 N` per mechanisme |
| Veerkracht open / gesloten | `172.74 N` / `198.24 N` per mechanisme |
| Veerenergie open↔gesloten | `236.50 J` per mechanisme |
| Ontwerplijnkracht (zonder / met trekveren) | `1488.96 N` / `797.91 N` |
| Aanbevolen motorvermogen (zonder / met trekveren) | `593 W → 750 W`-klasse / `219 W → 500 W`-klasse |
| Ontwerp-uitgangskoppel / remkoppel | `21.68 Nm` / `11.57 Nm` |
| Marges motorklasse (vermogen / koppel / rem) | `2.28` / `2.31` / `1.73` |
| Energie per cyclus / energiekost | `476.5 J` / `~0.029 kWh/jaar = ~0.010 euro/jaar` |
| Frequentiemarge (aandrijving vs voorbalk) | `~375x` (`0.0416 Hz` vs `15.60 Hz`) |
| Aandrijfhardware / constructie / totaal kost | `1759-2699` / `5272-10239` / `6631-11938 euro` |

### Zonder vs. met trekveren

| Case | Max &#124;F_s&#124; openen | Max &#124;F_s&#124; sluiten | Ontwerplijnkracht totaal | Aanbevolen motorvermogen |
|---|---:|---:|---:|---:|
| Zonder trekveren | `372.24 N` per mechanisme | `309.24 N` per mechanisme | `1488.96 N` | `593 W`, dus `750 W` klasse |
| Met trekveren | `199.47 N` per mechanisme | `115.16 N` per mechanisme | `797.91 N` | `219 W`, dus `500 W` klasse |

### Mast- en schuivergeleiding

| Grootheid | Waarde |
|---|---:|
| Max lokale schuiverreactie `A_x` | `1.04 kN` per mechanisme |
| Ontwerpbelasting schuivergeleiding, SF `2.0` | `2.08 kN` per mechanisme |
| Ontwerpbelasting per rol/glijblok, 4 dragende punten | `0.52 kN` |
| Max indicatief mastmoment `|A_x| · s` | `625 Nm` |
| Verhouding `max |A_x| / max |F_s|` met trekveren | `5.22` |
| Mastquick-check | `100x100x5 mm` staal, benutting `0.070` |
| Indicatieve beugelkracht bij `1 m` beugelafstand | `627 N` per steunpunt |
| Gemeenschappelijke aandrijfas | `tube_40x5` staal, `0.55 deg` verdraaiing, `2.52 MPa` torsiespanning |
| Geschatte lokale pulley-lagerlast | `0.80 kN` |

---

## Vraag 1 – Industrieel gebruik & ontwerpaanpassing

### Kernantwoord

**Direct antwoord:**

Het stangenmechanisme wordt industrieel gebruikt als uitklapbare draagstructuur: één invoerbeweging van de schuiver wordt via de stangenketen omgezet in een synchrone uitklapbeweging van een doek of paneel — denk aan terrasoverdekkingen, parasols, luifels, vouwbare shelters, machinekappen en inspectieluiken. Wij zijn gestart vanuit een enkel parasolachtig mechanisme om de kinematica en dynamica op te bouwen, en hebben dat aangepast tot een `6.0 m` brede overdekking met twee identieke mechanismen en een gemeenschappelijke voorbalk tussen de K-punten. Om het ontwerp aan te passen voor een andere toepassing pas je vooral de **geometrische parameters** (staaflengtes en `s_open`/`s_closed`) aan om de vorm en grootte van de uitklapbeweging te wijzigen, en de **timing binnen de cyclus** (sneller voor een lichte machinekap, trager voor een zware overdekking om het piekvermogen te beperken). De **positie van de motor** blijft conceptueel dezelfde — hij drijft de schuiver via een riem aan — maar de vereiste kracht, koppel en vermogen schalen mee met de nieuwe massa en geometrie.

**Ter ondersteuning:**

- Trade-off 2 vs. 3 mechanismen: 2 = mechanisch overzichtelijk, minder onderdelen/synchronisatiepunten, maar voorbalk draagt meer (benutting `0.75`). 3 = stijvere ondersteuning/lagere doorbuiging, maar extra schuiver, riem en synchronisatiepunt.
- Resultaat: `200x100x5 mm` aluminium koker als voorbalk, maatgevende loadcase = wind uplift, benutting `~0.75`, doorbuiging `~15.0 mm`.
- Beperking: windvlagen, asymmetrische wind, regenwaterophoping, doekspanning, vermoeiing en de echte mast-/muurverankering zijn enkel eerste-orde meegenomen.

### Wat als...

- **Toepassing = machinekap i.p.v. overdekking?** Compactere geometrie, sneller traject, kortere slag — Notebook 1 bepaalt de baan, Notebook 3 - Overdekking de dimensionering.
- **Drie mechanismen i.p.v. twee?** Voorbalk buigt minder door (lagere benutting dan `0.75`), maar extra schuiver + riem + synchronisatiepunt.
- **Terug naar `s_open = 0.125 m` (bijna horizontaal)?** Meer uitval, maar de lokale schuiverreactie schiet naar `5.28 kN` per mechanisme — bijna 5× hoger, mast-/geleidingsdimensionering veel moeilijker te verdedigen.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---|---|
| 3D-animatie van de overdekking — *Notebook 3 - Overdekking, sectie "3D-animatie van de overdekking"* | Toon de twee mechanismen op `6.0 m`, de voorbalk tussen de K-punten en het doekvlak: dit is de finale toepassing. |
| Animatie van het vlakke stangenmechanisme — *Notebook 1, sectie "Animatie"* | Toon de basiswerking: de schuiver langs de mast stuurt alle stangen en punt K. |
| Grafiek: Massa-opbouw van de overdekking — *Notebook 3 - Overdekking* | Wijs aan hoe voorbalk + doek samen de equivalente K-last (`27.32 kg`) per mechanisme vormen. |
| Grafiek: Voorbalk-benutting per weer-belastingsgeval — *Notebook 3 - Overdekking* | Wijs aan dat `wind uplift` de maatgevende case is met benutting `0.75`, en verdedig zo het `200x100x5 mm`-profiel. |

---

## Vraag 2 – Optimalisatie: precisie / energieverbruik / krachtgeneratie

### Kernantwoord

**Direct antwoord:**

Voor **precisie** optimaliseer je niet via de encoder — die heeft al een resolutie die ruim voldoende is voor een zonwering — maar via de mechanische stijfheid van de aandrijflijn: een voorgespannen tandriem, een stijve schuivergeleiding, een torsiestijve gemeenschappelijke as en eindschakelaars/homing om scheefstand tussen de twee mechanismen te vermijden. Voor **energieverbruik** geldt dat de zwaartekrachtarbeid de dominante term is en niet via de snelheid van de beweging te verlagen is — de massa moet sowieso dezelfde hoogte overwinnen; je optimaliseert dus via minder massa, minder wrijving en trekveren als energiebuffer, wat resulteert in `476.5 J` per cyclus, ongeveer `0.010 euro/jaar`. Voor **krachtgeneratie** kies je de combinatie van poelieradius, reductieverhouding, motorvermogen en riembreedte: in onze voorkeurscase geeft een tandriem met 48V BLDC-motor, `25:1` reductor en rem een ontwerplijnkracht van `797.91 N` en een uitgangskoppel van `21.68 Nm`, passend in de `500 W`-motorklasse met marges van `2.28` (vermogen), `2.31` (koppel) en `1.73` (rem).

**Ter ondersteuning:**

- Aandrijfkeuze: tandriem i.p.v. lineaire actuator (te lang/onpraktisch voor een brede overdekking, lastig synchroon te bedienen) of ketting (lawaaieriger, vraagt smering); tandriem is stiller, properder en heeft minder speling.
- Trekveren weglaten: lijnkracht stijgt van `797.91 N` naar `1488.96 N`, motorklasse van `500 W` naar `750 W`.
- Kleinere poelie (`r < 25 mm`): lager benodigd koppel, maar hoger toerental en sterkere riembuiging — meer riemvermoeiing.

### Wat als...

- **Kleinere poelie (`r < 25 mm`)?** Lager benodigd koppel, maar hoger toerental en sterkere riembuiging — meer riemvermoeiing.
- **Hogere encoderresolutie?** Lost het precisieprobleem niet op — de dominante foutbronnen zitten mechanisch (riemrek, speling, astorsie), niet in de meting.
- **Trekveren weglaten?** Ontwerplijnkracht stijgt van `797.91 N` naar `1488.96 N`, motorklasse van `500 W` naar `750 W` — krachtgeneratie en energieverbruik stijgen, precisie blijft ongewijzigd.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---|---|
| Grafiek: Lijnkracht, aandrijfkoppel, vermogen en poelietoerental — *Notebook 4* | Verdedig de keuze `r = 25 mm`, `25:1`, `48 V`: toon dat de motor binnen toerental/koppel blijft. |
| Grafiek: Motorclass versus ontwerpvereiste — *Notebook 4* | Wijs de marges `2.28` (vermogen) / `2.31` (koppel) / `1.73` (rem) aan op de gekozen `500 W`-klasse. |
| Grafiek: Precisiebronnen — *Notebook 4* | Toon dat encoderresolutie ruim voldoende is en de echte fouten uit speling/riemrek/stijfheid komen. |

---

## Vraag 3 – Vliegwiel: trade-offs

### Kernantwoord

**Direct antwoord:**

We hebben geen vliegwiel toegepast, omdat een vliegwiel energiepieken uitmiddelt in een **cyclisch** proces met snel afwisselende zware en lichte fasen (zoals een pers of compressor) — onze overdekking opent traag, blijft lang stil en sluit pas later, dus is er geen ritme om uit te middelen. In plaats daarvan kozen we voor **trekveren** als energiebuffer, parallel aan de aandrijving op de schuiver gemonteerd. Dat verkozen we boven een gasveer, omdat een gasveer een specifieke montagegeometrie vereist en hysterese/temperatuurgevoeligheid heeft, terwijl een trekveer een eenvoudige lineaire veer is die rechtstreeks in ons model past. De trekveren leveren `172.74 N` (open) tot `198.24 N` (gesloten) per mechanisme, met een opgeslagen veerenergie van `236.50 J` per mechanisme. Daardoor daalt de piekkracht bij openen van `372.24 N` naar `199.47 N` en de motorpiek van `593 W` naar `219 W`, terwijl de motorklasse conservatief op `500 W` blijft. De trade-off is dat de veer bij sluiten opnieuw opgespannen moet worden, maar dat wordt niet maatgevend omdat de zwaartekracht de schuiver dan omlaag helpt — bewust géén volledige compensatie, zodat het systeem controleerbaar blijft en de rem in elke stand zinvol blijft.

**Ter ondersteuning:**

- Gasveer-alternatief: compacter en "professioneler", maar specifieke montagegeometrie, hysterese en temperatuurgevoeligheid maken model en afstelling lastiger.
- Volledige compensatie (`F_s ≈ 0` bij openen) zou sluiten maatgevend maken en de rem zijn rol als enige houdkracht in tussenstanden doen verliezen.
- Trager bewegen: piekvermogen daalt verder (tot `38.32 W` bij `1.50x`), maar de positieve arbeid blijft `371.68 J` — het vliegwielargument blijft irrelevant, want het is geen cyclisch proces.

### Wat als...

- **Gasveer i.p.v. trekveer?** Compacter en "professioneler", maar specifieke montagegeometrie, hysterese en temperatuurgevoeligheid maken model en afstelling lastiger.
- **Trekveer volledig laten compenseren (`F_s ≈ 0` bij openen)?** Dan wordt sluiten juist maatgevend (veer moet tegen zwaartekracht in opnieuw opgespannen worden), en verliest de rem zijn rol als enige houdkracht in tussenstanden.
- **Trager bewegen?** Piekvermogen daalt verder (tot `38.32 W` bij `1.50x`), maar de positieve arbeid blijft `371.68 J` — het vliegwielargument blijft irrelevant, want het is geen cyclisch proces.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---|---|
| Grafiek: Veerwet (trekveerkracht versus slag s) — *Notebook 3 - Overdekking* | Toon dat de trekveer een gewone lineaire veer is, geen magische energiebron. |
| Grafiek: Aandrijfkracht openen/sluiten met en zonder trekveer — *Notebook 3 - Overdekking* | Wijs aan dat de piekkracht daalt van `372.24 N` naar `199.47 N`, en dat sluiten niet maatgevend wordt. |
| Grafiek: Arbeids-surplus en veerenergie (cumulatief surplus, energievergelijking) — *Notebook 3 - Trekveren* | Verbind met de theorie van energiefluctuatie/arbeidssurplus uit de les. |

---

## Vraag 4 – Keuze van bewegings-traject: trade-offs

### Kernantwoord

**Direct antwoord:**

We hebben gekozen voor het traject `condition_scurve`: een gladde `smooth_4567`-envelope die in de gesloten stand, waar `cond(A)` oploopt tot `68.36`, automatisch extra vertraagt met een snelheidsfactor tot `0.25`. Bij het openen gaat de schuiver van `s = 1.875 m` naar `s = 0.600 m` in een effectieve beweegtijd van ongeveer `20.00 s`, met een maximale snelheid van `0.1965 m/s` en een maximale versnelling van `0.0511 m/s²`. De belangrijkste afweging is **snelheid versus rust**: een constant-snelheids- of trapeziumprofiel is eenvoudiger maar geeft hardere versnellingsovergangen en meer schok; een gewone smooth-curve houdt dan weer geen rekening met de gevoeligere gesloten stand. Uit de parameterstudie blijkt dat sneller bewegen vooral het **piekvermogen** verhoogt (`92.82 W` bij `0.60×`, `57.06 W` bij `1.00×`, `38.32 W` bij `1.50×`), terwijl de piekkracht (`~398 N`) en de positieve arbeid (`371.68 J`) nagenoeg gelijk blijven — want vermogen is kracht maal snelheid. Daarom is `~20 s` een verdedigbare keuze: rustig genoeg in de gevoelige zone, zonder overdreven traag te zijn.

**Ter ondersteuning:**

- Het traject lost de zwaartekrachtarbeid niet op — daarvoor zijn massa, wrijving en veerassistentie belangrijker; het traject vermindert vooral dynamische effecten en piekvermogen.
- Zonder condition-afhankelijke vertraging (snelheidsfactor altijd `1`) zou de beweging met onverminderde snelheid door de gevoelige zone (`cond(A) ≈ 68.36`) gaan — kleine actuatorfouten geven daar grotere hoek-/krachtfouten.

### Wat als...

- **Constante snelheid of trapeziumprofiel?** Eenvoudiger te programmeren, maar harde versnellingsovergangen op de segmentgrenzen — meer schok en hogere piekkrachten.
- **Geen condition-afhankelijke vertraging (snelheidsfactor altijd `1`)?** De beweging gaat met onverminderde snelheid door de gevoelige zone (`cond(A) ≈ 68.36`) — kleine actuatorfouten geven daar grotere hoek-/krachtfouten.
- **Tijdschaal `0.60x` of `1.50x`?** Piekvermogen verandert sterk (`92.82 W` resp. `38.32 W`), maar piekkracht en arbeid (`371.68 J`) blijven nagenoeg gelijk — `P = F·v` verklaart waarom.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---|---|
| Grafiek: Actuatortraject `s(t)`, `ds(t)`, `dds(t)` en snelheidsfactor — *Notebook 1* | Toon de gladde baan, dat snelheid/versnelling binnen `0.1965 m/s` / `0.0511 m/s^2` blijven, en hoe de snelheidsfactor naar `0.25` daalt waar `cond(A)` hoog is. |
| Grafiek: `cond(A)` als functie van de slag s — *Notebook 1* | Toon dat de gesloten stand (`s=1.875 m`) kinematisch het gevoeligst is — daar grijpt de vertraging in. |
| Grafiek: Effect van tijdschaal op piekkracht/piekvermogen/arbeid — *Notebook 4* | Toon de trade-off: piekvermogen `92.82/57.06/38.32 W`, terwijl arbeid steeds `371.68 J` blijft. |

---

## Vraag 5 – Frequentie-inhoud van het ontwerp

### Kernantwoord

**Direct antwoord:**

We gebruiken de frequentie-inhoud van de schuiverversnelling en de aandrijfkracht als objectieve **nacontrole** op het gekozen traject: we vergelijken de dominante frequenties van de aandrijfbelasting met een geschatte eerste buigfrequentie van de voorbalk van ongeveer `15.60 Hz`. De dominante aandrijffrequentie ligt rond `0.0416 Hz`, met hogere harmonischen rond `0.0832`, `0.1247`, `0.1663` en `0.2079 Hz` — een marge van ongeveer `375×` met de voorbalk. Hieruit concluderen we dat resonantie met de voorbalk bij dit trage traject geen risico vormt. Deze analyse is dus geen ontwerpdoel op zich, maar een **controle-instrument**: als het ontwerp later veel sneller zou moeten bewegen, schuiven de aandrijffrequenties omhoog (ongeveer evenredig met `1/T`), daalt de marge, en zou een volledige modale analyse van voorbalk, mast, schuivers en doek nodig worden.

**Ter ondersteuning:**

- Beperking: geen volledige modale analyse — riemtrillingen, doekflapperen, masttrilling en windgusts zijn niet (volledig) gemodelleerd.
- Een stijver/zwaarder voorbalkprofiel zou de eigenfrequentie verder boven `15.60 Hz` brengen (marge nog ruimer), maar verhoogt ook de massa.

### Wat als...

- **Sneller traject (tijdschaal `0.60x`)?** Aandrijffrequenties schuiven omhoog (~`1/T`), de marge t.o.v. `15.60 Hz` daalt — bij voldoende versnelling is een echte modale analyse nodig.
- **Stijver/zwaarder voorbalkprofiel?** Eigenfrequentie stijgt verder boven `15.60 Hz`, marge wordt nog ruimer — maar de massa stijgt ook.
- **Resonantie met riem of doek?** Niet gemodelleerd — die hebben hun eigen, veel hogere of variabele frequenties.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---|---|
| Grafiek: Frequentie-inhoud van schuiverversnelling en aandrijfkracht — *Notebook 4* | Wijs de spectra en de frequentiemarge (`~375x`) t.o.v. `15.60 Hz` aan. |
| Grafiek: Massa-opbouw / voorbalkcontrole — *Notebook 3 - Overdekking* | Toon de structuur waarvoor `15.60 Hz` is geschat. |
| Grafiek: Actuatortraject `ds(t)`/`dds(t)` — *Notebook 1* | Verklaar waarom het spectrum laagfrequent blijft: de baan is glad en traag. |

---

## Vraag 6 – Dode punten en singulariteiten

### Kernantwoord

**Direct antwoord:**

Nee, ons ontwerp doorloopt geen echte singulariteit: het conditiegetal `cond(A)` van het snelheids-/versnellingsstelsel blijft eindig over de hele slag, met een maximum van `68.36` in de gesloten stand (`s = 1.875 m`). Het "veilig bereik" is gedefinieerd als `cond(A) < 1e4`, dus onze `68.36` zit daar met een marge van ongeveer `~146×` ruim onder. Dat dit hoger is dan in het midden van de slag, is op zich niet erg, maar wijst wel op een gevoeligere zone — daarom vertraagt ons traject (`condition_scurve`) daar automatisch met een snelheidsfactor tot `0.25`, in plaats van te vertrouwen op een dood punt als vergrendeling. Een dood punt zou hoogstens nuttig zijn om één eindstand te vergrendelen (zoals bij een klem of kniehefboom), maar wij willen tussenstanden kunnen kiezen, dus kiezen we bewust voor een gecontroleerde aandrijving met rem in plaats van een singulariteit als "slot".

**Ter ondersteuning:**

- Beperking: de singulariteitscontrole is kinematisch (starre lichamen) — speling, elastische vervorming en toleranties kunnen de effectieve gevoeligheid in de praktijk vergroten.
- Bij een verkleinde slag (`s_open` dichter naar `s_closed`) zou `cond(A)` verder stijgen — bij voldoende verkleining kan dit wel een echte singulariteit (`cond(A) → ∞`) geven.

### Wat als...

- **`s_open` nog dichter naar `s_closed` (slag verkleinen)?** `cond(A)` stijgt verder richting de gesloten stand — bij voldoende verkleining kunnen links "in elkaars verlengde" komen te liggen: een echte singulariteit (`cond(A) → ∞`).
- **Minimale snelheidsfactor verlagen (`0.25 → 0.10`)?** Beweging wordt nog rustiger in de gevoelige zone, maar de effectieve beweegtijd stijgt boven de huidige `~20.00 s`.
- **Een andere geometrie waarbij `cond(A) >> 1e4`?** Dan wordt het snelheids-/versnellingsstelsel numeriek bijna singulier en onbetrouwbaar — dan zou je een andere parametrisatie (keuze van coördinaten) moeten overwegen, niet enkel vertragen.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---|---|
| Grafiek: `cond(A)` als functie van de slag s — *Notebook 1* | Hoofdfiguur: wijs de piek `68.36` bij `s = 1.875 m` aan — eindig, niet oneindig, en leg uit dat dit geen echte singulariteit is. |
| Grafiek: `cond(A)` als functie van de tijd — *Notebook 1* | Toon wanneer in de beweging de gevoelige zone optreedt. |
| Grafiek: Actuatortraject met snelheidsfactor — *Notebook 1* | Toon dat de beweging automatisch vertraagt (factor `0.25`) in de zone met hogere `cond(A)`. |
| Animatie van het vlakke mechanisme of grafiek "Numerieke sluitingsfout" — *Notebook 1* | Maak zichtbaar dat het mechanisme overal een geldige, gesloten stand vindt — geen dood punt of onmogelijke configuratie. |

---

## Vraag 7 – Onbalans-eigenschappen

### Kernantwoord

**Direct antwoord:**

De inertiële onbalans van ons ontwerp is verwaarloosbaar: voor het enkelvoudige mechanisme bedraagt de aandrijfkracht inertie-only slechts ongeveer `2.99 N`, omdat het traject zeer traag is (effectieve beweegtijd `~20 s`). Dat betekent echter niet dat er geen grote krachten zijn — er is wel een grote, niet-inertiële **lokale schuiverreactie** `A_x` van ongeveer `1.04 kN` per mechanisme, veroorzaakt door zwaartekracht en geometrie, niet door onbalans. Die kracht moet door de schuivergeleiding en de mast gedragen worden, niet door de aandrijfriem. Bij twee gesynchroniseerde mechanismen vallen globale horizontale effecten deels weg, maar elk mechanisme behoudt lokaal zijn eigen grote dwarsreactie. Onbalans zou pas een probleem worden als de beweging veel sneller zou zijn: inertiekrachten schalen ongeveer met `1/T²`, dus `10×` sneller bewegen geeft ongeveer `100×` grotere inertietermen — dan zou de huidige `2.99 N` kunnen oplopen tot in de orde van `300 N` en wél een rol spelen.

**Ter ondersteuning:**

- Lopen de twee mechanismen niet meer synchroon (faseverschil), dan vallen globale horizontale effecten niet meer (deels) weg en ziet de gemeenschappelijke as een wisselend torsiemoment.
- Trekveren toevoegen/weghalen verandert de positie-afhankelijke aandrijfkracht `F_s` (tussen `372.24 N` en `199.47 N`), maar niet de inertiële onbalans (`~2.99 N` blijft nagenoeg ongewijzigd).

### Wat als...

- **De twee mechanismen lopen niet meer synchroon (faseverschil)?** Globale horizontale effecten vallen niet meer (deels) weg, en de gemeenschappelijke as ziet een wisselend torsiemoment tussen beide zijden.
- **Traject `10x` sneller?** Inertiekrachten stijgen ongeveer `100x` (van `~2.99 N` naar `~300 N`) — onbalans wordt dan een hoofdfactor in plaats van verwaarloosbaar.
- **Trekveren toevoegen/weghalen?** Verandert de positie-afhankelijke aandrijfkracht `F_s` (tussen `372.24 N` en `199.47 N`), maar niet de massa of versnelling — de inertiële onbalans (`~2.99 N`) blijft nagenoeg ongewijzigd.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---|---|
| Grafiek: Onbalans in tijd — norm onbalanskracht en onbalansmoment rond C — *Notebook 2* | Toon de zuivere inertiële onbalans uit de lessen. |
| Grafiek: Aandrijfkracht inertie-only — *Notebook 2* | Wijs `F_s ≈ 2.99 N` aan: de inertiële basis is klein. |
| Grafiek: Onbalans versus netto frame-resultante — *Notebook 3 - Overdekking* | Toon het verschil tussen de inertiële onbalans en de totale framebelasting met gewicht/wrijving. |
| Grafiek: Mast- en schuiverbelasting — *Notebook 3 - Overdekking* | Wijs de lokale `1.04 kN`-reactie aan: groot, maar niet inertieel. |

---

## Vraag 8 – Relatieve invloed van inertie, wrijving en stijfheid

### Kernantwoord

**Direct antwoord:**

Voor onze toepassing domineren **zwaartekracht en wrijving**, terwijl **inertie** door het trage traject klein blijft en **stijfheid** apart van de bewegingsvergelijkingen gecontroleerd wordt. Voor het enkelvoudige mechanisme stijgt de maximale aandrijfkracht van `2.99 N` (inertie-only) naar `74.89 N` zodra zwaartekracht en wrijving worden meegenomen; voor de volledige overdekking is dat `372.24 N` zonder trekveren en `199.47 N` met trekveren. Wrijving verhoogt de aandrijfkracht en wisselt van teken tussen openen en sluiten, maar mag nooit gebruikt worden als vergrendeling, omdat slijtage, vuil en smering haar sterk laten variëren. Stijfheid zit niet als veer/demper in de Newton-Euler-vergelijkingen — de stangen zijn star gemodelleerd — maar wordt apart gecontroleerd en is praktisch cruciaal: de voorbalk heeft een benutting van `0.75` met een doorbuiging van ongeveer `15.0 mm`, de mast een benutting van `0.070`, en de gemeenschappelijke aandrijfas een verdraaiing van `0.55°` met een torsiespanning van `2.52 MPa`. Deze stijfheidscontroles bepalen of de beweging precies en betrouwbaar blijft, ook al staan ze los van de krachtenbalans zelf.

**Ter ondersteuning:**

- De equivalente K-massa (`27.32 kg` per mechanisme) bundelt voorbalk (`46.98 kg`), doek (`4.66 kg`) en beslag tot één massa/traagheid in punt K, zodat dezelfde Newton-Euler-aanpak hergebruikt kan worden (`31.82 kg` bewegende modelmassa per mechanisme).
- Verdubbelt de wrijvingscoëfficiënt (versleten geleiding), dan stijgt `F_s` in beide richtingen en wordt het verschil tussen `372.24 N` (open) en `309.24 N` (sluit) groter — meer asymmetrie in de motorvraag.
- Een lichter voorbalkprofiel dan `200x100x5` doet benutting en doorbuiging stijgen — bij een te licht profiel overschrijdt de benutting `1.0` en is het profiel structureel niet meer verdedigbaar.
- Beperking: nog geen volledig elastisch meerlichamenmodel — bouten, lassen, fundering, toleranties en vermoeiing moeten apart gedimensioneerd worden.

### Wat als...

- **Wrijvingscoëfficiënt verdubbelt (versleten geleiding)?** `F_s` stijgt in beide richtingen, en het verschil tussen `372.24 N` (open) en `309.24 N` (sluit) zonder trekveren wordt groter — meer asymmetrie in de motorvraag.
- **Lichter voorbalkprofiel dan `200x100x5`?** Benutting (`0.75`) en doorbuiging (`~15.0 mm`) stijgen — bij een te licht profiel overschrijdt de benutting `1.0` en is het profiel structureel niet meer verdedigbaar.
- **Volledig elastisch meerlichamenmodel i.p.v. star?** Astorsie, voorbalkdoorbuiging en riemrek zouden dan in de bewegingsvergelijkingen zelf meespelen i.p.v. apart gecontroleerd te worden — een veel zwaarder (FEM/flexibel-multibody) model.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---|---|
| Grafiek: Krachtdecompositie — benodigde schuifkracht en componenten — *Notebook 3 - Overdekking* | Toon de relatieve bijdragen van inertie, zwaartekracht en wrijving. |
| Grafiek: Schuivergeleiding en wrijvingsmoment per scharnier — *Notebook 3 - Overdekking* | Toon hoe wrijving in de analyse zit en hoe ze van teken wisselt. |
| Grafiek: Voorbalk-benutting per weer-belastingsgeval — *Notebook 3 - Overdekking* | Toon dat stijfheid/sterkte (`0.75`) apart van de Newton-Euler-krachten gecontroleerd wordt. |
| Grafiek: Mast- en schuiverbelasting — *Notebook 3 - Overdekking* | Toon dat stijfheid van mast/collar praktisch belangrijker kan zijn dan inertie. |
| Grafiek: Gemeenschappelijke aandrijfas — sterkte en torsiestijfheid — *Notebook 4* | Verdedig de stijfheid van de parallelle aandrijving (`0.55 deg`, `2.52 MPa`). |

---

## Vraag 9 – Meerdere mechanismen parallel met dezelfde motor

### Kernantwoord

**Direct antwoord:**

In onze finale overdekking worden de twee mechanismen al **parallel** aangedreven door één motor via een **gemeenschappelijke as** (`tube_40x5` staal, `0.55°` verdraaiing over `6 m`, `2.52 MPa` torsiespanning). De ontwerplijnkracht (`797.91 N`) en het ontwerpkoppel (`21.68 Nm`) zijn de **som** van beide mechanismen, en de as zorgt voor mechanische synchronisatie van het doekvlak — beide schuivers krijgen exact dezelfde beweging, belangrijk omdat de voorbalk anders scheef zou trekken. De prijs daarvoor is dat de as torsiestijf moet zijn en het volledige koppel moet dragen; het alternatief, één motor per mechanisme, zou lokaal eenvoudiger zijn maar elektronische synchronisatie vereisen, met risico op wringen bij een synchronisatiefout. Belangrijk is dat de gemeenschappelijke as en riem enkel de **verticale** aandrijfkracht dragen — de lokale **horizontale** schuiverreactie van ongeveer `1.04 kN` per mechanisme (geleiding ontworpen op `2.08 kN` met SF `2.0`) blijft een lokaal probleem per schuiver en wordt niet door de as opgelost. Bij meer parallelle kopieën (bv. drie mechanismen) zouden ontwerplijnkracht en -koppel evenredig stijgen (ongeveer `3/2×`), zou de as herdimensioneerd moeten worden, maar zou de voorbalk minder doorbuigen door de extra steun.

**Ter ondersteuning:**

| Grootheid | Waarde |
|---|---:|
| Ontwerplijnkracht totaal | `797.91 N` |
| Ontwerp-uitgangskoppel | `21.68 Nm` |
| Aanbevolen motorvermogen | `219 W` vereist, dus `500 W` klasse |
| Aanbevolen remkoppel | `11.57 Nm` |
| Verdraaiing as / torsiespanning | `0.55 deg` over `6 m` / `2.52 MPa` |
| Geschatte lokale pulley-lagerlast | `0.80 kN` |

- Drie i.p.v. twee mechanismen: ontwerplijnkracht/-koppel naar ongeveer `~1197 N` / `~32.5 Nm`.
- Een te slank gekozen as laat verdraaiing en torsiespanning stijgen — bij te grote verdraaiing lopen de twee schuivers niet meer synchroon en trekt het doekvlak scheef.

**Eén mechanisme (lokaal) vs. twee mechanismen (huidig, via gemeenschappelijke as):**

| Grootheid | Eén mechanisme (één kant) | Twee mechanismen (huidig) | Verhouding |
|---|---:|---:|---:|
| Piek &#124;F_s&#124; openen / sluiten | `199.48 N` / `115.12 N` | `398.95 N` / `230.24 N` | `2.00x` |
| Ontwerplijnkracht (SF `2.0`) | `398.95 N` | `797.91 N` | `2.00x` |
| Koppel op lokale poelie / askoppel | `10.84 Nm` | `21.68 Nm` | `2.00x` |
| Piekvermogen aan schuiver | `28.53 W` | `57.06 W` | `2.00x` |
| Vereist motorvermogen (praktisch) | `~109.7 W` | `219.46 W` | `2.00x` |
| Vereist remkoppel | `~5.78 Nm` | `11.57 Nm` | `2.00x` |
| Gekozen motorklasse | `500 W` (enige klasse met genoeg rem) | `500 W` | onveranderd |
| Marges vermogen / koppel / rem | `~4.56` / `~4.61` / `~3.46` | `2.28` / `2.31` / `1.73` | gehalveerd |
| Gemeenschappelijke as | niet nodig | `tube_40x5`, `0.55 deg` / `2.52 MPa` over `6 m` | nieuw |
| Aantal motoren | `1` | `1` | onveranderd |

Bijna alles (kracht, koppel, vermogen, remkoppel) verdubbelt **exact** (`2.00x`) bij de overgang van één naar twee mechanismen, omdat beide mechanismen identiek en gesynchroniseerd zijn en geen enkele praktische ondergrens (vloerwaarde voor lijnkracht/koppel/vermogen/rem) bindend is in beide gevallen — het is dus een zuivere verdubbeling van de fysica, geen artefact van de notebook-instellingen. Wat **niet** verandert, is de gekozen motorklasse: zelfs voor één mechanisme zou je al in de `500 W`-klasse vallen, want de `250 W`-klasse heeft geen rem en valt daarmee sowieso af. Twee mechanismen via één gemeenschappelijke as betekenen dus niet "een grotere motor nodig", maar wel dat dezelfde `500 W`-motor met ongeveer de **helft van de marge** wordt benut (`2.28`/`2.31`/`1.73` i.p.v. `~4.56`/`~4.61`/`~3.46`) — en dat er één gemeenschappelijke as bijkomt voor de synchronisatie.

### Wat als...

- **Eén motor per mechanisme i.p.v. gemeenschappelijke as?** Lokaal eenvoudiger, geen torsiestijve as nodig, maar elektronische synchronisatie vereist — bij een synchronisatiefout wringt het doek/de voorbalk.
- **Drie mechanismen i.p.v. twee?** Ontwerplijnkracht en -koppel stijgen naar ongeveer `3/2×` (`~1197 N`, `~32.5 Nm`), de as moet herdimensioneerd worden, maar de voorbalkbenutting (`0.75`) daalt door de extra steun.
- **As te slank gekozen?** Verdraaiing (`0.55 deg`) en torsiespanning (`2.52 MPa`) stijgen — bij te grote verdraaiing lopen de twee schuivers niet meer synchroon en trekt het doekvlak scheef.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---|---|
| 3D-animatie van twee mechanismen met voorbalk/doek — *Notebook 3 - Overdekking, sectie "3D-animatie van de overdekking"* | Toon de fysieke parallelle opstelling op `6 m`. |
| Grafiek: Praktische aandrijfopstelling — *Notebook 4* | Toon motor, gemeenschappelijke as, lokale riemlussen en schuivers. |
| Grafiek: Ontwerplijnkracht en ontwerpkoppel (parallel aangedreven mechanismen) — *Notebook 4* | Toon dat de motor de som van beide mechanismen ziet (`797.91 N`, `21.68 Nm`). |
| Grafiek: Gemeenschappelijke aandrijfas — sterkte en synchronisatie/torsiestijfheid — *Notebook 4* | Verdedig dat één motor met gemeenschappelijke as realistisch is (`0.55 deg`, `2.52 MPa`). |
| Grafiek: Mast- en schuiverbelasting — *Notebook 3 - Overdekking* | Toon dat horizontale reacties (`1.04 kN`) lokaal blijven en niet door de riem worden gedragen. |

---

## Vraag 10 – Parameter wijzigen, effect voorspellen en numeriek controleren

### Kernantwoord

**Direct antwoord:**

De aanpak is steeds dezelfde drie stappen: **(1)** een parameter wijzigen, **(2)** op basis van fysisch inzicht voorspellen wat er met krachten/vermogen/conditionering gebeurt, en **(3)** dat numeriek bevestigen met een specifieke grafiek. De tabel hieronder geeft vijf representatieve voorbeelden, telkens met wat **verandert**, wat **(ongeveer) constant blijft**, en een korte verklaring + verwijzing naar de notebook.

| Parameter die je wijzigt | Wat verandert | Wat blijft (ongeveer) constant | Korte uitleg |
|---|---|---|---|
| **Tijdschaal / snelheid**<br>(`0.60×` sneller / `1.50×` trager) | • Piekvermogen: `92.82 W` → `57.06 W` → `38.32 W`<br>• Beweegtijd: `11.91 s` → `19.85 s` → `29.78 s` | • Positieve arbeid: exact `371.68 J`<br>• Piekkracht: `~398 N` | `P = F·v` — kracht en arbeid hangen af van massa/hoogte/wrijving, niet van snelheid; enkel het vermogen schaalt mee. → *Notebook 4*, tijdschaal-sweep herrunnen. |
| **Alle staaflengtes ×2**<br>(systeem opschalen) | • Massa: `×8` (`~L³`)<br>• `F_s`, koppel, vermogen: `×8` à `×16` (`199.47 N → ~1600 N`, `219 W → ~3.5 kW`)<br>• Motor (`500 W`) en as (`tube_40x5`) ver onderdimensioneerd | • Vorm en piekwaarde van `cond(A)`-curve (`68.36`) — kinematica is dimensieloos | Hoeken/snelheidsverhoudingen schalen niet mee met grootte, maar massa (`~L³`) en dus krachten/koppel/vermogen wel sterk. → *Notebook 1*, alle staaflengtes ×2 → volledige keten herrunnen (NB1→NB2→NB3-Overdekking(/Trekveren)→NB4). |
| **Slag / open stand `s_open`**<br>(`0.600 m → 0.125 m`) | • Uitval neemt toe<br>• Lokale schuiverreactie `A_x`: `~1.04 kN → ~5.28 kN` (`~5×`) | • Massa's en stanglengtes | Dieper uitschuiven geeft een ongunstiger hefboom dicht bij de gesloten stand — geleiding (ontworpen op `2.08 kN`) en mast (benutting `0.070`) zijn hier niet op voorzien. → *Notebook 1*, `s_open = 0.600 → 0.125` → herrun t.e.m. NB3-Overdekking (en NB4). |
| **Trekveren weglaten** | • `F_s` open per mechanisme: `199.47 N → 372.24 N`<br>• Ontwerplijnkracht: `797.91 N → 1488.96 N` (bijna `×2`)<br>• Motorklasse: `500 W → 750 W` | • Kinematica en massa's — NB1-NB3 hoeven niet herrunnen | De veer compenseerde een deel van de zwaartekracht bij openen — zonder veer draagt de motor die last alleen. → *Notebook 4*, `load_case = "overdekking_trekveren" → "overdekking"`. |
| **Aantal mechanismen**<br>(`3` i.p.v. `2`) | • Voorbalkbenutting daalt onder `0.75`, minder doorbuiging<br>• Ontwerplijnkracht/-koppel stijgen naar `~3/2×` | • `cond(A)`-curve en kinematica per mechanisme | Extra steunpunt verlicht de voorbalk, maar de aandrijving moet de som van `3` i.p.v. `2` mechanismen leveren. → *Notebook 3 - Overdekking*, `mechanism_count_total = 2 → 3` → herrun NB3-Overdekking(/Trekveren) → NB4. |

**Detail bij parameter 1 (tijdschaal) — exacte cijfers:**

| Tijdschaal | Beweegtijd | Piek \|F_s\| totaal | Piekvermogen totaal | Positieve arbeid |
|---:|---:|---:|---:|---:|
| `0.60` sneller | `11.91 s` | `397.86 N` | `92.82 W` | `371.68 J` |
| `1.00` huidig | `19.85 s` | `398.95 N` | `57.06 W` | `371.68 J` |
| `1.50` trager | `29.78 s` | `399.32 N` | `38.32 W` | `371.68 J` |

### Wat als...

- **Twee parameters tegelijk wijzigen (bv. trekveren + `s_open`-beperking)?** Effecten zijn niet simpelweg optelbaar omdat `F_s` en de transmissieverhoudingen positie-afhankelijk zijn — de volledige keten (NB1 → NB2/NB3/Overdekking → NB4) moet opnieuw gerund worden.
- **Enkel Notebook 4 herrunnen na een geometriewijziging, zonder Notebook 1?** Dan gebruik je een verouderde kinematica/`cond(A)`-curve — inconsistent met de nieuwe geometrie.
- **Niet alle staaflengtes gelijk `×2` (bv. enkel één staaf)?** De geometrische gelijkvormigheid verdwijnt: de hoeken- en `cond(A)`-curve veranderen van vorm, en de eenvoudige schalingsregels hierboven gelden niet meer — terug naar Notebook 1.

---

## Vaste bijvraag – Hoeveel kost de motor en hoeveel kost het energieverbruik?

### Kernantwoord

**Direct antwoord:**

De aandrijfhardware — motor, reductor, rem, riem, as, poelies en lagers — kost ongeveer `1759` tot `2699 euro`. De volledige constructie, inclusief voorbalk, doek, masten, stangen, schuivers, geleidingen en trekveren, kost ongeveer `5272` tot `10239 euro`, samen dus `6631` tot `11938 euro`. Het energieverbruik van de beweging zelf is daarentegen verwaarloosbaar: `476.5 J` per cyclus komt neer op ongeveer `0.029 kWh/jaar`, oftewel ongeveer `0.010 euro/jaar` bij `0.35 euro/kWh`. De kost van dit ontwerp zit dus volledig in de hardware, montage en onderhoud — niet in het elektriciteitsverbruik.

**Ter ondersteuning:**

- Motorreferentie: 48V `500 W` BLDC-kit met rem, controller, `25:1` planetaire gearbox en encoder — basis van de aandrijfhardwarekost.

| Component | Richtprijs |
|---|---:|
| Motor + reductor + rem/drive | `1359-1699 euro` |
| Riem/as/poelies/lagers | `400-1000 euro` |
| **Totaal aandrijfhardware** | **`1759-2699 euro`** |
| Constructie buiten motor | `5272-10239 euro` |
| **Motor + constructie samen** | **`6631-11938 euro`** |

Dit is een materiaal-/componentraming, geen aannemersofferte — werkuren, keuring, detailengineering, transport en bouwkundige verankering kunnen dit nog verhogen.

**Korte verantwoording per post (orde van grootte, geen offerte):**

| Onderdeel | Richtprijs | Waarom deze waarde? |
|---|---:|---|
| Motor-kit (48V `500 W` BLDC+rem+`25:1`+encoder) | `1359-1699 euro` | Catalogusprijs (`~$1390`) omgerekend, marge tot `1699` voor invoer/btw; klasse volgt uit marges `2.28/2.31/1.73`. |
| Riem/as/poelies/lagers (aandrijflijn) | `400-1000 euro` | Generieke onderdelen voor de gemeenschappelijke as + `2` lokale riemlussen. |
| Voorbalk alu `200x100x5` | `850-1000 euro` | `6 m` profiel, gedimensioneerd op benutting `0.75` (maatgevend: wind uplift). |
| Zonweringsdoek | `533-933 euro` | `13.32 m²` overdekkingsoppervlak × `40-70 euro/m²`. |
| Masten/steunen/voetplaten | `350-650 euro` | `2` masten + verankering, vast richtbudget. |
| Stangen, scharnieren, K-beslag | `600-1200 euro` | `2` mechanismen × `300-600 euro`/mechanisme. |
| Schuiver/collar + geleiding | `600-1300 euro` | `2` mechanismen × `300-650 euro`; geleiding ontworpen op `2.08 kN` (SF `2.0`). |
| Gemeenschappelijke aandrijfas | `250-450 euro` | `tube_40x5` over `6 m` + lagerblokken/koppelingen (`0.55°`, `2.52 MPa`). |
| Riemen/poelies/lagers/spanners (constructie) | `400-900 euro` | Lokale riemlussen + spanners per mechanisme. |
| Trekveren + bevestiging | `160-400 euro` | `2×2` trekveren (`236.50 J`/mechanisme) + ankers — enkel in trekveren-case. |
| Beugels, ankers, bouten | `350-900 euro` | Bevestiging mast-gevel/fundering, vast richtbudget. |
| Corrosiebescherming + klein materiaal | `300-800 euro` | Coating/verzinking + kleinmateriaal voor buitengebruik. |
| **Subtotaal componenten** | **`4393-8533 euro`** | Som van bovenstaande. |
| **+ `20%` fabricagemarge** | **`5272-10239 euro`** | Montage-uren, verspilling, onvoorzien (`fabrication_margin_fraction`). |

Elke regel is een vast richtbudget of een eenvoudige `aantal × eenheidsprijs`-berekening — geen leveranciersofferte. Als de prof doorvraagt op één post: verwijs naar de schaal (`per mechanisme`, `per m²`, `per m as`) en naar de `20%`-marge als vangnet voor wat niet apart begroot is.

### Wat als...

- **De controller verbruikt continu `5 W` stand-by?** Dat is `8760 h × 5 W ≈ 43.8 kWh/jaar ≈ 15.3 euro/jaar` — meer dan `1000×` de bewegingsenergie (`0.010 euro/jaar`). Stand-byverbruik, niet de beweging zelf, is dan de échte energiekost.
- **Zonder trekveren (`750 W` servo)?** Aandrijfhardware schuift naar een duurdere klasse, en het energieverbruik per cyclus stijgt evenredig met de hogere `F_s` (`1488.96 N` i.p.v. `797.91 N`) — maar blijft verwaarloosbaar t.o.v. de componentkost.
- **`2` cycli per dag i.p.v. `1`?** Energiekost verdubbelt naar ongeveer `0.020 euro/jaar` — nog steeds verwaarloosbaar; component- en onderhoudskost blijven dominant.

### Grafieken om te tonen

| Grafiek | Wat aanwijzen aan de prof |
|---|---|
| Grafiek: Motorclass en kostmarge (vermogen, koppel, rem) — *Notebook 4* | Verdedig waarom de gekozen motorklasse voldoende marge heeft (`2.28`/`2.31`/`1.73`). |
| Grafiek: Praktische aandrijfopstelling + print-output kost en energieverbruik — *Notebook 4, sectie "Aandrijfschets, kost en energieverbruik"* | Toon motorconcept, energie per cyclus (`476.5 J`), jaarlijkse energiekost en constructiekost. |
| Grafiek: Statische houdkracht en benodigd remkoppel — *Notebook 4* | Verdedig dat de motor/rem ook tussenstanden veilig kan vasthouden (`11.57 Nm`). |

---

## Examensamenvatting: kritische grenzen

| Check | Grens | Resultaat |
|---|---|---|
| Conditionering `cond(A)` | "Veilig bereik" `< 1e4` | max `68.36` ✓ (marge `~146x`) |
| Voorbalk benutting (maatgevend: wind uplift) | `< 1.0` | `0.75` ✓ |
| Voorbalk doorbuiging | indicatief | `~15.0 mm` |
| Mast benutting | `< 1.0` | `0.070` ✓ |
| Aandrijfas-verdraaiing / torsiespanning | indicatief | `0.55 deg` / `2.52 MPa` ✓ |
| Motorklasse marges (vermogen / koppel / rem) | `> 1.0` | `2.28` / `2.31` / `1.73` ✓ |
| Frequentiemarge (aandrijving vs. voorbalk) | `>> 1` | `~375x` ✓ |
| Schuivergeleiding ontwerp (`A_x` × SF `2.0`) | dimensioneringsbasis | `1.04 kN → 2.08 kN` ✓ |

**Top-3 inzichten voor het mondeling:**

1. **`cond(A) = 68.36` zit met een marge van `~146x` ruim onder het "veilig bereik" (`< 1e4`)** — hoog genoeg om bewust te vertragen (`condition_scurve`, snelheidsfactor tot `0.25`), maar geen echte singulariteit. Een numeriek criterium vertaald naar een praktische, controleerbare ontwerpkeuze.
2. **Trekveren zijn geen gratis energie**: ze verlagen de motorpiek van `593 W` naar `219 W` en de ontwerplijnkracht van `1488.96 N` naar `797.91 N`, maar bij sluiten wordt de veer opnieuw opgespannen. Dat dit niet maatgevend wordt komt doordat de zwaartekracht de schuiver dan omlaag helpt — niet doordat de veer "gratis" werkt.
3. **De motor ziet niet de hele belasting**: de riem/as draagt enkel de verticale aandrijfkracht (`797.91 N` totaal), terwijl elke schuiver lokaal een horizontale reactie van `1.04 kN` (ontwerp `2.08 kN` met SF `2.0`) naar de geleiding en mast (`625 Nm` indicatief mastmoment) moet afdragen. Motordimensionering (Vraag 2/9) en constructiedimensionering (Vraag 1/8) zijn dus deels ontkoppelde problemen.

---

## Opening van het examen: eigen inzichtsvraag & trade-off

> Volgens examenervaringen begint Herman vaak met: *"Heb je zelf een inzichtsvraag of opmerking over de taak?"* en *"Wat is een interessante trade-off die je gemaakt hebt?"* — bereid dit proactief voor en breng het zelf naar voren, vóór de prof het vraagt.

### Trade-off om zelf aan te kaarten: trekveer vs. contragewicht als energiebuffer

**Onze keuze**: trekveren parallel aan de aandrijving op de schuiver, die bij openen meetrekken en bij sluiten opnieuw worden opgespannen (Vraag 3).

**Het alternatief — contragewicht**: een massa, via kabel/katrol verbonden met de schuiver of een ander punt van het mechanisme, die het zwaartekrachtmoment van de overdekking deels compenseert.

| | Trekveer (onze keuze) | Contragewicht |
|---|---|---|
| Krachtkarakter | Lineaire veerkracht, positie-afhankelijk (`172.74 N` open → `198.24 N` gesloten per mechanisme) | Constant gewicht, maar het hefboommoment is positie-afhankelijk — net als de zwaartekracht op de overdekking zelf |
| Energieopslag | `236.50 J` per mechanisme tussen open en gesloten, geeft energie terug bij openen | Potentiële energie `m·g·h`, ook terugwinbaar — maar vereist een extra bewegende massa |
| Effect op massa/inertie | Verwaarloosbaar — geen extra bewegende massa | Voegt massa toe die mee versnelt/vertraagt — extra inertieterm (al klein bij dit trage traject) |
| Effect op motorpiek | `593 W → 219 W`, motorklasse `750 W → 500 W` | Vergelijkbaar mogelijk, maar het gewicht moet over de hele slag (`s = 1.875 → 0.600 m`) een gunstig moment leveren — minder "plug-and-play" |
| Ruimtebeslag/esthetiek | Compact, binnen het mechanisme gemonteerd | Vraagt extra ruimte en een eigen kabel/katrol-traject — zichtbaar element |
| Faalmodus | Veermoeheid, corrosie, geleidelijk verlies van voorspanning | Kabel- of katrolbreuk → ongecontroleerd vallend gewicht — veiligheidsrisico |

**Kernpunt**: voor déze toepassing is de trekveer de eenvoudigere, compactere keuze — geen extra bewegende massa, geen extra ruimtebeslag, en een lineaire veerwet die rechtstreeks in het bestaande model past. Een contragewicht zou pas aantrekkelijk worden als de geometrie toelaat het hefboommoment van het gewicht goed te laten matchen met het zwaartekrachtmoment over de hele slag, en als de extra massa/inertie geen probleem vormt.

### Inzichtsvraag om zelf te stellen: is de `condition_scurve`-vertraging gerechtvaardigd, of overdreven conservatief?

- Het "veilig bereik" is `cond(A) < 1e4`. Onze actuele `cond(A)max = 68.36` zit daar met een marge van `~146x` onder.
- Toch vertraagt `condition_scurve` de beweging in de gesloten stand met een snelheidsfactor tot `0.25` (4× trager) — precies in deze zone — en draagt zo bij aan de effectieve beweegtijd van `~20.00 s`.

**Vraag aan de prof**: is deze automatische vertraging, gegeven de enorme marge (`68.36 << 1e4`), een nuttige extra veiligheidsmarge tegen modelfouten, speling en toleranties — of is ze overdreven conservatief en kost ze onnodig beweegtijd? Dit is de spanning tussen een **numeriek criterium** (hoe goed-geconditioneerd is het wiskundig model) en een **praktisch ontwerpcriterium** (hoe gevoelig is het fysieke mechanisme voor kleine actuatorfouten in deze zone) — gecorreleerd, maar niet identiek. Een goed startpunt om zelf aan te kaarten.
