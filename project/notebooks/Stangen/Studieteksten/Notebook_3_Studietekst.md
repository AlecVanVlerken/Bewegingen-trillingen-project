# Notebook 3 - Studietekst inverse dynamica met zwaartekracht en wrijving

Deze tekst hoort bij `Notebook 3.ipynb`. Het doel is dat je de notebook kan
uitleggen vanuit de theorie uit de lessen: wat verandert er ten opzichte van
Notebook 2, hoe worden zwaartekracht en wrijving toegevoegd, welke controles
bewijzen dat de dynamica klopt, en wat betekenen de resultaten mechanisch.

Dit document is geen codecel-per-codecel uitleg. De nadruk ligt op de
mechanische redenering en de link met de theorie. De codeuitleg kan later apart
gemaakt worden.

## 0. Actuele hoofdcase

Notebook 3 gebruikt de kinematica uit `notebook1_kinematica_results.npz` en het
massamodel uit `notebook2_dynamica_results.npz`. Voor de huidige finale
basiscase betekent dat:

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
| Statische schuiverwrijving | `mu_slider_static = 0.12` |
| Viscose schuiverwrijving | `c_slider = 0.0 Ns/m` |
| Pinwrijving | `mu_pin = 0.05`, `r_pin = 6 mm` |
| Max `|F_s|` inertie | `2.99 N` |
| Max `|F_s|` zwaartekrachtcomponent | `66.42 N` |
| Max `|F_s|` wrijvingcomponent | `12.80 N` |
| Max `|F_s|` totaal | `74.89 N` |
| Max houdkracht `|F_hold|` | `63.43 N` |
| Max actuatorvermogen | `13.38 W` |
| Max lokale schuiverreactie `|R_Ax|` | `157.86 N` |
| Max netto framekracht | `73.90 N` |
| Max dynamisch residu | `5.1e-14` |
| Verschil inertiecheck t.o.v. Notebook 2 | `0.0 N` |

Belangrijke interpretatie:

- Notebook 2 was inertie-only;
- Notebook 3 voegt zwaartekracht en wrijving toe;
- bij deze trage beweging is inertie klein;
- zwaartekracht is dominant;
- wrijving is kleiner dan zwaartekracht, maar niet verwaarloosbaar;
- Notebook 3 is de echte mechanische belasting voor het enkelvoudige
  paraplumechanisme.

Mondelinge kernzin:

> Notebook 3 gebruikt dezelfde Newton-Euler-methode als Notebook 2, maar met
> bekende krachten voor zwaartekracht en wrijving. Daardoor krijgen we de echte
> statische en quasi-statische belasting van het mechanisme.

## 1. Koppeling met de lessen

| Lesonderdeel | Theorie | Toepassing in Notebook 3 |
|---|---|---|
| Les 2 | Kinematica van mechanismen | Posities, snelheden en versnellingen komen volledig uit Notebook 1. |
| Les 3 | Newton-Euler per lichaam | Per link blijven `Sigma Fx = m ax`, `Sigma Fy = m ay`, `Sigma M_cg = J alpha` gelden. |
| Les 3 | Inverse dynamica | De beweging is gegeven; onbekende reacties en aandrijfkracht worden opgelost. |
| Les 3 | Bekende externe krachten | Zwaartekracht en wrijving worden als bekende krachten/momenten toegevoegd aan het stelsel. |
| Les 3 | Actie-reactie | Gewrichtskrachten blijven interne actie-reactieparen. |
| Les 4 | Vermogen en arbeid | `P = F_s ds`, cumulatieve arbeid en energiebalans worden gecontroleerd. |
| Les 4 | Arbeids-surplus | `A_max` wordt gebruikt als maat voor energiebuffer en motordimensionering. |
| Wrijving | Coulombwrijving | Schuiverwrijving en pinwrijving zijn evenredig met normaalkracht/reactiekracht. |

De belangrijkste leslink:

> De matrixstructuur van de inverse dynamica blijft dezelfde als in Notebook 2.
> Alleen het rechterlid verandert omdat er extra bekende krachten worden
> toegevoegd.

## 2. Plaats in de notebookketen

De keten is:

```text
Notebook 1
  -> kinematica: s, ds, dds, hoeken, hoeksnelheden, hoekversnellingen

Notebook 2
  -> inverse dynamica zonder zwaartekracht/wrijving
  -> massa's, inerties, inertie-only referentie

Notebook 3
  -> zelfde inverse dynamica
  -> extra zwaartekracht, schuiverwrijving en pinwrijving

Notebook 3 - Trekveren
  -> zelfde als Notebook 3, maar met bekende veerkracht op de schuiver

Notebook 3 - Overdekking
  -> zelfde dynamische methode, maar met eigen massa's voor doek en voorbalk

Notebook 4
  -> motor, rem, poelie, riem/kabel, aandrijfas en kosten
```

Notebook 3 is dus de referentie zonder trekveer. De trekveren- en
overdekkingsnotebooks bouwen inhoudelijk op dezelfde aanpak verder.

## 3. Wat verandert ten opzichte van Notebook 2?

Notebook 2 lost:

```math
A w = b_\text{inertie}
```

Notebook 3 lost:

```math
A w =
b_\text{inertie}
+ b_\text{zwaartekracht}
+ b_\text{wrijving}
```

De onbekenden blijven dezelfde:

- schuiverreactie;
- actuatorkracht;
- frame-reactie in C;
- interne gewrichtskrachten.

Wat verandert:

- alle links krijgen gewicht;
- puntmassa `K` krijgt gewicht;
- schuiverwrijving wordt toegevoegd;
- pinwrijving wordt toegevoegd;
- statische houdkracht wordt berekend;
- vermogen en energiebalans bevatten nu ook zwaartekracht- en wrijvingsarbeid.

Belangrijk:

> Bekende krachten gaan naar het rechterlid. Onbekende reacties blijven in de
> matrix `A`.

## 4. Massamodel

Notebook 3 neemt het massamodel over uit Notebook 2. Daardoor blijven massa's en
inerties exact consistent.

De stangmassa komt uit een fysisch profiel:

```text
ronde aluminium buis 30x2 mm
aluminiumdichtheid 2700 kg/m3
beslagtoeslag 0.075 kg/m
```

De doorsnede is:

```math
A = \frac{\pi}{4}(D_o^2-D_i^2)
```

en:

```math
\lambda = \rho A + \lambda_\text{beslag}
```

Daaruit volgt:

```text
line_mass_density = 0.550 kg/m
```

De puntmassa in `K` blijft in deze basiscase:

```text
payload_mass_K = 3.00 kg
```

Dat is niet de overdekkingsmassa. De overdekkingsnotebook berekent later een veel
grotere K-last uit voorbalk, doek en beslag.

## 5. Zwaartekracht

Zwaartekracht werkt op elke massa:

```math
\mathbf{F}_g = m \mathbf{g}
```

met:

```math
\mathbf{g} = \begin{bmatrix}0 \\ -9.81\end{bmatrix}
```

Voor een link geldt:

```math
\sum \mathbf{F}_\text{onbekend} + \mathbf{F}_g = m\mathbf{a}_{cg}
```

Dus:

```math
\sum \mathbf{F}_\text{onbekend}
= m\mathbf{a}_{cg} - \mathbf{F}_g
= m(\mathbf{a}_{cg}-\mathbf{g})
```

In de code zie je dit principe terug als `a_cg - g_vec` of bij punt `K` als:

```math
m_K(\mathbf{a}_K-\mathbf{g})
```

Waarom is dit belangrijk?

- in Notebook 2 was stilstandskracht nul;
- in Notebook 3 is stilstandskracht niet nul, omdat gewicht nog steeds werkt;
- daarom kan Notebook 3 ook houdkracht in tussenstanden berekenen.

## 6. Schuiverwrijving

De schuiver beweegt langs de mast. De horizontale geleidingsreactie `R_Ax`
veroorzaakt een normaalkracht op de geleiding. De Coulombwrijving is:

```math
F_{\text{fric,slider},s}
= -\mu_{\text{slider}} |R_{Ax}|
\tanh\left(\frac{\dot{s}}{v_\epsilon}\right)
- c_{\text{slider}}\dot{s}
```

Met de huidige waarden:

```text
mu_slider = 0.08
mu_slider_static = 0.12
c_slider = 0.0
```

Deze waarden zijn geen exacte catalogusselectie, maar realistische
ontwerpwaarden. Droge kunststof-op-staal lineaire geleidingen zitten typisch in
de orde `mu = 0.05-0.23`. Een ideale rollengeleiding kan lager zitten, maar bij
een buitenopstelling spelen uitlijning, vuil, voorspanning en afdichtingen mee.
Daarom is `0.08` een verdedigbare dynamische basiswaarde. Voor de statische
houdanalyse gebruiken we `0.12`, omdat losbreekwrijving meestal hoger is dan
glijwrijving. We rekenen echter niet op deze wrijving als veilige rem; daarvoor
blijft een rem of mechanische vergrendeling nodig.

Bronorde: igus geeft voor drylin/iglidur lineaire glijmaterialen tegen staal
waarden rond `0.05-0.23`; JTEKT/Koyo vermeldt dat plain bearings sterk afhangen
van toepassing en in sommige gevallen `0.1-0.2` kunnen bereiken; SKF geeft voor
composietglijlagers eveneens brede ordes rond `0.02-0.25`.

`tanh(ds/v_eps)` wordt gebruikt in plaats van een harde `sign(ds)`. Dat maakt de
overgang rond nul snelheid numeriek gladder.

Tekenconventie:

- `s` is positief naar beneden;
- openen betekent `ds < 0`;
- wrijving werkt altijd tegen de beweging;
- dus bij openen werkt schuiverwrijving in positieve `s`-richting.

Belangrijk voor ontwerp:

> De riem of actuator moet de verticale aandrijfkracht leveren. De horizontale
> normaalkracht `R_Ax` wordt door schuivergeleiding en mast gedragen, niet door
> de riem.

## 7. Pinwrijving

Scharnierwrijving wordt gemodelleerd als een remmend moment in elk draaipunt.
De grootte is:

```math
M_{\text{pin}}
= \mu_{\text{pin}} r_{\text{pin}} |R_{\text{joint}}|
\tanh\left(\frac{\omega_\text{rel}}{\omega_\epsilon}\right)
```

Met:

```text
mu_pin = 0.05
r_pin = 0.006 m
```

De relatieve hoeksnelheid is:

```math
\omega_\text{rel} = \omega_a - \omega_b
```

Pinwrijving werkt als moment, niet als kracht. In Newton-Euler komt ze dus in
de momentvergelijking:

```math
\sum M_{cg} = J\alpha
```

Scharnierwrijving is hier kleiner dan de zwaartekrachtterm. Toch is ze nuttig om
het model realistischer te maken, want in een echte paraplu zijn scharnieren
niet verliesloos.

## 8. Waarom is de wrijvingsoplossing iteratief?

De wrijving hangt af van reactiekrachten:

- schuiverwrijving hangt af van `|R_Ax|`;
- pinwrijving hangt af van `|R_joint|`.

Maar die reactiekrachten zijn juist onbekenden van het Newton-Euler-stelsel.
Daarom kan de notebook de wrijving niet in een keer exact kennen.

De methode:

1. los op zonder wrijving;
2. gebruik die reacties om wrijving te schatten;
3. los opnieuw op met die wrijving;
4. herhaal tot de oplossing niet meer verandert.

Dit is een vaste-puntsiteratie. De notebook gebruikt:

```text
friction_iterations = 20
friction_tol = 1e-8
```

Waarom is dit verdedigbaar?

- wrijving is een correctie op de hoofdbelasting;
- de koppeling is niet extreem sterk;
- de residuen blijven klein;
- de energiecontrole bevestigt de oplossing.

## 9. Oplossingscases

Notebook 3 lost meerdere cases op:

| Case | Zwaartekracht | Wrijving | Doel |
|---|---|---|---|
| `case_inertia_check` | nee | nee | vergelijken met Notebook 2 |
| `case_gravity` | ja | nee | effect van gewicht isoleren |
| `case_total` | ja | ja | volledige belasting |

Daaruit volgen:

```math
F_{s,\text{inertie}} = F_s(\text{zonder zwaartekracht/wrijving})
```

```math
F_{s,\text{zwaartekracht}}
= F_s(\text{met zwaartekracht}) - F_{s,\text{inertie}}
```

```math
F_{s,\text{wrijving}}
= F_s(\text{totaal}) - F_s(\text{met zwaartekracht})
```

Deze decompositie is nuttig voor de verdediging: je kan tonen welke fysische
term de motorbelasting veroorzaakt.

Voor de huidige basiscase:

| Bijdrage | Max `|F_s|` |
|---|---:|
| Inertie | `2.99 N` |
| Zwaartekrachtcomponent | `66.42 N` |
| Wrijvingcomponent | `12.80 N` |
| Totaal | `74.89 N` |

Conclusie:

> Het mechanisme is quasi-statisch. De inertie is klein; zwaartekracht en
> wrijving bepalen de nuttige aandrijfkracht.

## 10. Energiebalans

Notebook 3 controleert de volledige oplossing met vermogen:

```math
P_\text{act}
= \frac{dE_\text{kin}}{dt}
+ P_\text{zwaartekracht}
+ P_\text{wrijving}
```

Actuatorvermogen:

```math
P_\text{act}=F_s\dot{s}
```

Kinetische energie:

```math
E_\text{kin}
= \sum_i
\left(
\frac{1}{2}m_i v_{cg,i}^2
+ \frac{1}{2}J_i\omega_i^2
\right)
+ \frac{1}{2}m_K v_K^2
```

Zwaartekrachtvermogen:

```math
P_g = \sum_i m_i g \dot{y}_{cg,i} + m_K g \dot{y}_K
```

Wrijvingsvermogen:

```math
P_\text{fric}
= -F_{\text{fric,slider},s}\dot{s}
+ \sum |M_{\text{pin}}\omega_\text{rel}|
```

De huidige energiebalansfout is ongeveer:

```text
1.1e-4 W
```

Dat is klein en komt vooral door de numerieke afgeleide van kinetische energie.

## 11. Statische houdanalyse

In Notebook 2 was de holdkracht nul, want er was geen zwaartekracht. In Notebook
3 is dat anders. De notebook berekent voor verschillende standen de statische
kracht die nodig is om de schuiver vast te houden.

Voor de huidige basiscase:

```text
max |F_hold| = 63.43 N
```

Interpretatie:

- de motor/rem moet ook in stilstand belasting kunnen opnemen;
- enkel vertrouwen op schuiverwrijving is niet veilig;
- een rem, zelfremmende overbrenging of mechanische vergrendeling is nodig voor
  tussenstanden.

De statische schuiverwrijvingsgrens wordt ook getoond:

```math
F_{\text{wrijving,max}} = \mu_{\text{static}} |R_{Ax}|
```

Die is een theoretische grens, geen veilige vergrendeling.

## 12. Vermogen en motordimensionering in Notebook 3

Notebook 3 berekent een lokale actuatorinschatting:

```math
P = F_s\dot{s}
```

en een poelie-equivalent:

```math
T = \frac{F_s r}{\eta}
```

met:

```text
r = 25 mm (quick check; Notebook 4 kiest de definitieve poelie automatisch)
eta = 0.78
```

Belangrijk:

> Deze actuatorsectie is indicatief. De finale motor-, rem-, poelie- en
> aandrijfaskeuze wordt in Notebook 4 gedaan.

Voor de huidige basiscase:

```text
max P_act = 13.38 W
```

Dit is klein omdat het enkelvoudige mechanisme licht is. Voor de finale
overdekking worden de lasten veel groter door voorbalk en doek.

## 13. Arbeids-surplus

Notebook 3 gebruikt ook de Les-4 grootheid:

```math
A(t)=\int_0^t(P-\bar{P})\,dt
```

en:

```math
A_\text{max}=\max(A)-\min(A)
```

Dat geeft een maat voor de energiefluctuatie of energiebufferbehoefte.

Bij dit mechanisme is een vliegwiel niet logisch als hoofdoplossing, omdat:

- het mechanisme traag beweegt;
- het niet continu op hoge snelheid cyclisch draait;
- het in tussenstanden moet kunnen stilstaan;
- zwaartekrachtcompensatie via veer/gasveer logischer is.

Daarom wordt in de latere trekveren-notebook een veer als alternatief onderzocht.

## 14. Framebelasting en onbalans

Notebook 3 maakt onderscheid tussen:

- inertiele onbalans;
- totale framebelasting met zwaartekracht en wrijving.

De inertiele onbalans is klein, zoals Notebook 2 al toonde. De totale
framebelasting is groter omdat gewicht en geleidingsreacties meespelen.

Voor de huidige basiscase:

```text
max |R_Ax| = 157.86 N
max netto framekracht = 73.90 N
```

In de finale overdekking worden deze waarden veel groter door de zwaardere
voorbalk en doek. Daarom heeft `Notebook 3 - Overdekking` een aparte analyse van
mast, schuiver, muurbeugels en voorbalk.

## 15. Wat bewijzen de figuren?

De nuttige figuren in Notebook 3 tonen:

- decompositie van `F_s` in inertie, zwaartekracht en wrijving;
- `F_s` als functie van tijd en schuiverstand;
- normaalkracht en schuiverwrijving;
- energiebalansfout;
- statische houdkracht over het bewegingsbereik;
- vermogen en cumulatieve arbeid;
- arbeids-surplus;
- framebelasting versus onbalans.

Deze figuren zijn vooral nuttig voor vragen over:

- relatieve invloed van inertie, wrijving en zwaartekracht;
- waarom trager bewegen niet alle krachten verlaagt;
- waarom een rem/vergrendeling nodig is;
- waarom Notebook 2 niet genoeg is voor motorselectie;
- waarom trekveren of gasveren later interessant worden.

## 16. Wat moet je kunnen verdedigen?

### Waarom Notebook 3 na Notebook 2?

Notebook 2 valideert de inertie-only Newton-Euler-opbouw. Notebook 3 gebruikt
dezelfde basis en voegt bekende krachten toe. Zo kan je controleren dat de
inertiecomponent gelijk blijft aan Notebook 2.

### Waarom zijn zwaartekracht en wrijving dominant?

Omdat het traject traag is. Inertie schaalt met versnelling. Zwaartekracht hangt
af van massa en geometrische hefboom en blijft ook bij stilstand aanwezig.

### Waarom is wrijving iteratief?

Omdat wrijving afhangt van reactiekrachten die zelf pas na het oplossen bekend
zijn.

### Waarom is de houdkracht belangrijk?

Omdat de overdekking in elke tussenstand kan moeten blijven staan. Een riem of
motorstroom alleen is geen veilige mechanische vergrendeling.

### Waarom is Notebook 3 nog niet de finale overdekking?

Omdat de massa in K hier nog `3.00 kg` is. De finale overdekking gebruikt een
zware voorbalk en doek, dus een eigen massamodel in `Notebook 3 - Overdekking`.

## 17. Typische valkuilen

- Notebook 3 verwarren met Notebook 2: Notebook 3 bevat wel zwaartekracht en
  wrijving.
- `F_drive_s` verwarren met de globale verticale kracht: `s` is positief naar
  beneden.
- De schuiverreactie `R_Ax` interpreteren als riemkracht. Dat is fout; `R_Ax`
  hoort bij geleiding/mast.
- Denken dat trager bewegen de zwaartekrachtlast verlaagt. Dat klopt niet; het
  verlaagt vooral inertie en piekvermogen.
- Denken dat statische wrijving een betrouwbare rem is. Dat is niet veilig voor
  tussenstanden.
- De actuatorsectie in Notebook 3 als finale motorselectie zien. Notebook 4 is
  daarvoor bedoeld.

## 18. Korte samenvatting

Notebook 3 is de echte dynamische basiscase voor een enkel stangenmechanisme.
De notebook leest de kinematica en inertie uit Notebook 1 en 2, voegt
zwaartekracht, schuiverwrijving en pinwrijving toe, en lost opnieuw hetzelfde
Newton-Euler-stelsel op.

De huidige resultaten tonen dat het mechanisme quasi-statisch is: inertie is
klein, zwaartekracht domineert en wrijving is secundair maar relevant. De
belangrijkste ontwerpconclusie is dat motor en rem niet alleen op versnelling
moeten worden gekozen, maar vooral op gewicht, houdkracht, wrijving en veilige
tussenstandvergrendeling.
