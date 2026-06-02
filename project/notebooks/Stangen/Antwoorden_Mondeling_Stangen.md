# Antwoorden mondeling - Stangenmechanisme

Dit is het actuele antwoorddocument voor de Stangen-analyse. De notebooks zijn de numerieke bron en bevatten de figuren, controles en animaties. Dit document vertelt het ontwerpverhaal en verbindt de resultaten aan de tien vragen uit `vragen.pdf`.

## Actuele hoofdcase

De uiteindelijke toepassing is een brede zonwerende overdekking, niet meer alleen een losse parasol. Het basismodel blijft hetzelfde vlakke stangenmechanisme uit Notebook 1, maar in `Notebook 3 - Overdekking.ipynb` worden twee identieke mechanismen gebruikt om een voorbalk en doekvlak te dragen.

| Grootheid | Actuele waarde |
|---|---:|
| Breedte overdekking | `6.0 m` |
| Aantal mechanismen | `2` |
| Uitval/diepte uit kinematica | `2.34 m` |
| Voorbalk | aluminium koker `200x100x5 mm` |
| Voorbalkmassa | `46.98 kg` totaal |
| Doekmassa | `4.91 kg` totaal |
| Equivalente K-massa | `27.44 kg` per mechanisme |
| Bewegende modelmassa | `31.94 kg` per mechanisme |
| Schuivertraject bij openen | `s = 1.875 -> 0.125 m` |
| Effectieve beweegtijd | ongeveer `30 s` |
| Max schuiversnelheid | `0.2495 m/s` |
| Max schuiverversnelling | `0.0627 m/s^2` |
| Max `cond(A)` | `68.36` bij de gesloten stand |

De voorkeursvariant is de overdekking met trekveren. De veer verandert de baan niet, maar verlaagt de motorlast voor hetzelfde traject:

| Case | Max `|F_s|` openen | Max `|F_s|` sluiten | Ontwerplijnkracht totaal | Aanbevolen motorvermogen |
|---|---:|---:|---:|---:|
| Zonder trekveren | `381.95 N` per mechanisme | `313.46 N` per mechanisme | `1527.80 N` | `745 W` klasse |
| Met trekveren | `327.48 N` per mechanisme | `217.03 N` per mechanisme | `1309.93 N` | `487 W` klasse |

De trekveer is dus geen gratis energiebron. Bij openen geeft ze energie terug. Bij sluiten wordt ze opnieuw opgespannen, maar de zwaartekracht helpt dan mee doordat de constructie terug naar beneden beweegt. De motor/rem blijft nodig om de beweging te controleren en tussenstanden vast te houden.

Belangrijk praktisch punt: de riem draagt alleen de verticale aandrijfkracht. De lokale horizontale schuiverreactie is veel groter:

| Grootheid | Waarde |
|---|---:|
| Max lokale schuiverreactie `A_x` | `5.28 kN` per mechanisme |
| Max indicatief mastmoment `|A_x| s` | `660 Nm` |
| Verhouding `max |A_x| / max |F_s|` met trekveren | `16.1` |
| Indicatieve beugelkracht bij `1 m` beugelafstand | `662 N` per steunpunt |

Dit is een van de belangrijkste beperkingen van het ontwerp. De motor kan de verticale aandrijfkracht leveren, maar de schuivergeleiding, mast en muur-/framebevestiging moeten de grote dwarsreacties dragen.

## Welke notebookfiguren gebruik je?

| Vraag | Belangrijkste figuren en controles |
|---|---|
| 1 | Notebook 1 animatie, Notebook 3 Overdekking 3D-animatie, massa-opbouw, voorbalkcontrole |
| 2 | Notebook 4 motorbelasting, poelie/reductie, precisiebronnen, rem, kost/energie |
| 3 | Notebook 3/4 arbeids-surplus, trekveer open/sluit-vergelijking |
| 4 | Notebook 1 traject `s`, `ds`, `dds`, `cond(A)`, Notebook 4 tijdschaalanalyse |
| 5 | Notebook 4 spectrum van versnelling/aandrijfkracht en frequentiemarge met voorbalk |
| 6 | Notebook 1 conditionering en singulariteitscontrole |
| 7 | Notebook 2 onbalans, Notebook 3 framebelasting versus onbalans |
| 8 | Notebook 3 krachtdecompositie, wrijving, mastbelasting, Notebook 3 Overdekking balkcontrole |
| 9 | Notebook 3 Overdekking 3D-animatie, Notebook 4 parallelmechanismen, aandrijfschets en ascontrole |
| 10 | Notebook 4 tijdschaalanalyse en vergelijking zonder/met trekveren |

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
De huidige 6 m-overdekking gebruikt twee mechanismen en een `200x100x5 mm` voorbalk. De structurele weercontrole geeft als maatgevende case `wind uplift`, met maximale benutting ongeveer `0.79`. De maximale doorbuiging in de weerloadcases is ongeveer `15.83 mm`. Dat is acceptabel binnen de aannames, maar geen gecertificeerde bouwkundige berekening.

**Beperkingen.**  
Windvlagen, asymmetrische wind, regenwaterophoping, doekspanning, vermoeiing en de echte mast-/muurverankering zijn alleen eerste-orde meegenomen. Voor uitvoering moet de constructie apart structureel gecontroleerd worden.

## Vraag 2 - Hoe optimaliseer je voor precisie, energieverbruik en krachtgeneratie?

**Eindkeuze.**  
De aandrijving is een 24/48 V DC- of BLDC-reductiemotor met encoder en rem, die een gemeenschappelijke as aandrijft. Per schuiver loopt een lokale tandriem/kabel langs de mast. De riem levert alleen de verticale kracht; de schuivergeleiding draagt de dwarskracht.

**Proces.**  
Eerst leek een lange lineaire actuator of schroefspindel logisch, omdat die direct de schuiverpositie oplegt. Voor een brede overdekking is dat minder praktisch: lange slag, zichtbare actuator, moeilijke montage en minder geschikt om twee mechanismen synchroon te bedienen. Daarna zijn ketting en tandriem vergeleken. De tandriem is gekozen omdat die stiller is, minder onderhoud vraagt, properder is en minder speling heeft. Een ketting is robuust, maar lawaaieriger, zwaarder en vraagt smering.

**Precisie.**  
De encoderresolutie is niet de beperkende factor. Notebook 4 geeft een theoretische resolutie die veel fijner is dan nodig voor een zonwering. De echte foutbronnen zijn riemrek, speling in de reductor, schuivergeleiding, torsie van de gemeenschappelijke as en scheefstand tussen de twee mechanismen. Daarom zijn een voorgespannen riem, stijve schuiver/collar, eindschakelaars, homing en een rem belangrijker dan alleen een fijnere encoder.

**Energieverbruik.**  
Trager bewegen verlaagt piekvermogen en schokken, maar niet de zwaartekrachtarbeid. De massa moet dezelfde hoogte overwinnen. Energie optimaliseren doe je vooral via lagere wrijving, minder massa, goede lagers en eventueel trekveren. Voor de voorkeurscase met trekveren rekent Notebook 4 ongeveer `1114 J` elektrisch-equivalent per open+sluitcyclus en ongeveer `0.024 euro/jaar` bewegingsenergie bij de ingestelde gebruiksparameters.

**Krachtgeneratie.**  
Voor meer kracht kan je een kleinere poelie, grotere reductie, sterkere motor of bredere riem kiezen. Een kleinere poelie verlaagt het nodige koppel, maar vraagt meer toerental en buigt de riem sterker. In de huidige voorkeurscase wordt gekozen voor een ontwerplijnkracht van `1309.93 N` totaal en een ontwerp-uitgangskoppel van `40.31 Nm`.

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
De trekveren leveren in de huidige case ongeveer `48.65 N` omhoog in open stand en `208.24 N` omhoog in gesloten stand per mechanisme. De opgeslagen veerenergie tussen open en gesloten is ongeveer `224.78 J` per mechanisme. Daarmee daalt de aanbevolen motorvermogensklasse van ongeveer `745 W` naar `487 W`.

**Trade-off.**  
De trekveer verlaagt de openingslast en remvereiste, maar voegt onderdelen, montagepunten en afstelling toe. Ze maakt sluiten niet gratis: de motor moet de veer opnieuw opspannen, al helpt de zwaartekracht daarbij. We kiezen geen volledige compensatie, zodat het systeem controleerbaar blijft en de rem in alle standen zinvol blijft.

## Vraag 4 - Keuze van bewegings-traject: welke trade-offs heb je gemaakt, en waarom?

**Eindkeuze.**  
Het gekozen traject is `condition_scurve`: een gladde `smooth_4567`-envelope met extra vertraging waar de kinematische conditionering slechter is.

**Proces.**  
Een constante snelheid of trapeziumprofiel is eenvoudig, maar geeft hardere overgangen in versnelling. Een gewone smooth-curve is beter, maar houdt geen rekening met het feit dat het mechanisme in de gesloten stand gevoeliger is. Uit Notebook 1 blijkt dat `cond(A)` daar maximaal ongeveer `68.36` wordt. Daarom is gekozen voor een condition-afhankelijk profiel.

**Numerieke onderbouwing.**  
Bij openen gaat de schuiver van `s = 1.875 m` naar `s = 0.125 m`. De effectieve beweegtijd is ongeveer `30 s`, met `max |ds| = 0.2495 m/s` en `max |dds| = 0.0627 m/s^2`. De minimale snelheidsfactor is `0.25`, waardoor de beweging rustiger wordt in de gevoeligste zone.

**Trade-off.**  
Het nadeel is dat openen langer duurt. Het voordeel is minder schok, lager piekvermogen, lagere inertiekrachten en betere controle bij de gesloten stand. Notebook 4 toont dat sneller bewegen vooral het piekvermogen verhoogt. Voor de voorkeurscase met trekveren is bij tijdschaal `1.0` het piekvermogen ongeveer `105.56 W` totaal aan de schuivers; bij `0.60` keer de tijd stijgt dit naar ongeveer `173.18 W`, terwijl de positieve arbeid ongeveer `709 J` blijft.

**Beperking.**  
Het traject lost de zwaartekrachtarbeid niet op. Daarvoor zijn massa, wrijving en veerassistentie belangrijker. Het traject vermindert vooral dynamische effecten en piekvermogen.

## Vraag 5 - Hoe gebruik je de analyse van de frequentie-inhoud?

**Eindkeuze.**  
De frequentieanalyse wordt gebruikt als controle dat de gekozen beweging geen hoge-frequente excitatie veroorzaakt en ruim wegblijft van de geschatte structurele frequenties.

**Proces.**  
Eerst was de beweging vooral gekozen op basis van snelheid, versnelling en conditionering. Daarna is in Notebook 4 het spectrum van schuiverversnelling en aandrijfkracht toegevoegd. Dat geeft een objectieve controle of het traject past bij een rustige positioneerbeweging.

**Numerieke onderbouwing.**  
Voor de overdekking met trekveren ligt de dominante frequentie van de aandrijfkracht rond `0.029 Hz`. De volgende componenten liggen rond `0.059 Hz`, `0.117 Hz`, `0.147 Hz` en `0.176 Hz`. De eenvoudige eerste buigfrequentie-inschatting van de voorbalk is ongeveer `15.58 Hz`. De marge tussen dominante aandrijffrequentie en voorbalkreferentie is dus ongeveer `531x`.

**Interpretatie.**  
Voor de huidige trage beweging is resonantie met de voorbalk geen belangrijk risico. Als het ontwerp later in enkele seconden moet openen of sluiten, schuiven de frequenties omhoog en wordt dit veel belangrijker. Dan is een echte modale analyse van voorbalk, mast, schuivers en doek nodig.

**Beperking.**  
De huidige frequentiecheck is geen volledige modale analyse. Ze vergelijkt de aandrijfexcitaties met een eenvoudige balkfrequentie. Riemtrillingen, doekflapperen, masttrilling en windgusts zijn niet volledig gemodelleerd.

## Vraag 6 - Heeft het ontwerp dode punten of singulariteiten? Is dat erg?

**Eindkeuze.**  
Het huidige traject gaat niet door een echte singulariteit. Er is wel een minder gunstige zone bij de gesloten stand.

**Proces.**  
In Notebook 1 wordt de conditionering van het snelheidsstelsel gevolgd. Dat is belangrijk omdat een mechanisme dicht bij een singulariteit zeer gevoelig wordt: kleine actuatorbewegingen kunnen grote hoekveranderingen of grote krachten veroorzaken. De oude volledige slaggrenzen `s_open = 0.125 m` en `s_closed = 1.875 m` blijven behouden.

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
Voor het enkelvoudige basismodel is de inertiele aandrijfkracht ongeveer `3.17 N` en de onbalanskracht ongeveer `0.54 N`. Dat is klein. Voor de brede overdekking is de lokale schuiverreactie daarentegen ongeveer `5.28 kN` per mechanisme. Dat is geen onbalans in de zin van massa maal versnelling, maar wel een echte constructieve belasting.

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
Voor het enkelvoudige mechanisme is `max |F_s|` inertie-only ongeveer `3.17 N`, terwijl de totale kracht met zwaartekracht en wrijving ongeveer `76.06 N` wordt. Voor de overdekking zonder trekveren stijgt de piek naar `381.95 N` per mechanisme. Met trekveren daalt die naar `327.48 N`.

**Wrijving.**  
Wrijving verhoogt de aandrijfkracht en dissipatie, en ze wisselt van teken tussen openen en sluiten. Daarom is de open/sluit-analyse belangrijk. Wrijving mag niet gebruikt worden als betrouwbare vergrendeling, want slijtage, vuil en smering veranderen haar sterk.

**Stijfheid.**  
Stijfheid zit niet als elastisch element in de Newton-Euler-stelsels; de stangen worden daar star gemodelleerd. Toch is stijfheid praktisch cruciaal: voorbalkdoorbuiging, mastmoment, schuiverkanteling, riemrek en astorsie bepalen of de beweging precies en betrouwbaar blijft. De voorbalkcontrole geeft `wind uplift` als maatgevend met benutting `0.79`. De gemeenschappelijke ascontrole kiest in Notebook 4 een massieve stalen as van `35 mm`, met ongeveer `1.19 deg` verdraaiing en `4.79 MPa` torsiespanning in de trekveercase.

**Beperking.**  
Er is nog geen volledig elastisch meerlichamenmodel. Voor uitvoering moeten mastprofiel, geleiderollen, lagerkrachten, riemrek en bevestigingen apart gedimensioneerd worden.

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
| Ontwerplijnkracht totaal | `1309.93 N` |
| Ontwerp-uitgangskoppel | `40.31 Nm` |
| Aanbevolen motorvermogen | `487 W` klasse |
| Aanbevolen remkoppel | `16.29 Nm` |
| Gekozen asdiameter in controle | `35 mm` massief staal |
| Verdraaiing as | `1.19 deg` over 6 m |
| Torsiespanning as | `4.79 MPa` |

**Belangrijk onderscheid.**  
De gemeenschappelijke as en riem leveren de verticale aandrijfkracht. Ze lossen de lokale horizontale schuiverreactie niet op. Elke schuiver/collar moet nog altijd ongeveer `5.28 kN` dwarsreactie kunnen opnemen.

**Beperking.**  
De ascontrole is eerste orde. Er is nog geen detailontwerp van lagersteunen, koppelingen, riemschijven, toleranties, montage, corrosie of noodbediening.

## Vraag 10 - Verander een parameter, voorspel het effect, en bereken de numerieke analyse

**Parameterstudie 1: tijdschaal van het traject.**  
Voorspelling: als de geometrie en massa gelijk blijven, verandert de zwaartekrachtarbeid bijna niet. Piekvermogen stijgt wel als de beweging sneller wordt. Inertiekrachten stijgen ongeveer met `1/T^2`, maar blijven bij onze trage beweging beperkt.

Notebook 4 bevestigt dit voor de overdekking met trekveren:

| Tijdschaal | Actieve beweegtijd | Piek `|F_s|` totaal | Piekvermogen totaal | Positieve arbeid |
|---:|---:|---:|---:|---:|
| `0.60` sneller | `17.91 s` | `654.33 N` | `173.18 W` | `709.32 J` |
| `1.00` huidig | `29.85 s` | `654.96 N` | `105.56 W` | `709.32 J` |
| `2.00` trager | `59.70 s` | `655.24 N` | `53.13 W` | `709.32 J` |

De kracht verandert nauwelijks omdat zwaartekracht domineert. Het vermogen verandert sterk omdat `P = F v`.

**Parameterstudie 2: trekveren toevoegen.**  
Voorspelling: trekveren verlagen de openingskracht en remvereiste, maar de sluitbeweging moet de veer opnieuw opspannen. De open/sluit-analyse is dus nodig om te controleren of sluiten niet maatgevend wordt.

Resultaat: zonder trekveren is de ontwerplijnkracht `1527.80 N`; met trekveren daalt die naar `1309.93 N`. De aanbevolen motorvermogensklasse daalt van `745 W` naar `487 W`. Sluiten wordt niet maatgevend. Dat maakt trekveren voor deze brede overdekking verdedigbaar.

**Parameterstudie 3: voorbalkprofiel.**  
Voorspelling: een zwaarder en hoger profiel verhoogt massa, maar verlaagt doorbuiging en spanning sterk door het grotere traagheidsmoment. De huidige keuze `200x100x5` is gekozen omdat lichtere profielen structureel minder verdedigbaar waren voor 6 m met twee mechanismen.

**Beperking.**  
Deze parameterstudies zijn nuttig voor ontwerpkeuzes, maar geen vervanging voor een volledige productdimensionering.

## Vaste bijvraag - Hoeveel kost de motor en hoeveel kost het energieverbruik?

**Motor en aandrijving.**  
Voor de voorkeurscase is de orde van grootte:

| Component | Richtprijs |
|---|---:|
| Motor + reductor + rem/drive | `350-900 euro` |
| Riem/as/poelies/lagers | `250-700 euro` |
| Totaal aandrijfhardware | `600-1600 euro` |

Dit is een richtprijs, geen exacte offerte. De uiteindelijke kost hangt af van IP-bescherming, remtype, reductiekast, lagers, riemtype, montage, controller en veiligheidsvoorzieningen.

**Energieverbruik.**  
Notebook 4 geeft voor de voorkeurscase ongeveer `1114 J` elektrisch-equivalent per open+sluitcyclus. Bij `1` cyclus per dag en `220` dagen per jaar is dat ongeveer `0.068 kWh/jaar`. Met `0.35 euro/kWh` is de bewegingsenergie ongeveer `0.02 euro/jaar`.

De energiekost van de beweging is dus verwaarloosbaar tegenover componentkost, montage, onderhoud en eventuele stand-by-elektronica. Als een controller continu stand-by verbruikt, kan dat veel groter zijn dan de bewegingsenergie zelf.

## Belangrijkste beperkingen om eerlijk te vermelden

1. De stangen worden star gemodelleerd. Elastische vervorming van stangen, schuiver, mast en riem zit niet volledig in het dynamisch model.
2. De voorbalkcontrole is eerste orde en Eurocode-geinspireerd, maar geen gecertificeerde bouwkundige berekening.
3. Windvlagen, asymmetrische wind, regenwaterophoping, doekspanning en doekflapperen zijn beperkt gemodelleerd.
4. De schuivergeleiding en mast zijn nog niet als echte profielen met lagers/rollen doorgerekend, terwijl ze ongeveer `5.28 kN` lokale dwarsreactie moeten dragen.
5. De gemeenschappelijke ascontrole is een orde-groottecontrole. Detailontwerp van lagers, koppelingen en riemschijven blijft nodig.
6. Kosten zijn richtwaarden. Voor een definitieve motorselectie moeten actuele catalogusprijzen en datasheets gecontroleerd worden.

## Korte eindconclusie

De analyse is verdedigbaar als eerste ontwerpstudie. De notebooks tonen de kinematica, inverse dynamica, zwaartekracht/wrijving, trekveren, brede overdekking, structurele voorbalkcontrole, motor-/riemdimensionering, frequentie-inhoud en parallelle aandrijving. De belangrijkste technische boodschap is dat de motoropstelling realistisch is voor de verticale aandrijfkracht, maar dat de echte constructieve uitdaging in de schuivergeleiding, mastbevestiging, voorbalkstijfheid en synchronisatie zit.
