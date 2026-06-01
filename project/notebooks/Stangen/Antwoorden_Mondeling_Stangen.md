# Antwoorden mondeling - Stangenmechanisme

Dit document bevat de tekstuele antwoorden bij de mondelinge vragen. De notebooks blijven de bron voor de berekeningen, grafieken en numerieke controlewaarden. De uitleg hieronder verwijst naar die notebookresultaten, maar herhaalt niet alle code.

## Vraag 4 - Keuze van bewegings-traject

### Kort antwoord

Voor het traject van de schuiver kiezen we een `condition_scurve`. Dat is een glad bewegingsprofiel met een `smooth_4567`-envelope, maar met een extra snelheidsverlaging op plaatsen waar de kinematische conditionering slechter is. In ons mechanisme is dat vooral de gesloten beginstand, bij `s = 1.875 m`, waar `cond(A)` maximaal ongeveer `68.36` is. De schuiver beweegt daar bewust trager, met een minimale snelheidsfactor van `0.25`.

De belangrijkste keuze is dus: we nemen een langere, rustigere beweging in ruil voor meer controle, minder schokken, minder piekvermogen en minder gevoeligheid in de slechtst geconditioneerde stand. Voor een grote overdekking of paraplu is dat logisch: snel openen is minder belangrijk dan veilig, voorspelbaar en zonder harde belastingpieken openen.

### Wat hebben we overwogen?

Een constante snelheid of een trapeziumprofiel is eenvoudig, maar minder geschikt voor dit mechanisme. Bij een harde start, stop of overgang in versnelling ontstaan schokachtige krachtwisselingen. Die belasten de scharnieren, de schuivergeleiding, de riem/kabel en de mast onnodig. Voor een lichte machine kan dat soms aanvaardbaar zijn, maar voor een grote doekoverdekking met lange stangen wil je net vermijden dat het frame in beweging of trilling wordt getrokken.

Een standaard `smooth_345` of `smooth_4567` is al beter. Deze profielen starten en eindigen met nul snelheid en nul versnelling. Daardoor verdwijnen de harde sprongen in de beweging. `smooth_4567` is zachter aan de randen dan `smooth_345`, omdat ook de overgang van de versnelling rustiger is. Het nadeel is dat zo'n standaard smooth-profiel alleen naar de tijd kijkt. Het weet niet dat het mechanisme in sommige standen gevoeliger is dan in andere standen.

Daarom is de uiteindelijke keuze `condition_scurve`. Dat profiel vertrekt van een gladde `smooth_4567`-vorm, maar herverdeelt de tijd over de slag volgens `cond(A)`. Waar het mechanisme minder gunstig staat, krijgt dat deel van de slag meer tijd. Waar de overbrenging beter is, mag de schuiver sneller bewegen. Zo gebruiken we de kinematische analyse niet alleen achteraf als controle, maar rechtstreeks als ontwerpcriterium voor het traject.

### Waarom is de gesloten stand belangrijk?

De schuivercoordinaat `s` is positief naar beneden gemeten vanaf punt C. Openen betekent dus dat `s` daalt van `1.875 m` naar `0.125 m`. In de gesloten beginstand is `cond(A)` het hoogst. Dat betekent niet dat het mechanisme daar singulier is, maar wel dat kleine fouten in de schuiverpositie, speling of geometrie sterker kunnen doorwerken naar de stanghoeken.

Daarom is het logisch om net daar rustiger te bewegen. De motor en riem leveren dan niet alleen een kracht, maar volgen ook een beter controleerbaar traject. De keuze voor de `condition_scurve` is dus niet alleen comfort, maar ook een manier om de minst gunstige kinematische zone minder agressief te doorlopen.

### Belangrijkste trade-offs

De eerste trade-off is cyclustijd tegenover rust. De gewenste bewegingstijd bovenaan Notebook 1 staat op `14 s`, maar door de snelheids- en versnellingslimieten en de condition-aware vertraging wordt de effectieve openingstijd ongeveer `30 s`. Dat lijkt traag, maar voor een grote terrasoverdekking is dat verdedigbaar. De gebruiker verwacht geen snelle machinebeweging; belangrijker zijn veiligheid, weinig geluid, weinig schokken en controleerbare tussenstanden.

De tweede trade-off is piekvermogen tegenover totale energie. Trager bewegen verlaagt vooral de snelheid en dus het piekvermogen, want `P = F_s * ds`. De zwaartekrachtarbeid verdwijnt niet: dezelfde massa moet nog altijd over hetzelfde traject bewogen worden. Daardoor blijft de mechanische energie per slag grotendeels bepaald door gewicht, wrijving en slag, niet door de exacte bewegingstijd. Notebook 4 toont dit met de tijdschaalanalyse: wanneer het traject trager wordt, daalt het piekvermogen duidelijk, terwijl de positieve arbeid bijna gelijk blijft.

De derde trade-off is zachtheid tegenover piekwaarden in het midden van de slag. Een heel zacht profiel aan begin en einde moet, bij dezelfde totale bewegingstijd, meer snelheid in het midden van de slag nemen. Dat kan hogere pieksnelheden en piekversnellingen geven. Voor ons ontwerp is de inertiecomponent klein ten opzichte van zwaartekracht en wrijving, maar het principe blijft belangrijk: een bewegingswet moet niet alleen glad zijn aan de randen, maar ook realistische snelheden en versnellingen in het midden houden.

### Invloed van trekveren

De trekveren veranderen het gekozen bewegings-traject niet. De schuiver volgt nog altijd exact dezelfde `s(t)`, `ds(t)` en `dds(t)` uit Notebook 1. Wat wel verandert, is de kracht die de motor moet leveren om datzelfde traject te volgen.

Voor de overdekkingscase is de piek van de aandrijfkracht zonder trekveren ongeveer `381.95 N` per mechanisme. Met trekveren daalt die naar ongeveer `327.48 N` per mechanisme. De veer helpt dus de motor tijdens het openen, maar slaat tegelijk energie op. Bij sluiten moet die veerenergie opnieuw in de veer gebracht worden. Dat is geen gratis energie, maar een herverdeling: lagere openingspiek en lagere houdlast tegenover extra belasting tijdens sluiten.

Voor vraag 4 betekent dit: de trekveer is geen alternatief bewegingsprofiel. Ze is een hulpmiddel om dezelfde gekozen beweging met minder motorpiekbelasting uit te voeren.

### Actuele numerieke onderbouwing

| Grootheid | Waarde | Betekenis |
|---|---:|---|
| Schuivertraject bij openen | `1.875 -> 0.125 m` | Openen is omhoog, dus `s` daalt |
| Effectieve openingstijd | ongeveer `30 s` | Bewust rustige beweging |
| Max `|ds|` | `0.2495 m/s` | Blijft binnen de ingestelde snelheidslimiet |
| Max `|dds|` | `0.0627 m/s²` | Ruim onder de ingestelde versnellingslimiet |
| Max `cond(A)` | `68.36` | Hoogste gevoeligheid in gesloten stand |
| Minimale snelheidsfactor | `0.25` | Viermaal trager in de slechtst geconditioneerde zone |
| Overdekking, max `|F_s|` zonder trekveren | `381.95 N` per mechanisme | Motorbelasting voor hoofdcase |
| Overdekking, max `|F_s|` met trekveren | `327.48 N` per mechanisme | Zelfde traject, lagere openingsbelasting |

Uit Notebook 4 volgt ook dat sneller bewegen vooral het piekvermogen verhoogt. Voor de overdekking zonder trekveren geeft de tijdschaalanalyse ongeveer:

| Tijdsschaal | Actieve beweegtijd | Piekvermogen aan schuiver | Positieve arbeid |
|---:|---:|---:|---:|
| `0.60` sneller | `17.91 s` | `266.50 W` | `1158.89 J` |
| `1.00` huidig | `29.85 s` | `161.36 W` | `1158.89 J` |
| `1.50` trager | `44.78 s` | `107.88 W` | `1158.89 J` |
| `2.00` trager | `59.70 s` | `80.99 W` | `1158.89 J` |

Deze tabel toont de kern van de keuze: trager bewegen verlaagt de piekvermogensvraag, maar de totale positieve arbeid blijft praktisch gelijk.

### Welke notebookgrafieken gebruik je om dit te verdedigen?

In Notebook 1 gebruik je de figuur **Controle van het actuatortraject**. Die toont `s(t)`, `ds(t)`, `dds(t)` en de combinatie van `cond(A)` met de snelheidsfactor. Dit is de belangrijkste figuur voor vraag 4, omdat ze meteen toont dat de beweging glad is en dat de schuiver trager beweegt waar de conditionering slechter is.

Daarna gebruik je de twee conditioneringgrafieken: `cond(A)` als functie van tijd en als functie van `s`. Die tonen waarom de gesloten beginstand de kritische zone is.

Daarna gebruik je de snelheids- en versnellingsgrafieken van punt `K`. Die tonen dat de gekozen schuiverbeweging ook aan het uiteinde van het mechanisme rustig blijft. Dat is belangrijk, want het doek en de voorbalk volgen uiteindelijk de beweging van de K-punten.

In Notebook 4 gebruik je de grafiek **Effect van sneller/trager traject**. Die verdedigt de tijdskeuze: sneller bewegen vraagt duidelijk meer piekvermogen, terwijl de arbeid bijna gelijk blijft.

### Eindconclusie

We kiezen niet voor het snelste traject, maar voor het meest controleerbare traject. De `condition_scurve` combineert een gladde start en stop met een snelheidsverlaging in de gevoeligste configuratie. Dat past bij een grote overdekking: de beweging mag ongeveer 30 s duren, zolang ze veilig, stil, voorspelbaar en mechanisch rustig is. Sneller bewegen zou vooral de motor- en vermogenspiek vergroten, zonder de zwaartekrachtarbeid echt te verminderen.
