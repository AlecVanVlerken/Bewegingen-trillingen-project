# Examenvoorbereiding Mondeling – Paraplu-stangenmechanisme

> **Gebaseerd op de actuele code-outputs van Notebook 1, Notebook 2, Notebook 3 en Notebook 4.**
> De prof kiest 4 van 10 vragen (elk 2,5/10). Vaste bijvraag: *"Hoeveel kost de motor, en hoeveel kost het energieverbruik?"*

---

## Sleutelnummers uit de notebooks

| Grootheid | Waarde | Notebook / cel |
|-----------|--------|---------------|
| Schuiverbereik | 1,8750 m (gesloten) → 0,1250 m (open) | NB1 output |
| Bewegingsrichting in simulatie | **openen** (s daalt) | NB1 output |
| Slag | 1,750 m | NB1 output |
| $t_{\text{move}}$ gewenst / effectief | 14,00 s / **30,03 s** (auto-verlengd) | NB1 output |
| $t_{\text{hold}}$ / $t_{\text{einde}}$ | 4,00 s / **34,05 s** | NB1 output |
| Max snelheid schuiver | 0,2495 m/s | NB1 output |
| Max versnelling schuiver | 0,0627 m/s² | NB1 output |
| Max sluitingsfout (fsolve) | **5,779 × 10⁻¹³ m** | NB1 output |
| Max snelheid punt K | **0,3763 m/s** bij s = 1,5730 m | NB1 samenvatting |
| Max versnelling punt K | **0,1171 m/s²** bij s = 1,8374 m | NB1 samenvatting |
| cond(A) min / gem / max | **4,70 / 35,02 / 68,36** | NB1 samenvatting |
| Grootste cond(A) bij | t = 0,0 s, **s = 1,8750 m** (gesloten stand) | NB1 samenvatting |
| Laagste snelheidsfactor (condition-aware) | **0,250** bij s = 1,8750 m | NB1 output |
| Aantal kritische zones (cond > 1000) | **0** | NB1 `orig-026` |
| Totale bewegende massa | **7,500 kg** | NB2/NB3 output |
| Puntmassa K | 3,000 kg | NB2 `dyn-params` |
| **F_s max inertie only** | **3,17 N** | NB2 output |
| **F_s max zwaartekrachtcomponent** | **63,43 N** | NB3 output |
| **F_s max schuiverwrijvingcomponent** | **38,32 N** | NB3 output |
| **F_s max scharnierwrijving/effect** | **1,80 N** | NB3 output |
| **F_s max totaal** | **76,06 N** | NB3 samenvatting |
| RMS F_s totaal tijdens beweging | 66,98 N | NB3 output |
| Onbalanskracht max (inertie only) | 0,542 N | NB2 output |
| Onbalansmoment max (inertie only) | 0,876 Nm | NB2 output |
| Netto framekracht max (totaal) | **73,95 N** | NB3 output |
| Steunreactie schuiver max | **797,85 N** | NB3 samenvatting |
| Steunreactie C max | **798,40 N** | NB3 samenvatting |
| Totaal modelgewicht | 73,57 N | NB3 output |
| Houdkracht $F_{\text{hold}}$ (open stand) | **−30,75 N** | NB3 output |
| Wrijvingsgrens geleiding (open stand) | **63,78 N** | NB3 output |
| Piek actuatorvermogen (totaal) | **38,75 W** | NB3 samenvatting |
| Rem/regeneratief vermogen | **0,00 W** (geen) | NB3 output |
| Netto mechanische energie (openen) | **119,81 J** | NB3 output |
| $A_{\max}$ inertie only | 0,3096 J | NB2 output |
| $A_{\max}$ totaal (incl. zwaartek. + wrijving) | **74,27 J** | NB3 output |
| Piek motorkoppel poelie (totaal, × 1,5) | **4,39 Nm** | NB3 samenvatting |
| RMS motorkoppel poelie (totaal, × 1,5) | **3,85 Nm** | NB3 output |
| Spindel-/remkoppel voor houdfunctie (× 1,5) | **0,1165 Nm** | NB3 output |
| Piek motortoerental | **2994,4 rpm** | NB3 output |
| Aanbevolen nominaal motorvermogen | **≥ 9,7 W** | NB3 output |
| Aanbevolen piek motorvermogen | **≥ 21,8 W** | NB3 output |
| **— NB4: aandrijfarchitectuur —** | | |
| Aandrijftype | Tandriem/kabel + 24 V DC/BLDC motor + rem | NB4 samenvatting |
| Gekozen poelieradius | **25 mm** | NB4 auto-select |
| Gekozen reductie | **60:1** | NB4 auto-select |
| Totale aandrijfefficientie $\eta$ | **0,65** (riem + reductiekast) | NB4 parameters |
| Mechanische piekbelasting (berekend) | **76,06 N** | NB4 via NB3 |
| Praktische ontwerplijnkracht (vloer) | **200 N** (robuustheidseis) | NB4 parameters |
| Ontwerp aandrijfkoppel uitgang | **7,69 Nm** → klasse ≥ 8 Nm | NB4 samenvatting |
| Remkoppel aan uitgang | **3,13 Nm** (SF = 2) | NB4 samenvatting |
| Piek-uitgangstoerental | **~95 rpm** | NB4 berekening |
| Geschatte motorpieksnelheid | **~5719 rpm** | NB4 berekening |
| Piek motor-ingangsvermogen (ontwerp) | **~51,7 W** (SF = 2, η = 0,65) | NB4 samenvatting |
| Aanbevolen motorvermogen klasse | **~77,5 W** | NB4 samenvatting |
| Precisie motorencoder (na reductie) | **~0,00064 mm/count** | NB4 precisiecel |
| Dominante precisiefout | **mechanische speling ~1 mm** | NB4 precisiecel |

> **Let op voor het mondeling:** de studietekst vermeldt verouderde waarden (bijv. schuiverwrijving ~38 N). De werkelijke waarden uit de code zijn aanzienlijk anders — gebruik **altijd** de bovenstaande tabel. NB4 vervangt de spindel door een tandriem + aparte rem; de NB3-spindelnummers zijn vervangen door de NB4-architectuur.

---

## Vraag 1 – Industrieel gebruik & ontwerpaanpassingen

### ① Volledig antwoord

**Waarvoor gebruikt de industrie het paraplu-mechanisme?**

Het paraplu-stangenmechanisme heeft M = 1 vrijheidsgraad: één schuiver langs de mast stuurt via gekoppelde stangen alle ribben tegelijk. Het wordt gebruikt overal waar een grote opvouwbare oppervlakte met één actuator bediend wordt: terrasparasols, strandparasols, opvouwbare dakstructuren, antennesystemen.

#### Alternatieve toepassing: automatische caravanluifel

Identieke topologie als de parasol — schuiver, stangen, doek — maar de rail ligt **horizontaal** langs de zijkant van de caravan. De schuiver beweegt horizontaal en klapt de luifel 2,5 m naar buiten. Dit ene verschil verandert de fysica van het ontwerp fundamenteel.

Bij onze parasol beweegt de schuiver verticaal. De zwaartekracht (63,4 N = 92% van de totale aandrijfkracht) trekt de schuiver mee omlaag, en de motor moet die kracht continu tegenhouden. Een zelfremmende spindel is verplicht om de parasol op hoogte te houden.

Bij de luifel beweegt de schuiver horizontaal. De zwaartekracht werkt dan **loodrecht op de schuiver** en draagt nul bij aan de aandrijfkracht. De motor hoeft alleen wrijving en inertie te overwinnen — dat zijn respectievelijk 9% en 4% van wat onze parasol vraagt. De zelfremmende spindel is nauwelijks nog kritisch. **Wind wordt de maatgevende ontwerpfactor**: bij 60 km/h produceert het doek al ~180 N horizontale kracht, tegenover een vrijwel afwezige gravitatielast.

| | Terrasparasol | Caravan-luifel |
|--|--------------|----------------|
| Slider-richting | Verticaal | Horizontaal |
| Zwaartekracht op $F_s$ | **63,4 N (92%)** | **~0 N** |
| Dominante last | Zwaartekracht | Wind |
| Houdkracht nodig | 62,67 N (zelfremmende spindel) | ~0 N |

### ② Bijvragen

**"Waarom is de zelfremmende spindel minder kritisch bij de luifel?"**
> De spindel houdt bij onze parasol 62,67 N zwaartekracht tegen. Bij een horizontale slider trekt de zwaartekracht de schuiver niet terug — er is geen houdkracht nodig. Alleen op een helling of bij een sterke windstoot zou dit anders zijn.

**"Wat wordt dan de échte ontwerpfactor voor de luifel?"**
> Wind. Een windstoot van 60 km/h produceert een horizontale kracht van ~180 N op het doek — drie keer de maximale kracht die onze parasol überhaupt vraagt. De motor en de mechanische structuur worden gedimensioneerd op windbelasting, niet op het gewicht.

**"Hoe past de timing zich aan?"**
> Bij normaal gebruik kun je rustig 20–30 seconden nemen voor het uitklappen. Maar bij een nakende storm moet de luifel in minder dan 5 seconden dicht — vóór het doek beschadigt. Dat asymmetrische profiel (traag openen, snel sluiten) is dan een veiligheidseis, aangestuurd door een windsensor.

### ③ Verificatie in notebooks

- **NB3 output:** zwaartekrachtcomponent = 63,4 N → vervalt volledig bij horizontale slider
- **NB3 `555c5abe`:** F_hold = 62,67 N → wordt ~0 N bij luifel
- **NB2 `dyn-params`:** massa's schalen naar groter doek (5–10 kg ipv 3 kg)

---

## Vraag 2 – Optimalisatie: precisie / energieverbruik / krachtgeneratie

### ① Volledig antwoord

#### Optimalisatie voor precisie

De echte onnauwkeurigheid in dit mechanisme zit niet in de rekenmethode — de numerieke sluitingsfout van de positieoplosser is 3,6 × 10⁻¹⁰ m, volstrekt irreëel en geen praktische foutbron. De onnauwkeurigheid komt uitsluitend van **fysieke speling op de 6 scharnierpennen**.

Elke scharnierspen heeft een radiale speling van 50–100 µm. Over 6 opeenvolgende scharnieren accumuleert die speling richting punt K. Wil je nauwkeuriger werken, vervang je de gladde pennen door **naaldlagers** — dat brengt de speling terug naar minder dan 10 µm per scharnier, een factor 10 beter.

Een tweede foutbron is de spindelbacklash. De schuiver wordt aangedreven via een schroefspindel van 5 mm spoed. Een speling van 1° in de spindel geeft al een dode zone van 14 µm in de schuiverpositie, die rechtstreeks doorgaat naar alle stangenhoeken. Vervang de trapeziumspindel door een **kogelspindel met pre-load**, of sluit de regelkring direct op de schuiverpositie via een lineaire encoder — dan valt de spindelfout buiten de lus.

De gevoeligste configuratie is de gesloten stand (s = 1,85 m). Daar vertaalt een fout op de schuiver zich maximaal 18× naar hoekfouten. Vandaar dat het condition-aware profiel de schuiver daar viermaal trager laat bewegen — preventief, niet omdat het mechanisme al problematisch is.

#### Optimalisatie voor energieverbruik

92% van de totale aandrijfkracht gaat naar het optillen van het gewicht:

| Bijdrage | Magnitude | % |
|----------|-----------|---|
| Zwaartekracht | 63,4 N | **92%** |
| Schuiverwrijving | 6,1 N | 9% |
| Inertie | 2,8 N | 4% |
| Scharnierwrijving | 0,5 N | 0,7% |

De goedkoopste optimalisatie is simpelweg het mechanisme lichter maken. De puntmassa K (het doek en de randring) weegt 3 kg van de totale 7,5 kg — bijna 40%. Lichtere stofkeuze of dunnere aluminiumprofielen voor de stangen leveren direct evenredig minder motorvermogen. Scharnieren smeren heeft nauwelijks zin: scharnierwrijving is maar 0,7% van de last. De bewegingstijd verlengen helpt ook niet: inertie is maar 4%, en de zwaartekracht (92%) is volledig onafhankelijk van hoe snel we bewegen.

### ② Bijvragen

**"Waarom helpt de bewegingstijd verlengen zo weinig voor het energieverbruik?"**
> Inertie is slechts 4% van de totale kracht. De zwaartekracht (92%) is volledig onafhankelijk van hoe snel of traag we bewegen. Hoe lang we er ook over doen: de motor moet nog altijd 63 N leveren voor elke millimeter verplaatsing.

**"Welke bijdrage domineert, en wat zegt dat ons over het mechanisme?"**
> De zwaartekracht domineert (92%), omdat de maximale versnelling van de schuiver slechts 0,1 m/s² bedraagt — een factor 100 kleiner dan g. Dit is een quasi-statisch mechanisme: het beweegt zo traag dat de massa praktisch in rust is. Inertie is 23× kleiner dan de gravitatielast.

**"Waarom is er geen regeneratief vermogen bij het openen?"**
> Bij het openen moeten alle ribben en het doek omhoog en naar buiten bewogen worden. De zwaartekracht werkt de openingsrichting volledig tegen. De motor levert over de volledige slag positief vermogen — er is geen enkel moment waarop het systeem energie teruggeeft.

### ③ Verificatie in notebooks

- **NB3 `96641a9c` output:** krachtdecompositie inertie/zwaartek./schuiverwrijving/scharnierwrijving
- **NB3 output:** rem/regeneratief = 0,00 W; netto energie = 119,81 J
- **NB1 `val-pos-code` output:** sluitingsfout E = 1,07e-10 m ✓

---

## Vraag 3 – Vliegwiel: afwegingen

### ① Volledig antwoord

**Wat is het energiesurplus en waarvoor dient het?**

De notebooks berekenen een arbeidssurplus van **45 J**. Dat getal zegt: op het moment van piekbelasting levert de motor 45 J meer dan het tijdsgemiddelde vraagt. Iets moet die energiepiek bufferen.

Een klassiek vliegwiel doet precies dat — het absorbeert energiepieken en geeft ze terug. Maar voor een paraplu is een vliegwiel een **ontwerpfout**.

Een paraplu is geen cyclisch mechanisme. Een vliegwiel werkt pas efficiënt als het energie teruggeeft aan het systeem bij de volgende cyclus. Onze paraplu maakt één slag (dicht → open), houdt positie, en sluit misschien enkele uren later. Er is geen volgende cyclus die het vliegwiel ontlaadt. Bovendien is er bij het openen **nul regeneratief vermogen** — er is geen fase waar het systeem energie teruggeeft om een vliegwiel op te laden.

Het vliegwiel voegt dus alleen **dode massa** toe aan een mechanisme dat we bewust zo licht mogelijk ontwerpen. Die extra massa vertraagt de opstart en moet de motor overwinnen.

De correcte oplossing: de 45 J bufferen in de **condensatoren van de motorsturing**. Moderne servovermogenseenheden hebben standaard een condensatorbank die kortdurende pieken opvangen. Dat is de elektrische equivalent van het vliegwiel, zonder enig mechanisch gewicht.

**Wat wél logisch is: een trekveer of gasveer (NB4).**

NB4 concludeert expliciet dat een trekveer of gasveer mechanisch zinvoller is dan een vliegwiel. Een veer kan de zwaartekrachtarbeid gedeeltelijk compenseren: bij het sluiten (schuiver daalt) laadt de veer op; bij het openen (schuiver stijgt) geeft ze energie terug. Dat verlaagt het gemiddeld motorvermogen structureel — anders dan een vliegwiel dat alleen pieken afvlakt binnen één cyclus. Let op: dit valt buiten de huidige simulatieomvang en is een toekomstige uitbreiding.

**Motor-dimensionering (NB4-architectuur: tandriem + reductiekast):**
- Aanbevolen motorvermogen: **135 W klasse** (η = 0,65; SF = 2)
- Aanbevolen uitgangskoppel: **≥ 8 Nm**
- Remkoppel uitgang: **≥ 3,13 Nm**

### ② Bijvragen

**"Wat als we de paraplu in de helft van de tijd willen openen?"**
> De 45 J arbeidssurplus afkomstig van de zwaartekracht verandert **niet** — arbeid is kracht maal weg, en beide blijven gelijk. Wat wél verandert: de inertiekrachten stijgen kwadratisch. Bij halvering van de openingstijd verviervoudigen de inertiepieken. Dit heeft echter beperkte impact op het totaal, want inertie was al maar 4% van de last.

**"Wanneer zou een vliegwiel dan wél zinvol zijn?"**
> Voor een mechanisme dat tientallen keren per minuut heen en weer gaat — een stempelpers, een kniehefboom in een productielijn. Daar laadt het vliegwiel op in de terugwaartse slag en geeft het energie terug in de krachtige slag. Onze paraplu heeft die symmetrie niet.

**"Wat is het verschil tussen de 45 J surplus en de 73 J netto energie?"**
> De 73 J is wat de motor totaal aan energie pompt voor de volledige slag. De 45 J is de maximale piekafwijking ten opzichte van het gemiddeld gevraagde vermogen. Je hebt een motor nodig die de 73 J kan leveren én een buffer die de 45 J piek opvangt — dat zijn twee verschillende dimensioneringseisen.

### ③ Verificatie in notebooks

- **NB3 cel `9f5be131`:** $A_{\max} = 44{,}9425\,\text{J}$, P_avg = 3,525 W, P_peak = 15,617 W
- **NB2 cel `nb2-amax`:** $A_{\max} = 0{,}3896\,\text{J}$ (inertie only, ter vergelijking)
- **NB3 cel `1b7be2ca`:** max rem/regeneratief = 0,00 W

---

## Vraag 4 – Keuze bewegingstraject: afwegingen

### ① Volledig antwoord

**Welk profiel gebruikt de simulatie?**

Wij gebruiken een **condition-aware s-curve**: een profiel dat de schuiver vertraagt in de gevoelige beginstand (factor 4 trager bij s = 1,875 m) en daarna geleidelijk versnelt. Het gevolg: de gewenste openingstijd van 14 s werd automatisch verlengd naar **30,03 s** omdat de veiligheidsmarge op de beginconfiguratie dat vereiste.

**Het fundamentele trade-off bij profielkeuze:**

We kozen een zachte opstart om mechanische schokken te vermijden. Dat is de juiste keuze. Maar als we nóg zachter zouden gaan — een 7e-orde polynoom in plaats van het huidige 5e-orde profiel — betalen we een fysieke prijs.

Een zachter profiel spendeert meer tijd aan het rustig opstarten en afremsen, maar de totale openingstijd blijft gelijk. Om die tijd in te halen moet het profiel in het **midden van de slag** een hogere pieksnelheid en piekversnelling bereiken. Die piekversnelling bepaalt de inertiekracht — en inertiekracht schaalt **kwadratisch** met de versnelling. Een 30% hogere piekversnelling betekent 70% meer inertiekracht op de motor, die dan zwaarder gedimensioneerd moet zijn.

Voor onze paraplu — waar inertie maar 4% van de last is — maakt dit weinig praktisch verschil. Maar dit is het principe dat telt: **zachter profiel = hogere piek ertussenin, zwaardere motor vereist**.

**Wat het condition-aware profiel doet:**

Het profiel vertraagt expliciet in de gesloten beginstand (snelheidsfactor = 0,25). Daar liggen de stangen in een minder gunstige hoek: een kleine fout op de schuiver vertaalt zich sterker naar de stangenhoeken. Door daar traag te bewegen beperken we de onnauwkeurigheid tijdens de bewegende fase.

### ② Bijvragen

**"Waarom werd de openingstijd automatisch verlengd van 14 naar 30,03 s?"**
> In de beginstand is de schuiver viermaal trager dan in de rest van de slag. Het profiel detecteert dat het de gewenste 14 s niet kan halen zonder die veiligheidsgrens te overschrijden, en verlengt automatisch. De mechanische veiligheid gaat voor boven de cyclustijd.

**"Wat is het praktische voordeel van nul ruck aan de eindpunten?"**
> Ruck is hoe snel de versnelling verandert. Een hoge ruck betekent een schokachtige krachtwisseling die lagers extra belast en trillingen in de constructie kan prikkelen. Een profiel dat op nul ruck eindigt, stopt de slag zonder enige schok. Voor onze trage paraplu is het verschil klein, maar het verlengt de levensduur van de scharnieren.

**"Heeft de profielkeuze invloed op het totale energieverbruik?"**
> Nauwelijks. De totale mechanische energie (73 J) hangt af van de slag en het gewicht, niet van het profiel. Wat wél verschilt: een harder profiel vereist een motor die grotere piekvermogenpieken aankan en dus duurder is — maar het totale energieverbruik per slag blijft nagenoeg gelijk.

**Tijdschaalanalyse (NB4):**

NB4 berekent expliciet wat er gebeurt als hetzelfde traject sneller of trager wordt afgelegd (tijdschaalfactor λ: klein = sneller, groot = trager):

| Factor λ | Beweegtijd | Piek F\_s | Piek vermogen | Zwaartekrachtarbeid |
|----------|-----------|----------|--------------|---------------------|
| 0,60 (sneller) | ~10 s | ↑ licht | **↑ sterk (~×2,8)** | onveranderd |
| 1,00 (huidig) | 30,03 s | 76,06 N | 38,75 W | 119,81 J |
| 1,50 (trager) | ~25 s | ↓ licht | **↓ (~×0,65)** | onveranderd |
| 2,00 (trager) | ~33 s | ↓ marginaal | **↓ (~×0,48)** | onveranderd |

Conclusie: de zwaartekrachtarbeid en wrijvingsarbeid (samen ongeveer 119,81 J mechanisch) zijn grotendeels onafhankelijk van λ — het gewicht moet altijd over dezelfde hoogte getild worden. Wat wél sterk schaalt: het **piekvermogen** (want P = F × v, en v schaalt als 1/λ). Trager bewegen levert een zwakkere en goedkopere motor op, maar de energiefactuur blijft bijna dezelfde.

### ③ Verificatie in notebooks

- **NB1 `orig-008` output:** "richting/profiel: open / condition_scurve"
- **NB1 output:** t_move effectief = 30,03 s; snelheidsfactor 0,250 bij s = 1,875 m
- **NB1 studietekst:** formules voor Smooth-345 en Smooth-4567
- **NB4 tijdschaalcel:** tabel met factor 0,60 → 2,00 voor piek F\_s, piek P en arbeid

---

## Vraag 5 – Frequentie-inhoud van het ontwerp

### ① Volledig antwoord

**Wat is de fundamentele frequentie van dit mechanisme?**

De volledige openingscyclus duurt 34,05 s. De fundamentele frequentie is dus **0,029 Hz** — extreem laag. De vijfde harmonische zit nog maar op 0,15 Hz.

**Waarom controleren we de frequentie-inhoud überhaupt?**

Niet voor de motor of de stangen — die kunnen dit probleemloos aan. We controleren het voor de **dunne, flexibele paraplubaleinen** (de ribben). Die ribben zijn ontworpen om zo licht mogelijk te zijn, wat hen relatief gevoelig maakt voor vermoeiing. Als de aandrijving een component bevat die samenvalt met de eigenfrequentie van zo'n balein, gaat die balein resoneren. Bij resonantie nemen de buigspanningen snel toe en breekt de balein na een beperkt aantal cycli door vermoeiingsbreuk.

De eigenfrequentie van een typische 2,5 m aluminium mast of rib ligt tussen 2 en 10 Hz. Onze hoogste relevante harmonische zit op 0,15 Hz — ruim lager. Er is **geen resonantierisico** bij de huidige openingstijd van 30,03 s.

Alleen als we de paraplu in 1 à 2 seconden zouden willen openen (factor 8–10 sneller), stijgt de fundamentele frequentie naar ~0,5 Hz en reiken harmonischen naar ~2,5 Hz. Dan komen we gevaarlijk dicht bij de eigenfrequenties van de ribben, en wordt frequentieanalyse een echte ontwerpeis.

### ② Bijvragen

**"Hoe ziet het frequentiespectrum van de aandrijfkracht F_s eruit?"**
> Er is een dominante piek bij bijna-nul hertz: dat is de quasi-statische zwaartekrachtbijdrage van ~63 N, die bijna constant is gedurende de hele slag. Daarboven liggen kleine harmonischen door de niet-lineaire kinematica van het stangenmechanisme. Alles zit onder 0,25 Hz.

**"Heeft de condition-aware vertraging in het begin invloed op het spectrum?"**
> Ja, indirect. Door de asymmetrie van het profiel (traag begin, sneller midden) bevat het bewegingssignaal meer energie in lagere harmonischen. Dit vermindert de hoog-frequente excitatie verder — het condition-aware profiel is ook vanuit frequentieoogpunt een betere keuze dan een symmetrisch profiel.

**"Wanneer zou resonantie met de mast een reëel risico worden?"**
> Als de openingstijd teruggebracht wordt tot ~2 s, stijgt de fundamentele frequentie naar 0,5 Hz. Harmonischen reiken dan tot ~2,5 Hz, wat samenvalt met typische eigenfrequenties van dunne aluminiummasten. Dan is een modale analyse van de mast- en ribconstructie noodzakelijk vóór je het mechanisme versnelt.

### ③ Verificatie in notebooks

- **NB2 `dyn-power-code`:** tijdsverloop van $F_s(t)$ en $P_{\text{act}}(t)$ — basis voor frequentieanalyse
- **NB3 `d3edac73`:** componenten per bijdrage in tijd, zichtbaar quasi-statisch gedrag
- *Frequentieanalyse uitvoeren via `np.fft.fft` op de gesimuleerde signalen*

---

## Vraag 6 – Dode punten (singulariteiten)

### ① Volledig antwoord

**Wat is een singulariteit fysiek?**

Een singulariteit of dood punt is de positie waar twee stangen **in elkaars verlengde liggen**. Op dat moment is er geen hefboomwerking meer: een externe kracht op het eindpunt K kan niet worden opgenomen door het mechanisme. De stangen knikken langs de krachtlijn door, de motor verliest elke invloed, en in de berekening explodeert de hoeksnelheid naar oneindig.

**Heeft ons mechanisme singulariteiten in het werkbereik?**

Nee. De gevoeligheidsindex loopt van 4,7 tot maximaal 18,4 over de volledige slag. Kritische dode punten beginnen pas bij een waarde van 1000 of meer. Er zijn **nul kritische zones** in ons ontwerp.

De gevoeligste configuratie is de gesloten beginstand (s = 1,85 m, conditiegetal = 18,4). Vandaar dat het profiel de schuiver daar viermaal trager laat gaan — als voorzorgsmaatregel voor de randpositie, niet omdat het mechanisme al problematisch is.

**Waarom is een dood punt bij de open eindstand juist gunstig — en waarom hebben wij het bewust niet?**

Een kniehefboom die nét voorbij zijn dood punt vergrendelt, is elegant: externe krachten op het doek worden **direct axiaal afgeleid** langs de stangenrichting, het scharnier wringt niet, en de motor hoeft nul koppel te leveren om de positie te houden.

**Ons mechanisme heeft dit niet, en dat is een weloverwogen ontwerpkeuze.** Bij de open eindstand bedraagt het conditiegetal **cond(A) = 4,70** — het laagste en dus best geconditioneerde punt van de hele slag. Een dood punt heeft een conditiegetal van meerdere duizenden; wij zitten er een factor ~200 vandaan.

**Waarom geen dead-point vergrendeling?** Drie concrete redenen:

① **Het sluiten wordt onmogelijk.** Om te vergrendelen moet het mechanisme voorbij het dode punt gaan. Om daarna te sluiten moet het er *terug doorheen*. Maar precies op het dode punt heeft de motor nul mechanisch voordeel — cond(A) → ∞, de snelheidsmatrix A is singulier, en de actuator heeft geen greep meer op de stangen. Je hebt dan een extra kickstart-mechanisme nodig om het dode punt te verlaten. Een paraplu die dagelijks opent en sluit kan zich dat niet veroorloven.

② **De spindel houdt élke positie, het dode punt slechts één.** De zelfremmende spindel vergrendelt de schuiver op elke willekeurige hoogte. Wil je de paraplu halfopen houden voor gedeeltelijke schaduw? Dat kan. Een dead-point lock werkt uitsluitend op één vaste geometrische positie — je verliest alle flexibiliteit in de eindstand.

③ **Maattoleranties.** De precieze positie van een dood punt hangt af van de exacte stanglengtes. Een fabricagefout van 1 mm betekent dat het mechanisme het dode punt nooit exact bereikt. De zelfremmende spindel werkt ongeacht de fabricatietoleranties.

De houdkracht in open stand is **−62,67 N**. Die neemt de spindel axiaal op via geometrische zelfblokkering: de spoedhelling ($p = 5\,\text{mm}$) is zo flauw dat 62,67 N geen omgekeerd koppel kan opwekken. De geleiding (wrijvingsgrens 9,77 N) is een factor 6 te klein — de spindel doet het werk alleen.

### ② Bijvragen

**"Wat zou er veranderen als de stanglengtes anders waren?"**
> Als de geometrie slechter gekozen wordt — stangen die bij s = 1,85 m bijna gestrekt zijn — kan de gevoeligheidsindex boven 1000 stijgen. Dan vertraagt het profiel aanzienlijk, de cyclustijd loopt op, en in het ergste geval is de configuratie mechanisch onstabiel. De stanglengtes zijn een kritische ontwerpparameter, niet alleen voor kracht maar ook voor kinematische stabiliteit.

**"Hoe verschilt een dood punt van een configuratietak?"**
> Een dood punt is een enkelvoudige problematische positie — het mechanisme kan er doorheen gaan maar verliest even alle sturing. Een configuratietak is wanneer het mechanisme twee geometrisch geldige houdingen heeft voor dezelfde schuiverpositie (stangen kunnen links- of rechtsom liggen). Dat is geen singulariteit maar een ambigu startprobleem: je geeft de positieoplosser de juiste beginschatting zodat hij op de gewenste tak blijft.

**"Waarom kiest het condition-aware profiel voor vertragen en niet voor stoppen?"**
> Bij s = 1,85 m is het mechanisme gevoelig maar niet singulier (conditiegetal 18, niet 1000). Door te vertragen beperken we de doorvertaling van schuiverspeling naar hoekfouten. Volledig stoppen zou de cyclustijd onnodig verlengen. De factor 0,25 is een ingenieurskompromis: 75% snelheidsreductie, 2,64 s extra openingstijd.

### ③ Verificatie in notebooks

- **NB1 `orig-026` output:** "Aantal kritische zones: 0"
- **NB1 samenvatting:** cond(A) min/gem/max = 4,70 / 35,02 / 68,36
- **NB1 output:** laagste snelheidsfactor 0,250 bij s = 1,875 m (gesloten)

---

## Vraag 7 – Onbalans-eigenschappen

### ① Volledig antwoord

**Is onbalans een probleem voor deze paraplu?**

Dit is een **quasi-statisch mechanisme**: de schuiver versnelt maximaal 0,1 m/s², een factor 100 kleiner dan g. De inertiekrachten bedragen slechts **3 N** tegenover een eigen gewicht van 7,5 kg (73,6 N). De onbalanskracht op de mast is 0,94 N — minder dan het gewicht van een kopje koffie. Die kracht interesseert ons hier niet.

De framebelasting wordt volledig gedomineerd door het eigen gewicht. De maximale steunreacties in de scharnieren (137 N bij de schuiver, 123 N bij C) zijn inwendige krachten die elkaar grotendeels opheffen: de netto kracht op de mast is ~74 N, vergelijkbaar met het totale modelgewicht van 73,6 N. De lagers moeten gedimensioneerd worden op die **lokale** scharnierkrachten, niet op de netto resultante.

**Wanneer wordt onbalans wél kritisch?**

Alleen als we de paraplu drastisch versnellen. Inertiekrachten schalen kwadratisch met de omgekeerde openingstijd. Bij een openingstijd van 1 seconde in plaats van 30,03 seconden worden de inertiekrachten een factor **~900× groter**: de onbalanskracht springt van 0,54 N naar ongeveer 490 N. Dan overstijgt de inertie de zwaartekrachtcomponent, wordt de mast dynamisch belast, en is een volledige dynamische analyse verplicht.

### ② Bijvragen

**"Waarom zijn de steunreacties bij de schuiver en C (137 N en 123 N) zo veel groter dan de netto resultante (74 N)?"**
> Het mechanisme heeft meerdere gesloten lussen. Inwendige krachten tussen de stangen kunnen groot zijn terwijl ze gedeeltelijk compenseren aan de buitenkant. Vergelijk het met een brug: de kabelspanning kan honderden kilonewton zijn terwijl de nettokracht op de fundering veel kleiner is. Elk lager draagt wel de volledige lokale kracht — vandaar dat je per scharnier dimensioneert, niet op de netto resultante.

**"Is de globale krachtenbalans een goede check?"**
> Ja. De som van alle externe krachten op het mechanisme moet gelijk zijn aan de som van alle massa-maal-versnelling termen. In NB2 is die afwijking 6,75 × 10⁻¹⁶ N — machineprecisie. Dat bevestigt dat er geen krachten verloren zijn in de dynamische berekening.

### ③ Verificatie in notebooks

- **NB2 `dyn-force-code` output:** F_shak max = 0,943 N; M_shak max = 1,483 Nm
- **NB2 `dyn-check-code` output:** globale balanscontrole = 6,75e-16 N ✓
- **NB3 `534b66d2` output:** max frame-reactie A = 137,31 N; C = 122,66 N; netto = 74,22 N

---

## Vraag 8 – Relatieve invloeden: inertie, wrijving en stijfheid

### ① Volledig antwoord

**Wat domineert de aandrijfkracht?**

De aandrijfkracht bij de schuiver splitst in vier bijdragen:

| Bijdrage | Grootte | % van totaal |
|----------|---------|-------------|
| Zwaartekracht | 63,4 N | **92%** |
| Schuiverwrijving | 6,1 N | 9% |
| Inertie | 2,8 N | 4% |
| Scharnierwrijving | 0,5 N | 0,7% |

De zwaartekracht domineert volledig: 92% van wat de motor levert gaat naar het optillen van 7,5 kg. De inertie is 23× kleiner dan de gravitatielast — dit is een quasi-statisch mechanisme.

**Wat leert ons de vergelijking van de vier bijdragen?**

Inertie (2,8 N) is zo klein omdat de schuiver maar 0,1 m/s² versnelt. Pas als we de paraplu in minder dan ~0,8 s zouden openen, overstijgt de inertie de zwaartekracht. Tot die drempel is dynamische analyse formeel correct maar niet nodig voor ontwerpdoeleinden.

Schuiverwrijving (6,1 N) is de tweede bijdrage en de moeite waard om te verbeteren — een rollager in de geleiding verlaagt µ van 0,05 naar 0,01 en spaart meteen 5 N op de motor. Scharnierwrijving (0,5 N) heeft geen zin om te optimaliseren.

**Kan wrijving de parasol op positie houden?**

Nee. De houdkracht die nodig is in open stand bedraagt 62,67 N. De maximale wrijvingsgrens van de geleiding is slechts 9,77 N — een factor 6 te weinig. In gesloten stand is de grens zelfs maar 0,73 N. Een mechanische vergrendeling is **absoluut verplicht** — wrijving is hier geen betrouwbare houdfunctie.

**Hoe lost NB4 de houdfunctie op?**

NB4 kiest bewust voor een **aparte rem** aan de reductoruitgang, niet voor een zelfremmende spindel. De redenering is drieledig:

- De tandriem/kabel houdt de schuiver niet vanzelf vast (geen geometrische zelfblokkering)
- Schuiverwrijving is onbetrouwbaar: slijtage, water, vuil en temperatuur veranderen µ
- Een gedimensioneerde rem is deterministisch en onafhankelijk van toevallige wrijving

Het benodigde remkoppel aan de reductoruitgang: $T_{\text{rem}} = 2{,}0 \times 62{,}67\,\text{N} \times 0{,}025\,\text{m} = \mathbf{3{,}13\,\text{Nm}}$

Omgerekend naar de motoras (reductie 60:1, η = 0,75): $T_{\text{rem,motor}} \approx 0{,}070\,\text{Nm}$ — uitstekend haalbaar.

De rem vergrendelt ook bij **tussenstanden**: een paraplu halfopen laten (voor gedeeltelijke schaduw) is volledig ondersteund, want de rem houdt élke schuiverpositie vast.

### ② Bijvragen

**"Wanneer domineert inertie over zwaartekracht?"**
> Als de piekversnelling van de schuiver groter wordt dan g = 9,81 m/s². Voor ons mechanisme betekent dat een openingstijd korter dan ~0,8 s. Dan pas is de aandrijving echt dynamisch. Tot die drempel kun je de motor quasi-statisch dimensioneren op het gewicht.

**"Waarom kiest NB4 een aparte rem en geen zelfremmende spindel?"**
> Een zelfremmende spindel integreert de houdfunctie in de transmissie, maar bindt het ontwerp aan één specifiek spoed/diameter-paar. De aparte rem is onafhankelijk instelbaar, werkt bij elke tussenstand, en faalt niet als de spindel gesmeerd raakt of slijt. Bovendien past de tandriem/kabel-architectuur beter bij lage snelheden en hoge koppels dan een dunne spindelas.

**"Wanneer speelt stijfheid van de stangen wél een rol?"**
> In ons model zijn alle stangen star. In werkelijkheid buigen de dunne aluminium ribben lichtjes door onder belasting en wind. Die doorbuiging verandert de geometrie en daarmee de hoeken — bij dit trage en licht-geconditioneerde mechanisme verwaarloosbaar. Maar bij een snelle paraplu met lange, dunne ribben onder windbelasting wordt elasticiteit een reëel ontwerp-aspect.

### ③ Verificatie in notebooks

- **NB3 `96641a9c` output:** exacte krachtdecompositie per bijdrage
- **NB3 `555c5abe` output:** statische houdkracht en wrijvingsgrens per positie
- **NB4 houdcel:** T\_rem = 3,13 Nm uitgang; T\_rem,motor ≈ 0,070 Nm
- **NB4 parameters:** brake\_safety\_factor = 2,0; drive\_pulley\_radius = 25 mm

---

## Vraag 9 – Meerdere mechanismen parallel aan dezelfde motor

### ① Volledig antwoord

**Scenario: een echte paraplu met 6 ribben**

Een echte parasol heeft 6 ribben rond een centrale mast. Elke rib is een afzonderlijk stangenmechanisme, maar alle 6 worden aangedreven door dezelfde schuiver. Dat is precies het N = 6 scenario.

**De snelheden veranderen niet.** De schuiver beweegt volgens hetzelfde bewegingsprofiel — de snelheid en versnelling van elke individuele rib zijn identiek aan ons model. Wat wijzigt: alle krachten schalen lineair mee.

- Totale aandrijfkracht: 6 × 76,06 N = **~456 N**
- Piek motorvermogen: 6 × 38,75 W = **~233 W** → nominaal ≥ **75 W**
- Piek motorkoppel: 6 × 0,103 Nm = **~0,62 Nm**

De spindel draagt nu 413 N axiale druk. Dat is een **knikcontrole** waard: een dunne spindelas kan onder die last doorbuigen (Euler-knik). De spindeldiameter moet navenant groter gekozen worden.

**Het grote mechanische voordeel van de radiale symmetrie:**

De 6 ribben zijn gelijkmatig verdeeld rondom de mast (60° tussenhoek). Dit heeft een elegante consequentie: de **zijdelingse inertiekrachten van de 6 ribben heffen elkaar perfect op**. Elke rib duwt zijdelings in een andere richting, en de vectorsom is nul. De mast ondervindt geen netto zijkracht en begint niet te trillen of buigen onder de dynamische belasting. Alleen de verticale zwaartekrachtcomponent telt 6× op — dat is de axiale druk op de spindel.

### ② Bijvragen

**"Waarom openen we de 6 ribben niet gefaseerd (uit fase) om de motorpiek te verlagen?"**
> Fysiek onmogelijk voor een parapludoek. Het doek verbindt alle ribben als één aaneengesloten vlak — als rib 1 al half open is en rib 2 nog dicht, scheurt het doek of raakt het verstrikt. Alle ribben moeten exact synchroon bewegen. We accepteren de hogere motorpiek van 0,62 Nm. De echte ontwerplimiet is trouwens niet de motor, maar de spindelas: moet die onder 413 N axiale druk niet knikken? Dat is de controlefactor die de spindeldiameter bepaalt.

**"Hoe verandert de dimensionering als we van 1 naar 6 ribben gaan?"**
> Motor en spindel moeten 6× zwaarder gedimensioneerd worden op koppel, vermogen en axiale belastingscapaciteit. De geometrie en de kinematica van elke rib individueel blijven identiek — NB1 hoef je niet opnieuw te draaien. Alleen de kracht- en vermogensberekeningen in NB3 schalen lineair mee.

### ③ Verificatie in notebooks

- **NB3 output:** basiswaarden voor N=1 (piek 76,06 N, 38,75 W, 4,39 Nm aan de poelie)
- **NB2 `dyn-params-code`:** massa's per link — direct schalen met N

---

## Vraag 10 – Parameterwijziging: voorspelling en verificatie

> **Sleutelregel voor elk scenario:** de kinematica (NB1) is puur geometrisch — hoeken, snelheden en versnellingen hangen uitsluitend af van de stanglengtes en het bewegingsprofiel, **nooit** van massa, wrijving of spindelspoed. Wijzig je geen geometrie of timing, dan hoef je NB1 nooit opnieuw te draaien.

---

### Scenario A – Wrijvingscoëfficiënt verdubbelen (µ: 0,05 → 0,10)

*Simuleert een vuile of roestige mast.*

**Kwantitatieve voorspelling:**

| Bijdrage | Huidig | Na wijziging |
|----------|--------|-------------|
| Zwaartekracht | 63,43 N | **63,43 N** (onveranderd) |
| Schuiverwrijving | 6,09 N | ~12,2 N (+6,1 N) |
| Inertie + scharnier | 3,26 N | **3,26 N** (onveranderd) |
| **F\_s totaal** | **76,06 N** | **~83 N (+9%)** |
| Motorvermogen piek | 38,75 W | ~42 W |

Wrijving is slechts 9% van de totale kracht. Verdubbelen ervan geeft slechts +9% op F\_s. De motor merkt het nauwelijks.

**Wat niet verandert:** alle hoeken, snelheden en versnellingen (NB1 volledig geldig), inertiekrachten (NB2), houdkracht 62,67 N.

**Welke notebooks herberekenen:** alleen **NB3** (krachtdecompositie en motordimensionering).

---

### Scenario B – Massa puntmassa K verdubbelen (3 → 6 kg)

*Simuleert een zwaarder doek of extra randversteviging.*

**Kwantitatieve voorspelling:**

Totale massa stijgt van 7,5 kg naar 10,5 kg (+40%). De zwaartekrachtbijdrage schaalt evenredig — maar niet exact 40%, want de bijdrage van K hangt ook af van de positie van zijn zwaartepunt in de kinematische keten. Ruwe schatting:

$$\Delta F_{s,\text{zwk}} \approx \Delta m \times g \times \bar{k} \approx 3\,\text{kg} \times 9{,}81 \times 0{,}86 \approx 25\,\text{N}$$

| | Huidig | Na wijziging |
|--|--------|-------------|
| F\_s totaal | 76,06 N | **~103 N (+36%)** |
| Motorvermogen piek | 38,75 W | ~53 W |
| Houdkracht (open stand) | 62,67 N | **~86 N** |

Dit is de meest impactvolle parameterwijziging — factor 4× groter effect dan wrijving verdubbelen. Het bewijst opnieuw dat gewicht de dominante factor is.

**Kritische bijwerking:** de houdkracht in open stand stijgt naar ~86 N. De spindel moet dit nog altijd houden — controleer of het houdsmoment (spindel $p = 5\,\text{mm}$) nog voldoende is.

**Welke notebooks herberekenen:** **NB2** (inertiekrachten herschalen) en **NB3** (alle krachten en motordimensionering). NB1 onveranderd.

---

### Scenario C – Openingstijd halveren (t\_move: 30,03 → 15,02 s)

*Snellere bediening — bijv. voor automatische sluiting bij windstoot.*

Inertiekrachten schalen **kwadratisch** met de omgekeerde openingstijd:

$$F_{\text{inertie,nieuw}} = F_{\text{inertie,oud}} \times \left(\frac{t_{\text{oud}}}{t_{\text{nieuw}}}\right)^2 = 2{,}76 \times 4 = 11\,\text{N}$$

| Bijdrage | Huidig | Na halvering |
|----------|--------|-------------|
| Zwaartekracht | 63,43 N | **63,43 N** (onveranderd) |
| Inertie | 2,76 N | **~11 N** (×4) |
| Wrijving | 6,09 N | ~6,5 N (licht ↑ door hogere v) |
| **F\_s totaal** | **76,06 N** | **~90 N (+18%)** |
| Max snelheid schuiver | 0,2495 m/s | ~0,499 m/s (×2) |
| Piek motortoerental | 2850,8 rpm | ~5700 rpm |
| Piek actuatorvermogen | 38,75 W | **~155 W** (×4, want P = F×v: F×2, v×2) |

Het piekVERMOGEN stijgt met een factor 4 — dit is de werkelijke beperkende factor, niet de kracht. De motor moet opnieuw gedimensioneerd worden.

**Welke notebooks herberekenen:** **NB1** (nieuw bewegingsprofiel met t\_move = 8,32 s), daarna **NB2** en **NB3** volledig opnieuw.

---

### Scenario D – Poelieradius wijzigen (r: 25 → 40 mm)

*NB4 vervangt de spindel door een tandriem/kabel met poelie. De aandrijfparameter is nu de poelieradius, niet de spindelspoed.*

**Motorkoppel en toerental (poelieformule):**

$$T_{\text{uitgang}} = \frac{F_{\text{ontwerp}} \cdot r}{\eta} \quad;\quad n_{\text{uitgang}} = \frac{v_{\text{schuiver}}}{2\pi r}$$

| Poelieradius | Koppel uitgang | Piek-uitgangstoerental | Status |
|-------------|---------------|----------------------|--------|
| r = 15 mm | 3,08 Nm | 151 rpm → **te snel** | niet haalbaar |
| r = 25 mm | 7,69 Nm | 95 rpm ✓ | **gekozen** |
| r = 40 mm | 12,31 Nm | 57 rpm ✓ | haalbaar maar koppel ↑ |
| r = 60 mm | 18,46 Nm | 38 rpm ✓ | **overschrijdt koppelgrens** |

*(F\_ontwerp = 200 N, η = 0,65, grens = 120 rpm piek, max voorkeurskoppel = 12 Nm)*

Grotere poelie → meer koppel nodig, lager toerental → zwaardere reductor/motor. Kleinere poelie → minder koppel, hogere rpm, meer riembuiging en slijtage. NB4 kiest r = 25 mm automatisch als de beste balans.

**Wat verandert er verder niet:**

Krachten (NB3) en kinematica (NB1, NB2) zijn volledig onveranderd. Alleen de vertaling van lijnkracht naar motorkoppel en toerental wijzigt. De rem moet ook herberekend worden: $T_{\text{rem}} = 2{,}0 \times 62{,}67 \times r$.

**Kritische check: zelfremmende conditie bestaat niet meer.**

De tandriem/kabel heeft geen zelfremmende werking. Ongeacht de poelieradius is een **aparte rem verplicht**. Dit is een fundamenteel verschil met de spindel uit de oude NB3-architectuur.

**Welke notebooks herberekenen:** NB1, NB2, NB3 volledig onveranderd. Alleen **NB4** herberekenen met nieuwe `preferred_pulley_radius`.

---

### Scenario E – Stanglengtes wijzigen (bijv. riblengte +20%)

*De meest fundamentele wijziging — raakt de geometrie zelf.*

Dit is het enige scenario waarbij je **NB1 verplicht opnieuw moet draaien** vóór je ook maar iets kunt voorspellen. Alle andere scenario's laten de geometrie intact; een stanglengte wijzigen verandert de kinematische sluitingsvergelijkingen.

**Wat de stanglengtes bepalen:**

De aandrijfkracht $F_s$ volgt uit het principe van virtuele arbeid:

$$F_s \cdot \delta s = \sum_i \vec{F}_i \cdot \delta \vec{r}_i$$

De verhouding $\delta \vec{r}_{cm} / \delta s$ — hoeveel het zwaartepunt stijgt per eenheid schuiverbeweging — hangt uitsluitend af van de geometrie. Verander je een stanglengte, dan verandert die verhouding, en dus $F_s$, **zelfs als het gewicht en de wrijving identiek blijven**.

**Drie concrete gevolgen:**

① **Kinematica verandert volledig.** Alle hoeken $\theta_i(s)$, hoeksnelheden en hoekversnellingen zijn andere functies van $s$. NB1 opnieuw uitvoeren is niet optioneel.

② **Conditiegetal verandert.** Het huidige maximum is cond(A) = 68,36 bij de gesloten stand. Met een langere stang kan de gesloten configuratie dichter bij een singulariteit uitkomen — het conditiegetal kan sterk stijgen. Als cond > 1000 ergens in het werkbereik opduikt, moet het condition-aware profiel de schuiver daar nog sterker vertragen of de stanglengtes worden herontworpen. Omgekeerd: een beter gekozen geometrie kan cond(A)\_max verlagen, waardoor de condition-aware vertraging kleiner hoeft te zijn en de openingstijd korter kan.

③ **Begin- en eindstand verschuiven.** $s_{\text{gesloten}}$ en $s_{\text{open}}$ zijn de schuiverposities waarbij het doek volledig dicht of volledig open is — dit zijn geometrische grenzen bepaald door de stanglengtes. Na een wijziging hoeven 1,85 m en 0,75 m niet meer te kloppen. De slag verandert.

**Voorspelling zonder NB1 draaien: niet betrouwbaar.**

Voor massa, wrijving of spoed kun je de richting en orde van grootte voorspellen. Voor stanglengtes is de afhankelijkheid sterk niet-lineair en richting-afhankelijk: een langere bovenste stang versus een langere onderste stang hebben tegengestelde effecten op $F_s$. De enige correcte werkwijze:

1. Stanglengte aanpassen in NB1 → Run All
2. Controleer nieuw cond(A) profiel — zijn er kritische zones?
3. Controleer nieuwe $s_{\text{open}}$ en $s_{\text{gesloten}}$
4. Draa NB2 opnieuw → nieuwe inertiekrachten
5. Draa NB3 opnieuw → nieuwe $F_s$, motordimensionering

**Welke notebooks herberekenen:** **NB1 → NB2 → NB3**, in die volgorde. NB1 als eerste want alle latere notebooks laden de `.npz`-bestanden die NB1 aanmaakt.

---

### ② Overzichtstabel: welk notebook herberekenen?

| Parameterwijziging | NB1 | NB2 | NB3 | NB4 |
|-------------------|-----|-----|-----|-----|
| Wrijving µ | ✗ | ✗ | **✓** | ✓ |
| Massa K | ✗ | **✓** | **✓** | ✓ |
| Openingstijd t\_move | **✓** | **✓** | **✓** | ✓ |
| Poelieradius r | ✗ | ✗ | ✗ | **✓** |
| Stanglengtes | **✓** | **✓** | **✓** | ✓ |

*NB4 altijd als laatste draaien — het laadt de .npz-uitvoer van NB3.*

### ③ Verificatie in notebooks

- **NB3 `06cc4881`:** wrijvingsparameters aanpassen
- **NB2 `dyn-params-code`:** massa's per link aanpassen
- **NB1 `orig-008`:** bewegingsprofiel en t\_move aanpassen
- **NB4 parameters:** `preferred_pulley_radius`, `drive_efficiency`, `gear_ratio_candidates`
- **NB4 samenvatting:** herziene motordimensionering en remkoppel na elke wijziging

---

## Vaste bijvraag – Motorkosten en energieverbruik

### ① Volledig antwoord

**Gekozen aandrijfarchitectuur (NB4):**

NB4 kiest een **24 V DC/BLDC reductiemotor met encoder en rem**, die een gesloten tandriem/kabel langs de mast aandrijft. De taakverdeling is strict:

| Component | Taak |
|-----------|------|
| Motor + reductiekast 60:1 | Verticale aandrijfkracht, positionering |
| Tandriem/kabel + poelie 25 mm | Overbrenging lijnkracht langs mast |
| Rem aan reductoruitgang | Vasthouden in open, gesloten en tussenstand |
| Schuivergeleiding + mast | Zijreacties en kantelmomenten (≠ riem!) |
| Encoder + controller | Precisie, homing, eindpositiebeveiliging |

**Motor-specificaties (NB4-berekening):**

```
Aandrijfarchitectuur       : tandriem/kabel, 24 V DC/BLDC
Poelieradius               : 25 mm
Reductie                   : 60:1
Totale efficientie η        : 0,65 (riem + reductiekast)
Veiligheidsfactor           : 2,0

Mechanische piekbelasting   : 76,06 N  (berekend)
Praktische ontwerplijnkracht: 200 N    (robuustheidseis)
Aandrijfkoppel uitgang      : 7,69 Nm  → klasse ≥ 8 Nm
Remkoppel uitgang           : 3,13 Nm  (SF = 2 × 62,67 N × 0,025 m)
Remkoppel motoras           : ~0,070 Nm

Piek-uitgangstoerental      : ~95 rpm
Motorpieksnelheid           : ~5719 rpm  (95,3 × 60)
Gemiddeld motortoerental    : ~1500 rpm

Piek motor-ingangsvermogen  : ~51,7 W   (2,0 × 16,79 / 0,65)
Aanbevolen motorvermogen    : 135 W klasse
Aanbevolen uitgangskoppel   : ≥ 8 Nm
```

**Indicatieve componentprijzen:**

| Component | Prijs |
|-----------|-------|
| 24 V BLDC motor met encoder (bijv. Maxon EC-i 40) | €120–250 |
| Reductiekast 60:1 (planetair) | €60–150 |
| Tandriem + poelie 25 mm + spanklem | €20–50 |
| Elektromagnetische rem aan uitgang | €30–80 |
| Controller/driver (24 V, ~6 A piek) | €50–150 |
| **Totaal voor één paraplu** | **€280–680** |

**Energieverbruik:**

Netto mechanische energie per openingsslag (NB3): **119,81 J**

Motoringang per slag (η = 0,65): $E_{\text{motor}} = 72{,}96 / 0{,}65 \approx 112\,\text{J}$

Voor dagelijks gebruik (1× openen + 1× sluiten, niet-regeneratief):
$$E_{\text{dag}} \approx 2 \times 112\,\text{J} = 224\,\text{J}$$

Bij 200 werkdagen per jaar:
$$E_{\text{jaar}} = 200 \times 224\,\text{J} = 44{.}800\,\text{J} \approx 0{,}0124\,\text{kWh}$$

Aan €0,30/kWh: **≈ €0,0037/jaar** — volledig verwaarloosbaar.

Het stand-byverbruik van de controller (~2 W continu) overstijgt het mechanische verbruik met een factor **>400**.

### ② Bijvragen

**"Hoe berekent u het aandrijfkoppel aan de poelieuitgang?"**

$$T_{\text{uitgang}} = \frac{F_{\text{ontwerp}} \cdot r}{\eta} = \frac{200 \times 0{,}025}{0{,}65} = 7{,}69\,\text{Nm}$$

Omgerekend naar motoras (reductie 60:1, η\_kast = 0,75):
$$T_{\text{motor}} = \frac{7{,}69}{60 \times 0{,}75} = 0{,}171\,\text{Nm}$$

**"Waarom is de ontwerplijnkracht 200 N terwijl de berekende waarde maar 69 N is?"**

> NB4 legt een praktische vloer van 200 N op. Dit zijn niet de berekende krachten maar een robuustheidseis: een te licht gedimensioneerde riem/kabel slijt sneller, rekt meer, en geeft minder nauwkeurige positionering. De 200 N dekt ook onverwachte windstoten, ijsaanslag en versneld openen in noodgeval.

**"Waarom is het energieverbruik zo laag ondanks η = 0,65?"**

> De slag duurt ongeveer 30 s en het mechanisme weegt slechts 7,5 kg. Zelfs met 35% transmissieverliezen blijft de totale energie per slag zeer klein tegenover dagelijks stand-byverbruik van een controller. De gebruiksfrequentie (1–2 slagen per dag) maakt het energieverbruik van de aandrijving praktisch verwaarloosbaar voor de exploitatiekosten.

---

## Examensamenvatting: kernformules

| Stap | Formule / Methode | Sleutelmatrix |
|------|-------------------|---------------|
| Positie | $\vec{F}(\boldsymbol{\theta},s) = \vec{0}$ → `fsolve` | intern (Jacobiaan) |
| Snelheid | $A\boldsymbol{\omega} = \mathbf{b}\dot{s}$ | $A_{6\times6}$ |
| Versnelling | $A\boldsymbol{\alpha} = \mathbf{b}\ddot{s} + \mathbf{c}(\boldsymbol{\omega}, \dot{s})$ | zelfde $A$ |
| Inv. dynamica | $A_{21\times21}\mathbf{w} = \mathbf{b}$ | $A_{21\times21}$ |
| Motorkoppel (spindel) | $T = F_s p/(2\pi\eta)$ | — |
| Motorkoppel (poelie, NB4) | $T_{\text{uitgang}} = F_{\text{ontwerp}} \cdot r / \eta$ | — |
| Remkoppel (NB4) | $T_{\text{rem}} = \text{SF} \times \lvert F_{\text{hold}} \rvert \times r$ | — |
| Poelie-toerental (NB4) | $n = v_{\text{schuiver}} / (2\pi r)$ | — |
| Arbeids-surplus | $A_{\max} = \max A_\theta - \min A_\theta$ | — |
| Energiebalans | $P_{\text{act}} = \dot{E}_{\text{kin}} + P_{\text{zwk}} + P_{\text{wrijving}}$ | validatie |

**Valkuilen:**
- De dynamische matrix $A_{21\times21}$ (krachten) ≠ de kinematische matrix $A_{6\times6}$ (hoeksnelheden) — zelfde naam, fundamenteel andere matrices
- Wrijvingskrachten gaan naar het **rechterlid** van Newton-Euler (bekende krachten)
- Centripetale termen staan in $\mathbf{C}$, **niet** in $A$
- Verplichte volgorde: **NB1 → NB2 → NB3 → NB4** (elke notebook laadt .npz van de vorige)
- NB4 gebruikt η = 0,65 (tandriem + reductiekast), niet 0,80 (spindel uit NB3)
