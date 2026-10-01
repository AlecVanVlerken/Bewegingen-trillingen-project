# Bewegingen-trillingen-project

[English](README.md) · **Nederlands**

Dit project voor het vak **Beweging en Trillingen** werkt vanuit een voorgeschreven beweging naar het mechanische ontwerp van twee systemen: een opvouwbaar stangenmechanisme dat wordt uitgewerkt tot een gemotoriseerde overdekking en een roterende nok met een translerende rolvolger. Python-notebooks verbinden geometrie en beweging met gewrichtskrachten, veerondersteuning, constructiebelastingen en de dimensionering van de aandrijving.

De overdekking vormt de belangrijkste ontwerpstudie. Twee opvouwbare mechanismen dragen een doek van 6 m breed en moeten met één motor gelijktijdig openen. De afzonderlijke nokkenstudie onderzoekt hoe de bewegingswet, contactgeometrie en volgerveer het koppel en de energievraag van een continu roterende aandrijving beïnvloeden. Beide zijn berekende ontwerpen; de repository bevat geen metingen aan een prototype.

![Berekende opening van de overdekking met beide stangenmechanismen, de voorbalk en het doek](project/Stangen/figuren/overdekking_animatie.gif)

*De opgeslagen animatie toont de volledige opstelling. De weergave is versneld: de gemodelleerde opening duurt 20 s, gevolgd door 4 s stilstand.*

## Een overdekking die opent zonder haar geleidingen te overbelasten

Elke ondersteuning is een vlak stangenmechanisme met acht schakels, inclusief de vaste mast, en één vrijheidsgraad. Een schuiver drijft de verbonden ribben aan; het buitenste punt K draagt de voorbalk. De schuivercoördinaat $s$ wordt vanaf het vaste draaipunt C naar beneden gemeten. Het mechanisme opent dus wanneer $s$ afneemt. In het gekozen geval beweegt de schuiver van 1,875 m naar 0,600 m, met een slag van 1,275 m en een horizontale uitval van ongeveer 2,22 m.

De open stand is een bewuste afweging. Als het mechanisme verder strekt naar een bijna horizontaal dak, nemen de dwarsreacties aan de schuiver en mast sterk toe. Het uiteindelijke ontwerp stopt met de buitenste rib onder een neerwaartse helling van ongeveer **15,3°**. Het levert wat bereik in om de geleidingsbelasting beheersbaar te houden. Deze geometrische keuze komt vóór de motorselectie: een kleinere actuator lost een te grote dwarsbelasting niet op.

[Notebook 1](project/Stangen/Notebook%201.ipynb) lost zes sluitingsvergelijkingen uit drie vectorlussen op met SciPy's `fsolve`. Elke gevonden configuratie dient als startpunt voor de volgende stap, zodat de berekening dezelfde assemblagetak volgt. Differentiatie van die vergelijkingen levert lineaire stelsels voor de snelheden en versnellingen van de stangen. De invoer `condition_scurve` gebruikt een bewegingswet van de zevende graad en vertraagt nabij de gevoeligere gesloten configuratie. Die vertraging vermindert daar de bewegingseisen, maar verandert de geometrische conditionering niet.

[Notebook 3 - Overdekking](project/Stangen/Notebook%203%20-%20Overdekking.ipynb) stelt vervolgens 21 Newton–Euler-vergelijkingen op voor de zeven bewegende lichamen. Uit de bekende beweging volgen de actuator- en gewrichtskrachten, met zwaartekracht, schuiverwrijving en penwrijving. Reactieafhankelijke wrijving wordt iteratief opgelost. De voorbalk, het doek en de bevestigingen leveren een equivalente massa aan de ribpunt van ongeveer 27,32 kg per mechanisme. Bij de gekozen lage openingssnelheid bepalen vooral zwaartekracht en wrijving de aandrijfbelasting.

De onderstaande figuur onderscheidt de lokale steunreacties van de actuatorkracht. De krachtcurven staan uitgezet tegen de tijd; linksonder wordt de horizontale schuiverreactie vergeleken met de verticale aandrijfkracht. Zonder veren moet de geleiding ongeveer **1,04 kN** opnemen, terwijl de maximale openingskracht ongeveer **372 N** bedraagt. Tegengestelde reacties kunnen elkaar in de totale krachtenbalans van het frame opheffen en toch grote lokale belastingen veroorzaken. Mast, geleiding en bevestigingsbeugels moeten daarom afzonderlijk worden gedimensioneerd.

![Lokale mastreacties, mastmoment en vergelijking van de dwarsbelasting met de aandrijfkracht, zonder veren](project/Stangen/figuren/mast_en_schuiverbelasting.png)

## Veren verlagen de aandrijfbelasting

Twee voorgespannen trekveren per mechanisme ondersteunen de schuiver rechtstreeks. Elke veer heeft een gemodelleerde stijfheid van 10 N/m; samen leveren ze ongeveer 172,7 N in de open stand en 198,2 N in de gesloten stand. Tijdens het sluiten slaan de veren energie op, die ze bij het openen weer afgeven. Ze verlagen de motorbelasting, terwijl de constructie de horizontale geleidingsbelasting blijft opnemen.

De vergelijking gebruikt de opgeslagen resultaten van de overdekking met en zonder veren. De kracht tegen de schuiverpositie toont de lagere openingsbelasting. De grafieken van vermogen en houdkracht laten zien hoe het voordeel over de slag verandert. Het teken van de kracht volgt de naar beneden positieve schuivercoördinaat; positief vermogen betekent dat de actuator arbeid levert.

![Beweging, schuiverkracht, vermogen en statische houdkracht van de overdekking met en zonder trekveren](project/Stangen/figuren/trekveren_kracht_en_energie.png)

| Berekende grootheid | Zonder veren | Met veren |
| --- | ---: | ---: |
| Maximale openingskracht per mechanisme, absolute waarde | 372,2 N | 199,5 N |
| Energiefluctuatie uit het arbeidsoverschot per mechanisme | 241,0 J | 105,9 J |
| Geschikte motorklasse in de notebookvergelijking | 750 W servo met rem | 500 W BLDC met rem |

Voor deze onderbroken beweging met stilstand pakt zwaartekrachtcompensatie de belangrijkste belasting rechtstreeks aan. Een vliegwiel past beter bij de herhaalde energie-uitwisseling van de nokkenstudie hieronder. De overdekking heeft nog steeds een rem of vergrendeling nodig om tussenstanden vast te houden.

## Het doek dragen en beide zijden samen aandrijven

De voorbalk moet voldoende stijf zijn over een overspanning van 6 m, maar zijn massa verhoogt ook de belasting op de stangenmechanismen. De notebook controleert een vrij opgelegde aluminium rechthoekige koker van **200 × 100 × 5 mm** op eigengewicht, opgehoopt regenwater, sneeuw, neerwaartse wind en opwaartse windbelasting. De doekbelasting wordt aan de balk toegewezen over de helft van de uitval. De figuur vergelijkt doorbuiging, gecombineerde spanning en torsie met de gekozen grenzen; een benuttingsgraad van 1 bereikt een grens.

![Benuttingsgraad van de voorbalk voor de zes berekende weersbelastingen](project/Stangen/figuren/voorbalk_weersbelasting.png)

Opwaartse windbelasting is maatgevend met een maximale benuttingsgraad van **0,749**. De berekende doorbuiging bedraagt in absolute waarde **15,0 mm** bij een grens van 20 mm. De von Mises-spanning bedraagt ongeveer **27,7 MPa** bij een grens van 100 MPa. Deze weerscontroles betreffen de constructie. De aandrijfberekening gebruikt het normale bewegingsgeval met zwaartekracht en wrijving; de motorselectie toont dus niet aan dat de overdekking onder die weersbelastingen kan bewegen.

[Notebook 4](project/Stangen/Notebook%204.ipynb) vertaalt de belastingen met veerondersteuning naar één motor, een gemeenschappelijke as en twee lokale riem-/kabelaandrijvingen. De as synchroniseert de mechanismen; de gedeelde voorbalk vervult die functie niet. Beide zijden moeten in fase blijven om scheeftrekken van doek en balk te voorkomen. De voorgestelde as loopt boven of achter de overdekking, buiten de loopruimte.

![Berekende aandrijfopstelling met één gemeenschappelijke as en twee lokale schuiveraandrijvingen](project/Stangen/figuren/aandrijfopstelling.png)

Met een poelieradius van 25 mm en een overbrenging van 25:1 geeft de opgeslagen dimensionering een gezamenlijke ontwerplijnkracht van ongeveer **798 N**, een aandrijfkoppel aan de uitgang van **21,7 N·m** en een vereist remkoppel aan de uitgang van **11,6 N·m**. Het maximale berekende ingangsvermogen van de motor bedraagt 146,3 W. Ook koppel en remcapaciteit bepalen de keuze: de notebook selecteert een aangenomen **500 W, 48 V BLDC-klasse** met reductor, encoder en rem. Het gaat om een vergelijking van gemodelleerde componentklassen, niet om een gespecificeerde commerciële samenstelling.

De gekozen buisas van Ø40/30 mm verdraait ongeveer **0,55°** over 6 m bij een grens van 2°. De positioneernauwkeurigheid hangt daardoor naast de encoder ook af van de stijfheid van as, riem en geleiding. Het ontwerp vergelijkt bovendien het spectrum van de openingskracht met een geschatte balkfrequentie. Dat blijft een eerste controle en is geen modale analyse van de volledige opstelling.

Bij de aangenomen ene openings- en sluitingscyclus per dag gedurende 220 dagen verbruikt de beweging met veerondersteuning ongeveer **0,029 kWh per jaar**, exclusief het stand-byverbruik van de besturing. De geschatte hardwarekosten in de notebook van ongeveer **€6.600–€11.900**, exclusief professionele installatie en engineering, leggen de nadruk op het grotere vraagstuk: mechanische complexiteit en componentkosten wegen zwaarder dan de bewegingsenergie bij zo weinig gebruik. Dit zijn aannames uit de notebook, geen actuele offertes.

Het stangenmodel gebruikt starre lichamen, gevolgd door afzonderlijke controles van balk, mast en as. Verbindingen, verankeringen, vermoeiing, de dynamica van vervormbare lichamen en ongelijkmatige weersbelasting vragen verdere uitwerking vóór de bouw. Numerieke sluitings- en vermogensbalanscontroles toetsen de interne consistentie van de berekening; experimentele validatie ontbreekt.

## Een nok die contact behoudt en wisselend koppel opvangt

De onafhankelijke [nokkenstudie](project/Nokken/) gebruikt een verticale rolvolger zonder excentriciteit. In een **cyclus van 0,500 s bij 120 rpm** stijgt de volger naar 10 mm, staat stil, stijgt naar 50 mm, keert terug en staat opnieuw stil. De gekozen bewegingswet van de vijfde graad is

$$
y(\tau)=10\tau^3-15\tau^4+6\tau^5,
\qquad s=s_0+h\,y(\tau).
$$

Hier loopt $\tau$ van 0 tot 1 over elk bewegingssegment en is $h$ de verplaatsingsverandering met teken. Verplaatsing, snelheid en versnelling sluiten continu aan op de stilstanden, hoewel de ruk eindige sprongen vertoont. De bewegingswet beïnvloedt daardoor zowel de traagheidskracht als haar frequentie-inhoud. Bij ongewijzigde geometrie neemt de versnelling toe met het kwadraat van het toerental.

![Berekende nokrotatie en beweging van de rolvolger](project/Nokken/figuren/nok_animatie.gif)

*De opgeslagen animatie toont twee omwentelingen van het berekende profiel. De afspeelsnelheid wijkt af van de fysieke bedrijfssnelheid.*

[Nok_1](project/Nokken/Nok_1_Kinematica_en_Geometrie.ipynb) vergelijkt bewegingswetten en gebruikt grenzen aan drukhoek en kromming om de geometrie te kiezen. Het opgeslagen geval gebruikt `R_0 = 50 mm` en een rolstraal van 15 mm. De drukhoeken lopen van ongeveer −23,8° tot +21,9°, binnen het gekozen criterium van ±30°. De geometriecontrole meldt geen ondersnijding.

[Nok_2](project/Nokken/Nok_2_Dynamica_Veer_Vliegwiel.ipynb) voegt een equivalente volgermassa van 29 kg, de voorgeschreven externe belasting, een veer van 25 kN/m, demping en geleidingswrijving toe. De berekende minimale veervoorspanning bedraagt **284,5 N**; de gekozen **300 N** houdt de berekende normaalkracht gedurende de hele cyclus positief. De grafiek splitst de krachtbijdragen op tegen de nokhoek. Contact moet behouden blijven, maar extra voorspanning verhoogt ook de belasting en wrijving.

![Bijdragen aan de berekende normaalkracht van de nok over één omwenteling](project/Nokken/figuren/normaalkracht.png)

De aandrijving moet een wisselend koppel leveren bij een constant voorgeschreven toerental. De opgeslagen berekening geeft een gemiddeld mechanisch vermogen van **110 W** en een gemiddeld koppel van **8,78 N·m**. De onderstaande curve van het cumulatieve arbeidsoverschot toont waar energie moet worden geleverd of gebufferd ten opzichte van het gemiddelde koppel. Het verschil tussen maximum en minimum bedraagt **65,9 J**: dit is de energiefluctuatie, niet het totale energieverbruik per cyclus.

![Cumulatief arbeidsoverschot van de nok met de energiefluctuatie voor de vliegwieldimensionering](project/Nokken/figuren/energiefluctuatie.png)

Voor een snelheidsfluctuatiecoëfficiënt $K=0.05$ volgt de vliegwielschatting

$$
I=\frac{\Delta E}{K\omega^2}=8.35\ \mathrm{kg\,m^2}.
$$

Dat aanzienlijke traagheidsmoment maakt een servoaandrijving een mogelijk alternatief. Geen van beide oplossingen is hier gebouwd of experimenteel getest. De volgerbeweging is in deze analyse voorgeschreven. De positieve normaalkracht toetst dus de haalbaarheid van contact binnen het model; de berekening simuleert geen contactverlies en botsingen.

## De berekeningen volgen

De belangrijkste route voor de overdekking is **Notebook 1 → Notebook 3 - Overdekking → Notebook 4**. De overdekkingsnotebook leest de kinematica rechtstreeks uit Notebook 1. De nokkennotebooks bevatten elk hun eigen instellingen; lees ze in genummerde volgorde om geometrie, krachten en animatie te volgen.

| Notebook | Rol in het ontwerp |
| --- | --- |
| [Stangen / Notebook 1](project/Stangen/Notebook%201.ipynb) | Stangengeometrie, bewegingswet, kinematica en conditionering |
| [Stangen / Notebook 3 - Overdekking](project/Stangen/Notebook%203%20-%20Overdekking.ipynb) | Overdekkingsbelastingen, veerondersteuning, constructiecontroles en 3D-beweging |
| [Stangen / Notebook 4](project/Stangen/Notebook%204.ipynb) | Aandrijving, motorklasse, rem, as en positionering |
| [Nokken / Nok_1](project/Nokken/Nok_1_Kinematica_en_Geometrie.ipynb) | Bewegingswet, nokprofiel, drukhoek en kromming |
| [Nokken / Nok_2](project/Nokken/Nok_2_Dynamica_Veer_Vliegwiel.ipynb) | Contactkracht, veervoorspanning, koppel en vliegwiel |
| [Nokken / Nok_3](project/Nokken/Nok_3_Animatie.ipynb) | Animatie van nok en volger |

De eerdere studie van één mechanisme loopt na Notebook 1 via [Notebook 2](project/Stangen/Notebook%202.ipynb), [Notebook 3](project/Stangen/Notebook%203.ipynb) en [Notebook 3 - Trekveren](project/Stangen/Notebook%203%20-%20Trekveren.ipynb). De [notebook voor parameteroptimalisatie](project/Stangen/Notebook%201%20parameter%20optimalisatie.ipynb) is een afzonderlijke verkennende geometriestudie.

```text
project/
  Stangen/                  Notebooks voor stangenmechanisme en overdekking
    resultaten/             Opgeslagen NPZ-berekeningen voor volgende notebooks
    figuren/                Geëxporteerde grafieken en animaties
    afbeeldingen/           Oorspronkelijke mechanisme- en aandrijfillustraties
  Nokken/                   Nokkennotebooks
    figuren/                Geëxporteerde grafieken en animatie
    afbeeldingen/           Oorspronkelijke nokillustratie
```

De notebooks bevatten de gedetailleerde afleidingen, modelparameters en opgeslagen uitvoer. De figuren en getallen hier volgen hun opgeslagen ontwerpgevallen.

## Het werk bekijken en reproduceren

Begin met de notebooks en hun opgeslagen uitvoer. Om ze uit te voeren, gebruik je een lokale Python 3-omgeving met NumPy, SciPy, Matplotlib, pandas en IPython. De interactieve widgetgrafieken vereisen ook `ipympl`. Exacte pakketversies en een minimale Python-versie zijn niet vastgelegd; NumPy moet `np.trapezoid` ondersteunen.

Voor een nieuwe omgeving op Windows:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install numpy scipy matplotlib pandas ipympl jupyterlab ipykernel
.\.venv\Scripts\python.exe -m jupyter lab
```

Selecteer deze omgeving als notebookkernel in JupyterLab of VS Code. De werkmap moet de map van de notebook zijn: `project/Stangen` of `project/Nokken`. Parameters staan in de instelcellen van de notebooks; de NPZ-bestanden onder `resultaten/` zijn berekende resultaten, geen experimentele invoer.

Voer voor de overdekking Notebook 1 van boven naar beneden uit, daarna Notebook 3 - Overdekking met `compute_spring_assist_case = True` en vervolgens Notebook 4 met `load_case = "overdekking_trekveren"`. Gebruik `load_case = "overdekking"` in Notebook 4 voor de overdekking zonder veren. Na een wijziging aan geometrie of beweging moet je de volgende notebooks opnieuw uitvoeren om hun invoerbestanden te verversen.

Voer voor het eerdere enkele mechanisme Notebooks 1, 2 en 3 uit, daarna indien nodig Notebook 3 - Trekveren. Notebook 4 selecteert die gevallen met `load_case = "baseline"` of `load_case = "trekveren"`. Houd hun resultaten gescheiden van de volledige overdekking. De nokkennotebooks werken onafhankelijk; gewijzigde parameters moeten handmatig in elke notebook worden overgenomen.

Uitvoering vernieuwt de notebookuitvoer en, voor de stangenstudie, de opgeslagen NPZ-resultaten. De README-figuren zijn statische exports en worden niet automatisch bijgewerkt. De zes grafieken komen uit de bestaande berekeningen en beide GIF's hergebruiken opgeslagen animatieframes. Voor deze documenten is geen mechanische analyse opnieuw uitgevoerd.
