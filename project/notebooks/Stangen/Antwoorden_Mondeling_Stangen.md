# Antwoorden mondeling - Stangenmechanisme

Dit is het actuele antwoorddocument voor de Stangen-analyse. De notebooks zijn de numerieke bron en bevatten de figuren, controles en animaties. Dit document vertelt het ontwerpverhaal en verbindt de resultaten aan de tien vragen uit `vragen.pdf`.

## Actuele hoofdcase

De uiteindelijke toepassing is een brede zonwerende overdekking, niet meer alleen een losse parasol. Het basismodel blijft hetzelfde vlakke stangenmechanisme uit Notebook 1, maar in `Notebook 3 - Overdekking.ipynb` worden twee identieke mechanismen gebruikt om een voorbalk en doekvlak te dragen.

| Grootheid | Actuele waarde |
|---|---:|
| Breedte overdekking | `6.0 m` |
| Aantal mechanismen | `2` |
| Uitval/diepte uit kinematica | `2.22 m` |
| Helling open stand | ongeveer `15.3 deg` naar beneden |
| Voorbalk | aluminium koker `200x100x5 mm` |
| Voorbalkmassa | `46.98 kg` totaal |
| Doekmassa | `4.66 kg` totaal |
| Equivalente K-massa | `27.32 kg` per mechanisme |
| Bewegende modelmassa | `31.82 kg` per mechanisme |
| Schuivertraject bij openen | `s = 1.875 -> 0.600 m` |
| Effectieve beweegtijd | ongeveer `20.00 s` |
| Max schuiversnelheid | `0.1965 m/s` |
| Max schuiverversnelling | `0.0511 m/s^2` |
| Max `cond(A)` | `68.36` bij de gesloten stand |

De voorkeursvariant is de overdekking met trekveren. De veer verandert de baan niet, maar verlaagt de motorlast voor hetzelfde traject:

| Case | Max `|F_s|` openen | Max `|F_s|` sluiten | Ontwerplijnkracht totaal | Aanbevolen motorvermogen |
|---|---:|---:|---:|---:|
| Zonder trekveren | `372.24 N` per mechanisme | `309.24 N` per mechanisme | `1488.96 N` | `593 W`, dus `750 W` klasse |
| Met trekveren | `199.47 N` per mechanisme | `115.16 N` per mechanisme | `797.91 N` | `219 W`, dus `500 W` klasse |

De trekveer is dus geen gratis energiebron. Bij openen geeft ze energie terug. Bij sluiten wordt ze opnieuw opgespannen, maar de zwaartekracht helpt dan mee doordat de constructie terug naar beneden beweegt. De motor/rem blijft nodig om de beweging te controleren en tussenstanden vast te houden.

Belangrijk praktisch punt: de riem draagt alleen de verticale aandrijfkracht. De lokale horizontale schuiverreactie is veel groter:

| Grootheid | Waarde |
|---|---:|
| Max lokale schuiverreactie `A_x` | `1.04 kN` per mechanisme |
| Ontwerpbelasting schuivergeleiding, SF `2.0` | `2.08 kN` per mechanisme |
| Ontwerpbelasting per rol/glijblok, 4 dragende punten | `0.52 kN` |
| Max indicatief mastmoment `|A_x| s` | `625 Nm` |
| Verhouding `max |A_x| / max |F_s|` met trekveren | `5.22` |
| Mastquick-check | `100x100x5 mm` staal, benutting `0.070` |
| Indicatieve beugelkracht bij `1 m` beugelafstand | `627 N` per steunpunt |

Dit is een van de belangrijkste beperkingen van het ontwerp. De motor kan de verticale aandrijfkracht leveren, maar de schuivergeleiding, mast en muur-/framebevestiging moeten de dwarsreacties dragen.

Om te vermijden dat het geheel als een losse paal omvalt, wordt de mast niet als vrijstaande slanke staaf beschouwd. De praktische oplossing is een momentvaste bevestiging: minstens twee stevige muur- of framebeugels met verticale afstand, of een gelijkwaardig stijf mastframe aan de basis. Met de finale hellende open stand is het indicatieve mastmoment ongeveer `625 Nm`; bij `1 m` beugelafstand komt dat overeen met ongeveer `627 N` per steunpunt in de eerste-orde check. Dat is veel beter verdedigbaar dan de vroegere bijna horizontale variant.

## Welke notebookfiguren gebruik je? Uitgebreide gids per vraag

Gebruik de notebooks niet als diareeks waarin je alles toont. Toon per vraag alleen de figuren die het antwoord echt bewijzen. De andere figuren zijn reserve voor doorvragen. De onderstaande selectie is daarom opgesplitst in **essentieel** en **optioneel**.

### Vraag 1 - Industrie/toepassing en ontwerpaanpassing

**Essentieel tonen:**

| Notebook | Figuur/animatie | Waarom tonen |
|---|---|---|
| Notebook 1 | Animatie van het vlakke stangenmechanisme | Toont de basiswerking: een schuiver langs de mast stuurt alle stangen en punt K. Dit is de kern van de industriele toepassing. |
| Notebook 3 - Overdekking | 3D-animatie van de overdekking | Toont de finale toepassing: twee identieke mechanismen op 6 m breedte, voorbalk tussen K-punten en doekvlak. Hiermee wordt duidelijk dat we van parasol naar overdekking zijn gegaan. |
| Notebook 3 - Overdekking | Massa-opbouw | Verdedigt waarom de overdekking zwaarder is dan het basismodel: voorbalk, doek en beslag komen als equivalente K-last in het model. |
| Notebook 3 - Overdekking | Voorbalk: benutting per weer-loadcase | Laat zien dat het gekozen `200x100x5` profiel niet willekeurig is, maar volgt uit structurele controle. |

**Optioneel bij doorvragen:**

- Notebook 3 - Overdekking: effect van breedte en profielvergelijking. Gebruik dit als men vraagt waarom geen smaller/breder ontwerp, lichter profiel of drie mechanismen.
- Notebook 1: controle van de positie-oplossing. Gebruik dit als men vraagt of de kinematische baan numeriek betrouwbaar is.

**Niet als eerste tonen:** alle individuele hoekplots van alle links. Die zijn correct, maar minder sterk om de toepassing te verdedigen.

### Vraag 2 - Optimaliseren voor precisie, energieverbruik en krachtgeneratie

**Essentieel tonen:**

| Notebook | Figuur/plot | Waarom tonen |
|---|---|---|
| Notebook 4 | Lijnkracht in de aandrijving | Toont de totale kracht die de motor/riem moet leveren voor de gekozen loadcase. |
| Notebook 4 | Aandrijfkoppel inclusief verliezen | Verdedigt de keuze van poelie, reductie en motorkoppel. |
| Notebook 4 | Statische houdkracht en benodigd remkoppel | Toont dat tussenstanden niet op wrijving of motorstroom steunen, maar op een gedimensioneerde rem. |
| Notebook 4 | Poelieradius en motorselectie: poelietoerental, koppel, reductie en motorsnelheid | Verdedigt de keuze `r = 25 mm`, `25:1`, `48 V`, en dat de motor binnen toerental/koppel blijft. |
| Notebook 4 | Precisie van de aandrijving | Laat zien dat encoderresolutie fijn genoeg is en dat echte fouten vooral uit speling, riemrek en stijfheid komen. |

**Optioneel bij doorvragen:**

- Notebook 4: vermogen/koppel/rem-motorclass. Gebruik dit als men vraagt waarom `500 W` en niet kleiner.
- Notebook 4: praktische aandrijfopstelling. Gebruik dit om fysisch uit te leggen waar motor, as, riem en schuiver zitten.
- Notebook 3 - Overdekking: mast- en schuiverbelasting. Gebruik dit om te benadrukken dat optimalisatie niet alleen motor is, maar ook geleiding en mast.

**Belangrijk bij uitleg:** zeg expliciet dat precisie niet alleen encoder is. De mechanische stijfheid en de rem zijn belangrijker dan micrometerresolutie.

### Vraag 3 - Vliegwiel en energieopslag

**Essentieel tonen:**

| Notebook | Figuur/plot | Waarom tonen |
|---|---|---|
| Notebook 3 - Overdekking | Trekveerfiguur: veerwet | Toont dat de trekveer een gewone lineaire veer is, geen magische energiebron. |
| Notebook 3 - Overdekking | Trekveerfiguur: aandrijfkracht openen/sluiten | Bewijst dat de veer de openingslast verlaagt en dat sluiten niet maatgevend wordt. |
| Notebook 3 - Overdekking | Trekveerfiguur: actuatorvermogen | Laat zien dat trekveren piekvermogen verlagen, dus nuttiger zijn dan een vliegwiel voor deze toepassing. |
| Notebook 3 - Overdekking | Arbeids-surplus en motordimensionering | Verbindt met de theorie van energiefluctuatie/arbeidssurplus. |

**Optioneel bij doorvragen:**

- Notebook 3 - Trekveren: cumulatief surplus en energievergelijking. Gebruik dit als men dieper vraagt naar energieopslag in de veer.
- Notebook 4: arbeid en arbeids-surplus in de tijdschaalanalyse. Gebruik dit om te tonen dat trager bewegen vooral piekvermogen verlaagt.

**Niet overclaimen:** een trekveer vervangt geen rem. Ze helpt de motor bij openen, maar kan het systeem niet veilig in elke stand vasthouden.

### Vraag 4 - Keuze van bewegings-traject

**Essentieel tonen:**

| Notebook | Figuur/plot | Waarom tonen |
|---|---|---|
| Notebook 1 | Controle van het actuatortraject: `s(t)`, `ds(t)`, `dds(t)` | Toont de gekozen baan, rustige start/stop en beperkte snelheid/versnelling. |
| Notebook 1 | Controle van het actuatortraject: conditionering en snelheidsfactor | Verdedigt waarom `condition_scurve` vertraagt bij de gevoelige gesloten stand. |
| Notebook 1 | `cond(A)` als functie van `s` | Laat zien dat de gesloten stand kinematisch gevoeliger is. |
| Notebook 4 | Tijdschaalanalyse: piek kracht, piek vermogen, arbeid en surplus | Toont de trade-off: sneller bewegen verhoogt vooral piekvermogen, niet de zwaartekrachtarbeid. |

**Optioneel bij doorvragen:**

- Notebook 1: snelheid van punt K in tijd en tegenover `s`. Gebruik dit om te tonen dat ook het uiteinde rustig beweegt.
- Notebook 1: versnelling van punt K. Gebruik dit als men vraagt waarom inertie klein blijft.
- Notebook 1: animatie. Gebruik dit om de hellende open stand intuitief te tonen.

**Niet nodig als hoofdfiguur:** alle hoeksnelheden/hoekversnellingen van alle links. Die zijn meer detailbewijs dan hoofdargument.

### Vraag 5 - Frequentie-inhoud

**Essentieel tonen:**

| Notebook | Figuur/plot | Waarom tonen |
|---|---|---|
| Notebook 4 | Spectrum van schuiverversnelling | Laat zien welke frequenties het opgelegde traject introduceert. |
| Notebook 4 | Spectrum van aandrijfkracht | Toont de frequentie-inhoud van de belasting die de aandrijving en structuur ziet. |
| Notebook 4 | Frequentiemarge | Vergelijkt dominante aandrijffrequenties met de geschatte voorbalkfrequentie. |

**Optioneel bij doorvragen:**

- Notebook 1: `ds(t)` en `dds(t)`. Gebruik dit om te verklaren waarom het spectrum laagfrequent blijft.
- Notebook 3 - Overdekking: voorbalkcontrole. Gebruik dit als men vraagt welke structuur in de frequentiecheck representatief is.

**Beperking die je erbij zegt:** dit is geen volledige modale analyse van doek, mast, riem en verbindingen. Het is een eerste controle dat de gekozen trage beweging niet dicht bij de eenvoudige voorbalkfrequentie zit.

### Vraag 6 - Dode punten en singulariteiten

**Essentieel tonen:**

| Notebook | Figuur/plot | Waarom tonen |
|---|---|---|
| Notebook 1 | `cond(A)` als functie van `s` | Belangrijkste figuur: toont dat `cond(A)` stijgt bij gesloten stand, maar niet naar oneindig gaat. |
| Notebook 1 | `cond(A)` als functie van tijd | Toont wanneer in de beweging de gevoelige zone optreedt. |
| Notebook 1 | Actuatortraject met snelheidsfactor | Toont dat de beweging automatisch vertraagt in de zone met hogere `cond(A)`. |
| Notebook 1 | Animatie of positie-oplossing | Maakt zichtbaar dat we niet door een dood punt of onmogelijke stand gaan. |

**Optioneel bij doorvragen:**

- Notebook 1: positievalidatie/sluitingsfout. Gebruik dit als men vraagt of de hoge conditionering numerieke fouten veroorzaakt.
- Notebook 1: controle van punt K-baan. Gebruik dit om te tonen dat de baan continu blijft.

**Belangrijk onderscheid:** `cond(A) = 68.36` is ongunstiger dan het midden van de slag, maar geen echte singulariteit. Daarom vertragen we, maar vergrendelen we niet via een dood punt.

### Vraag 7 - Onbalans-eigenschappen

**Essentieel tonen:**

| Notebook | Figuur/plot | Waarom tonen |
|---|---|---|
| Notebook 2 | Onbalans in tijd: norm onbalanskracht en onbalansmoment rond C | Toont de zuivere inertiele onbalans uit de lessen. |
| Notebook 2 | Aandrijfkracht inertie-only | Laat zien dat inertie bij het trage traject klein is. |
| Notebook 3 - Overdekking | Onbalans versus netto frame-resultante | Toont het verschil tussen inertiele onbalans en totale framebelasting met gewicht/wrijving. |
| Notebook 3 - Overdekking | Afzonderlijke steunreacties | Laat zien dat lokale reacties groot kunnen zijn, ook als globale onbalans klein is. |

**Optioneel bij doorvragen:**

- Notebook 3: framebelasting en onbalans voor het enkelvoudige mechanisme. Gebruik dit om de overgang van basismodel naar overdekking te tonen.
- Notebook 1: versnellingen van punt K. Gebruik dit om te verklaren waarom onbalans klein blijft.

**Belangrijk bij uitleg:** onbalans is niet hetzelfde als steunreactie. Onbalans is inertieel; mast- en schuiverbelasting worden vooral door gewicht, geometrie en wrijving bepaald.

### Vraag 8 - Relatieve invloed van inertie, wrijving en stijfheid

**Essentieel tonen:**

| Notebook | Figuur/plot | Waarom tonen |
|---|---|---|
| Notebook 3 - Overdekking | Krachtdecompositie: benodigde schuifkracht en componenten | Toont relatieve bijdragen van inertie, zwaartekracht en wrijving. |
| Notebook 3 - Overdekking | Schuivergeleiding en wrijvingsmoment per scharnier | Toont hoe wrijving in de analyse zit. |
| Notebook 3 - Overdekking | Voorbalk: benutting per weer-loadcase | Toont dat stijfheid/sterkte buiten de Newton-Euler-krachten apart gecontroleerd wordt. |
| Notebook 3 - Overdekking | Mast- en schuiverbelasting | Toont dat stijfheid van mast/collar praktisch belangrijker kan zijn dan inertie. |
| Notebook 4 | Gemeenschappelijke aandrijfas: sterkte, torsiestijfheid en massa | Verdedigt de stijfheid van de parallelle aandrijving. |

**Optioneel bij doorvragen:**

- Notebook 2: inertie-only krachten. Gebruik dit om te tonen dat de traagheidsbasis klein is.
- Notebook 3: energiebalans-validatie. Gebruik dit als men vraagt of krachtcomponenten consistent zijn met vermogen/energie.

**Belangrijk bij uitleg:** de stangen worden star gemodelleerd in Newton-Euler, maar structurele stijfheid wordt apart gecontroleerd via voorbalk, mast, geleiding, riem/as en frequentie.

### Vraag 9 - Meerdere mechanismen parallel met dezelfde motor

**Essentieel tonen:**

| Notebook | Figuur/plot | Waarom tonen |
|---|---|---|
| Notebook 3 - Overdekking | 3D-animatie van twee mechanismen met voorbalk/doek | Toont de fysieke parallelle opstelling. |
| Notebook 4 | Praktische aandrijfopstelling | Toont een motor, gemeenschappelijke as, lokale riemlussen en schuivers. |
| Notebook 4 | Ontwerplijnkracht en ontwerpkoppel | Toont dat de motor de som van beide mechanismen ziet. |
| Notebook 4 | Gemeenschappelijke aandrijfas: sterkte en synchronisatie/torsiestijfheid | Verdedigt dat een motor met as realistisch is. |
| Notebook 3 - Overdekking | Mast- en schuiverbelasting | Toont dat horizontale reacties lokaal blijven en niet door de riem worden gedragen. |

**Optioneel bij doorvragen:**

- Notebook 3 - Overdekking: effect van breedte/profielvergelijking. Gebruik dit als men vraagt waarom twee mechanismen en geen drie.
- Notebook 4: poelieradius en motorselectie. Gebruik dit als men vraagt of een motor te zwaar wordt.

**Belangrijk bij uitleg:** de voorbalk synchroniseert niet. Synchronisatie moet via de gemeenschappelijke as, gelijke poelies en gelijke riemspanning komen.

### Vraag 10 - Parameter wijzigen, effect voorspellen en numeriek controleren

**Essentieel tonen:**

| Parameterwijziging | Notebookfiguren | Waarom tonen |
|---|---|---|
| Tijdschaal sneller/trager | Notebook 4: piek kracht, piek vermogen, arbeid en surplus | Duidelijkste voorbeeld: piekvermogen verandert sterk, arbeid bijna niet. |
| Trekveren aan/uit | Notebook 3 - Overdekking: aandrijfkracht openen/sluiten, actuatorvermogen, houdkracht | Toont een ontwerpwijziging die motorlast verlaagt. |
| Open stand beperken | Notebook 1: animatie/positie-oplossing en `cond(A)`; Notebook 3 - Overdekking: mastbelasting | Toont waarom `s_open = 0.600 m` beter is dan de oude bijna horizontale stand. |
| Voorbalkprofiel wijzigen | Notebook 3 - Overdekking: profielvergelijking en weerloadcase-benutting | Toont massa/stijfheid-trade-off. |
| Aantal mechanismen wijzigen | Notebook 3 - Overdekking: effect van breedte; Notebook 4: parallelmechanismen | Toont hoe motorbelasting, balkdoorbuiging en synchronisatie veranderen. |

**Optioneel bij doorvragen:**

- Notebook 1: punt K-snelheid/versnelling. Gebruik dit als men een geometrische parameter of slaglengte wil bespreken.
- Notebook 2: onbalans. Gebruik dit als men vraagt hoe snellere beweging inertiele effecten zou vergroten.

**Belangrijk bij uitleg:** bij elke parameterwijziging eerst voorspellen, dan Notebook 1 opnieuw runnen als de kinematica verandert, daarna Notebook 2/3/Overdekking/4 opnieuw runnen voor krachten en motor.

### Vaste bijvraag - Motorkost en energieverbruik

**Essentieel tonen:**

| Notebook | Figuur/plot | Waarom tonen |
|---|---|---|
| Notebook 4 | Motorclass en kostmarge: vermogen, koppel en rem | Verdedigt waarom de gekozen motorclass voldoende marge heeft. |
| Notebook 4 | Aandrijfschets, kost en energieverbruik | Toont motorconcept, energie per cyclus, jaarlijkse energiekost en constructiekost. |
| Notebook 4 | Statische houdkracht en benodigd remkoppel | Verdedigt dat de motor/rem ook tussenstanden veilig kan vasthouden. |

**Optioneel bij doorvragen:**

- Notebook 4: poelieradius en motorselectie. Gebruik dit om te tonen dat de concrete motor niet alleen op vermogen, maar ook op toerental en koppel is gekozen.
- Notebook 4: praktische aandrijfopstelling. Gebruik dit om de kostposten fysisch te koppelen aan motor, as, riem, poelies, lagers en rem.

**Belangrijk bij uitleg:** bewegingsenergie is bijna gratis; aankoop, montage, stand-byverbruik, onderhoud en bouwkundige verankering zijn veel belangrijker.

## Analysehistoriek en notebookketen

De analyse is bewust stapsgewijs opgebouwd. De latere notebooks vervangen de
vroegere niet volledig; ze gebruiken dezelfde basis en voegen telkens een laag
realisme toe.

| Stap | Rol | Directe input | Waarom nodig |
|---|---|---|---|
| `Notebook 1.ipynb` | Kinematica | parameters bovenaan | Bepaalt de baan, hoeken, snelheden, versnellingen en `cond(A)` |
| `Notebook 2.ipynb` | Inertie-only inverse dynamica | `notebook1_kinematica_results.npz` | Bouwt de Newton-Euler-aanpak op en toont onbalans/inertie zonder zwaartekracht |
| `Notebook 3.ipynb` | Baseline met zwaartekracht en wrijving | Notebook 1 + Notebook 2 | Voegt de echte statische lasten, wrijving, houdkracht en framebelasting toe voor het enkelvoudige mechanisme |
| `Notebook 3 - Trekveren.ipynb` | Enkel mechanisme met trekveren | Notebook 1 + Notebook 2 + Notebook 3 baseline | Herhaalt dezelfde dynamica, maar voegt een trekveerpakket toe en vergelijkt met baseline |
| `Notebook 3 - Overdekking.ipynb` | Finale brede overdekking | Notebook 1 | Gebruikt dezelfde Newton-Euler-logica als Notebook 3 en dezelfde veerlogica als de trekveernotebook, maar met eigen overdekkingsmassa's, voorbalk, doek, weercontrole en meerdere mechanismen |
| `Notebook 4.ipynb` | Aandrijving/motor/rem/riem/as | gekozen Notebook-3-loadcase | Leest eender welke compatibele loadcase: `baseline`, `trekveren`, `overdekking`, `overdekking_trekveren` of `custom` |

Belangrijk: `Notebook 3 - Overdekking` bouwt inhoudelijk voort op Notebook 2,
Notebook 3 en `Notebook 3 - Trekveren`, maar leest niet letterlijk alle vorige
`.npz`-bestanden in. Dat is bewust: de overdekking heeft een ander massamodel
dan het enkelvoudige mechanisme. De kinematica komt wel altijd uit Notebook 1,
zodat trajectwijzigingen automatisch doorwerken wanneer de keten opnieuw wordt
gerund.

## Vraag 1 - Waarvoor gebruikt de industrie dit mechanisme, en hoe pas je het aan voor een andere toepassing?

**Eindkeuze.**  
Het mechanisme wordt gebruikt als uitklapbare draagstructuur voor een zonwerende overdekking. Industrieel past dit type mechanisme bij systemen waar een doek, paneel, kap of arm synchroon uitklapt met een enkele invoer: terrasoverdekkingen, parasols, luifels, vouwbare shelters, machinekappen en inspectieluiken.

**Hoe we daar geraakt zijn.**  
We zijn gestart vanuit een enkel parasolachtig stangenmechanisme. Dat was nuttig om de kinematica, snelheden, versnellingen en inverse dynamica op te bouwen. Daarna bleek dat een losse parasol minder interessant was als eindtoepassing voor een cafegevel van 5 tot 8 m. Daarom is het ontwerp uitgebreid naar twee identieke mechanismen op 6 m breedte, verbonden door een voorbalk aan de K-punten.

**Alternatieven.**  
Een enkele centrale parasol is eenvoudiger, maar dekt minder goed een lange gevel. Drie mechanismen zouden de voorbalk stijver ondersteunen en de doorbuiging verlagen, maar vragen meer stangen, meer schuivers, meer lokale riemen en meer synchronisatie. Twee mechanismen is daarom de gekozen balans: mechanisch nog overzichtelijk, maar breed genoeg voor een realistische terrasoverdekking.

**Aanpassingen voor een andere toepassing.**  
Voor een machinekap zou ik de geometrie compacter maken, sneller bewegen en een kortere slag kiezen. Voor een grote luifel of overdekking kies ik juist een rustig traject, een stijvere voorbalk, een duidelijke rem en een aandrijving boven/achter de constructie. De geometrische parameters in Notebook 1 bepalen de baan; de massa's en belastingen in Notebook 3 Overdekking bepalen de dimensionering.

**Numerieke onderbouwing.**  
De huidige 6 m-overdekking gebruikt twee mechanismen en een `200x100x5 mm` voorbalk. De structurele weercontrole geeft als maatgevende case `wind uplift`, met maximale benutting ongeveer `0.75`. De maximale doorbuiging in de weerloadcases is ongeveer `15.0 mm`. Dat is acceptabel binnen de aannames, maar geen gecertificeerde bouwkundige berekening.

**Open stand met helling.**  
In een eerdere versie ging het mechanisme bijna horizontaal open tot `s_open = 0.125 m`. Dat gaf veel uitval, maar ook een lokale horizontale schuiverreactie van ongeveer `5.28 kN` per mechanisme. Voor de finale hoofdcase kiezen we daarom bewust `s_open = 0.600 m`. De kap blijft dan ongeveer `15 deg` afhellen, wat realistischer is voor een overdekking en de lokale schuiverreactie verlaagt naar ongeveer `1.04 kN` per mechanisme. De trade-off is minder uitval, maar de constructie wordt veel beter verdedigbaar.

**Beperkingen.**  
Windvlagen, asymmetrische wind, regenwaterophoping, doekspanning, vermoeiing en de echte mast-/muurverankering zijn alleen eerste-orde meegenomen. Voor uitvoering moet de constructie apart structureel gecontroleerd worden.

## Vraag 2 - Hoe optimaliseer je voor precisie, energieverbruik en krachtgeneratie?

**Eindkeuze.**  
De aandrijving is een 48 V BLDC/servo-reductiemotor met encoder en rem, die een gemeenschappelijke as aandrijft. Per schuiver loopt een lokale tandriem/kabel langs de mast. De riem levert alleen de verticale kracht; de schuivergeleiding draagt de dwarskracht.

**Proces.**  
Eerst leek een lange lineaire actuator of schroefspindel logisch, omdat die direct de schuiverpositie oplegt. Voor een brede overdekking is dat minder praktisch: lange slag, zichtbare actuator, moeilijke montage en minder geschikt om twee mechanismen synchroon te bedienen. Daarna zijn ketting en tandriem vergeleken. De tandriem is gekozen omdat die stiller is, minder onderhoud vraagt, properder is en minder speling heeft. Een ketting is robuust, maar lawaaieriger, zwaarder en vraagt smering.

**Precisie.**  
De encoderresolutie is niet de beperkende factor. Notebook 4 geeft een theoretische resolutie die veel fijner is dan nodig voor een zonwering. De echte foutbronnen zijn riemrek, speling in de reductor, schuivergeleiding, torsie van de gemeenschappelijke as en scheefstand tussen de twee mechanismen. Daarom zijn een voorgespannen riem, stijve schuiver/collar, eindschakelaars, homing en een rem belangrijker dan alleen een fijnere encoder.

**Energieverbruik.**  
Trager bewegen verlaagt piekvermogen en schokken, maar niet de zwaartekrachtarbeid. De massa moet dezelfde hoogte overwinnen. Energie optimaliseren doe je vooral via lagere wrijving, minder massa, goede lagers en eventueel trekveren. Voor de voorkeurscase met trekveren rekent Notebook 4 ongeveer `476.5 J` elektrisch-equivalent per open+sluitcyclus en ongeveer `0.010 euro/jaar` bewegingsenergie bij de ingestelde gebruiksparameters.

**Krachtgeneratie.**  
Voor meer kracht kan je een kleinere poelie, grotere reductie, sterkere motor of bredere riem kiezen. Een kleinere poelie verlaagt het nodige koppel, maar vraagt meer toerental en buigt de riem sterker. In de huidige voorkeurscase wordt gekozen voor een ontwerplijnkracht van `797.91 N` totaal en een ontwerp-uitgangskoppel van `21.68 Nm`.

**Concrete motorclass.**  
Notebook 4 vertaalt dit nu naar een motorcategorie. Voor de voorkeurscase met trekveren volstaat de klasse `500 W 48V BLDC + rem + gearbox` in de quick-check, met marges ongeveer `2.28` op vermogen, `2.31` op uitgangskoppel en `1.73` op remkoppel. Zonder trekveren schuift de analyse naar een `750 W servo + rem` klasse. Dit blijft een datasheetklasse, geen definitieve productselectie.

**Beperkingen.**  
Notebook 4 dimensioneert de aandrijving op orde van grootte. Een echte componentkeuze vraagt cataloguscontrole: riemtandbelasting, poeliediameter, lagerlasten, remtype, IP-bescherming, corrosie en controllerkeuze.

## Vraag 3 - Vliegwiel: welke trade-offs heb je gemaakt, en waarom?

**Eindkeuze.**  
Een vliegwiel is niet de juiste hoofdoplossing. De betere energiebuffer is een beperkte trekveerassistentie, eventueel gecombineerd met elektrische buffering in de motorsturing.

**Proces.**  
De vraag naar een vliegwiel komt uit cyclische machines: persen, nokkenmechanismen of compressoren. Daar wisselen zware en lichte fasen snel af en kan een vliegwiel energie tijdelijk opslaan. Onze overdekking opent traag, blijft lang stilstaan en sluit pas later. Er is dus geen continu proces waarin een vliegwiel nuttig blijft uitmiddelen.

**Alternatieven.**  
Een gasveer is compact en professioneel, maar vraagt een specifieke montagegeometrie, heeft hysterese en is temperatuurgevoelig. Een trekveer is eenvoudiger te modelleren: ze werkt parallel aan de aandrijving op de schuiver en trekt omhoog. Daarom is de trekveer als eerste versie gekozen.

**Numerieke onderbouwing.**  
De trekveren leveren in de huidige case ongeveer `172.74 N` omhoog in open stand en `198.24 N` omhoog in gesloten stand per mechanisme. De opgeslagen veerenergie tussen open en gesloten is ongeveer `236.50 J` per mechanisme. Daarmee daalt de berekende aanbevolen motorpiek van ongeveer `593 W` naar `219 W`; de gekozen motorclass blijft conservatief `500 W`.

**Trade-off.**  
De trekveer verlaagt de openingslast en remvereiste, maar voegt onderdelen, montagepunten en afstelling toe. Ze maakt sluiten niet gratis: de veer wordt tijdens sluiten opnieuw opgespannen. In de finale open/sluit-analyse is sluiten echter niet maatgevend: de zwaartekracht helpt de schuiver omlaag en levert de energie om de veer opnieuw op te spannen, terwijl motor en rem vooral de snelheid en tussenstanden controleren. We kiezen geen volledige compensatie, zodat het systeem controleerbaar blijft en de rem in alle standen zinvol blijft.

## Vraag 4 - Keuze van bewegings-traject: welke trade-offs heb je gemaakt, en waarom?

**Eindkeuze.**  
Het gekozen traject is `condition_scurve`: een gladde `smooth_4567`-envelope met extra vertraging waar de kinematische conditionering slechter is.

**Proces.**  
Een constante snelheid of trapeziumprofiel is eenvoudig, maar geeft hardere overgangen in versnelling. Een gewone smooth-curve is beter, maar houdt geen rekening met het feit dat het mechanisme in de gesloten stand gevoeliger is. Uit Notebook 1 blijkt dat `cond(A)` daar maximaal ongeveer `68.36` wordt. Daarom is gekozen voor een condition-afhankelijk profiel.

**Numerieke onderbouwing.**  
Bij openen gaat de schuiver van `s = 1.875 m` naar `s = 0.600 m`. De effectieve beweegtijd is ongeveer `20.00 s`, met `max |ds| = 0.1965 m/s` en `max |dds| = 0.0511 m/s^2`. De minimale snelheidsfactor is `0.25`, waardoor de beweging rustiger wordt in de gevoeligste zone.

**Trade-off.**  
Het nadeel is dat openen langer duurt dan een agressief profiel. Het voordeel is minder schok, lager piekvermogen, lagere inertiekrachten en betere controle bij de gesloten stand. Notebook 4 toont dat sneller bewegen vooral het piekvermogen verhoogt. In de tijdschaalstudie vraagt de snelle `0.60`-case ongeveer `92.82 W`, terwijl de huidige case ongeveer `57.06 W` vraagt en de tragere `1.50`-case ongeveer `38.32 W`. De piekkracht verandert daarbij nauwelijks en de positieve arbeid blijft ongeveer gelijk. Daarom is `20 s` een verdedigbare eindkeuze: rustiger, maar niet overdreven traag.

**Beperking.**  
Het traject lost de zwaartekrachtarbeid niet op. Daarvoor zijn massa, wrijving en veerassistentie belangrijker. Het traject vermindert vooral dynamische effecten en piekvermogen.

## Vraag 5 - Hoe gebruik je de analyse van de frequentie-inhoud?

**Eindkeuze.**  
De frequentieanalyse wordt gebruikt als controle dat de gekozen beweging geen hoge-frequente excitatie veroorzaakt en ruim wegblijft van de geschatte structurele frequenties.

**Proces.**  
Eerst was de beweging vooral gekozen op basis van snelheid, versnelling en conditionering. Daarna is in Notebook 4 het spectrum van schuiverversnelling en aandrijfkracht toegevoegd. Dat geeft een objectieve controle of het traject past bij een rustige positioneerbeweging.

**Numerieke onderbouwing.**  
Voor de overdekking met trekveren ligt de dominante frequentie van de aandrijfkracht rond `0.0416 Hz`. De volgende componenten liggen rond `0.0832 Hz`, `0.1247 Hz`, `0.1663 Hz` en `0.2079 Hz`. De eenvoudige eerste buigfrequentie-inschatting van de voorbalk is ongeveer `15.60 Hz`. De marge tussen dominante aandrijffrequentie en voorbalkreferentie is dus ongeveer `375x`.

**Interpretatie.**  
Voor de huidige trage beweging is resonantie met de voorbalk geen belangrijk risico. Als het ontwerp later in enkele seconden moet openen of sluiten, schuiven de frequenties omhoog en wordt dit veel belangrijker. Dan is een echte modale analyse van voorbalk, mast, schuivers en doek nodig.

**Beperking.**  
De huidige frequentiecheck is geen volledige modale analyse. Ze vergelijkt de aandrijfexcitaties met een eenvoudige balkfrequentie. Riemtrillingen, doekflapperen, masttrilling en windgusts zijn niet volledig gemodelleerd.

## Vraag 6 - Heeft het ontwerp dode punten of singulariteiten? Is dat erg?

**Eindkeuze.**  
Het huidige traject gaat niet door een echte singulariteit. Er is wel een minder gunstige zone bij de gesloten stand.

**Proces.**  
In Notebook 1 wordt de conditionering van het snelheidsstelsel gevolgd. Dat is belangrijk omdat een mechanisme dicht bij een singulariteit zeer gevoelig wordt: kleine actuatorbewegingen kunnen grote hoekveranderingen of grote krachten veroorzaken. De gesloten stand blijft `s_closed = 1.875 m`; de open stand is bewust beperkt tot `s_open = 0.600 m` zodat de overdekking niet bijna horizontaal eindigt.

**Numerieke onderbouwing.**  
De maximale `cond(A)` is ongeveer `68.36`. Dat is hoog genoeg om voorzichtig te zijn, maar niet oneindig. Daarom vertraagt het traject daar met de condition-afhankelijke snelheidsfactor.

**Waarom geen dood punt als vergrendeling?**  
Een dood punt kan nuttig zijn voor een klem of kniehefboom, maar hier willen we tussenstanden kunnen kiezen. Een dood punt vergrendelt hoogstens een eindstand en maakt terug vertrekken moeilijk. Daarom vertrouwen we niet op een singulariteit, maar op een gecontroleerde aandrijving met rem.

**Beperking.**  
De singulariteitscontrole is kinematisch. In werkelijkheid kunnen speling, elastische vervorming en toleranties de effectieve gevoeligheid vergroten.

## Vraag 7 - Wat zijn de onbalans-eigenschappen? Vormen die een probleem?

**Eindkeuze.**  
Inertiele onbalans is voor de huidige trage beweging geen hoofdprobleem. De echte praktische belasting zit in gewicht, wrijving, steunreacties en mast-/schuiverkrachten.

**Proces.**  
Notebook 2 berekent eerst inertie-only. Dat was nuttig om te zien of het mechanisme dynamisch zwaar belast wordt door versnellingen. Daarna tonen Notebook 3 en Notebook 3 Overdekking dat zwaartekracht en lokale reacties veel belangrijker zijn.

**Numerieke onderbouwing.**  
Voor het enkelvoudige basismodel is de inertiele aandrijfkracht ongeveer `2.99 N` en blijft de inertiele onbalans klein. Voor de brede overdekking is de lokale schuiverreactie daarentegen ongeveer `1.04 kN` per mechanisme. Dat is geen onbalans in de zin van massa maal versnelling, maar wel een echte constructieve belasting.

**Interpretatie.**  
Bij twee symmetrische mechanismen kunnen globale horizontale effecten deels wegvallen, maar lokaal blijft elk mechanisme zijn eigen grote dwarsreactie hebben. De riem ziet die kracht niet; de schuivergeleiding en mast wel.

**Wanneer wordt onbalans wel kritisch?**  
Als de beweging veel sneller wordt. Inertiekrachten schalen ruwweg met `1/T^2`. Een beweging die tien keer sneller is, geeft ongeveer honderd keer grotere inertietermen. Dan kan onbalans wel dominant worden.

## Vraag 8 - Wat zijn de relatieve invloeden van inertie, wrijving en stijfheid?

**Eindkeuze.**  
Voor deze toepassing domineren zwaartekracht, wrijving en constructiestijfheid. Inertie is door het trage traject klein.

**Proces.**  
We hebben de analyse in lagen opgebouwd. Notebook 2 toont inertie-only. Notebook 3 voegt zwaartekracht en wrijving toe. Notebook 3 Overdekking voegt de brede voorbalk, doeklast, weerbelasting, mastbelasting en trekveren toe.

**Numerieke onderbouwing.**  
Voor het enkelvoudige mechanisme is `max |F_s|` inertie-only ongeveer `2.99 N`, terwijl de totale kracht met zwaartekracht en wrijving ongeveer `74.89 N` wordt. Voor de overdekking zonder trekveren stijgt de piek naar `372.24 N` per mechanisme. Met trekveren daalt die naar `199.47 N`.

**Wrijving.**  
Wrijving verhoogt de aandrijfkracht en dissipatie, en ze wisselt van teken tussen openen en sluiten. Daarom is de open/sluit-analyse belangrijk. Wrijving mag niet gebruikt worden als betrouwbare vergrendeling, want slijtage, vuil en smering veranderen haar sterk.

**Stijfheid.**  
Stijfheid zit niet als elastisch element in de Newton-Euler-stelsels; de stangen worden daar star gemodelleerd. Toch is stijfheid praktisch cruciaal: voorbalkdoorbuiging, mastmoment, schuiverkanteling, riemrek en astorsie bepalen of de beweging precies en betrouwbaar blijft. De voorbalkcontrole geeft `wind uplift` als maatgevend met benutting `0.75`. De mastquick-check kiest `100x100x5 mm` staal met benutting `0.070` op het indicatieve krachtkoppel. De gemeenschappelijke ascontrole kiest in Notebook 4 een holle stalen as `tube_40x5`, met ongeveer `0.55 deg` verdraaiing en `2.52 MPa` torsiespanning in de trekveercase.

**Beperking.**  
Er is nog geen volledig elastisch meerlichamenmodel. De mast, geleiderollen, lagerkrachten, riemrek en bevestigingen zijn nu als eerste orde gecontroleerd, maar voor uitvoering moeten bouten, lassen, fundering, toleranties en vermoeiing nog apart gedimensioneerd worden.

## Vraag 9 - Wat verandert als verschillende kopies parallel door dezelfde motor worden aangedreven?

**Eindkeuze.**  
Het gekozen parallelontwerp gebruikt twee identieke mechanismen op 6 m breedte, synchroon aangedreven door een gemeenschappelijke as boven/achter de constructie. Vanaf die as loopt per mechanisme een lokale riem/kabel naar de schuiver.

**Proces.**  
We wilden liefst twee mechanismen: links en rechts van de overdekking. Dat is constructief eenvoudig en laat een vrije doorgang zonder extra middenmast. Het nadeel is dat de voorbalk 6 m overspant en dus stijf genoeg moet zijn. Lichtere balkprofielen waren onvoldoende; daarom is `200x100x5 mm` gekozen. Drie mechanismen zouden de balkbelasting verminderen, maar maken aandrijving, montage en synchronisatie complexer.

**Een motor of meerdere motoren?**  
Een motor met gemeenschappelijke as is mechanisch synchroon: beide schuivers krijgen dezelfde beweging. Dat is goed voor een doekvlak, want de voorbalk mag niet scheef trekken. Het nadeel is dat de as torsiestijf moet zijn en het totale koppel moet dragen. Twee motoren zijn lokaal eenvoudiger, maar vragen elektronische synchronisatie; bij foutieve synchronisatie kan het doek of de voorbalk wringen.

**Numerieke onderbouwing.**  
In de voorkeurscase met trekveren rekent Notebook 4:

| Grootheid | Waarde |
|---|---:|
| Ontwerplijnkracht totaal | `797.91 N` |
| Ontwerp-uitgangskoppel | `21.68 Nm` |
| Aanbevolen motorvermogen | `219 W` vereist, dus `500 W` klasse |
| Aanbevolen remkoppel | `11.57 Nm` |
| Gekozen asoptie in controle | `tube_40x5` staal |
| Verdraaiing as | `0.55 deg` over 6 m |
| Torsiespanning as | `2.52 MPa` |
| Geschatte lokale pulley-lagerlast | `0.80 kN` |

**Belangrijk onderscheid.**  
De gemeenschappelijke as en riem leveren de verticale aandrijfkracht. Ze lossen de lokale horizontale schuiverreactie niet op. Elke schuiver/collar moet nog altijd ongeveer `1.04 kN` dwarsreactie kunnen opnemen. Met veiligheidsfactor `2.0` ontwerpen we de geleiding op ongeveer `2.08 kN` per mechanisme.

**Beperking.**  
De ascontrole is eerste orde. Er is nog geen detailontwerp van lagersteunen, koppelingen, riemschijven, toleranties, montage, corrosie of noodbediening.

## Vraag 10 - Verander een parameter, voorspel het effect, en bereken de numerieke analyse

**Parameterstudie 1: tijdschaal van het traject.**  
Voorspelling: als de geometrie en massa gelijk blijven, verandert de zwaartekrachtarbeid bijna niet. Piekvermogen stijgt wel als de beweging sneller wordt. Inertiekrachten stijgen ongeveer met `1/T^2`, maar blijven bij onze trage beweging beperkt.

Notebook 4 bevestigt dit voor de overdekking met trekveren:

| Tijdschaal | Actieve beweegtijd | Piek `|F_s|` totaal | Piekvermogen totaal | Positieve arbeid |
|---:|---:|---:|---:|---:|
| `0.60` sneller | `11.91 s` | `397.86 N` | `92.82 W` | `371.68 J` |
| `1.00` huidig | `19.85 s` | `398.95 N` | `57.06 W` | `371.68 J` |
| `1.50` trager | `29.78 s` | `399.32 N` | `38.32 W` | `371.68 J` |

De kracht verandert nauwelijks omdat zwaartekracht domineert. Het vermogen verandert sterk omdat `P = F v`.

**Parameterstudie 2: trekveren toevoegen.**  
Voorspelling: trekveren verlagen de openingskracht en remvereiste, maar de sluitbeweging moet de veer opnieuw opspannen. De open/sluit-analyse is dus nodig om te controleren of sluiten niet maatgevend wordt.

Resultaat: zonder trekveren is de ontwerplijnkracht `1488.96 N`; met trekveren daalt die naar `797.91 N`. De berekende aanbevolen motorpiek daalt van `593 W` naar `219 W`; de gekozen motorclass wordt `500 W 48V BLDC + rem + gearbox`. Sluiten wordt niet maatgevend. Dat maakt trekveren voor deze brede overdekking verdedigbaar.

De open/sluit-energie ondersteunt die keuze: met trekveren is de positieve motorarbeid bij openen ongeveer `185.84 J` per mechanisme, terwijl de sluitrichting in de huidige simulatie geen positieve motorarbeid vraagt. De aandrijving blijft wel nodig om gecontroleerd te remmen en de positie vast te houden.

**Parameterstudie 3: voorbalkprofiel.**  
Voorspelling: een zwaarder en hoger profiel verhoogt massa, maar verlaagt doorbuiging en spanning sterk door het grotere traagheidsmoment. De huidige keuze `200x100x5` is gekozen omdat lichtere profielen structureel minder verdedigbaar waren voor 6 m met twee mechanismen.

**Parameterstudie 4: slag beperken.**  
Dit is nu doorgevoerd in de finale hoofdcase. De oude bijna horizontale open stand `s_open = 0.125 m` gaf veel uitval, maar maakte de schuiver-/mastbelasting moeilijker verdedigbaar. Door `s_open = 0.600 m` te kiezen blijft de overdekking ongeveer `15 deg` hellend en daalt de lokale schuiverreactie van ongeveer `5.28 kN` naar `1.04 kN` per mechanisme. Het nadeel is dat de uitval daalt naar ongeveer `2.22 m`; het voordeel is een veel realistischer constructieve belasting.

**Beperking.**  
Deze parameterstudies zijn nuttig voor ontwerpkeuzes, maar geen vervanging voor een volledige productdimensionering.

## Vaste bijvraag - Hoeveel kost de motor en hoeveel kost het energieverbruik?

**Motor en aandrijving.**  
Voor de voorkeurscase is de orde van grootte:

| Component | Richtprijs |
|---|---:|
| Motor + reductor + rem/drive | `1359-1699 euro` |
| Riem/as/poelies/lagers | `250-700 euro` |
| Totaal aandrijfhardware | `1759-2699 euro` |
| Constructie buiten motor | `5272-10239 euro` |
| Motor + constructie samen | `6631-11938 euro` |

De gekozen klasse in Notebook 4 is `500 W 48V BLDC + rem + gearbox` voor de trekveercase. Zonder trekveren wordt eerder `750 W servo + rem` maatgevend. De concrete referentiekeuze is een 48 V 500 W BLDC-kit met rem, controller, 25:1 planetaire gearbox en externe encoder: ongeveer `1359 euro` omgerekend, of `1699 euro` met marge voor BTW/import/variatie. Met riem, as, poelies, lagers en montage komt de totale aandrijfhardware op `1759-2699 euro`.

De constructie buiten de motor bevat de voorbalk, doek, masten/steunen, stangen, schuivers, geleidingen, aandrijfas, riemen/poelies/lagers, trekveren, beugels, ankers en corrosiebescherming. Dat is een materiaal- en componentraming, geen aannemersofferte: werkuren, keuring, detailengineering, transport en bouwkundige verankering kunnen dit nog duidelijk verhogen.

**Energieverbruik.**  
Notebook 4 geeft voor de voorkeurscase ongeveer `476.5 J` elektrisch-equivalent per open+sluitcyclus. Bij `1` cyclus per dag en `220` dagen per jaar is dat ongeveer `0.029 kWh/jaar`. Met `0.35 euro/kWh` is de bewegingsenergie ongeveer `0.010 euro/jaar`.

De energiekost van de beweging is dus verwaarloosbaar tegenover componentkost, montage, onderhoud en eventuele stand-by-elektronica. Als een controller continu stand-by verbruikt, kan dat veel groter zijn dan de bewegingsenergie zelf.

## Belangrijkste beperkingen om eerlijk te vermelden

1. De stangen worden star gemodelleerd. Elastische vervorming van stangen, schuiver, mast en riem zit niet volledig in het dynamisch model.
2. De voorbalkcontrole is eerste orde en Eurocode-geinspireerd, maar geen gecertificeerde bouwkundige berekening.
3. Windvlagen, asymmetrische wind, regenwaterophoping, doekspanning en doekflapperen zijn beperkt gemodelleerd.
4. De schuivergeleiding en mast zijn nu eerste-orde gecontroleerd, maar nog niet als volledig product met bouten, lassen, rollen, toleranties en vermoeiing uitgewerkt.
5. De gemeenschappelijke ascontrole bevat nu holle/massieve asopties en lagerlastorde, maar detailontwerp van lagers, koppelingen en riemschijven blijft nodig.
6. Kosten zijn richtwaarden. Voor een definitieve motorselectie moeten actuele catalogusprijzen en datasheets gecontroleerd worden.

## Korte eindconclusie

De analyse is verdedigbaar als eerste ontwerpstudie. De notebooks tonen de kinematica, inverse dynamica, zwaartekracht/wrijving, trekveren, brede overdekking, structurele voorbalkcontrole, motor-/riemdimensionering, frequentie-inhoud en parallelle aandrijving. De belangrijkste technische boodschap is dat de motoropstelling realistisch is voor de verticale aandrijfkracht, maar dat de echte constructieve uitdaging in de schuivergeleiding, mastbevestiging, voorbalkstijfheid en synchronisatie zit.
