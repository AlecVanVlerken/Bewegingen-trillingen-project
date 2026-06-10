# Notebook 3 - Overdekking - Studietekst

Deze tekst hoort bij `Notebook 3 - Overdekking.ipynb`. De notebook is de
uitbreiding van het enkelvoudige paraplumechanisme naar een brede
zonwerende overdekking met meerdere identieke mechanismen. De kinematica blijft
volledig afkomstig uit `Notebook 1.ipynb`, maar het massamodel, de belastingen
en de structurele controles worden aangepast aan de brede toepassing.

Dit document legt de analyse uit vanuit de theorie uit de lessen. Het is geen
codecel-per-codecel uitleg. De nadruk ligt op:

- wat de notebook mechanisch modelleert;
- welke theorie erachter zit;
- welke aannames zijn gemaakt;
- welke resultaten belangrijk zijn voor de verdediging;
- welke beperkingen je expliciet moet benoemen.

## 0. Actuele hoofdcase

De huidige hoofdcase is een brede overdekking met twee identieke mechanismen.
De voorkeursvariant voor de aandrijving is de loadcase met trekveren, maar de
baseline zonder trekveren blijft altijd de referentie.

| Grootheid | Actuele waarde |
|---|---:|
| Breedte overdekking | `6.00 m` |
| Aantal mechanismen | `2` |
| Afstand tussen mechanismen | `6.00 m` |
| Uitval/diepte uit Notebook 1 | `2.22 m` |
| Schuivertraject bij openen | `s = 1.875 m -> 0.600 m` |
| Effectieve beweegtijd | `20.00 s` |
| Voorbalkprofiel | aluminium koker `200x100x5 mm` |
| Voorbalkmassa | `46.98 kg` totaal |
| Doekmassa | `4.66 kg` totaal |
| Equivalente puntmassa in K | `27.32 kg` per mechanisme |
| Totale bewegende modelmassa | `31.82 kg` per mechanisme |
| Schuiverwrijving | `mu_slider = 0.08` |
| Statische schuiverwrijving | `mu_slider_static = 0.12` |
| Pinwrijving | `mu_pin = 0.05`, `r_pin = 6 mm` |
| Max `|F_s|` inertie | `21.96 N` per mechanisme |
| Max `|F_s|` zwaartekrachtcomponent zonder veer | `318.26 N` per mechanisme |
| Max `|F_s|` wrijvingcomponent | `84.63 N` per mechanisme |
| Max `|F_s|` totaal zonder veer | `372.24 N` per mechanisme |
| Max `|F_s|` totaal met trekveren | `199.48 N` per mechanisme |
| Max houdkracht zonder veer | `296.33 N` per mechanisme |
| Max houdkracht met trekveren | `115.65 N` per mechanisme |
| Max lokale schuiverreactie `|R_Ax|` | `1.04 kN` per mechanisme |
| Max indicatief mastmoment `|R_Ax| s` | `625 Nm` |
| Maatgevende voorbalkcase | `wind uplift` |
| Max voorbalkdoorbuiging weercases | `14.98 mm` |
| Max voorbalkbenutting | `0.75` |

Belangrijke interpretatie:

- de overdekking gebruikt dezelfde kinematische baan als Notebook 1;
- de massa's zijn veel groter dan in het enkelvoudige mechanisme;
- daardoor stijgen zwaartekracht, wrijving en inertie;
- zwaartekracht en wrijving domineren nog steeds;
- de trekveren verlagen vooral de verticale aandrijfkracht en houdkracht;
- de horizontale schuiverreactie blijft een belangrijke constructieve belasting;
- de riem draagt die horizontale kracht niet.

Mondelinge kernzin:

> `Notebook 3 - Overdekking` gebruikt dezelfde Newton-Euler-aanpak als Notebook
> 3, maar vervangt het kleine puntmassamodel door een brede overdekking met
> voorbalk, doek, meerdere mechanismen, weerbelasting, mastcontrole en optionele
> trekveren.

## 1. Plaats in de notebookketen

De notebookketen is bewust stapsgewijs opgebouwd:

```text
Notebook 1
  -> kinematica: s, ds, dds, hoeken, snelheden, versnellingen, cond(A)

Notebook 2
  -> inertie-only inverse dynamica
  -> Newton-Euler-opbouw, onbalans, massa's, inertiecontrole

Notebook 3
  -> enkel mechanisme met zwaartekracht en wrijving
  -> echte baselinebelasting voor het enkelvoudige mechanisme

Notebook 3 - Trekveren
  -> zelfde als Notebook 3, maar met bekende trekveerkracht op de schuiver

Notebook 3 - Overdekking
  -> finale brede loadcase met eigen massa's, voorbalk, doek, weercontrole,
     mastbelasting, open/sluitanalyse en optionele trekveren

Notebook 4
  -> motor, rem, poelie, riem, gemeenschappelijke as, kosten en energie
```

Belangrijk:

- `Notebook 3 - Overdekking` leest de kinematica uit Notebook 1;
- de overdekking gebruikt een eigen massamodel, omdat het geen kleine parasol
  meer is;
- de Newton-Euler-logica is dezelfde als in Notebook 2 en Notebook 3;
- Notebook 4 kan de `.npz`-output van deze notebook direct gebruiken.

Als de geometrie of het traject in Notebook 1 wordt aangepast, moet de keten
opnieuw worden gerund. Dan volgen `s`, `ds`, `dds`, de linkhoeken en alle
afgeleide krachten automatisch mee.

## 2. Link met de lessen

| Lesonderdeel | Theorie | Toepassing in deze notebook |
|---|---|---|
| Kinematica | Positie-, snelheids- en versnellingsanalyse | Alle bewegingen komen uit Notebook 1. Massa verandert de kinematica niet. |
| Inverse dynamica | Beweging gekend, krachten onbekend | Per tijdstap wordt `A w = b` opgelost. |
| Newton-Euler | `Sigma Fx = m ax`, `Sigma Fy = m ay`, `Sigma M_cg = J alpha` | Voor elke bewegende link worden de drie vergelijkingen opgesteld. |
| Actie-reactie | Interne gewrichtskrachten komen paarsgewijs voor | Scharnierkrachten tussen links worden als onbekenden opgelost. |
| Bekende externe krachten | Zwaartekracht, veer, wrijving | Deze krachten komen in het rechterlid van het stelsel. |
| Vermogen | `P = F_s dot{s}` | Actuatorvermogen wordt uit aandrijfkracht en schuiversnelheid berekend. |
| Arbeid/energie | `E = int P dt` | Cumulatieve arbeid en energiebalans controleren de berekening. |
| Coulombwrijving | Wrijvingskracht evenredig met normaalkracht | Schuiverwrijving hangt af van `|R_Ax|`, pinwrijving van scharnierreacties. |
| Balktheorie | `V`, `M`, `sigma`, `delta` voor een ligger | De voorbalk wordt als eenvoudig opgelegde ligger gecontroleerd. |
| Torsie | Torsiemoment door excentrische belasting | Wind/weerbelasting met excentriciteit geeft torsie en twist. |

De belangrijkste theoretische stap blijft dezelfde als in Notebook 3:

```math
A w =
b_\text{inertie}
+ b_\text{zwaartekracht}
+ b_\text{wrijving}
+ b_\text{veer}
```

waarbij de veerterm alleen aanwezig is in de optionele trekveerloadcase.

## 3. Waarom een aparte overdekkingsnotebook?

De enkelvoudige Notebook 3 is correct voor een enkel mechanisme met een beperkte
puntmassa in `K`. De finale toepassing is zwaarder:

- er is een brede voorbalk tussen de twee uiteinden;
- het doek heeft massa en weerbelasting;
- de constructie moet over 6 m breedte werken;
- twee mechanismen worden parallel aangedreven;
- mast, schuiver en muur-/framebevestiging worden belangrijker;
- de motorselectie moet de totale belasting van beide mechanismen meenemen.

Daarom is een aparte overdekkingsnotebook nodig. Anders zou Notebook 3 te veel
verschillende doelen tegelijk krijgen.

Mondelinge kernzin:

> De overdekkingsnotebook is een loadcase bovenop dezelfde dynamische methode,
> niet een nieuwe kinematica.

## 4. Kinematica blijft dezelfde

De beweging wordt opgelegd door Notebook 1. De notebook gebruikt:

- `t`;
- `s`;
- `ds`;
- `dds`;
- linkhoeken;
- hoeksnelheden;
- hoekversnellingen;
- punttrajecten zoals `K`.

Dat betekent:

- zwaardere massa's veranderen de kinematica niet;
- zwaardere massa's veranderen wel de krachten;
- als `s_open`, `s_closed`, linklengtes of trajecttijd veranderen, verandert de
  kinematica pas nadat Notebook 1 opnieuw is gerund;
- de overdekkingsnotebook volgt die nieuwe kinematica daarna automatisch.

Voor de huidige hoofdcase:

```text
s_closed = 1.875 m
s_open   = 0.600 m
t_move   = 20.00 s
```

De open stand is bewust niet volledig horizontaal. De kap blijft ongeveer
hellend. Dat is realistischer voor een zonwering en verlaagt de horizontale
schuiverreactie sterk ten opzichte van de oude bijna horizontale open stand.

## 5. Massamodel van de overdekking

### 5.1 Voorbalk

De voorbalk verbindt de uiteinden van de overdekking over de breedte. De huidige
keuze is:

```text
front_beam_profile = 200x100x5 mm
materiaal = aluminium
front_beam_mass_total = 46.98 kg
```

Waarom zo zwaar?

Eerdere lichtere profielen waren gunstiger voor motorkracht, maar structureel
te slap voor 6 m overspanning en wind/uplift. De voorbalk is dus niet alleen een
massaterm: hij moet de breedte mechanisch haalbaar maken.

### 5.2 Doek

Het doek krijgt een oppervlaktemassa:

```text
fabric_areal_density = 0.35 kg/m^2
fabric_mass_total = 4.66 kg
```

Dat is een realistische orde voor technisch zonweringsdoek. In werkelijkheid is
het doek verdeeld over het oppervlak en deels gedragen door de interne ribben.
Voor de motorbelasting wordt conservatief een instelbaar deel van de doekmassa
naar de K-punten gebracht.

### 5.3 Equivalente puntmassa in K

Per mechanisme wordt een equivalente puntmassa gebruikt:

```math
m_{K,\text{eq}}
= \frac{m_\text{voorbalk}}{N_\text{mech}}
+ f_\text{doek naar K}
\frac{m_\text{doek}}{N_\text{mech}}
+ m_\text{beslag per K}
```

Met de huidige waarden:

```text
payload_mass_K_equivalent = 27.32 kg per mechanisme
```

Dat is veel hoger dan de `3 kg` uit het enkelvoudige basismodel. Dat is logisch,
want de voorbalk is nu de dominante massa.

Belangrijk:

- dit is conservatief voor motorbelasting;
- het is geen exacte eindige-elementenverdeling van het doek;
- de structurele balkcontrole wordt apart gedaan;
- als later wordt gekozen dat minder doekmassa effectief op `K` werkt, kan
  `fabric_mass_to_K_fraction` worden verlaagd.

## 6. Structurele controle van de voorbalk

De voorbalk wordt als eerste orde gemodelleerd als een eenvoudig opgelegde balk
tussen de mechanismen.

Voor een gelijkmatig verdeelde lijnlast `q` over overspanning `L`:

```math
V_\text{max} = \frac{qL}{2}
```

```math
M_\text{max} = \frac{qL^2}{8}
```

```math
\delta_\text{max}
= \frac{5 q L^4}{384 E I}
```

De buigspanning wordt geschat met:

```math
\sigma = \frac{M}{W}
```

waar `W` de sectiemodulus is. Door excentrische belasting wordt ook torsie
meegenomen:

```math
T = q L e
```

waar `e` de belastingsexcentriciteit is.

### 6.1 Weerloadcases

De notebook bekijkt onder andere:

- eigengewicht + doek;
- regenwater `10 mm`;
- regenwater `25 mm`;
- sneeuw;
- wind neerwaarts;
- wind uplift.

De huidige ingestelde weerdrukken zijn onder andere:

| Grootheid | Waarde |
|---|---:|
| Windbasisnelheid | `26 m/s` |
| Windpiekdruk | `760.5 Pa` |
| Wind neerwaarts | `608.4 Pa` |
| Wind uplift | `-912.6 Pa` |
| Sneeuwdruk | `400 Pa` |

De maatgevende case is:

```text
wind uplift
```

Met de huidige voorbalk:

| Grootheid | Waarde |
|---|---:|
| Max doorbuiging weercases | `14.98 mm` |
| Doorbuigingslimiet `L/300` | `20.0 mm` |
| Max von Mises-benadering | `27.70 MPa` |
| Max twist | `0.034 deg` |
| Max benutting | `0.75` |
| Status | acceptabel binnen aannames |

Mondelinge kernzin:

> De balkcontrole is geen gecertificeerde Eurocodeberekening, maar ze toont dat
> het gekozen profiel als eerste orde verdedigbaar is voor de opgelegde
> weerloadcases.

### 6.2 Waarom niet alleen motorkracht bekijken?

De motoranalyse zegt of de schuivers kunnen bewegen. De balkanalyse zegt of de
overdekking structureel bruikbaar blijft.

Een lichtere voorbalk verlaagt de motorkracht, maar verhoogt doorbuiging,
spanning en torsie. De gekozen `200x100x5 mm` balk is dus een trade-off:

- nadeel: zwaarder, dus meer motorbelasting;
- voordeel: veel stijver, betere doorbuiging, betere windweerstand.

## 7. Inverse dynamica met overdekkingsmassa

De Newton-Euler-methode is dezelfde als in Notebook 2 en 3.

Per bewegende link:

```math
\sum F_x = m a_x
```

```math
\sum F_y = m a_y
```

```math
\sum M_{cg} = J_{cg}\alpha
```

Alle vergelijkingen worden per tijdstap samengebracht in:

```math
A w = b
```

De onbekenden `w` zijn:

- aandrijfkracht;
- steunreacties aan de schuiver en mast;
- interne scharnierkrachten;
- eventueel geleidmomenten.

De beweging is gekend. De krachten worden berekend. Dat is inverse dynamica.

## 8. Wrijving

De schuiverwrijving blijft dezelfde vorm als in Notebook 3:

```math
F_{\text{fric,slider},s}
= -\mu_{\text{slider}} |R_{Ax}|
\tanh\left(\frac{\dot{s}}{v_\epsilon}\right)
- c_{\text{slider}}\dot{s}
```

Met:

```text
mu_slider = 0.08
mu_slider_static = 0.12
c_slider = 0.0
```

Waarom `tanh`?

Een harde `sign(ds)` geeft een sprong bij nul snelheid. Numeriek is dat lastig,
zeker omdat de beweging start en eindigt met `ds = 0`. De `tanh`-benadering
maakt de overgang rond nul snelheid glad, maar gedraagt zich bij grotere
snelheden vrijwel als `sign(ds)`.

De pinwrijving wordt als remmend moment gemodelleerd:

```math
M_\text{pin}
= \mu_\text{pin} r_\text{pin} |R_\text{joint}|
\tanh\left(\frac{\omega_\text{rel}}{\omega_\epsilon}\right)
```

Met:

```text
mu_pin = 0.05
pin_radius = 6 mm
```

Waarom iteratief?

Wrijving hangt af van reactiekrachten. Die reactiekrachten zijn zelf pas bekend
nadat het stelsel is opgelost. Daarom wordt:

1. eerst opgelost zonder of met vorige wrijvingsschatting;
2. uit die oplossing worden reactiekrachten bepaald;
3. daaruit wordt nieuwe wrijving berekend;
4. het stelsel wordt opnieuw opgelost tot convergentie.

## 9. Openen en sluiten

De notebook rekent niet alleen de openingsrichting door. Ze bevat ook een
sluitanalyse:

- openen: `s = 1.875 -> 0.600 m`, dus `ds < 0`;
- sluiten: dezelfde baan omgekeerd, dus `ds > 0`;
- wrijving wisselt van richting;
- zwaartekracht verandert niet van richting;
- bij trekveren moet de veer bij sluiten opnieuw opgespannen worden.

Dit is belangrijk voor motorselectie. Een veer die openen helpt, kan sluiten
zwaarder maken. Daarom mag je niet alleen naar openen kijken.

Huidige waarden zonder trekveren:

| Grootheid | Waarde |
|---|---:|
| Max `|F_s|` openen | `372.24 N` per mechanisme |
| Max `|F_s|` sluiten | `309.24 N` per mechanisme |
| Max actuatorvermogen openen | `64.29 W` per mechanisme |
| Max actuatorvermogen sluiten | `51.40 W` per mechanisme |

Huidige waarden met trekveren:

| Grootheid | Waarde |
|---|---:|
| Max `|F_s|` openen | `199.48 N` per mechanisme |
| Max `|F_s|` sluiten | `115.16 N` per mechanisme |
| Max actuatorvermogen openen | `27.86 W` per mechanisme |
| Max actuatorvermogen sluiten | `13.95 W` per mechanisme |

In deze hoofdcase blijven trekveren dus gunstig in de worst-case
open/sluitvergelijking.

## 10. Krachtdecompositie

De notebook splitst de aandrijfkracht op in:

- inertie;
- zwaartekracht;
- wrijving;
- eventueel veerbijdrage.

Voor de overdekking zonder trekveren:

| Component | Max `|F_s|` per mechanisme |
|---|---:|
| Inertie | `21.96 N` |
| Zwaartekracht | `318.26 N` |
| Wrijving | `84.63 N` |
| Totaal | `372.24 N` |

Interpretatie:

- inertie is groter dan bij het enkelvoudige mechanisme, omdat de massa groter
  is;
- inertie blijft toch kleiner dan zwaartekracht en wrijving;
- trager bewegen verlaagt vooral inertie en piekvermogen;
- trager bewegen verlaagt de zwaartekrachtarbeid niet;
- minder wrijving of veerassistentie heeft meer invloed op de statische last.

Mondelinge kernzin:

> De overdekking wordt niet zwaar omdat ze snel beweegt, maar omdat ze massa
> tegen zwaartekracht en geleidingswrijving moet bewegen.

## 11. Houdkracht en rem

Omdat de overdekking in tussenstanden moet kunnen blijven staan, wordt ook een
statische houdanalyse gedaan.

Zonder trekveren:

```text
max |F_hold| = 296.33 N per mechanisme
```

Met trekveren:

```text
max |F_hold| = 115.65 N per mechanisme
```

De statische schuiverwrijving wordt wel berekend, maar ze wordt niet als veilige
rem gebruikt. Dat is belangrijk.

Waarom niet?

- wrijving verandert door vuil, vocht, slijtage en temperatuur;
- statische wrijving is onzeker;
- bij trillingen of wind kan de schuiver loskomen;
- een zonwering moet veilig in tussenstand blijven.

Daarom moet het ontwerp fysisch een rem, zelfremmende transmissie of
mechanische vergrendeling gebruiken. Notebook 4 vertaalt de houdkracht naar
rem-/uitgangskoppel.

## 12. Mast- en schuiverbelasting

De riem levert de verticale aandrijfkracht. De horizontale kracht uit het
mechanisme wordt niet door de riem gedragen.

De belangrijkste lokale schuiverreactie is:

```text
max |R_Ax| = 1.04 kN per mechanisme
```

Met veiligheidsfactor `2.0`:

```text
F_guide_design = 2.08 kN per mechanisme
```

Als vier rollen/glijblokken de belasting delen:

```text
ongeveer 0.52 kN per rol/glijblok
```

Dit is veel belangrijker dan het op het eerste gezicht lijkt. De motor hoeft
niet duizenden Newton horizontaal te leveren, maar de schuivergeleiding en mast
moeten die reactie wel lokaal opnemen.

### 12.1 Indicatief mastmoment

De notebook gebruikt een eerste-orde maat:

```math
M_\text{mast} \approx |R_{Ax}| s
```

Voor de huidige hoofdcase:

```text
M_mast,max = 625 Nm
```

Bij twee muur-/framebeugels met verticale afstand `1 m` geeft dat ruwweg:

```text
F_beugel = 626 N per steunpunt
```

De mastquick-check kiest bijvoorbeeld:

```text
100x100x5 mm staal
benutting = 0.070
```

Belangrijk:

- dit is een eerste orde check, geen volledige bouwkundige verankering;
- de mast moet momentvast verbonden worden;
- een enkele zwakke beugel is niet genoeg;
- twee beugels op afstand of een stijve frameconstructie is nodig.

Mondelinge kernzin:

> De riem lost alleen de verticale aandrijving op. Stabiliteit komt uit de
> schuivergeleiding, mast en momentvaste bevestiging.

## 13. Onbalans en framebelasting

Notebook 2 introduceert onbalans als inertieel effect. Bij de overdekking is het
belangrijk om onderscheid te maken tussen:

- inertiele onbalans;
- totale framebelasting;
- lokale schuiver- en mastreacties.

De totale framebelasting bevat ook:

- zwaartekracht;
- wrijving;
- steunreacties;
- eventueel veerreacties.

Daarom is framebelasting niet hetzelfde als onbalans. De inertie blijft relatief
klein, maar de constructieve reacties kunnen groot zijn door gewicht en
hefboomwerking.

Voor de overdekking zonder trekveren:

```text
max netto framekracht = 313.71 N
```

Met trekveren:

```text
max netto framekracht = 102.69 N
```

De lokale `R_Ax` blijft echter ongeveer `1.04 kN`, omdat de veer vooral
verticaal werkt.

## 14. Trekveeroptie

De notebook kan optioneel dezelfde trekveerlogica gebruiken als
`Notebook 3 - Trekveren.ipynb`.

De gekozen opstelling:

- twee trekveren per mechanisme;
- symmetrisch links en rechts van de collar;
- parallel aan de mast;
- bevestigd tussen vast frame en schuiver;
- niet in de riem;
- niet als geleiding.

De veerwet:

```math
F_\text{spring,up}
= F_\text{open}
+ k(s-s_\text{open})
```

Omdat `s` positief naar beneden is, levert een opwaartse veer een negatieve
bijdrage in `s`-richting.

Huidige waarden per mechanisme:

| Grootheid | Waarde |
|---|---:|
| Totale veerkracht open | `172.74 N` |
| Totale veerkracht gesloten | `198.24 N` |
| Directe totale veerstijfheid | `20.00 N/m` |
| Fysieke veerstijfheid per veer | `0.010 N/mm` |
| Fysieke veerweg over volledige slag | `1275 mm` |
| Fysieke veerkracht per veer | `86.37 -> 99.12 N` |
| Extra veerenergie gesloten-open | `236.50 J` |

Effect:

- openingskracht daalt van `372.24 N` naar `199.48 N`;
- houdkracht daalt van `296.33 N` naar `115.65 N`;
- motorpiek in Notebook 4 daalt van `593 W` naar `219 W`;
- motorclass daalt van `750 W` naar `500 W`;
- sluiten blijft in deze case niet maatgevend.

Trade-off:

- voordeel: lagere motorlast, lagere remvereiste, rustiger aandrijving;
- nadeel: extra onderdelen, montage, afstelling, slijtage en veerenergie bij
  sluiten;
- geen volledige compensatie kiezen, want dan wordt het systeem minder
  controleerbaar.

De fysieke veer is hier bewust wel een directe veer over de volledige
schuiverslag. Daarom is de veerconstante laag en moet de veer lang genoeg zijn
om ongeveer `1.275 m` extra rek te kunnen opnemen.

## 15. Motorlink met Notebook 4

Deze notebook berekent de mechanische belasting per mechanisme. Notebook 4 doet
de aandrijfselectie.

Voor de huidige overdekking met trekveren geeft Notebook 4:

| Grootheid | Waarde |
|---|---:|
| Totale ontwerplijnkracht | `797.91 N` |
| Aanbevolen motorpiekvermogen | `219 W` |
| Aanbevolen uitgangskoppel | `21.68 Nm` |
| Gekozen motorclass | `500 W` |
| Automatisch gekozen poelieradius | `25 mm` |

Zonder trekveren:

| Grootheid | Waarde |
|---|---:|
| Totale ontwerplijnkracht | `1488.96 N` |
| Aanbevolen motorpiekvermogen | `593 W` |
| Gekozen motorclass | `750 W` |

De motoropstelling is:

- motor beneden of beschermd aan de zijkant;
- overbrenging naar een hoge gemeenschappelijke aandrijfas;
- per mast een lokale tandriemlus;
- schuiver vast aan een aangedreven riemtak;
- retourtak loopt vrij;
- onderaan retour-/spanpoelie;
- bovenaan aandrijfpoelie op of gekoppeld aan de gemeenschappelijke as;
- rem of zelfremmende transmissie voor tussenstanden.

Belangrijk:

> De riem is geen schuivergeleiding. De riem levert `F_s`; de geleiding draagt
> `R_Ax` en het kantelmoment.

## 16. 3D-animatie

De notebook bevat een 3D-animatie met:

- twee identieke mechanismen;
- breedterichting tussen de mechanismen;
- voorbalk tussen de K-zones;
- transparant doekvlak;
- dezelfde 2D-kinematica uit Notebook 1.

Waarom nuttig?

- ze toont dat de overdekking geen losse enkelvoudige parasol meer is;
- ze maakt duidelijk dat de mechanismen parallel werken;
- ze helpt om de breedte, voorbalk en doek fysisch te begrijpen;
- ze ondersteunt vraag 9 over meerdere kopies van het mechanisme.

De animatie is visueel ondersteunend. De krachtanalyse zelf komt uit de
Newton-Euler-berekeningen.

## 17. Welke figuren zijn belangrijk?

Gebruik niet elke figuur in een verdediging. De nuttigste zijn:

| Thema | Figuur/sectie | Waarom tonen? |
|---|---|---|
| Massamodel | massa-opbouw doek/voorbalk/K-last | Toont waarom de K-massa veel groter is dan in het basismodel. |
| Voorbalk | profielvergelijking en weerbenutting | Verdedigt waarom `200x100x5` gekozen is. |
| Krachten | krachtdecompositie `F_s` | Toont inertie vs zwaartekracht vs wrijving. |
| Trekveer | zonder/met veer kracht en vermogen | Toont waarom de veer nuttig is. |
| Open/sluit | aandrijfkracht openen/sluiten | Controleert dat sluiten niet vergeten is. |
| Houdkracht | statische houdkracht en remvereiste | Antwoord op tussenstanden vasthouden. |
| Frame/mast | `R_Ax`, mastmoment, beugelkracht | Verdedigt schuivergeleiding en mastbelasting. |
| Voorbalkweer | benutting per loadcase | Antwoord op wind, regen, sneeuw. |
| 3D-animatie | overdekkingsanimatie | Maakt de finale toepassing visueel duidelijk. |

Redundante figuren zijn minder nuttig. Voor de verdediging moet elke figuur een
vraag beantwoorden:

- Waarom deze massa?
- Waarom deze voorbalk?
- Waarom deze motor?
- Waarom trekveren?
- Kan de mast de reacties opnemen?
- Wat gebeurt er bij wind?

## 18. Beperkingen van de analyse

Deze notebook is sterk genoeg voor een eerste ontwerpverdediging, maar niet voor
productie of certificatie.

Belangrijke beperkingen:

- windanalyse is Eurocode-geinspireerd, maar geen gecertificeerde
  Eurocodeberekening;
- asymmetrische windvlagen zijn niet volledig uitgewerkt;
- doekspanning en doekflapperen zijn vereenvoudigd;
- regenwaterophoping in een hoek is niet volledig gemodelleerd;
- mast-/muurankers zijn eerste-orde gecontroleerd, niet definitief
  gedimensioneerd;
- de voorbalk wordt per overspanning als eenvoudig opgelegde balk benaderd;
- de stangen worden star gemodelleerd;
- vermoeiing en corrosie zijn niet volledig behandeld;
- echte componentselectie vraagt datasheets voor motor, rem, riem, poelies,
  lagers, veren, rollen en profielen.

Mondeling moet je dus niet overclaimen. Zeg:

> De notebook geeft een verdedigbare eerste dimensionering en vergelijking van
> loadcases. Voor uitvoering is een aparte structurele en componentgerichte
> detailberekening nodig.

## 19. Typische mondelinge vragen

### Waarom twee mechanismen en geen drie?

Twee mechanismen houden de constructie eenvoudiger: minder schuivers, minder
riemen, minder lagers en eenvoudiger synchronisatie. Het nadeel is dat de
voorbalk 6 m overspant en dus stijver/zwaarder moet zijn. De voorbalkcontrole
toont dat `200x100x5 mm` als eerste orde haalbaar is. Drie mechanismen zouden
de balkbelasting en doorbuiging verlagen, maar maken aandrijving en montage
complexer.

### Waarom niet alle doekmassa exact verdelen over de ribben?

Dat zou realistischer zijn, maar ook veel complexer. Voor motorbelasting is de
huidige keuze conservatief: een groot deel van de doek- en voorbalkmassa wordt
naar K gebracht. Voor de voorbalk zelf wordt daarnaast een aparte
structurele balkcontrole gedaan.

### Waarom is de trekveer nuttig?

Omdat de belasting vooral quasi-statisch is. De veer compenseert een deel van
de zwaartekrachtlast, terwijl het traject hetzelfde blijft. Ze verlaagt de
motorpiek en houdkracht, maar maakt sluiten niet gratis.

### Waarom blijft `R_Ax` belangrijk met trekveren?

De trekveren werken verticaal. De grote horizontale reactie komt uit de
mechanismegeometrie en de krachten in de stangen. Daarom verlaagt de veer vooral
`F_s`, maar niet automatisch de lokale horizontale schuiverbelasting.

### Wat houdt de overdekking in tussenstand?

Niet de schuiverwrijving alleen. Een motorrem, zelfremmende transmissie of
mechanische vergrendeling moet de positie houden. Notebook 4 gebruikt de
houdkracht uit deze notebook om de remvereiste te bepalen.

### Wat als de overdekking sneller moet bewegen?

Dan stijgen inertie en piekvermogen. De zwaartekrachtarbeid verandert weinig.
Omdat het systeem typisch maar een paar keer per dag opent/sluit, is een rustig
traject verdedigbaar.

### Kan de constructie omvallen?

Niet als de mast momentvast wordt bevestigd. De notebook geeft een indicatief
mastmoment en beugelkrachten. De praktische oplossing is een stijve mast of
twee muur-/framebeugels met voldoende verticale afstand.

## 20. Kernsamenvatting

`Notebook 3 - Overdekking` vertaalt het stangenmechanisme naar een praktische
brede zonwering. De kinematica blijft uit Notebook 1, de inverse dynamica blijft
Newton-Euler zoals in Notebook 2 en 3, maar de belasting wordt realistischer:
voorbalk, doek, zwaartekracht, wrijving, open/sluit, mastbelasting,
weercontrole en trekveeroptie.

De belangrijkste conclusie is:

> De finale overdekking is mechanisch verdedigbaar als de zware voorbalk,
> schuivergeleiding, mastbevestiging en gemeenschappelijke aandrijving bewust
> worden ontworpen. De motorbelasting is met trekveren duidelijk lager, maar de
> riem mag nooit als geleiding voor de horizontale reacties worden gezien.
