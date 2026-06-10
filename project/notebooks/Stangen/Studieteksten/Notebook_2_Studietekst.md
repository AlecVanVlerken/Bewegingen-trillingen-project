# Notebook 2 - Studietekst inverse dynamica, onbalans en actuatoranalyse

Deze tekst hoort bij `Notebook 2.ipynb`. Het doel is dat je de notebook kan
uitleggen vanuit de theorie uit de lessen: wat berekenen we, waarom mag dat, met
welke vergelijkingen, en wat betekenen de resultaten mechanisch.

Dit document is geen codecel-per-codecel uitleg. De nadruk ligt op de theorie en
de mechanische redenering achter Notebook 2. Notebook 2 is de eerste stap van
kinematica naar dynamica: de beweging uit Notebook 1 is gegeven, en de krachten
die nodig zijn om die beweging te veroorzaken worden berekend.

## 0. Actuele hoofdcase

Notebook 2 gebruikt altijd de kinematica uit `notebook1_kinematica_results.npz`.
Voor de huidige finale hoofdcase betekent dat:

| Grootheid | Actuele waarde |
|---|---:|
| Bewegingsrichting | openen |
| Schuivertraject | `s = 1.875 m -> 0.600 m` |
| Effectieve beweegtijd | `20.00 s` |
| Max schuiversnelheid | `0.1965 m/s` |
| Max schuiverversnelling | `0.0511 m/s^2` |
| Max `cond(A)` uit Notebook 1 | `68.36` |
| Schuivermassa | `1.50 kg` |
| Lijnmassa stangen | `0.550 kg/m` |
| Puntmassa in K | `3.00 kg` |
| Totale bewegende modelmassa | `7.50 kg` |
| Max inertiele aandrijfkracht | `2.99 N` |
| Max inertiele onbalanskracht | `0.50 N` |
| Max inertieel onbalansmoment rond C | `0.81 Nm` |
| Max dynamisch residu | `6.2e-16` |

Belangrijke interpretatie:

- deze krachten zijn alleen inertiekrachten;
- zwaartekracht, wrijving, wind en veerwerking zitten hier nog niet in;
- de echte motorbelasting komt later uit Notebook 3, `Notebook 3 - Trekveren`,
  `Notebook 3 - Overdekking` en Notebook 4.

Mondelinge kernzin:

> Notebook 2 bewijst dat de inertie door het rustige traject klein is. Het is
> nog niet de finale motorbelasting, maar wel de basiscontrole van de
> Newton-Euler-dynamica.

## 1. Koppeling met de lessen

| Lesonderdeel | Theorie | Toepassing in Notebook 2 |
|---|---|---|
| Les 1A | Dynamica tegenover kinematica | Notebook 1 gaf beweging; Notebook 2 berekent krachten uit die beweging. |
| Les 2 | Positie-, snelheids- en versnellingsanalyse | De posities, snelheden en versnellingen uit Notebook 1 worden rechtstreeks ingeladen. |
| Les 2/3 | Starre-lichaamkinematica | Zwaartepunten en punt K worden berekend met `v_P = v_O + omega x r` en `a_P = a_O + alpha x r - omega^2 r`. |
| Les 3 | Newton-Euler voor vlakke lichamen | Per link worden `Sigma F_x = m a_x`, `Sigma F_y = m a_y`, `Sigma M_cg = J alpha` opgesteld. |
| Les 3 | Actie-reactie in gewrichten | Interne gewrichtskrachten verschijnen met tegengesteld teken op verbonden links. |
| Les 3 | Inverse dynamica als lineair stelsel | Per tijdstap wordt `A w = b` opgelost voor alle onbekende reacties en aandrijfkracht. |
| Les 4 | Vermogen en arbeid | `P = F_s ds` en `E = int P dt` worden gebruikt voor actuatorvermogen en arbeid. |
| Les 4 | Arbeids-surplus | Het verschil tussen momentaan vermogen en gemiddeld vermogen geeft een energiebuffermaat. |
| Onbalans | Shaking force en shaking moment | De resulterende kracht en het moment die het mechanisme op het frame uitoefent worden bepaald. |

De belangrijkste leslink is:

> Eerst bepaal je de beweging kinematisch. Daarna gebruik je Newton-Euler om per
> bewegende link de krachten en momenten te vinden. Omdat de beweging gekend is,
> wordt inverse dynamica een lineair algebraisch probleem per tijdstap.

## 2. Wat analyseert Notebook 2?

Notebook 2 leest de kinematica van Notebook 1 en berekent:

- de massa's en massatraagheden van de schuiver en stangen;
- de zwaartepuntposities, -snelheden en -versnellingen;
- de gewrichtskrachten in B, C, D, E, F, G, H, I en J;
- de horizontale geleidingsreactie op de schuiver;
- de verticale inertiele aandrijfkracht;
- de onbalanskracht en het onbalansmoment op het frame;
- het inertiele actuatorvermogen;
- de kinetische energie en energiebalanscontrole;
- een poelie-/riem-/motor-equivalent als eerste actuatorinschatting;
- het arbeids-surplus volgens de energiemethode;
- het bestand `notebook2_dynamica_results.npz` voor latere notebooks.

Notebook 2 berekent bewust nog niet:

- zwaartekracht;
- schuiverwrijving;
- pinwrijving;
- trekveren;
- windbelasting;
- voorbalkdoorbuiging;
- definitieve motor-, rem-, riem- of asdimensionering.

Die uitbreidingen horen bij Notebook 3 en Notebook 4.

## 3. Theorie: inverse dynamica

Er zijn twee manieren om dynamica te gebruiken.

Voorwaartse dynamica:

```text
gegeven krachten -> bereken beweging
```

Inverse dynamica:

```text
gegeven beweging -> bereken krachten
```

Notebook 2 gebruikt inverse dynamica. De beweging is bekend uit Notebook 1:

- `s(t)`;
- `ds(t)`;
- `dds(t)`;
- linkhoeken `theta_i(t)`;
- hoeksnelheden `dtheta_i(t)`;
- hoekversnellingen `ddtheta_i(t)`;
- puntposities, vooral punt `K`.

De vraag wordt dan:

> Welke actuator-, steun- en gewrichtskrachten zijn nodig zodat de massa's exact
> die opgelegde beweging volgen?

Dat is nuttig omdat het ontwerp niet vrij mag bewegen onder willekeurige krachten.
We willen net een gekozen traject opleggen en dan weten wat motor, frame en
scharnieren moeten opnemen.

## 4. Massamodel

### 4.1 Schuiver en stangen

De schuiver wordt als aparte massa genomen:

```text
slider_mass = 1.50 kg
```

De stangen krijgen een massa op basis van een lijnmassadichtheid:

```math
m_i = \lambda L_i
```

met:

- `lambda = 0.550 kg/m`;
- `L_i`: lengte van link `i`.

Die lijnmassadichtheid wordt niet meer als losse waarde gekozen. Ze wordt in de
notebook berekend uit een realistisch buisprofiel:

```text
ronde aluminium buis: buitendiameter 30 mm, wanddikte 2 mm
aluminiumdichtheid: 2700 kg/m^3
beslagtoeslag: 0.075 kg/m
```

De doorsnede van de buis is:

```math
A = \frac{\pi}{4}(D_o^2 - D_i^2)
```

met:

```math
D_i = D_o - 2t
```

Daarmee:

```math
\lambda = \rho A + \lambda_\text{beslag}
```

Voor `D_o = 0.030 m`, `t = 0.002 m`, `rho = 2700 kg/m^3` en
`\lambda_beslag = 0.075 kg/m` volgt:

```text
lambda ≈ 0.550 kg/m
```

Dat is een eerste-orde model voor robuuste aluminium stangen inclusief kleine
bevestigingsdelen. Het is nog geen definitieve sterkteberekening van elk
stangprofiel, maar het is wel gebaseerd op een fysisch profiel. Voor de dynamica
is vooral belangrijk dat langere stangen automatisch zwaarder worden.

Waarom deze grootteorde verdedigbaar is:

- een schuiver/collar rond een mast met lagers, bouten en bevestigingspunt is
  realistisch eerder rond kilogrammen dan rond tientallen gram;
- `1.50 kg` is daarom een conservatieve maar nog plausibele inschatting voor
  een robuuste buitentoepassing;
- `0.550 kg/m` komt overeen met een aluminium buis van ongeveer `30x2 mm`
  plus een realistische toeslag voor scharnieren, bouten en eindstukken;
- de massa's zijn eenvoudig aanpasbaar en worden later automatisch gebruikt in
  de krachtberekening.

Deze waarden zijn dus representatief voor het enkelvoudige basismechanisme. Ze
zijn niet bedoeld als volledige massabegroting van de brede overdekking.

### 4.2 Puntmassa in K

In punt `K` staat een equivalente puntmassa:

```text
payload_mass_K = 3.00 kg
```

Die massa stelt bij het enkelvoudige paraplumechanisme een deel van doek, rand,
rib en beslag voor. Ze wordt niet gewoon bij link 8 opgeteld, want haar afstand
tot het zwaartepunt van link 8 is belangrijk voor het momentenevenwicht.

De bijdrage van de puntmassa is:

```math
\mathbf{F}_K = m_K \mathbf{a}_K
```

en voor het moment rond het zwaartepunt van link 8:

```math
M_K = \mathbf{r}_{cg8,K} \times m_K \mathbf{a}_K
```

Daarmee beïnvloedt de massa in `K` zowel de krachtenbalans als de momentbalans.

Voor het enkelvoudige mechanisme is `3.00 kg` een vereenvoudigde representatie
van doek/rand/rib-massa aan het uiteinde. In `Notebook 3 - Overdekking` is die
waarde bewust niet dezelfde: daar wordt de equivalente K-last opnieuw berekend
uit de massa van de voorbalk, de doekmassa en het beslag. De huidige
overdekkingscase heeft daardoor een veel grotere equivalente massa in K,
ongeveer `27.32 kg` per mechanisme.

Dat verschil is dus geen inconsistentie:

- Notebook 2: basismechanisme, lichte equivalente payload;
- Notebook 3: zelfde basismechanisme met zwaartekracht en wrijving;
- Notebook 3 - Overdekking: finale brede toepassing met eigen massamodel.

Mondelinge kernzin:

> De stang- en schuivermassa's blijven gelijk, maar de massa aan K verandert
> omdat de finale overdekking een zware voorbalk en doek draagt.

### 4.3 Massatraagheidsmomenten

De stangen worden als uniforme slanke staven gemodelleerd. Voor een staaf met
massa `m_i` en lengte `L_i` is het massatraagheidsmoment rond het zwaartepunt:

```math
J_{cg,i} = \frac{1}{12} m_i L_i^2
```

De schuiver heeft in dit model geen rotatie:

```math
J_2 = 0
```

Mondelinge kernzin:

> Massa beïnvloedt Notebook 2 alleen via de inertietermen. De kinematica zelf
> verandert niet als we de massa groter maken, zolang we het traject opgelegd
> houden.

## 5. Starre-lichaamkinematica voor de zwaartepunten

Newton-Euler gebruikt de versnelling van het zwaartepunt van elk lichaam.
Notebook 1 geeft vooral gewrichtspunten en hoeken. Daarom reconstrueert
Notebook 2 eerst de zwaartepuntkinematica.

Voor een punt `P` op een star lichaam, met referentiepunt `O`:

```math
\mathbf{v}_P = \mathbf{v}_O + \boldsymbol{\omega} \times \mathbf{r}_{OP}
```

```math
\mathbf{a}_P =
\mathbf{a}_O
+ \boldsymbol{\alpha} \times \mathbf{r}_{OP}
- \omega^2 \mathbf{r}_{OP}
```

In 2D wordt het kruisproduct geschreven met een loodrechte vector:

```math
\mathrm{perp}(x,y)=(-y,x)
```

Dus:

```math
\mathbf{v}_P = \mathbf{v}_O + \omega \, \mathrm{perp}(\mathbf{r}_{OP})
```

```math
\mathbf{a}_P =
\mathbf{a}_O
+ \alpha \, \mathrm{perp}(\mathbf{r}_{OP})
- \omega^2 \mathbf{r}_{OP}
```

De versnelling bestaat uit:

- translatie van het referentiepunt;
- tangentiële term door hoekversnelling;
- centripetale term door hoeksnelheid.

Die laatste term is kwadratisch in `omega`. Daarom kunnen snelle trajecten de
inertiekrachten sterk verhogen.

## 6. Newton-Euler vergelijkingen per bewegende link

Voor elke bewegende link worden drie vergelijkingen opgesteld:

```math
\sum F_x = m a_{cg,x}
```

```math
\sum F_y = m a_{cg,y}
```

```math
\sum M_{cg} = J_{cg}\alpha
```

Er zijn zeven bewegende links:

- link 2: schuiver;
- link 3 tot en met 8: stangen.

Dus:

```text
7 links x 3 vergelijkingen = 21 vergelijkingen
```

De onbekenden zijn:

| Onbekende | Betekenis |
|---|---|
| `R_Ax` | horizontale reactie van de schuivergeleiding |
| `F_act_y` | verticale actuator-/aandrijfkracht op de schuiver |
| `M_A` | geleidingsmoment op de schuiver |
| `C_x`, `C_y` | frame-reactie in punt C |
| `B_x`, `B_y` | gewrichtskracht in B |
| `D_x`, `D_y` | gewrichtskracht in D |
| `E_x`, `E_y` | gewrichtskracht in E |
| `F_x`, `F_y` | gewrichtskracht in F |
| `G_x`, `G_y` | gewrichtskracht in G |
| `H_x`, `H_y` | gewrichtskracht in H |
| `I_x`, `I_y` | gewrichtskracht in I |
| `J_x`, `J_y` | gewrichtskracht in J |

Samen geeft dat 21 onbekenden. Per tijdstap wordt dus een vierkant lineair stelsel
opgelost:

```math
A_k \mathbf{w}_k = \mathbf{b}_k
```

met:

- `A_k`: coëfficiëntenmatrix van de onbekende krachten en momenten;
- `w_k`: vector met de 21 onbekenden;
- `b_k`: bekende inertietermen uit massa en versnelling.

## 7. Tekenconventie en actie-reactie

Een gewrichtskracht werkt op de ene link met tegengesteld teken als op de andere
link. Dat is de derde wet van Newton:

```math
\mathbf{F}_{i \rightarrow j} = -\mathbf{F}_{j \rightarrow i}
```

Dit is belangrijk omdat interne krachten dan verdwijnen wanneer je alle links
samen als één systeem bekijkt. Alleen externe krachten blijven over:

- kracht van de schuivergeleiding;
- actuatorkracht;
- frame-reactie in C.

Die eigenschap wordt later gebruikt als globale controle.

Mondelinge kernzin:

> De gewrichtskrachten zijn intern. Ze kunnen lokaal groot zijn, maar voor de
> globale onbalans vallen ze per actie-reactie-paar weg.

## 8. Dynamische controle

Notebook 2 controleert de oplossing op twee manieren.

### 8.1 Residu van het lineaire stelsel

Na het oplossen van:

```math
A\mathbf{w}=\mathbf{b}
```

wordt gecontroleerd of:

```math
A\mathbf{w}-\mathbf{b} \approx 0
```

De huidige maximale restfout is ongeveer:

```text
6.2e-16
```

Dat betekent dat het lineaire stelsel numeriek zeer goed is opgelost.

### 8.2 Globale kracht- en momentcontrole

Als alle bewegende links samen beschouwd worden, vallen de interne
gewrichtskrachten weg. De som van externe krachten moet dan gelijk zijn aan de
som van alle inertiekrachten:

```math
\mathbf{F}_A + \mathbf{F}_C = \sum_i m_i \mathbf{a}_{cg,i} + m_K \mathbf{a}_K
```

Voor het moment rond punt `C` geldt hetzelfde:

```math
M_A + \mathbf{r}_{CB} \times \mathbf{F}_A
=
\sum_i
\left(
\mathbf{r}_{C,cg_i} \times m_i \mathbf{a}_{cg,i}
+ J_i \alpha_i
\right)
+ \mathbf{r}_{C,K} \times m_K \mathbf{a}_K
```

Als deze controle niet klopt, is er meestal een tekenfout, een verkeerd
aangrijpingspunt of een ontbrekende puntmassa.

## 9. Energiebalans

Omdat Notebook 2 geen zwaartekracht en geen wrijving bevat, is het een
inertie-only systeem. Dan moet het actuatorvermogen gelijk zijn aan de afgeleide
van de kinetische energie:

```math
P_\text{act}(t) = \frac{dE_\text{kin}}{dt}
```

In de code wordt het actuatorvermogen berekend als:

```math
P_\text{act} = F_s \dot{s}
```

Let op: dit is `F_s * ds`, niet `F_s * s`.

- `F_s` is de gegeneraliseerde aandrijfkracht in de schuiverrichting `s`;
- `\dot{s}` of `ds` is de schuiversnelheid;
- kracht maal snelheid is vermogen.

De eenheden tonen dat:

```math
N \cdot \frac{m}{s} = \frac{J}{s} = W
```

De kinetische energie is:

```math
E_\text{kin}
=
\sum_i
\left(
\frac{1}{2}m_i v_{cg,i}^2
+ \frac{1}{2}J_i\omega_i^2
\right)
+ \frac{1}{2}m_K v_K^2
```

De energiebalans is belangrijk omdat ze onafhankelijk controleert of de
aandrijfkracht fysisch klopt. Als de krachtbalans per link juist lijkt, maar de
energiebalans niet klopt, is er vaak een tekenfout in de aandrijfkracht of
vermogensberekening.

Deze controle is dus geen extra ontwerpbelasting, maar een validatie:

- de Newton-Euler-oplossing geeft een kracht `F_s`;
- die kracht levert of absorbeert vermogen via `F_s ds`;
- dat vermogen moet exact overeenkomen met de verandering van kinetische energie
  zolang er geen zwaartekracht, wrijving of veerenergie in het model zit.

Huidige maximale fout in de energiebalans:

```text
ongeveer 1.1e-4 W
```

Dat is klein en vooral numeriek, omdat `dE_kin/dt` met eindige differenties wordt
benaderd.

## 10. Aandrijfkracht en tekenconventie

In de notebook is `F_act_y` de verticale kracht van de actuator op de schuiver in
het globale assenstelsel. De schuivercoördinaat `s` is positief naar beneden,
terwijl `y` positief naar boven is.

Daarom geldt:

```math
F_s = -F_{act,y}
```

Met deze definitie is `F_s` de gegeneraliseerde aandrijfkracht in positieve
`s`-richting.

Het vermogen is:

```math
P = F_s \dot{s}
```

Bij openen is `ds < 0`, want de schuiver gaat omhoog. Het teken van `P` zegt of
de actuator energie levert of energie opneemt.

## 11. Onbalansanalyse

Notebook 2 berekent ook de inertiële onbalans. Dat is de kracht en het moment
dat het bewegende mechanisme op het vaste frame uitoefent door versnellingen.

De onbalanskracht wordt gedefinieerd als:

```math
\mathbf{F}_\text{shak} = -(\mathbf{F}_A+\mathbf{F}_C)
```

Het onbalansmoment rond `C`:

```math
M_\text{shak,C} = -M_\text{ext,C}
```

Interpretatie:

- als de massa's snel versnellen, krijgt het frame een inertiële reactie;
- bij een rustig traject zijn die reacties klein;
- dit is niet hetzelfde als de totale framebelasting in Notebook 3 of de
  overdekkingsnotebook.

Wat gebeurt er fysisch bij grote onbalans?

Een mechanisme met grote onbalanskrachten trekt en duwt periodiek aan zijn frame.
Dat kan leiden tot:

- trillingen van de mast of machinebasis;
- lawaai;
- vermoeiingsbelasting in bouten en lagers;
- slechtere positioneringsnauwkeurigheid;
- nood aan zwaardere fundering of demping.

Grote onbalans ontstaat vooral door:

- hoge versnellingen;
- grote bewegende massa's;
- massa's ver van het steunpunt;
- asymmetrische beweging;
- periodieke beweging op hoge snelheid.

Typische maatregelen in machines zijn:

- beweging trager of vloeiender maken;
- massa's lichter maken;
- symmetrische mechanismen gebruiken zodat inertiekrachten elkaar opheffen;
- contramassa's of balancering toevoegen;
- stijver frame of dempers voorzien;
- aandrijfprofiel aanpassen om piekversnellingen te verlagen.

Bij ons is dit veel minder kritisch, omdat de beweging traag is en niet continu
hoogfrequent cyclisch draait. Daarom is de inertiële onbalans klein. De
belangrijkste constructieve belasting komt later uit gewicht, wrijving,
voorbalklast, mastmoment en geleidingsreacties.

Huidige kernwaarden:

| Grootheid | Waarde |
|---|---:|
| Max inertiële onbalanskracht | `0.50 N` |
| Max inertieel onbalansmoment rond C | `0.81 Nm` |
| Max inertiële aandrijfkracht | `2.99 N` |

Daaruit volgt:

> De onbalans door inertie is in dit ontwerp geen hoofdprobleem. De latere
> zwaartekracht-, wrijvings- en structurele belastingen zijn veel belangrijker.

## 12. Actuatorvermogen en poelie-/riem-equivalent

Notebook 2 bevat een poelie-/riem-equivalent:

```text
drive_pulley_radius_nb2 = 0.025 m
drive_travel_per_rev_nb2 = 2*pi*drive_pulley_radius_nb2
```

De gekozen actuatorparameters zijn bewust eenvoudig:

```text
actuator_efficiency = 0.78
actuator_safety_factor = 1.50
```

`actuator_efficiency` is nodig omdat niet alle mechanische of elektrische
energie ideaal wordt omgezet. Bij een riem, poelie, reductiekast of motor gaat een
deel verloren door wrijving, lagers, tandwielen en elektrische verliezen. Voor
een eerste inertie-only vergelijking met riem en reductie gebruiken we `0.78`.

De veiligheidsfactor `1.50` voorkomt dat de piekwaarde exact als ontwerpwaarde
wordt genomen. In Notebook 4 wordt voor de finale aandrijving conservatiever
gewerkt met grotere veiligheidsfactoren.

Dat betekent:

```math
n = \frac{\dot{s}}{p}
```

waarbij `p` de riemverplaatsing per poelieomwenteling is. Het motorkoppel volgt
uit vermogensbehoud:

```math
F_s \dot{s} = T \omega
```

Met:

```math
\omega = 2\pi \frac{\dot{s}}{p}
```

krijg je:

```math
T = \frac{F_s p}{2\pi \eta}
```

Belangrijke interpretatie:

- deze sectie is een equivalent/vergelijking, geen definitieve motorarchitectuur;
- ze is nuttig om de relatie tussen lineaire kracht, poelieradius, toerental en koppel
  te begrijpen;
- de finale implementatie gebruikt Notebook 4 met motor, rem,
  riem/kabel/poelie en eventueel gemeenschappelijke aandrijfas.

Dus: de actuatorparameters in Notebook 2 zijn niet gekozen als definitieve
motorselectie. Ze dienen om de lesformules voor vermogen, koppel,
rendement en toerental te koppelen aan de inertie-only kracht.

Mondelinge kernzin:

> Het poelie-/riem-equivalent in Notebook 2 is een theoretische inertie-only actuatoromrekening. Onze
> finale ontwerpkeuze staat in Notebook 4.

## 13. Arbeids-surplus volgens Les 4

Les 4 introduceert het arbeids-surplus als maat voor de energie die tijdelijk
moet worden opgeslagen of geleverd bovenop het gemiddelde vermogen.

De notebook berekent:

```math
A(t)=\int_0^t (P(\tau)-\bar{P})\,d\tau
```

en:

```math
A_\text{max} = \max(A)-\min(A)
```

Voor Notebook 2 is dit inertie-only. De actuele waarde is:

```text
A_max = 0.254 J
```

Dat is zeer klein. Dat past bij het rustige traject en de lage inertiekrachten.

Belangrijke nuance:

- `A_max` is geen vaste eigenschap van het mechanisme alleen;
- het hangt af van de gekozen bewegingswet;
- sneller openen verhoogt de vermogenspieken en dus ook de energiebufferbehoefte;
- zwaartekracht en wrijving veranderen het beeld sterk, dus Notebook 4 is nodig
  voor de finale motoranalyse.

## 14. Waarom zijn de krachten zo klein?

De inertiekracht schaalt met massa en versnelling:

```math
F_\text{inertie} \sim m a
```

Voor rotatie geldt analoog:

```math
M_\text{inertie} \sim J \alpha
```

Omdat het traject bewust rustig is:

- `max |ds| = 0.1965 m/s`;
- `max |dds| = 0.0511 m/s^2`;

zijn de inertiekrachten klein. Dit betekent niet dat de motor nauwelijks belast
wordt. Het betekent alleen dat de dynamische versnellingstermen klein zijn.

In Notebook 3 komen er extra termen bij:

- gewicht van alle massa's;
- schuiverwrijving;
- pinwrijving;
- statische houdkracht.

In de overdekkingsnotebook komt daar nog bij:

- zwaardere voorbalk;
- doekmassa;
- structurele balkbelasting;
- mast- en schuiverbelasting.

Daarom is Notebook 2 vooral een basis- en validatienotebook.

## 15. Relatie met Notebook 3, trekveren, overdekking en Notebook 4

De notebookketen is:

1. Notebook 1 bepaalt de kinematica.
2. Notebook 2 voert inverse dynamica uit zonder zwaartekracht en wrijving.
3. Notebook 3 gebruikt dezelfde Newton-Euler-aanpak, maar voegt zwaartekracht en
   wrijving toe.
4. `Notebook 3 - Trekveren` voegt een bekende veerkracht op de schuiver toe.
5. `Notebook 3 - Overdekking` gebruikt dezelfde dynamische methode voor de
   bredere eindtoepassing met eigen massa's voor doek en voorbalk.
6. Notebook 4 leest een Notebook-3-loadcase en dimensioneert motor, rem, riem,
   poelie en aandrijfas.

Belangrijk:

> De kinematica blijft dezelfde als de massa's veranderen, omdat de beweging
> opgelegd wordt. De massa's veranderen de krachten, niet de geometrische baan.

Bij een echte machine zou een te zwakke motor de opgelegde baan misschien niet
kunnen volgen. In de analyse nemen we de baan eerst als doel op en berekenen we
daarna welke motor nodig is.

## 16. Wat moet je kunnen verdedigen?

### Waarom inverse dynamica?

Omdat we het traject kennen en willen weten welke krachten nodig zijn om dat
traject te volgen.

### Waarom eerst zonder zwaartekracht en wrijving?

Omdat het een zuivere basiscontrole is:

- de Newton-Euler-opbouw wordt getest;
- de inertiebijdrage wordt apart zichtbaar;
- latere notebooks kunnen zwaartekracht en wrijving daar logisch bovenop zetten.

### Waarom een puntmassa in K?

Omdat punt `K` een nuttige representatie is van massa die aan het uiteinde wordt
meegenomen. Die massa heeft een eigen versnelling en veroorzaakt ook een moment
op link 8.

### Waarom is Notebook 2 niet de finale motorbelasting?

Omdat zwaartekracht en wrijving ontbreken. De inertiekracht is ongeveer `2.99 N`,
terwijl de finale overdekkingslasten honderden newton per mechanisme zijn.

### Waarom zijn de onbalanskrachten klein?

Omdat het traject traag is en de versnellingen klein zijn. De grootste
constructieve zorgen liggen daarom niet bij inertiële onbalans, maar bij
zwaartekracht, wrijving, mastmoment, voorbalkdoorbuiging en aandrijfhouding.

### Wat bewijst de energiebalans?

Ze bewijst dat het actuatorvermogen consistent is met de verandering van
kinetische energie. Dat is een onafhankelijke fysische validatie van de
inverse-dynamicaresultaten.

### Hoe wordt de dynamische piekkracht bepaald?

De notebook kijkt naar de tijdstappen waarin het mechanisme echt beweegt, dus
waar `ds` of `dds` niet nul is. Daarbinnen wordt de grootste absolute waarde van
de inertiële aandrijfkracht genomen:

```math
F_\text{dyn,piek} = \max |F_s|
```

Voor de huidige hoofdcase is dat ongeveer `2.99 N`. Die waarde is klein omdat
Notebook 2 alleen inertie bevat en het traject traag is.

### Wat is het verschil tussen geselecteerde reactiekrachten en shaking forces?

De geselecteerde reactiekrachten, zoals `R_A`, `R_C`, `R_E` en `R_J`, zijn lokale
krachten in bepaalde verbindingen. Die worden geplot omdat ze nuttig en leesbaar
zijn.

De shaking force is globaal: ze is de totale inertiële kracht die het bewegende
mechanisme op het vaste frame uitoefent. Daarvoor worden alle massa's via de
Newton-Euler-oplossing meegenomen. Interne gewrichtskrachten vallen in die
globale som weg door actie-reactie.

## 17. Typische valkuilen

- `F_drive_s` en `F_act_y` hebben een tegengesteld teken door de keuze van `s`.
- Notebook 2 bevat geen zwaartekracht, dus stilstandskrachten zijn nul.
- Onbalans uit Notebook 2 is niet hetzelfde als totale framebelasting.
- De puntmassa in `K` moet ook in het momentenevenwicht zitten.
- De poelie-/riemsectie is niet de finale aandrijfarchitectuur.
- Massa's veranderen de krachten, niet de opgelegde kinematica.
- Grote reacties in latere notebooks komen vooral door zwaartekracht en
  geometrische hefboomwerking, niet door Notebook-2-inertie.

## 18. Korte samenvatting

Notebook 2 zet de kinematica van Notebook 1 om naar inertiële krachten via
Newton-Euler. Per bewegende link worden drie vergelijkingen opgesteld, samen
`21` vergelijkingen voor `21` onbekenden. De oplossing geeft gewrichtskrachten,
actuatorkracht, frame-reacties, onbalans en vermogen.

De huidige resultaten tonen dat de inertie zeer klein is door het rustige
traject: de maximale inertiële aandrijfkracht is ongeveer `2.99 N`. Daardoor is
Notebook 2 vooral belangrijk als methodische basis en validatie. De echte
belasting van de paraplu-overdekking komt pas wanneer zwaartekracht, wrijving,
zwaardere payloads, trekveren en de motorarchitectuur worden toegevoegd in de
latere notebooks.
