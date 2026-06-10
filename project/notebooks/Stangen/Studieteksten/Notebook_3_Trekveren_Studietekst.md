# Notebook 3 - Trekveren - Studietekst

Deze tekst hoort bij `Notebook 3 - Trekveren.ipynb`. Deze notebook is inhoudelijk
bijna dezelfde analyse als `Notebook 3.ipynb`, maar met een extra bekende
trekveerkracht op de schuiver. De bedoeling is om de belasting zonder veer en
met veer eerlijk te vergelijken.

De gedeelde theorie met Notebook 3 blijft dezelfde:

- dezelfde kinematica uit Notebook 1;
- hetzelfde massamodel uit Notebook 2;
- dezelfde Newton-Euler-opbouw;
- dezelfde zwaartekracht;
- hetzelfde schuiver- en pinwrijvingsmodel;
- dezelfde energie- en vermogenscontroles.

Wat extra is:

- veeropstelling;
- veerwet;
- automatische veerdimensionering;
- vergelijking zonder/ met trekveren;
- veerenergie en effect op motorbelasting.

Als de basisformules voor zwaartekracht, wrijving of Newton-Euler in Notebook 3
worden aangepast, moet dezelfde redenering ook in deze trekverenstudietekst
worden aangepast.

## 0. Actuele hoofdcase

Notebook 3 - Trekveren gebruikt dezelfde basiskinematica en massa's als
Notebook 3.

| Grootheid | Actuele waarde |
|---|---:|
| Schuivertraject | `s = 1.875 m -> 0.600 m` |
| Effectieve beweegtijd | `20.00 s` |
| Stangprofiel | ronde aluminium buis `30x2 mm` |
| Stanglijnmassa | `0.550 kg/m` |
| Schuivermassa | `1.50 kg` |
| Puntmassa in K | `3.00 kg` |
| Totale bewegende modelmassa | `7.50 kg` |
| Schuiverwrijving | `mu_slider = 0.08` |
| Pinwrijving | `mu_pin = 0.05`, `r_pin = 6 mm` |
| Aantal trekveren | `2` |
| Totale veerkracht open | `18.90 N` |
| Totale veerkracht gesloten | `44.40 N` |
| Veerstijfheid totaal | `20.00 N/m` |
| Veerstijfheid per veer | `10.00 N/m = 0.010 N/mm` |
| Extra veerenergie gesloten-open | `40.36 J` |
| Max `|F_s|` zonder veer | `74.89 N` |
| Max `|F_s|` met trekveren | `55.97 N` |
| Max houdkracht zonder veer | `63.43 N` |
| Max houdkracht met trekveren | `43.25 N` |
| Max actuatorvermogen met trekveren | `7.67 W` |

Belangrijke interpretatie:

- de trekveer verandert de kinematica niet;
- de trekveer verandert ook de massa's niet;
- de trekveer levert een bekende opwaartse kracht op de schuiver;
- daardoor moet de motor minder kracht leveren bij openen en vasthouden;
- bij sluiten moet de motor gecontroleerd energie terug in de veer steken.

Mondelinge kernzin:

> De trekveer is geen nieuwe bewegingswet. Het is een bekende externe kracht op
> de schuiver die een deel van de zwaartekrachtbelasting compenseert.

## 1. Waarom een trekveer-notebook?

Notebook 3 zonder veer toont dat zwaartekracht de belasting domineert. Dat maakt
een veer interessant, omdat een veer geen inertiekracht compenseert maar wel
een deel van de quasi-statische gewichtslast kan overnemen.

De vraag is niet alleen:

```text
verlaagt de veer de openingskracht?
```

maar ook:

```text
blijft de veer controleerbaar?
wat gebeurt er met houden?
wat gebeurt er met sluiten?
hoeveel energie moet opnieuw in de veer?
```

Daarom staat de veer niet gewoon als klein extra stukje in Notebook 3. Er is een
aparte loadcase, zodat de resultaten zonder veer en met veer naast elkaar
kunnen worden gezet.

## 2. Fysische opstelling

De gekozen opstelling is:

- twee trekveren;
- symmetrisch links en rechts van de schuiver/collar;
- parallel aan de mast;
- bevestigd tussen vast frame/mast en schuiver;
- niet in de riem of kabel zelf.

Waarom twee veren?

- de schuiver wordt symmetrischer belast;
- er komt minder wringmoment op de collar;
- elke veer hoeft maar de helft van de kracht te leveren;
- het is praktisch monteerbaar naast de mast.

Waarom niet in de riem?

De riem moet de gecontroleerde aandrijfkracht leveren. De schuivergeleiding en
mast moeten de dwarsreacties dragen. Als de veer in de riem zou zitten, wordt de
aandrijving elastischer en minder precies. Daarom werkt de veer parallel aan de
aandrijving, niet als onderdeel van de riem.

## 3. Tekenconventie

De globale `y`-as is positief omhoog. De schuivercoordinaat `s` is positief naar
beneden.

Dus:

- openen: `s` daalt, `ds < 0`;
- een veer die helpt openen trekt omhoog;
- omhoog is globale `+y`;
- in `s`-richting is die kracht negatief.

De notebook gebruikt daarom:

```math
F_{\text{spring,up}} > 0
```

als globale opwaartse kracht, en:

```math
F_{\text{spring},s} = -F_{\text{spring,up}}
```

in de schuivercoordinaat.

In de Newton-Euler-opbouw wordt de veer toegevoegd als bekende kracht op link 2
in punt `B`:

```math
\mathbf{F}_\text{spring}
= \begin{bmatrix}0 \\ F_{\text{spring,up}}\end{bmatrix}
```

## 4. Veerwet

De veerkracht is lineair als functie van de schuiverstand:

```math
F_{\text{spring,up}}(s)
= F_{\text{open}} + k(s-s_{\text{open}})
```

met:

```math
k =
\frac{F_{\text{closed}}-F_{\text{open}}}
{s_{\text{closed}}-s_{\text{open}}}
```

Omdat `s_closed > s_open`, wordt de veer sterker wanneer de paraplu gesloten is.
Dat is nuttig, want bij het begin van openen is de zwaartekrachtlast vaak het
lastigst.

De veerenergie is:

```math
E_\text{spring}(s)
= F_\text{open}(s-s_\text{open})
+ \frac{1}{2}k(s-s_\text{open})^2
```

Hier is `k` de directe stijfheid van de twee trekveren samen. De veer is gewoon
naast de mast gemonteerd: geen extra kabel, poelie of hefboom. Omdat de
schuiverslag groot is, moet de veer wel lang en relatief slap zijn.

```text
fysieke veerstijfheid per veer = 0.010 N/mm = 10 N/m
fysieke veerweg over slag      = 1275 mm
fysieke veerkracht per veer    = 9.45 -> 22.20 N
```

De code kiest de open-kracht/voorspanning automatisch lager wanneer een hogere
directe veerconstante anders te veel gesloten-standkracht zou geven. Zo blijft
de veer ondergecompenseerd en blijft de motor/rem controle houden.

Voor de huidige case:

```text
E_spring,closed - E_spring,open = 40.36 J
```

Dat betekent:

- bij openen kan de veer energie teruggeven;
- bij sluiten moet die energie opnieuw in de veer worden opgeslagen;
- zwaartekracht kan bij sluiten helpen, maar wrijving blijft verlies.

## 5. Automatische veerdimensionering

De notebook gebruikt standaard:

```text
spring_design_mode = "fraction_of_baseline_hold"
spring_assist_fraction_open = 0.60
spring_assist_fraction_closed = 0.70
spring_max_assist_fraction = 0.80
```

Dat betekent:

- bij open stand wil de veer ongeveer 60% van de baseline-houdlast helpen;
- bij gesloten stand ongeveer 70%;
- nergens mag de assist groter worden dan 80% van de benodigde houdlast.

Waarom geen 100% compensatie?

Omdat volledige compensatie ongunstig kan zijn:

- de motor verliest controlegevoel rond evenwicht;
- sluiten wordt zwaarder;
- toleranties, wrijving en wind kunnen het systeem in de verkeerde richting
  trekken;
- de rem/vergrendeling moet nog steeds betrouwbaar zijn.

Daarom is de veer bewust ondergecompenseerd.

De notebook bevat ook manuele parameters:

```text
spring_force_open_total_manual = 20 N
spring_force_closed_total_manual = 50 N
```

Die zijn bedoeld voor een handmatige studie, maar de standaardcase schaalt mee
met de baseline-houdkracht. Dat is beter als massa's of geometrie later
veranderen.

## 6. Zelfde Newton-Euler-basis als Notebook 3

De matrixstructuur blijft:

```math
A w = b
```

Voor Notebook 3 zonder veer:

```math
A w =
b_\text{inertie}
+ b_\text{zwaartekracht}
+ b_\text{wrijving}
```

Voor Notebook 3 - Trekveren:

```math
A w =
b_\text{inertie}
+ b_\text{zwaartekracht}
+ b_\text{wrijving}
+ b_\text{veer}
```

De veer is bekend, dus ze gaat naar het rechterlid zoals zwaartekracht en
wrijving. De onbekenden blijven dezelfde reactiekrachten en actuatorkracht.

Belangrijk:

> De veer verandert niet de sluitingsvergelijkingen, niet de linkhoeken en niet
> de snelheden. Ze verandert alleen de krachten die nodig zijn om dezelfde
> beweging uit te voeren.

## 7. Wrijving blijft hetzelfde

De schuiverwrijving blijft:

```math
F_{\text{fric,slider},s}
= -\mu_{\text{slider}} |R_{Ax}|
\tanh\left(\frac{\dot{s}}{v_\epsilon}\right)
- c_{\text{slider}}\dot{s}
```

De pinwrijving blijft:

```math
M_{\text{pin}}
= \mu_{\text{pin}} r_{\text{pin}} |R_{\text{joint}}|
\tanh\left(\frac{\omega_\text{rel}}{\omega_\epsilon}\right)
```

De gebruikte waarden zijn dezelfde als in Notebook 3: `mu_slider = 0.08`,
`mu_slider_static = 0.12`, `mu_pin = 0.05` en `c_slider = 0.0`. Dat is
belangrijk: de trekveer wordt vergeleken met de baseline onder dezelfde
wrijvingsaannames. De schuiverwaarde is een realistische orde-grootte voor een
lage-wrijving outdoor geleiding; ze is conservatiever dan een ideaal rollager,
maar niet zo pessimistisch als droge metaal-op-metaal geleiding.

Omdat de veer verticaal op de schuiver werkt, verandert ze niet automatisch de
horizontale reactie `R_Ax` in deze symmetrische basiscase. Daarom blijven de
lokale zijreacties ongeveer gelijk. Wat vooral verandert is de verticale
aandrijfkracht.

Voor de huidige case:

```text
max |R_Ax| zonder/met veer = 157.86 N
```

Dat is logisch: de veer helpt in de verticale richting, maar lost niet de
horizontale geleidingsreactie op.

## 8. Oplossingscases in de trekveren-notebook

De notebook lost opnieuw meerdere cases op:

| Case | Zwaartekracht | Wrijving | Veer | Doel |
|---|---|---|---|---|
| `case_inertia_check` | nee | nee | nee | validatie t.o.v. Notebook 2 |
| `case_gravity_spring` | ja | nee | ja | effect van zwaartekracht + veer zonder wrijving |
| `case_total` | ja | ja | ja | volledige veercase |

Daarnaast laadt de notebook de baseline uit:

```text
notebook3_gravity_friction_results.npz
```

Daardoor kan ze direct vergelijken:

- zonder veer;
- met trekveren.

## 9. Krachtvergelijking

Voor de huidige single-mechanism case:

| Grootheid | Zonder veer | Met trekveren |
|---|---:|---:|
| Max `|F_s|` | `74.89 N` | `55.97 N` |
| Max houdkracht `|F_hold|` | `63.43 N` | `43.25 N` |
| Max actuatorvermogen | `13.38 W` | `7.67 W` |
| Arbeids-surplus | baselinewaarde | lager met veer |

De trekveer verlaagt dus duidelijk:

- openingskracht;
- houdkracht;
- piekvermogen;
- rem-/houdkoppel.

Maar ze is geen gratis energiebron. Ze verplaatst energie:

- bij openen helpt ze;
- bij sluiten moet ze opnieuw worden uitgerekt;
- bij elke cyclus gaat wrijvingsenergie verloren.

## 10. Energiebalans met veer

Met een veer moet de energiebalans uitgebreid worden. Conceptueel:

```math
P_\text{act}
= \frac{dE_\text{kin}}{dt}
+ P_\text{zwaartekracht}
+ P_\text{wrijving}
+ \frac{dE_\text{spring}}{dt}
```

Afhankelijk van de tekenconventie kan de veerterm aan de andere kant van de
vergelijking staan, maar fysisch betekent het:

- als de veer energie afgeeft, moet de motor minder leveren;
- als de veer wordt opgespannen, moet energie in de veer worden gestoken.

De notebook controleert of de totale vermogensbalans klopt. De huidige
energiebalansfout is ongeveer:

```text
1.1e-4 W
```

Net zoals bij Notebook 3 komt die kleine restfout vooral door numerieke
differentiatie.

## 11. Statische houdanalyse met veer

De holdkracht wordt met veer opnieuw berekend over het bewegingsbereik.

Zonder veer:

```text
max |F_hold| = 63.43 N
```

Met trekveren:

```text
max |F_hold| = 43.25 N
```

Dat is een belangrijk voordeel. Een kleinere houdkracht betekent:

- minder remkoppel nodig;
- minder continue motorbelasting als men actief zou vasthouden;
- veiligere en lichtere aandrijving.

Maar:

> Ook met trekveren blijft een rem of mechanische vergrendeling nodig. De veer
> verlaagt de houdlast, maar vervangt geen veilige lock.

## 12. Framebelasting en veeranker

De veer werkt tussen schuiver en vast frame/mast. Daardoor komt er ook een kracht
op het veeranker.

Voor de huidige case:

```text
max veerankerbelasting ~ 44.40 N
```

Dat is klein ten opzichte van de grotere structurele belastingen in de
overdekking. Toch is het fysisch belangrijk:

- de veer helpt niet zonder reactiepunt;
- het frame moet de veerankerbelasting opnemen;
- de veer moet veilig geleid en afgeschermd worden.

De horizontale schuiverreactie blijft ongeveer gelijk, omdat de veer verticaal
werkt.

## 13. Waarom trekveren en geen gasveren in deze notebook?

Trekveren zijn gekozen als eerste model omdat:

- de krachtwet eenvoudig is;
- de energie direct berekenbaar is;
- de parameters duidelijk instelbaar zijn;
- de montage parallel aan de mast begrijpelijk is;
- ze goed aansluiten bij de theorie van arbeid en energie.

Gasveren kunnen praktischer en compacter zijn, maar vragen extra modellering:

- montagehoek;
- slaglengte;
- hysterese;
- temperatuurinvloed;
- interne demping;
- niet-lineaire kracht.

Daarom is de trekveer beter voor de eerste verdedigbare analyse. Gasveren kunnen
later als praktischer alternatief besproken worden.

## 14. Wat bewijzen de figuren?

De nuttige figuren in `Notebook 3 - Trekveren` tonen:

- veerkracht als functie van `s`;
- veerenergie als functie van `s`;
- baseline `F_s` versus veer `F_s`;
- krachtreductie tijdens openen;
- vermogen zonder en met veer;
- cumulatieve arbeid zonder en met veer;
- statische houdkracht zonder en met veer;
- framebelasting en veerankerbelasting;
- quick motorcheck voor poelie/riem.

Deze figuren zijn vooral nuttig voor vragen over:

- energieopslag versus motorbelasting;
- waarom een vliegwiel niet logisch is, maar een veer wel;
- hoe zwaartekrachtcompensatie werkt;
- waarom sluiten nog steeds energie vraagt;
- waarom de veer niet in de riem hoort;
- waarom trekveren de verticale aandrijfkracht verlagen maar niet alle
  structurele reacties oplossen.

## 15. Wat moet je kunnen verdedigen?

### Waarom trekt de veer omhoog?

Omdat openen overeenkomt met `s` laten dalen. Een opwaartse kracht helpt dus bij
openen.

### Waarom twee veren?

Voor symmetrie, lagere kracht per veer en minder torsie op de schuiver/collar.

### Waarom niet 100% compenseren?

Omdat de motor en rem controle moeten houden in elke stand. Volledige
compensatie kan sluiten lastiger maken en gevoelig worden voor toleranties,
wrijving of wind.

### Waarom daalt de motorbelasting?

Omdat de veer een deel van de zwaartekrachtlast overneemt. De motor moet minder
opwaartse kracht leveren bij openen en minder houdkracht leveren in stilstand.

### Waarom is dit geen gratis energiewinst?

Omdat de veer bij sluiten opnieuw wordt opgespannen. Energie wordt verschoven
tussen openen en sluiten; wrijving blijft echt verlies.

### Waarom blijft Notebook 4 nodig?

Notebook 3 - Trekveren bepaalt de mechanische belasting. Notebook 4 vertaalt die
naar motor, poelie, rem, riem, as, veiligheid en kost.

## 16. Typische valkuilen

- Denken dat de trekveer de kinematica verandert. Dat doet ze niet.
- Denken dat de veer alle krachten verlaagt. Ze verlaagt vooral de verticale
  aandrijf- en houdkracht, niet automatisch de horizontale geleidingsreactie.
- De veer in de riem zetten. Dat zou de aandrijving elastischer en minder precies
  maken.
- Denken dat een veer energie "gratis" maakt. Ze slaat energie op en geeft die
  later terug, met wrijvingsverlies.
- Geen rem/vergrendeling voorzien omdat er een veer is. Dat is niet veilig.
- De single-mechanism trekveerresultaten verwarren met de finale overdekkingscase.
  De overdekking gebruikt dezelfde gedachte, maar met veel grotere massa's.

## 17. Relatie met de finale overdekking

Voor de finale `Notebook 3 - Overdekking` wordt hetzelfde principe gebruikt:

- baseline zonder trekveren;
- optionele trekverenloadcase;
- Notebook 4 kan beide `.npz`-bestanden lezen.

In de brede overdekking zijn trekveren interessanter dan in het enkelvoudige
mechanisme, omdat de voorbalk en doek de zwaartekrachtlast sterk verhogen.

De trekveren-notebook is dus een tussenstap:

```text
eerst bewijzen op enkel mechanisme
  -> daarna toepassen op brede overdekking
  -> motoranalyse in Notebook 4
```

## 18. Korte samenvatting

`Notebook 3 - Trekveren` is dezelfde inverse-dynamica-analyse als Notebook 3,
maar met een extra bekende opwaartse veerkracht op de schuiver. De veer is
symmetrisch gemodelleerd als twee trekveren parallel aan de mast.

De huidige resultaten tonen dat de trekveren de piek-aandrijfkracht van ongeveer
`74.89 N` naar `55.97 N` verlagen en de maximale houdkracht van `63.43 N` naar
`43.25 N`. De prijs daarvoor is dat bij sluiten opnieuw ongeveer `40.36 J` in de
veer moet worden opgeslagen. Dat is verdedigbaar, omdat de toepassing traag is
en het systeem niet constant cyclisch op hoge snelheid draait.

De belangrijkste conclusie:

> Trekveren zijn nuttig als zwaartekrachtcompensatie en motorontlasting, maar ze
> vervangen geen goede rem, geen stijve schuivergeleiding en geen correcte
> motorselectie.
