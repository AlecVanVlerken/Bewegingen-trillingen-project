# Bijvragen mondeling - Stangenmechanisme

Dit document is een apart Q&A-blad bij `Antwoorden_Mondeling_Stangen.md`.
Het hoofdantwoorddocument blijft de korte navigatie door de tien vragen uit
`vragen.pdf`. Dit bestand dient om doorvragen op te vangen.

Gebruik dit document niet als vervanging van de notebooks. De notebooks blijven
de numerieke bron. De belangrijkste actuele case is:

| Grootheid | Waarde |
|---|---:|
| Toepassing | brede zonwerende overdekking |
| Breedte | `6.0 m` |
| Aantal mechanismen | `2` |
| Uitval | `2.34 m` |
| Schuivertraject openen | `s = 1.875 m` naar `s = 0.125 m` |
| Beweegtijd | ongeveer `30 s` |
| Voorkeurscase | overdekking met trekveren |
| Piek `|F_s|` met trekveren | `327.48 N` per mechanisme |
| Ontwerplijnkracht totaal | `1309.93 N` |
| Ontwerp-uitgangskoppel | `40.31 Nm` |
| Motorclass | `500 W BLDC/servo + rem` |
| Bewegingsenergie open+sluit | ongeveer `1114 J` |

## Vraag 1 - Industrieel gebruik en ontwerpaanpassing

### Waarom is het ontwerp van parasol naar overdekking gegaan?

Een losse parasol was nuttig om het vlakke stangenmechanisme op te bouwen:
kinematica, snelheden, versnellingen, inverse dynamica en onbalans. Voor een
cafe- of restauranttoepassing is een brede geveloverdekking echter realistischer.
Daarom is het basismodel behouden, maar uitgebreid naar twee identieke
mechanismen die samen een voorbalk en doekvlak dragen.

Het antwoord is dus: de mechanische kern is hetzelfde, maar de toepassing is
groter geworden. Daardoor zijn vooral massa, voorbalkstijfheid, mastbelasting en
aandrijving belangrijker geworden.

### Waarom twee mechanismen en geen een of drie?

Een mechanisme is eenvoudiger, maar het draagt een brede balk niet goed en geeft
veel asymmetrische belasting. Drie mechanismen zouden de voorbalk stijver
ondersteunen, maar maken de aandrijving en synchronisatie moeilijker. Twee
mechanismen is een tussenkeuze: breed genoeg voor `6 m`, maar nog overzichtelijk
qua aandrijving.

Bij twee mechanismen moet de aandrijving ongeveer de dubbele verticale
aandrijfkracht leveren tegenover een enkel mechanisme. Het voordeel is dat de
overdekking links en rechts ondersteund wordt en minder scheeftrekt.

### Wat verandert als de overdekking breder wordt?

Bij grotere breedte stijgen de voorbalkmassa, doekmassa, windlast en
doorbuiging. Vooral de voorbalk wordt dan kritisch, omdat doorbuiging bij een
balk sterk toeneemt met de overspanning. Je moet dan:

- een groter of stijver balkprofiel kiezen;
- eventueel een derde mechanisme toevoegen;
- de gemeenschappelijke as stijver maken;
- de motor en rem groter dimensioneren;
- de muur- of mastbevestiging zwaarder uitvoeren.

Het basismodel in Notebook 1 verandert niet noodzakelijk. De massa- en
belastingparameters in `Notebook 3 - Overdekking` en Notebook 4 veranderen wel.

### Waar zit de motor praktisch?

De motor staat best bovenaan of achteraan aan de mast/framezijde, niet op het
bewegende uiteinde. De huidige keuze is een motor met reductie, encoder en rem
die een gemeenschappelijke as aandrijft. Vanaf die as loopt per mechanisme een
lokale tandriem of kabel langs de mast naar de schuiver.

Dat is praktisch omdat de zware motor op het frame blijft. De bewegende delen
blijven lichter, en beide mechanismen kunnen via dezelfde as synchroon lopen.

### Wat draagt de mast en wat draagt de riem?

De riem of kabel draagt vooral de verticale aandrijfkracht in de richting van de
schuiver. Dat is de kracht `F_s` uit de inverse dynamica.

De grote horizontale steunreacties op de schuiver worden niet door de riem
gedragen. Die gaan via de schuiver/collar, geleiderollen of glijblokken naar de
mast. Daarom is de mast en schuivergeleiding constructief kritischer dan de riem
alleen. In de voorkeurscase is de verhouding tussen maximale dwarsreactie en
aandrijfkracht ongeveer `16.1`.

### Wat gebeurt er bij windbelasting?

Wind werkt vooral op het doekvlak en op de voorbalk. In de notebook is dit als
eerste-orde structurele controle opgenomen. De maatgevende loadcase is `wind
uplift`, met maximale benutting ongeveer `0.79`. Dat betekent dat het ontwerp
binnen de aannames acceptabel is, maar het is geen gecertificeerde bouwkundige
berekening.

Bij een echte toepassing moeten windvlagen, asymmetrische wind, verankering,
vermoeiing en veiligheid apart gecontroleerd worden.

## Vraag 2 - Optimalisatie voor precisie, energie en kracht

### Wat is de grootste precisiefout?

Niet de encoderresolutie. Notebook 4 toont dat de theoretische lineaire
encoderresolutie veel fijner is dan nodig. De echte foutbronnen zijn:

- riemrek;
- speling in reductor of koppelingen;
- torsie van de gemeenschappelijke as;
- schuiverkanteling;
- flexibiliteit van mast en voorbalk;
- toleranties in scharnieren;
- ongelijke belasting links en rechts.

Daarom is mechanische stijfheid belangrijker dan alleen een betere encoder.

### Hoe maak je het preciezer?

Je maakt het preciezer door:

- een voorgespannen tandriem te gebruiken;
- spelingarme reductie te kiezen;
- een rem of vergrendeling te voorzien;
- eindschakelaars en homing te gebruiken;
- de schuiver/collar stijver te maken;
- de gemeenschappelijke as torsiestijf genoeg te maken;
- beide zijden mechanisch synchroon te houden.

Voor een zonwering is absolute micrometerprecisie niet nodig. Belangrijker is
dat de twee zijden gelijk lopen en dat tussenstanden betrouwbaar blijven staan.

### Hoe verlaag je energieverbruik?

Trager bewegen verlaagt piekvermogen, maar niet de zwaartekrachtarbeid. De massa
moet nog altijd omhoog of omlaag verplaatst worden. Energieverbruik verlaag je
vooral door:

- minder massa te gebruiken;
- wrijving in schuiver en scharnieren te verlagen;
- goede lagers en geleiding te kiezen;
- de voorbalk niet zwaarder te maken dan nodig;
- trekveren of gasveren te gebruiken als gedeeltelijke compensatie;
- onnodige stand-by-verliezen in de elektronica te vermijden.

Voor de huidige voorkeurscase is de bewegingsenergie ongeveer `1114 J` per
open+sluitcyclus. De energiekost zelf is dus verwaarloosbaar tegenover de
componentkost.

### Hoe krijg je meer kracht?

Meer kracht kan door:

- een sterkere motor te kiezen;
- een grotere reductieverhouding te gebruiken;
- een kleinere poelie te gebruiken;
- een bredere of sterkere riem te kiezen;
- twee motoren te gebruiken in plaats van een gemeenschappelijke as;
- veerassistentie toe te voegen.

De trade-off is dat meer reductie meestal lagere snelheid geeft, en een kleinere
poelie de riem sterker buigt en hogere motorsnelheid vraagt.

### Waarom tandriem/kabel en geen ketting of lange spindel?

Een lange spindel of lineaire actuator legt de schuiverpositie heel direct op,
maar wordt bij een slag van ongeveer `1.75 m` groot, zichtbaar en moeilijker te
integreren. Een ketting is sterk, maar zwaarder, luider en vraagt onderhoud.

Een tandriem of kabel langs de mast is lichter, stiller en makkelijker weg te
werken. Voor een brede overdekking is dat praktischer. De voorwaarde is wel dat
de schuivergeleiding de dwarsreacties opneemt.

## Vraag 3 - Vliegwiel en energiebuffering

### Waarom gebruiken we geen vliegwiel?

Een vliegwiel is logisch bij continu cyclische machines: persen, motoren,
compressoren of nokkenmechanismen. Daar wisselen arbeid en teruglevering snel af
binnen elke cyclus.

Onze overdekking beweegt traag, stopt lang en moet posities vasthouden. Een
vliegwiel zou tijdens stilstand geen nuttige functie hebben en maakt het systeem
zwaarder en gevaarlijker. Daarom is het geen goede hoofdoplossing.

### Waar zou je een vliegwiel zetten als het toch moest?

Als men toch een vliegwiel wil, dan zet je het best op de lage-snelheidsas dicht
bij de last of op een goed gekozen motoras met rekening voor de reductie. Op de
motoras wordt het effect door de reductie anders vertaald naar de uitgang.

Maar voor dit ontwerp zou ik dat enkel als theoretische vergelijking vermelden.
Praktisch is een rem, gecontroleerde motorregeling en eventueel een veer veel
logischer.

### Wat betekent `A_max` dan nog?

`A_max` blijft nuttig als maat voor energiefluctuatie binnen de beweging. Het
zegt hoeveel tijdelijke energiebuffering nodig zou zijn om pieken uit te
middelen. Bij een vliegwiel wordt dat vertaald naar traagheidsmoment. Bij onze
toepassing interpreteren we het eerder als belasting voor motorregeling,
elektrische buffer of veerassistentie.

### Waarom is een trekveer of gasveer logischer?

Een veer werkt direct tegen de zwaartekrachtlast van het mechanisme. Ze helpt
vooral bij openen, waar de motor het zwaarst belast wordt. In de huidige case
verlaagt trekveerassistentie de aanbevolen motorvermogensklasse van ongeveer
`745 W` naar ongeveer `487 W`.

Een veer past dus beter bij een trage positioneerbeweging dan een vliegwiel.

### Is een trekveer gratis energie?

Nee. Bij openen geeft de veer energie terug, maar bij sluiten moet de veer
opnieuw opgespannen worden. De winst zit in het spreiden van belasting en het
verlagen van de openingspiek. De motor en rem blijven nodig om de beweging te
controleren en tussenstanden vast te houden.

## Vraag 4 - Keuze van bewegingstraject

### Waarom geen constante snelheid?

Constante snelheid lijkt eenvoudig, maar geeft problemen aan begin en einde: de
snelheid moet daar plots van nul naar een vaste waarde gaan of omgekeerd. Dat
betekent theoretisch hoge versnelling of ruk. Voor een grote overdekking is dat
niet realistisch en niet comfortabel.

Daarom gebruiken we een glad traject dat vanuit stilstand start en terug tot
stilstand komt.

### Waarom `smooth_4567`?

Een `smooth_4567`-wet is gladder dan een eenvoudige derde- of vijfdegraads wet
aan de randen. Ze maakt snelheid, versnelling en jerk aan begin en einde rustiger.
Dat past beter bij een grote, trage overdekking.

### Waarom condition-aware vertragen?

Notebook 1 toont dat `cond(A)` het grootst is bij de gesloten stand. Daar is het
mechanisme gevoeliger: kleine fouten of bewegingen kunnen grotere effecten
hebben in hoeken, snelheden en krachten. Daarom beweegt de schuiver daar trager.

Dat is geen zwarte-doos optimalisatie, maar een uitlegbare ontwerpkeuze:
vertraging waar het mechanisme ongunstiger staat.

### Wat gebeurt er als je sneller opent?

Als je dezelfde beweging in minder tijd uitvoert:

- snelheden stijgen ongeveer met `1/T`;
- versnellingen stijgen ongeveer met `1/T^2`;
- inertiekrachten stijgen ongeveer met `1/T^2`;
- piekvermogen stijgt omdat `P = F v`;
- zwaartekrachtarbeid blijft ongeveer gelijk.

Bij onze trage beweging domineert zwaartekracht, dus de piekkracht verandert
niet heel sterk. Het piekvermogen verandert wel duidelijk.

### Hoe kies je de opentijd?

Voor een grote terrasoverdekking is `25-30 s` verdedigbaar. Het voelt rustig,
vermijdt schokken, geeft de gebruiker tijd om te stoppen, en houdt inertie klein.
Sneller kan technisch, maar dan stijgen piekvermogen, trillingsrisico en
veiligheidseisen.

## Vraag 5 - Frequentie-inhoud

### Waarom analyseren we frequenties bij zo'n traag systeem?

Omdat ook een trage beweging periodieke of quasi-periodieke excitatie bevat. Als
de aandrijfkracht of versnelling frequentiecomponenten heeft dicht bij een
eigenfrequentie van voorbalk, mast of as, kan resonantie ontstaan.

Bij onze huidige beweging is de dominante frequentie veel lager dan de geschatte
eerste buigfrequentie van de voorbalk. Dat ondersteunt de keuze voor een rustig
traject.

### Wat betekent de dominante frequentie?

De dominante frequentie is de belangrijkste herhalende component in de kracht of
versnelling. Voor de overdekking met trekveren ligt die rond `0.029 Hz`. Dat is
zeer laag. De geschatte eerste buigfrequentie van de voorbalk is ongeveer
`15.58 Hz`. De marge is dus groot.

### Welke frequentie wil je vermijden?

Je wil vermijden dat aandrijfcomponenten samenvallen met eigenfrequenties van:

- de voorbalk;
- de mast;
- de gemeenschappelijke as;
- de riem/kabel;
- het doekvlak.

De notebook controleert vooral de voorbalk als eerste-orde check. Voor een echt
product moet je ook doekflapperen en masttrilling bekijken.

### Wat als je sneller opent?

De frequenties in de aandrijving schuiven omhoog. Ook versnellingen en
inertiekrachten worden groter. Daardoor kan resonantie belangrijker worden. Als
men de opentijd sterk verlaagt, is een uitgebreidere modale analyse nodig.

## Vraag 6 - Dode punten en singulariteiten

### Wat betekent `cond(A)` fysisch?

`cond(A)` zegt hoe gevoelig de oplossing van het snelheidsstelsel is. Als
`cond(A)` groot wordt, betekent dit dat kleine fouten in invoer of geometrie
grote fouten in hoeksnelheden kunnen geven. Dicht bij een singulariteit kan de
matrix bijna niet meer betrouwbaar opgelost worden.

### Is `cond(A) = 68` erg?

Het is niet oneindig en dus geen echte singulariteit, maar het is wel een
waarschuwing. De grootste waarde zit bij de gesloten stand. Daarom wordt daar
traag bewogen en ontwerpen we niet nog dichter tegen de geometrische grens.

### Wat is een dood punt?

Een dood punt is een configuratie waarin de aandrijving geen nuttig moment of
kracht meer kan overbrengen in de gewenste richting. Mechanisch voordeel kan dan
naar nul gaan of juist zeer groot worden. In zulke zones kan het mechanisme vast
lopen of extreem gevoelig worden.

### Hoe vermijd je singulariteiten?

Door:

- slaggrenzen niet te dicht bij de geometrische grens te leggen;
- stanglengtes zo te kiezen dat links niet bijna collineair worden;
- de conditionering over het traject te controleren;
- trager te bewegen in gevoelige zones;
- eventueel een andere aandrijfplaats of mechanismegeometrie te kiezen.

### Waarom gebruiken we geen dood punt als vergrendeling?

Omdat we tussenstanden willen kunnen kiezen. Een dood punt kan een eindstand
mechanisch helpen blokkeren, maar maakt vertrekken moeilijker en is gevoelig voor
toleranties. Een rem of vergrendeling is betrouwbaarder.

## Vraag 7 - Onbalans

### Wat is onbalans precies?

Onbalans is de dynamische belasting die het bewegende mechanisme op het frame
zet door versnellingen van massa's. In Notebook 2 is dat inertie-only:
zwaartekracht en wrijving zitten daar nog niet in.

Het is dus niet hetzelfde als alle steunreacties. Een grote steunreactie door
gewicht of geometrie is niet automatisch inertiele onbalans.

### Waarom is onbalans klein maar framebelasting groot?

Omdat het mechanisme traag beweegt. Inertiekrachten zijn daardoor klein. De grote
belastingen komen vooral van:

- gewicht van voorbalk en doek;
- schuiverwrijving;
- ongunstige hefboomwerking;
- horizontale reactie in de schuivergeleiding;
- mastmoment.

De inertiele onbalanskracht van het basismodel is maar ongeveer `0.54 N`, terwijl
de lokale schuiverreactie in de overdekking veel groter is.

### Wordt onbalans meegenomen in vermogen?

Niet rechtstreeks als aparte term. Vermogen komt uit de aandrijfkracht maal de
schuiversnelheid: `P = F_s ds`. De inertiele effecten zitten wel in `F_s`, maar
bij deze trage beweging zijn ze klein tegenover zwaartekracht en wrijving.

### Hoe los je onbalans op?

Voor dit ontwerp is inertiele onbalans geen hoofdprobleem. De oplossing zit
vooral in rustig bewegen, symmetrische opbouw en een stijve frame/mastbevestiging.

Bij snellere mechanismen kan men balanceren met tegenmassa's of symmetrische
tegenbewegingen. Hier is dat minder zinvol dan het correct dimensioneren van
mast en geleiding.

### Wat verandert bij symmetrische dubbele uitvoering?

Twee mechanismen kunnen sommige globale horizontale effecten compenseren, als ze
spiegelbeeldig en synchroon bewegen. Maar lokaal moet elke schuiver nog steeds
zijn eigen dwarsreactie dragen. De motor moet ongeveer de som van beide
verticale aandrijfkrachten leveren.

## Vraag 8 - Inertie, wrijving en stijfheid

### Welke kracht domineert?

Bij het trage traject domineert zwaartekracht, gevolgd door schuiverwrijving en
constructieve reacties. Inertie is klein. Dat is logisch: versnellingen zijn
klein omdat de beweging ongeveer `30 s` duurt.

### Waarom is inertie zo klein?

Inertie hangt af van versnelling. Als de opentijd groot is, zijn snelheden en
versnellingen klein. Inertiekrachten schalen ongeveer met `1/T^2`. Daarom is
inertie in Notebook 2 klein, maar zou ze bij een veel sneller traject wel
belangrijk worden.

### Is de wrijving realistisch gemodelleerd?

Het wrijvingsmodel is eenvoudig maar verdedigbaar:

- Coulombwrijving in de schuiver op basis van normaalreactie;
- scharnierwrijving als eenvoudig bus-/pinmoment;
- smoothing met `tanh` om numerieke problemen rond nul snelheid te vermijden.

Het is geen tribologisch detailmodel. De wrijvingscoefficienten zijn
ontwerpaannames en moeten bij een echte uitvoering gevalideerd worden.

### Is viskeuze wrijving nodig?

Voor een eerste model is Coulombwrijving het belangrijkste. Viscose wrijving kan
klein gekozen worden of zelfs nul gezet worden als je alleen droge glijwrijving
wil modelleren. Ze is vooral nuttig als numerieke demping of als de geleiding
duidelijk snelheidsafhankelijke verliezen heeft.

### Wat betekent stijfheid hier?

Stijfheid betekent dat het systeem niet te veel vervormt onder belasting. In de
Newton-Euler-dynamica worden de stangen star verondersteld. De praktische
stijfheid wordt apart gecontroleerd via:

- voorbalkdoorbuiging;
- mastmoment;
- schuiver/collar-belasting;
- torsie van de gemeenschappelijke as;
- frequentiemarge.

### Waar zit de kritische vervorming?

De belangrijkste plekken zijn:

- voorbalk over `6 m`;
- mast en muurbevestiging;
- schuiver/collar rond de mast;
- gemeenschappelijke as;
- riemrek;
- scharnieren en stangen.

De voorbalkcontrole is maatgevend door wind uplift. De mast en schuivergeleiding
zijn belangrijk door de grote horizontale reacties.

### Is dit een volledige sterkteberekening?

Nee. Het is een goede eerste-orde ontwerpcontrole voor het examen. Voor echte
bouw of verkoop heb je nog nodig:

- detaildimensionering van bouten, lassen en lagers;
- vermoeiingscontrole;
- echte windnormcontrole;
- toleranties en montage;
- corrosie/IP-bescherming;
- veiligheidsanalyse.

## Vraag 9 - Parallel aangedreven mechanismen

### Moet de motor dubbele kracht leveren?

Ja, ongeveer wel voor de verticale aandrijfkracht. Als twee identieke mechanismen
tegelijk bewegen, moet de aandrijving de som van de krachten leveren. In Notebook
4 is dat meegenomen via `mechanism_count = 2`.

De motor ziet dus de totale lijnkracht. Per schuiver blijft de lokale kracht
ongeveer de kracht van een mechanisme.

### Wat als een kant achterloopt?

Dan kan de voorbalk scheef trekken en wringen. Dat kan extra belasting geven in
de balk, scharnieren, schuivers en riem. Daarom is een gemeenschappelijke as
aantrekkelijk: ze koppelt beide zijden mechanisch.

Bij twee aparte motoren zou je elektronische synchronisatie nodig hebben.

### Waarom een gemeenschappelijke as?

Omdat ze beide mechanismen mechanisch gelijk laat lopen. Dat is eenvoudiger en
robuuster dan twee motoren exact synchroon regelen. De trade-off is dat de as
torsiestijf genoeg moet zijn en het totale koppel moet dragen.

### Wat als je twee motoren gebruikt?

Twee motoren maken de mechanische as eenvoudiger of overbodig, maar geven een
regeltechnisch probleem. Als de motoren niet exact gelijk lopen, kan de balk
wringen. Je hebt dan encoders, synchronisatieregeling en beveiliging nodig.

### Worden horizontale reacties gecompenseerd?

Globaal kunnen symmetrische horizontale reacties deels wegvallen in het gebouw of
frame. Lokaal verdwijnen ze niet. Elke schuiver en mast moet zijn eigen
dwarsreactie opnemen.

## Vraag 10 - Parameter veranderen

Deze vraag moet je breder voorbereiden dan alleen "sneller of trager bewegen".
De prof kan vragen om een stanglengte te veranderen, een andere aandrijver te
kiezen, of een massa aan te passen. Het veilige antwoord is steeds:

1. voorspel kwalitatief het effect;
2. verander de parameter bovenaan in de juiste notebook;
3. run de keten opnieuw;
4. vergelijk baan, `cond(A)`, krachten, vermogen en motorselectie.

### Case A - Trajecttijd veranderen

Voorspelling:

- snelheid schaalt met `1/T`;
- versnelling schaalt met `1/T^2`;
- inertiekrachten schalen ongeveer met `1/T^2`;
- piekvermogen stijgt bij sneller openen;
- zwaartekrachtarbeid blijft ongeveer gelijk.

Bij onze huidige trage beweging verandert de piekkracht weinig, omdat
zwaartekracht domineert. Het piekvermogen verandert wel sterk.

### Case B - Een stanglengte veranderen

Bijvoorbeeld: verander een lengte van link 8 of een arm die punt K bepaalt.

Voorspelling:

- de baan van punt K verandert;
- de hefboomwerking tussen schuiver en K verandert;
- `cond(A)` kan verbeteren of verslechteren;
- snelheden en versnellingen van K veranderen;
- de zwaartepunten bewegen anders;
- krachten in Notebook 2 en 3 veranderen;
- de motorbelasting in Notebook 4 verandert mee.

Als een stang langer wordt, kan de uitval groter worden, maar ook het moment op
mast en scharnieren stijgen. Een langere arm geeft vaak meer bereik, maar slechtere
krachten en meer doorbuiging. Een kortere arm kan stijver en krachtiger zijn, maar
geeft minder uitval.

Belangrijk: bij een lengteverandering moet je eerst Notebook 1 opnieuw runnen.
Anders gebruiken Notebook 2, 3 en 4 oude kinematica.

### Case C - Een andere stang als aandrijver gebruiken

Dit is een belangrijke bijvraag. De mobiliteit van het mechanisme blijft `M = 1`,
maar de gekozen invoer verandert. Nu leggen we de schuiverpositie `s(t)` op. Als
we een andere stang aandrijven, leggen we bijvoorbeeld een hoek `theta_i(t)` op.

Dan verandert de analyse als volgt:

- in Notebook 1 wordt `theta_i(t)` de bekende invoer;
- de schuiverpositie `s` wordt dan een onbekende uit de sluitingsvergelijkingen;
- de snelheidsmatrix verandert, omdat `theta_i_dot` bekend is in plaats van `ds`;
- de inverse dynamica vraagt geen schuifkracht `F_s`, maar een aandrijfkoppel
  `M_i` op die stang;
- het vermogen wordt `P = M_i omega_i` in plaats van `P = F_s ds`;
- singulariteiten kunnen op andere plaatsen optreden;
- de praktische montage kan moeilijker worden als de aangedreven stang niet
  geaard is.

Voor ons ontwerp is de schuiver als aandrijver logisch omdat hij langs de mast
beweegt. Een motor, riem, kabel en rem kunnen daar makkelijk op het frame
geplaatst worden. Een zwevende stang aandrijven zou een bewegende actuator,
extra massa of complexe transmissie vragen.

Een geaarde draaipunt-aandrijving kan wel theoretisch interessant zijn. Dan zou
je een motor aan een vast scharnier zetten en een koppel opleggen. Dat kan compact
zijn, maar de benodigde koppels kunnen groot worden en de bewegingsbaan van de
schuiver is dan niet meer direct opgelegd.

### Case D - Massa of payload veranderen

Als de voorbalk, doekmassa of puntmassa K groter wordt:

- zwaartekrachtcomponent stijgt ongeveer lineair;
- houdkracht stijgt;
- motorvermogen en remvereiste stijgen;
- frame- en mastreacties stijgen;
- inertiekrachten stijgen ook, maar blijven bij trage beweging minder dominant.

Dit is een zeer verdedigbare parameterstudie omdat ze direct past bij een
overdekking: breder doek of zwaardere voorbalk betekent grotere belasting.

### Case E - Aantal mechanismen veranderen

Als je van twee naar drie mechanismen gaat:

- de last per mechanisme kan dalen;
- de voorbalkdoorbuiging daalt;
- de aandrijving en synchronisatie worden complexer;
- het totale aantal onderdelen stijgt;
- de motor kan nog steeds centraal, maar de as en riemverdeling worden complexer.

Als je naar een mechanisme gaat:

- aandrijving is eenvoudiger;
- voorbalkbelasting en scheeftrekken worden veel slechter;
- voor een 6 m overdekking is dat minder verdedigbaar.

### Case F - Trekveerparameters veranderen

Als de veer stijver of sterker wordt:

- openen vraagt minder motor kracht;
- sluiten vraagt meer kracht om de veer opnieuw op te spannen;
- houdkracht verandert;
- te veel compensatie kan oncontroleerbaar worden.

Daarom is gedeeltelijke compensatie beter dan volledige compensatie. De veer
moet helpen, niet het systeem overnemen.

## Vaste bijvraag - Motorprijs en energieverbruik

### Hoeveel kost de motor?

Voor de voorkeurscase is de gekozen klasse `500 W BLDC/servo + rem`. De
richtprijs voor motor, reductor, rem en drive ligt in de orde `350-900 euro`.
Met riem, as, poelies en lagers wordt de totale aandrijfhardware geschat op
`600-1600 euro`.

Dit is geen definitieve offerte. Voor een echte keuze moeten datasheets,
beschermingsgraad, remtype, duty cycle, lagerlasten en riemtype gecontroleerd
worden.

### Hoeveel kost het energieverbruik?

De bewegingsenergie is zeer klein. Notebook 4 geeft ongeveer `1114 J` per
open+sluitcyclus. Bij een beperkt aantal cycli per jaar komt dat op ongeveer
`0.068 kWh/jaar`, of ongeveer `0.024 euro/jaar` bij de ingestelde prijs.

De energiekost van de beweging is dus verwaarloosbaar. Stand-by-verbruik,
controller, onderhoud en componentkost zijn veel belangrijker.

## Snelle verdediging als de prof kritisch doorvraagt

### "Is dit niet te weinig voor een echt product?"

Ja, voor een echt product zijn extra controles nodig. Maar voor deze analyse is
de methode correct: eerst kinematica, dan inverse dynamica, dan zwaartekracht en
wrijving, daarna aandrijving, trekveren, framebelasting en frequentiecheck.

### "Wat is jullie grootste onzekerheid?"

De grootste onzekerheden zijn windbelasting, echte schuivergeleiding,
mastverankering, riemrek, doekgedrag en cataloguskeuze van motor/rem. De
notebooks geven een eerste ontwerpstudie, geen gecertificeerd bouwkundig dossier.

### "Wat is de belangrijkste technische conclusie?"

De motor kan de verticale aandrijfkracht leveren, zeker met trekveerassistentie.
De echte uitdaging zit in de zijdelingse schuiverreacties, mastbevestiging,
voorbalkstijfheid en synchronisatie van de twee mechanismen.

### "Waarom is het ontwerp verdedigbaar?"

Omdat elke stap numeriek gekoppeld is:

1. Notebook 1 geeft baan, snelheden, versnellingen en conditionering.
2. Notebook 2 geeft inertiele inverse dynamica en onbalans.
3. Notebook 3 geeft zwaartekracht, wrijving en houdkracht.
4. Notebook 3 Overdekking geeft massa, balk, mast en trekveren.
5. Notebook 4 vertaalt dat naar riem, motor, rem, frequentie en kost.

De aannames zijn expliciet, en de beperkingen worden niet verstopt.
