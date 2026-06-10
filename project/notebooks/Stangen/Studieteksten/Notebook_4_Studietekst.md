# Notebook 4 - Studietekst

Deze tekst hoort bij `Notebook 4.ipynb`. De notebook vertaalt de mechanische
belasting uit een Notebook-3-loadcase naar een praktisch aandrijfconcept:
motor, reductie, poelie, riem/kabel, rem, encoder, gemeenschappelijke as,
energieverbruik en kostorde.

De notebook berekent dus niet opnieuw de kinematica of de Newton-Euler
dynamica. Die komen uit de vorige notebooks. Notebook 4 gebruikt de resultaten
als invoer en beantwoordt de vraag:

> Welke aandrijving moet deze beweging fysisch kunnen leveren en vasthouden?

## 0. Actuele hoofdcase

De huidige voorkeurscase is:

```python
load_case = "overdekking_trekveren"
```

Die loadcase komt uit `Notebook 3 - Overdekking.ipynb` en gebruikt de brede
overdekking met twee identieke mechanismen, gemeenschappelijke aandrijving en
directe trekveren aan de schuivers.

| Grootheid | Actuele waarde |
|---|---:|
| Bronbestand | `notebook3_overdekking_trekveren_results.npz` |
| Aantal aangedreven mechanismen | `2` |
| Breedte overdekking | `6.00 m` |
| Uitval/diepte | `2.22 m` |
| Equivalente K-massa | `27.32 kg` per mechanisme |
| Max aandrijfkracht openen | `199.48 N` per mechanisme |
| Max aandrijfkracht sluiten | `115.12 N` per mechanisme |
| Totale piekbewegingskracht | `398.95 N` voor twee mechanismen |
| Totale piekhoudkracht | `231.30 N` voor twee mechanismen |
| Ontwerplijnkracht met veiligheidsfactor | `797.91 N` |
| Gekozen poelieradius | `25 mm` |
| Gekozen reductie | `25:1` |
| Pieksnelheid uitgang | `75.06 rpm` |
| Gemiddelde uitgangssnelheid | `24.53 rpm` |
| Ontwerpkoppel aan uitgang/poelie | `21.68 Nm` |
| Vereist remkoppel aan uitgang | `11.57 Nm` |
| Aanbevolen motorvermogen | `219 W` minimum, gekozen klasse `500 W` |
| Geselecteerde motorklasse | `500 W 48V BLDC + rem + gearbox` |
| Motormarge vermogen/koppel/rem | `2.28 / 2.31 / 1.73` |
| Geselecteerde aandrijfas | stalen buis `40x5 mm` |
| Torsiehoek as over 6 m | `0.55 deg` |
| Schuifspanning as | `2.52 MPa` |
| Energie per open+sluitcyclus | `476.5 J` |
| Jaarlijkse bewegingsenergie | `0.029 kWh/jaar` bij 1 cyclus/dag, 220 dagen |
| Jaarlijkse energiekost | ongeveer `0.01 euro/jaar` |
| Richtprijs aandrijfhardware | `1759-2699 euro` |
| Constructie buiten motor | `5272-10239 euro` |
| Motor + constructie samen | `6631-11938 euro` |

Belangrijke interpretatie:

- Notebook 4 bevat nu een concrete referentiekeuze voor motor/reductor/controller.
- Dat blijft een budgettaire productklasse, geen definitieve aankoopofferte.
- De motor moet niet alleen bewegen, maar ook tussenstanden gecontroleerd
  kunnen vasthouden.
- De riem/kabel levert de verticale aandrijfkracht.
- De schuivergeleiding en mast dragen de horizontale reacties en momenten.

## 1. Plaats in de notebookketen

De logica van de volledige analyse is:

```text
Notebook 1
  -> kinematica: s, ds, dds, hoeken, snelheden, versnellingen

Notebook 2
  -> inertie-only dynamica en onbalans

Notebook 3
  -> zwaartekracht, wrijving, reacties en echte aandrijfkracht

Notebook 3 - Trekveren
  -> zelfde dynamica, maar met directe trekveren op de schuiver

Notebook 3 - Overdekking
  -> finale brede overdekking, meerdere mechanismen, voorbalk, weerbelasting,
     mastbelasting en optionele trekveren

Notebook 4
  -> motor, poelie, riem/kabel, rem, encoder, as, synchronisatie, kost en energie
```

Notebook 4 is bewust losgekoppeld van de dynamische afleiding. Daardoor kan
dezelfde aandrijfanalyse worden gebruikt voor verschillende loadcases:

| `load_case` | Betekenis |
|---|---|
| `"baseline"` | enkel mechanisme uit Notebook 3 |
| `"trekveren"` | enkel mechanisme met trekveren |
| `"overdekking"` | brede overdekking zonder trekveren |
| `"overdekking_trekveren"` | brede overdekking met trekveren |
| `"custom"` | zelf gekozen `.npz`-bestand |

Dat is belangrijk voor parameterstudies. Als de geometrie, massa's, trajecttijd
of breedte veranderen, moeten Notebook 1 en de juiste Notebook-3-loadcase
opnieuw gerund worden. Notebook 4 leest daarna automatisch de nieuwe krachten.

## 2. Theorie: van schuiverkracht naar motorkoppel

De output van Notebook 3 is de aandrijfkracht langs de schuivercoordinate `s`.
Bij de praktische aandrijving wordt die kracht geleverd door een riem of kabel
die rond een poelie loopt.

De hoofdrelaties zijn:

```text
T = F r / eta
omega = v / r
P = F v
```

met:

- `F`: verticale lijnkracht in de riem/kabel;
- `r`: poelie- of trommelradius;
- `eta`: rendement van riem, poelie, lagers en overbrenging;
- `T`: koppel op de poelie-/reductoruitgang;
- `v = |ds|`: schuiversnelheid;
- `P`: mechanisch vermogen aan de schuiver.

Een grotere poelie geeft:

- lagere rotatiesnelheid;
- meer verplaatsing per omwenteling;
- maar hoger koppel voor dezelfde lijnkracht.

Een kleinere poelie geeft:

- lager koppel;
- hogere rotatiesnelheid;
- betere resolutie per motorhoek;
- maar mogelijk meer slijtage en kleinere buigradius voor riem/kabel.

Daarom selecteert Notebook 4 de poelieradius niet alleen op vermogen, maar ook
op praktische grenzen voor koppel, rem en minimumdiameter.

## 3. Ontwerpkracht en veiligheidsfactor

De notebook gebruikt niet gewoon de piekkracht uit Notebook 3 als ontwerpwaarde.
Hij neemt:

```text
line_force_design =
    max(
        drive_safety_factor * hoogste bewegings- of houdkracht,
        praktische minimumontwerpkracht
    )
```

Voor de huidige overdekking met trekveren:

- piek bewegingskracht totaal: `398.95 N`;
- piek houdkracht totaal: `231.30 N`;
- veiligheidsfactor: `2.0`;
- ontwerpwaarde: `797.91 N`.

Die veiligheidsfactor is nodig omdat het model geen volledige productiedetails
bevat:

- extra wrijving door montagefouten;
- vuil, veroudering en vocht;
- riemspanning;
- kleine asymmetrie tussen mechanismen;
- variatie in doekmassa;
- windinvloed tijdens bewegen;
- onzekerheid in rendement.

De ontwerpwaarde is dus bewust conservatiever dan de nominale berekende kracht.

## 4. Houdkracht en rem

Een belangrijke ontwerpvraag is of het mechanisme in elke tussenstand kan
blijven staan. Notebook 4 berekent daarom naast de bewegingskracht ook de
statische houdkracht uit de Notebook-3-loadcase.

Voor een poelie geldt:

```text
T_hold = |F_hold| r
T_brake_design = brake_safety_factor T_hold
```

Voor de huidige loadcase is het vereiste remkoppel aan de uitgang ongeveer:

```text
11.57 Nm
```

Daarom is de gekozen aandrijving geen gewone vrije DC-motor. De verdedigbare
keuze is:

- reductiemotor of servo/BLDC-motor;
- encoder voor positie;
- mechanische rem of zelfremmende overbrenging;
- eindschakelaars of homing voor referentieposities.

Alleen rekenen op motorstroom of schuiverwrijving is geen goede oplossing.
Motorstroom verbruikt energie en is niet veilig bij spanningsuitval.
Schuiverwrijving is onzeker en verandert door vuil, slijtage en smering.

## 5. Motorkeuze

De notebook vergelijkt eenvoudige motorklassen. De huidige uitkomst is:

```text
500 W 48V BLDC + rem + gearbox
```

Dat betekent niet dat exact die motor verplicht is. Het betekent:

- de motor moet ongeveer deze vermogensklasse halen;
- de uitgang moet minstens het vereiste koppel kunnen leveren;
- de rem moet het statische koppel kunnen vasthouden;
- de motor moet een encoder of vergelijkbare terugkoppeling hebben.

Voor de huidige case:

- aanbevolen piekvermogen: `219 W`;
- aanbevolen uitgangskoppel: `21.68 Nm`;
- vereist remkoppel: `11.57 Nm`;
- gekozen klasse: `500 W`, `50 Nm`, `20 Nm rem`.

Die keuze heeft duidelijke marge. Dat is nodig omdat de overdekking buiten
staat en omdat de analyse niet alle praktijkverschijnselen volledig bevat.

## 6. Precisie

Voor precisie kijkt Notebook 4 naar drie bronnen:

1. encoderresolutie;
2. speling/backlash;
3. elastische vervorming van riem, aandrijving en constructie.

De encoderresolutie wordt omgerekend naar lineaire schuiververplaatsing:

```text
resolutie = 2 pi r / (reductie * encoder_counts)
```

Met een encoder en reductie is de theoretische resolutie veel kleiner dan wat
voor een terrasoverdekking nodig is. De echte beperking zit dus niet in de
encoder, maar in:

- speling in reductiekast en koppelingen;
- riemrek;
- torsie van de gemeenschappelijke as;
- flexibiliteit van schuiver, mast en voorbalk;
- asymmetrische belasting door wind of doek.

Daarom is de praktische conclusie:

- tandriem is beter dan ketting voor precisie en geluid;
- riem voorspannen;
- stijve schuivergeleiding gebruiken;
- encoder en eindschakelaars gebruiken;
- rem gebruiken voor stilstand;
- voor twee mechanismen synchroniseren via de gemeenschappelijke as, niet via
  het doek of de voorbalk.

## 7. Energieverbruik en trajecttijd

Notebook 4 maakt ook een tijdschaalanalyse. Daarin wordt gekeken wat er gebeurt
als hetzelfde traject sneller of trager wordt uitgevoerd.

Belangrijk onderscheid:

- zwaartekrachtarbeid hangt vooral af van begin- en eindpositie;
- wrijvingsarbeid hangt vooral af van de afgelegde weg;
- inertiekracht hangt af van versnelling en schaalt ongeveer met `1/tijd^2`;
- vermogen hangt af van kracht maal snelheid.

Trager bewegen verlaagt dus vooral:

- piekvermogen;
- inertiekrachten;
- schokken;
- gevoeligheid voor singulariteiten.

Maar trager bewegen maakt de zwaartekrachtarbeid niet gratis kleiner. De massa
moet nog altijd omhoog gebracht worden. Daarom is een rustige beweging goed
voor comfort en motorvermogen, maar geen oplossing voor alle belastingen.

Voor deze toepassing is traag bewegen verdedigbaar omdat de overdekking typisch
een paar keer per dag beweegt, niet continu.

## 8. Krachtgeneratie

Voor meer kracht zijn de belangrijkste ontwerpknoppen:

- hogere reductieverhouding;
- sterkere motor;
- kleinere poelie;
- bredere/sterkere riem;
- lagere wrijving in schuiver en scharnieren;
- trekveren of gasveren als zwaartekrachtcompensatie;
- minder zware voorbalk of doek;
- extra mechanismen om lokale constructieve lasten te verdelen.

Elke keuze heeft een nadeel:

| Maatregel | Voordeel | Nadeel |
|---|---|---|
| Grotere reductie | meer koppel | lagere snelheid, mogelijk meer speling |
| Kleinere poelie | minder motorkoppel | hogere snelheid, kleinere buigradius |
| Sterkere motor | meer marge | duurder, zwaarder |
| Trekveren | lagere openings- en houdkracht | sluiten moet gecontroleerd gebeuren, extra onderdelen |
| Extra mechanismen | lagere lokale structurele belasting | meer complexiteit, meer synchronisatie |
| Tandriem | stil en precies | gevoelig voor uitlijning en spanning |
| Ketting | robuust | lawaai, smering, speling |

Voor de huidige toepassing is de combinatie `motor + reductie + tandriem/kabel +
rem + encoder` de meest verdedigbare keuze.

## 9. Gemeenschappelijke as en meerdere mechanismen

De finale overdekking gebruikt twee identieke mechanismen die door dezelfde
motor worden aangedreven. Notebook 4 rekent daarom ook de gemeenschappelijke
aandrijfas door.

Het principe is:

- een motor drijft een horizontale as aan;
- de as ligt hoog of achter de constructie, buiten de wandelzone;
- op die as zitten twee lokale aandrijfpoelies;
- elke poelie drijft een eigen gesloten riem/kabellus langs een mast;
- elke schuiver zit vast aan een aangedreven riemtak;
- de vrije riemtak loopt alleen terug;
- de schuivergeleiding draagt de horizontale reactiekracht.

De voorbalk of het doek mag niet gebruikt worden als synchronisatie-element.
Als een kant achterloopt, zou de voorbalk scheef belast worden. De
gemeenschappelijke as zorgt dat beide lokale poelies dezelfde hoekverdraaiing
krijgen.

Notebook 4 controleert de as als eerste orde:

```text
tau = T r / J
theta = T L / (G J)
```

met:

- `T`: asontwerpkoppel;
- `J`: polair traagheidsmoment;
- `G`: schuifmodulus;
- `L`: aslengte.

De huidige geselecteerde as is:

```text
stalen buis 40x5 mm
```

met:

- torsiehoek over 6 m: `0.55 deg`;
- schuifspanning: `2.52 MPa`;
- indicatieve lagerbelasting: `798 N`.

Dat maakt een enkele motor met gemeenschappelijke as verdedigbaar als eerste
ontwerp. Het is nog geen volledige productie-uitwerking: lagerkeuze,
koppelingen, spiebanen, montageplaten, uitlijning en bescherming moeten in een
volgend ontwerpstadium verder uitgewerkt worden.

## 10. Frequentie-inhoud

Vraag 5 gaat over frequentie-inhoud. Notebook 4 gebruikt daarvoor twee dingen:

1. spectrum van de schuiverversnelling;
2. spectrum van de aandrijfkracht.

Daarna wordt een eerste buigfrequentie van de voorbalk geschat met een
klassieke eenvoudig-opgelegde-balk-benadering. De vergelijking komt neer op:

```text
f1 ~ sqrt(E I / (m L^4))
```

De exacte factor hangt af van de randvoorwaarden, maar het doel is hier niet
een volledige modale analyse. Het doel is een eerste check:

> Ligt de aandrijffrequentie ver onder de vermoedelijke structurele
> eigenfrequentie?

Voor de huidige case:

- dominante aandrijfreferentie: ongeveer `0.042 Hz`;
- geschatte eerste voorbalkfrequentie: ongeveer `15.6 Hz`;
- scheidingsfactor: ongeveer `375`.

Dat ondersteunt de conclusie dat de gekozen beweging zeer rustig is en niet
dicht bij de eerste voorbalkmodus zit. De analyse dekt geen doekflapperen,
windvlagen, riemtrillingen of volledige mastmodi. Die beperkingen moeten
expliciet vermeld worden.

## 11. Kost en energieverbruik

De vaste bijvraag over motorkost en energieverbruik wordt in Notebook 4
beantwoord als orde van grootte.

Voor de huidige case:

- elektrische equivalentenergie per open+sluitcyclus: `476.5 J`;
- jaarlijkse bewegingsenergie bij 1 cyclus/dag en 220 dagen: `0.029 kWh`;
- jaarlijkse energiekost bij `0.35 euro/kWh`: ongeveer `0.01 euro/jaar`;
- totale aandrijfhardware: `1759-2699 euro`, waarvan de concrete motor/reductor-kit ongeveer `1359-1699 euro` bedraagt.
- constructie buiten motor: `5272-10239 euro`;
- motor plus constructie samen: `6631-11938 euro`.

De energiekost is dus verwaarloosbaar tegenover de aankoopkost. De
motorkost wordt vooral bepaald door:

- IP-bescherming voor buitengebruik;
- rem;
- encoder;
- reductiekast;
- koppelingen;
- lagers;
- as en poelies;
- montage en afscherming.

De prijs is geen catalogusofferte. Het is een richtwaarde voor een robuuste
technische oplossing.

## 12. Waarom geen vliegwiel?

Een vliegwiel is nuttig bij machines met cyclische energiewisselingen en
continue rotatie. Deze overdekking beweegt traag, stopt lang in tussenstanden
en moet veilig kunnen blijven staan.

Daarom is een vliegwiel hier niet de beste keuze:

- het helpt vooral bij continue cycli, niet bij enkele open/sluitbewegingen;
- het maakt stilstand en positionering moeilijker;
- het voegt draaiende massa en veiligheidsrisico toe;
- het helpt niet rechtstreeks tegen statische zwaartekrachtbelasting.

Voor deze toepassing zijn logischer:

- trekveren of gasveren voor zwaartekrachtcompensatie;
- rem of zelfremmende overbrenging voor tussenstanden;
- rustige trajectkeuze om piekvermogen te beperken;
- goede geleiding om wrijving en speling te verminderen.

## 13. Welke figuren zijn belangrijk?

De nuttige figuren in Notebook 4 zijn:

| Figuur | Wat toont ze? | Waarvoor gebruiken? |
|---|---|---|
| Aandrijfkracht, vermogen en koppel | totale belasting van de gekozen loadcase | motorkeuze en piekbelasting |
| Houdkracht en remkoppel | vereiste om tussenstanden vast te houden | stand houden en veiligheid |
| Poelieradius en reductie | invloed van radius en gear ratio | trade-off kracht/snelheid/precisie |
| Motorclass | waarom 500 W + rem gekozen wordt | kost- en motordimensie |
| Precisiebronnen | resolutie, backlash en elasticiteit | optimalisatie voor precisie |
| Tijdschaalanalyse | effect van sneller/trager bewegen | trajectkeuze en energie |
| Mechanism count | effect van meerdere mechanismen | parallelle kopies met een motor |
| Ascontrole | torsie, spanning en massa van aandrijfas | synchronisatie en haalbaarheid |
| Frequentiespectrum | aandrijffrequenties versus voorbalkfrequentie | frequentie-inhoud |
| Aandrijfschets en kost | fysische opstelling en kostorde | eindverdediging |

Niet elke figuur moet getoond worden in een mondeling antwoord. De kernfiguren
zijn meestal:

1. kracht/vermogen/koppel;
2. houdkracht/rem;
3. poelie-radius/motorclass;
4. ascontrole;
5. frequentiespectrum;
6. kost/energie.

## 14. Beperkingen

Notebook 4 is een aandrijfvoorontwerp. De belangrijkste beperkingen zijn:

- geen definitieve leveranciersselectie of CE-/IP-uitwerking;
- geen gedetailleerde lager- en koppelingdimensionering;
- geen vermoeiingsberekening van de as;
- geen volledige riemtand- of kabeltrommelberekening;
- geen regellus-simulatie van de motorcontroller;
- geen volledige modale analyse;
- geen windvlaag tijdens beweging;
- geen tolerantieketen van alle montageonderdelen.

Voor het project is dat aanvaardbaar omdat de notebook de belangrijkste
ontwerpkeuzes onderbouwt: motorvermogen, koppel, rem, poelie, as,
synchronisatie, precisie, energieverbruik en kostorde.

## 15. Mondelinge kernsamenvatting

Notebook 4 vertaalt de krachten uit Notebook 3 naar een praktische
aandrijving. Voor de finale overdekking met trekveren zijn twee mechanismen
gekoppeld aan een gemeenschappelijke hoge aandrijfas. De riem/kabel levert
alleen de verticale schuiverkracht; de schuivergeleiding en mast dragen de
horizontale reacties. De zwaarste richting is openen. Met veiligheidsfactor
komt de ontwerplijnkracht op ongeveer `798 N`, wat bij een poelie van `25 mm`
een uitgangskoppel van ongeveer `21.7 Nm` geeft. Daarom is een `500 W`
BLDC/servo-reductiemotor met encoder en rem een verdedigbare klasse. De
energiekost is zeer klein; de aankoopkost en mechanische robuustheid zijn veel
belangrijker.
