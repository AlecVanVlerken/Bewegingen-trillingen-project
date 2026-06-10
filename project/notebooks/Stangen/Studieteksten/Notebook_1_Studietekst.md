# Notebook 1 - Studietekst kinematische analyse

Deze tekst hoort bij `Notebook 1.ipynb`. Het doel is dat je de notebook kan
uitleggen vanuit de theorie uit de lessen: wat berekenen we, waarom mag dat, met
welke vergelijkingen, en hoe komt dat terug in de code en de figuren.

Dit document is dus geen codecel-per-codecel uitleg. Die maken we apart. Hier
ligt de nadruk op de mechanische en wiskundige redenering achter Notebook 1.

## 0. Actuele hoofdcase

De actuele finale hoofdcase gebruikt een hellende open stand. Dat is belangrijk:
de oude bijna horizontale open stand gaf te grote schuiver- en mastreacties in
de latere overdekkingsanalyse. Daarom is de eindstand aangepast naar
`s_open = 0.600 m`.

| Grootheid | Actuele waarde |
|---|---:|
| Bewegingsrichting | openen |
| Bewegingsprofiel | `condition_scurve` |
| Gesloten stand | `s_closed = 1.875 m` |
| Open stand | `s_open = 0.600 m` |
| Slag | `1.275 m` |
| Effectieve beweegtijd | `20.00 s` |
| Hold-tijd na beweging | `4.0 s` |
| Max schuiversnelheid | `0.1965 m/s` |
| Max schuiverversnelling | `0.0511 m/s^2` |
| Max snelheid punt K | `0.4410 m/s` |
| Max versnelling punt K | `0.1745 m/s^2` |
| Max `cond(A)` | `68.36` |
| Max sluitingsfout | `5.95e-13 m` |
| Open positie punt K | `(2.221 m, -0.608 m)` |
| Open hoek punt K t.o.v. horizontaal | ongeveer `-15.3 deg` |

Tekenconventie:

- oorsprong in punt `C`;
- `x` horizontaal naar rechts;
- `y` verticaal omhoog;
- `s` wordt positief naar beneden gemeten langs de mast;
- openen betekent dus dat `s` daalt van `1.875 m` naar `0.600 m`.

## 1. Koppeling met de lessen

| Lesonderdeel | Theorie | Toepassing in Notebook 1 |
|---|---|---|
| Les 1A | Kinematica versus dynamica | Notebook 1 berekent alleen posities, snelheden en versnellingen. Krachten komen pas later. |
| Les 1B | Kinematisch diagram | Het mechanisme wordt vereenvoudigd tot starre links en ideale gewrichten. |
| Les 1B | Mobiliteit | Met Gruebler-Kutzbach tonen we dat het mechanisme een vrijheidsgraad heeft. |
| Les 2 | Positieanalyse | De hoeken volgen uit sluitingsvergelijkingen van gesloten vectorlussen. |
| Les 2 | `fsolve` en configuratietak | Het niet-lineaire stelsel wordt numeriek opgelost en de vorige oplossing wordt als startwaarde gebruikt. |
| Les 2 | Snelheidsanalyse | Differentieren van de sluitingsvergelijkingen geeft `A omega = b`. |
| Les 2 | Versnellingsanalyse | Nogmaals differentieren geeft `A alpha = C`, met dezelfde matrix `A`. |
| Les 2 | Dode punten en singulariteiten | `cond(A)` wordt gebruikt als maat voor geometrische gevoeligheid. |
| Les 5 | Bewegingswetten | Het schuivertraject is glad, met nul snelheid en nul versnelling aan begin/einde. |
| Python-algoritmes | Vierstangen-algoritme | De notebook volgt dezelfde opbouw: parameters, positie, snelheid, versnelling, animatie, opslag. |

De belangrijkste leslink is deze:

> Eerst beschrijf je de geometrie met sluitingsvergelijkingen. Daarna
> differentieer je die vergelijkingen naar tijd voor snelheid en versnelling.
> Dat is precies de methode uit Les 2, maar uitgebreid van een vierstangenmechanisme
> naar ons grotere paraplu-stangenmechanisme.

## 2. Wat analyseert Notebook 1?

Notebook 1 is de kinematische basis van de hele Stangen-analyse. De notebook
vertrekt van een opgelegde schuiverbeweging `s(t)` en berekent:

- de hoeken `theta3` tot en met `theta8`;
- de hoeksnelheden `dtheta3` tot en met `dtheta8`;
- de hoekversnellingen `ddtheta3` tot en met `ddtheta8`;
- de posities van de gewrichten;
- de positie, snelheid en versnelling van punt `K`;
- de conditionering van de snelheidsmatrix;
- de sluitingsfouten en validatiefouten;
- het bestand `notebook1_kinematica_results.npz` voor Notebook 2 en later.

Notebook 1 berekent bewust geen:

- krachten;
- massa's;
- zwaartekracht;
- wrijving;
- motorvermogen;
- remkoppel;
- frame- of mastbelasting.

Die zaken horen bij Notebook 2, 3, de overdekkingsloadcases en Notebook 4.

## 3. Theorie: kinematica versus dynamica

In Les 1A wordt het deel "Beweging" opgesplitst in kinematica en dynamica.

Kinematica:

- positie;
- hoek;
- snelheid;
- hoeksnelheid;
- versnelling;
- hoekversnelling.

Dynamica:

- krachten;
- momenten;
- inertie;
- energie;
- vermogen.

Voor ons project betekent dit:

1. Notebook 1 zoekt de beweging van het mechanisme.
2. Notebook 2 en 3 gebruiken die beweging om krachten te bepalen.
3. Notebook 4 gebruikt de krachten om een motor en aandrijving te dimensioneren.

Als Notebook 1 fout is, zijn alle latere dynamische resultaten fout. Daarom
bevat Notebook 1 veel controles: sluitingsfout, dubbele positievalidatie,
snelheidsresidu, conditionering en animatie.

Mondelinge kernzin:

> Notebook 1 is puur kinematisch. Ze bepaalt hoe het mechanisme beweegt, niet
> welke krachten daarvoor nodig zijn.

## 4. Theorie: kinematisch diagram en mobiliteit

### 4.1 Kinematisch diagram

In Les 1B wordt een mechanisme herleid tot een kinematisch diagram. Details zoals
profielen, bouten, materiaal en lagers worden voor de kinematica weggelaten. Wat
overblijft zijn:

- starre links;
- roterende gewrichten;
- prismatische gewrichten;
- vaste grondlink;
- topologie: welke links zijn met elkaar verbonden.

Voor ons mechanisme:

| Element | Kinematisch model |
|---|---|
| Mast/frame | vaste grondlink |
| Schuiver/collar | prismatisch gewricht langs de mast |
| Stangen/ribben | starre links |
| Scharnieren | roterende gewrichten |
| Punt K | outputpunt aan het uiteinde van link 8 |

De stangen worden in Notebook 1 als star beschouwd. Elastische doorbuiging komt
pas later in de overdekkingsanalyse aan bod.

### 4.2 Mobiliteitsformule

De vlakke Gruebler-Kutzbach-formule is:

```math
M = 3(n - 1) - 2 f_1 - f_2
```

met:

- `M`: mobiliteit, dus aantal onafhankelijke invoeren;
- `n`: aantal links inclusief grondlink;
- `f_1`: aantal gewrichten met een vrijheidsgraad;
- `f_2`: aantal gewrichten met twee vrijheidsgraden.

Waarom `3(n - 1)`?

Een vrij star lichaam in het vlak heeft drie vrijheidsgraden:

```text
x-translatie, y-translatie, rotatie
```

De grondlink is vast, dus die telt niet mee. Daarom krijg je `3(n - 1)`.

Waarom `-2 f_1`?

Een roterend of prismatisch gewricht laat een vrijheidsgraad toe en legt dus
twee vlakke beperkingen op.

Voor ons mechanisme is de conclusie:

```math
M = 1
```

Er is dus een enkele onafhankelijke invoer. In Notebook 1 kiezen we daarvoor de
schuiverpositie:

```text
s(t)
```

Alle andere hoeken volgen uit de geometrische sluitingsvoorwaarden.

Mondelinge kernzin:

> Het mechanisme heeft een vrijheidsgraad. Daarom is de schuiverpositie `s(t)`
> genoeg om de volledige stand van alle links te bepalen.

## 5. Geometrische parameters en punten

De notebook gebruikt segmentlengtes zoals `r3a`, `r3b`, enzovoort. Dat betekent
niet dat link 3 uit twee losse staven bestaat. Het betekent dat een starre link
een tussenpunt heeft.

Voorbeeld:

- link 3 loopt van `B` via `D` naar `E`;
- `r3a` is het stuk `B -> D`;
- `r3b` is het stuk `D -> E`;
- de volledige richting is `theta3`.

Daarom wordt een punt op een link geschreven als:

```math
P = P_0 + r \begin{bmatrix}\cos\theta \\ \sin\theta\end{bmatrix}
```

De belangrijkste vaste en bewegende punten zijn:

| Punt | Betekenis |
|---|---|
| `C` | vast punt aan de mast, oorsprong |
| `B` | schuiverpunt op de mast |
| `D, E, F, G, H, I, J` | interne scharnierpunten |
| `K` | uiteinde van de buitenste rib, outputpunt |

De schuiverpositie is:

```math
B = C + \begin{bmatrix}0 \\ -s\end{bmatrix}
```

omdat `s` positief naar beneden wordt gemeten.

## 6. Bewegingswet van de schuiver

### 6.1 Waarom een bewegingswet?

Les 5 behandelt bewegingswetten. Daarin is een belangrijk principe:

> Een bruikbaar bewegingsprofiel moet minstens positie, snelheid en versnelling
> voldoende glad maken, zodat er geen onrealistische schokken of oneindige
> krachten ontstaan.

Voor een zonwering of luifel willen we:

- zachte start;
- zachte stop;
- geen sprongen in snelheid;
- geen sprongen in versnelling;
- lage piekvermogens;
- controle in gevoelige configuraties.

Daarom gebruiken we niet gewoon constante snelheid.

### 6.2 Genormaliseerde tijd

We schrijven de beweging met een genormaliseerde tijd:

```math
\tau = \frac{t}{t_{\text{move}}}, \qquad 0 \le \tau \le 1
```

Met:

```math
\Delta s = s_{\text{end}} - s_{\text{start}}
```

wordt:

```math
s(t) = s_{\text{start}} + \Delta s \, h(\tau)
```

De snelheid en versnelling volgen via de kettingregel:

```math
\dot{s}(t) = \frac{\Delta s}{t_{\text{move}}} h'(\tau)
```

```math
\ddot{s}(t) = \frac{\Delta s}{t_{\text{move}}^2} h''(\tau)
```

Voor de ruk geldt op dezelfde manier:

```math
\dddot{s}(t) = \frac{\Delta s}{t_{\text{move}}^3} h'''(\tau)
```

Dit is exact de dimensieloze aanpak uit Les 5: eerst definieer je een
genormaliseerde bewegingswet `h(tau)`, daarna schaal je naar echte lengte en tijd.

### 6.3 Bewegingswetten die we overwogen

#### Sinusvormige of harmonische wet

Een klassieke eenvoudige wet is:

```math
h(\tau)=\frac{1}{2}\left(1-\cos(\pi\tau)\right)
```

Eigenschappen:

- de positie loopt glad van 0 naar 1;
- de snelheid is nul aan begin en einde;
- de versnelling is niet nul aan begin en einde.

Dat laatste is de reden waarom dit voor onze finale overdekking minder geschikt
is. Als de schuiver voor de beweging stilstaat, springt de versnelling bij het
startmoment naar een eindige waarde. Dat geeft een sprong in inertiekracht en
kan schokkerig aanvoelen.

#### Trapezium- of constant-snelheidsprofiel

Een trapeziumprofiel versnelt eerst, beweegt daarna met ongeveer constante
snelheid en vertraagt op het einde. Het voordeel is eenvoud en korte cyclustijd.
Het nadeel is dat de overgang tussen versnellen, constante snelheid en vertragen
hard kan zijn als je die niet afrondt. Voor een brede luifel met riem, schuiver,
doek en voorbalk willen we net geen harde overgang.

#### Smooth-345

De klassieke 3-4-5 bewegingswet is:

```math
h(\tau) = 10\tau^3 - 15\tau^4 + 6\tau^5
```

Eigenschappen:

```math
h(0)=0,\quad h(1)=1
```

```math
h'(0)=h'(1)=0
```

```math
h''(0)=h''(1)=0
```

Dus positie, snelheid en versnelling sluiten mooi aan op stilstand.

De ruk is aan begin en einde niet nul. Dat is geen fout, maar het betekent dat
`smooth_345` minder zacht is dan `smooth_4567` als het systeem gevoelig is voor
trillingen of speling.

#### Smooth-4567

De 4-5-6-7 bewegingswet is:

```math
h(\tau) = 35\tau^4 - 84\tau^5 + 70\tau^6 - 20\tau^7
```

Deze is nog zachter aan de randen, omdat ook de ruk aan begin en einde gunstiger
is. Dat past bij de fundamentele gedachte uit Les 5: gladheid verlaagt
dynamische pieken.

De prijs is dat de dimensieloze piekwaarden in het midden van de beweging hoger
kunnen liggen. Als je begin en einde zachter maakt, moet de beweging elders meer
snelheid of versnelling opnemen om dezelfde slag in dezelfde tijd af te leggen.

Een nuttige vergelijking:

| Bewegingswet | Snelheid aan eindpunten | Versnelling aan eindpunten | Ruk aan eindpunten | Belangrijkste nadeel |
|---|---:|---:|---:|---|
| Sinus/harmonisch | 0 | niet 0 | 0 | versnelling springt bij start/stop |
| Trapezium | meestal niet glad | sprongen | sprongen | hard voor riem en constructie |
| Smooth-345 | 0 | 0 | niet 0 | ruk springt bij start/stop |
| Smooth-4567 | 0 | 0 | 0 | hogere piekwaarden in het midden |
| Condition-scurve | 0 | 0 | ongeveer glad | complexer en trager |

### 6.4 Condition-scurve

Notebook 1 gebruikt standaard:

```python
motion_profile = "condition_scurve"
```

Dat is een uitbreiding op een gewone smooth-curve. De redenering is:

1. Maak eerst een quasi-statische condition-map over het schuiverbereik.
2. Bereken bij elke schuiverpositie `cond(A)`.
3. Maak daaruit een snelheidsfactor.
4. Beweeg trager waar `cond(A)` hoog is.
5. Gebruik nog steeds een `smooth_4567`-achtige envelope zodat start en stop
   glad blijven.

De snelheidsfactor is conceptueel:

```math
f(s) = 1 - (1 - f_{\min}) \tilde{\kappa}(s)^\gamma
```

waarbij:

- `f_min = condition_min_speed_factor`;
- `gamma = condition_slow_gain`;
- `\tilde{\kappa}(s)` een genormaliseerde maat is voor `log(cond(A))`.

In de actuele notebook:

```text
condition_min_speed_factor = 0.25
condition_slow_gain = 1.25
```

Dus in de slechtst geconditioneerde zones kan de lokale snelheid tot ongeveer
25 procent van de nominale snelheid dalen.

Belangrijk: dit verandert de kinematische baan niet. Het verandert alleen de
tijdsverdeling over die baan.

Mondelinge kernzin:

> De `condition_scurve` combineert de gladheid van Les 5 met een extra
> vertraging in geometrisch gevoelige zones.

### 6.5 Historiek van de bewegingswet

De bewegingswet is niet in een keer gekozen. De redenering was:

1. **Sinusvormige beweging:** dit sluit goed aan bij de basis uit de lessen en
   geeft een eenvoudige, vloeiende beweging. Het nadeel is dat de randvoorwaarden
   en de invloed van gevoelige mechanismestanden minder expliciet instelbaar
   zijn.
2. **Polynoomprofiel:** hiermee kunnen begin- en eindvoorwaarden rechtstreeks
   worden opgelegd. Dat is beter voor een mechanisme dat uit stilstand moet
   vertrekken en weer in stilstand moet eindigen.
3. **Smooth-profiel:** `smooth_345` en `smooth_4567` vermijden sprongen in
   snelheid en versnelling. Dat past beter bij een grote overdekking, omdat
   schokken in doek, voorbalk, riem en mast ongewenst zijn.
4. **Condition-scurve:** dit is de finale keuze. Ze gebruikt een gladde
   `smooth_4567`-basis, maar verdeelt de tijd opnieuw volgens `cond(A)`. Daardoor
   beweegt het mechanisme trager in de geometrisch gevoeligere zones.

Daarom blijven `smooth_345` en `smooth_4567` in de notebook staan. Ze zijn niet
de finale hoofdkeuze, maar ze zijn nuttig als klassieke referentie of fallback.
De finale analyse gebruikt:

```python
motion_profile = "condition_scurve"
```

### 6.6 Waarom deze tijden en limieten?

De trajectparameters zijn:

```text
t_move_desired = 20.0 s
t_move_effective = 20.00 s
actuator_v_limit = 0.25 m/s
actuator_a_limit = 0.10 m/s^2
```

De gewenste `20 s` is gekozen als praktische orde van grootte voor een brede
overdekking: niet zo traag dat de bediening vervelend wordt, maar wel rustig
genoeg voor de schuiver, doek, voorbalk, riem en mast. Bij deze waarde blijven
de snelheids- en versnellingslimieten automatisch gerespecteerd, zodat
`t_move_effective = 20.00 s`.

De limieten zijn ontwerp-richtwaarden:

- `0.25 m/s` houdt de schuiverbeweging rustig;
- `0.10 m/s^2` houdt de versnelling en dus de inertiekracht beperkt;
- de combinatie zorgt voor een realistische motor- en riemdimensionering.

Een langere bewegingstijd kan altijd. Dat verlaagt vooral:

- versnellingen;
- inertiekrachten;
- piekvermogen;
- trillingsgevoeligheid.

Maar het verlaagt niet:

- de zwaartekrachtarbeid;
- de statische houdkracht;
- de horizontale schuiver- en mastreacties door het gewicht en de geometrie.

Dat is belangrijk voor de interpretatie van Notebook 3 en 4. De inertie is in
ons ontwerp relatief klein; zwaartekracht, wrijving en structurele reacties zijn
dominanter. Daarom is extreem traag bewegen geen echte oplossing voor alle
krachten. Het traject moet vooral rustig en controleerbaar zijn.

Notebook 4 bevestigt dit kwantitatief. Als de oudere `15.6 s`-run naar ongeveer
`19.5 s` wordt geschaald, daalt het piekvermogen met ongeveer 19 procent, maar
de piekkracht verandert minder dan 0.1 procent. Een nog langere beweging van
ongeveer `31 s` halveert het piekvermogen ongeveer, maar verlaagt de maatgevende
lijnkracht en het houdkoppel praktisch niet. Daardoor is `20 s` een goede
eindkeuze: rustiger en lager piekvermogen dan de snellere run, maar zonder
onnodig lange bediening.

### 6.7 Waarom deze condition-controlwaarden?

De condition-control gebruikt:

```text
condition_min_speed_factor = 0.25
condition_slow_gain = 1.25
condition_map_points = 301
trajectory_shape_points = 3001
```

De minimale snelheidsfactor `0.25` betekent dat de beweging in de slechtst
geconditioneerde zone tot ongeveer een kwart van de nominale lokale snelheid kan
zakken. Ze wordt niet nul, want dan zou het mechanisme lokaal praktisch blijven
hangen.

De gain `1.25` maakt de vertraging sterker bij hoge `cond(A)`, maar niet zo
sterk dat de volledige cyclus onnodig traag wordt. Grotere waarden concentreren
de vertraging meer rond de slechtste zone. Kleinere waarden spreiden de
vertraging gelijkmatiger uit.

`condition_map_points = 301` is een numeriek compromis. Te weinig punten kunnen
de speed factor onnauwkeurig of hoekig maken. Veel meer punten geven bijna
hetzelfde resultaat, maar kosten meer rekentijd. `trajectory_shape_points = 3001`
wordt gebruikt voor de interne tijdsherverdeling, zodat de afgeleide grootheden
`ds` en `dds` glad blijven.

### 6.8 Waarom zijn `custom` en de foutcontroles nodig?

`motion_direction = "custom"` blijft nodig voor parameterstudies. Daarmee kan
je bijvoorbeeld testen of een minder open eindstand de mastbelasting verlaagt,
zonder `s_open` en `s_closed` zelf te herdefinieren:

```python
motion_direction = "custom"
s_start_custom = s_closed
s_end_custom = 0.800
```

De notebook controleert daarna of `s_start` en `s_end` binnen het geldige bereik
liggen. Die check is nodig omdat de geometrie en condition-map alleen
gevalideerd zijn tussen `s_open` en `s_closed`. Buiten dat interval kan het
mechanisme in een ongeldige of niet-getrackte configuratie terechtkomen.

Ook de andere foutcontroles zijn bewust aanwezig:

- `t_move_desired > 0`: een bewegingstijd nul of negatief is fysisch onmogelijk;
- `t_hold >= 0`: negatieve wachttijd heeft geen betekenis;
- `0 < condition_min_speed_factor <= 1`: anders wordt de snelheidsfactor
  onbruikbaar;
- `s_start != s_end`: een traject met nul slag levert geen zinvolle dynamica;
- sluitingsresiduals en dubbele positievalidatie: controleren of de gevonden
  configuratie numeriek en geometrisch klopt.

Deze controles lijken klein, maar ze beschermen alle volgende notebooks. Een
foute kinematica zou anders rechtstreeks leiden tot foutieve krachten,
vermogens en motorkeuzes.

## 7. Waarom `s_open = 0.600 m`?

In een vorige versie liep de open stand tot:

```text
s_open = 0.125 m
```

Dat gaf een bijna horizontale eindstand. Kinematisch kon dat, maar in de latere
overdekkingsanalyse gaf die stand grote horizontale schuiverreacties. De finale
keuze is daarom:

```text
s_open = 0.600 m
```

Gevolgen:

- punt `K` eindigt op ongeveer `(2.221 m, -0.608 m)`;
- de kap helt ongeveer `15.3 deg` naar beneden;
- de slag wordt korter;
- de bewegingstijd wordt `20.00 s`;
- de overdekkingsanalyse krijgt veel lagere schuiver- en mastreacties;
- de toepassing blijft realistisch, want luifels en overdekkingen zijn meestal
  niet perfect horizontaal.

Dit is een goed voorbeeld van vraag 10:

> Als je een parameter verandert, voorspel je het effect en reken je de volledige
> keten opnieuw door.

## 8. Positieanalyse met sluitingsvergelijkingen

### 8.1 Theorie uit Les 2

Les 2 toont positieanalyse via sluitingsvergelijkingen. Voor een gesloten
mechanisme moeten de vectoren rond elke lus optellen tot nul:

```math
\sum_i \vec{r}_i = \vec{0}
```

In componentvorm levert elke vectorlus twee vergelijkingen:

- een x-vergelijking;
- een y-vergelijking.

### 8.2 Onbekenden

Voor Notebook 1 zijn de onbekenden:

```math
\boldsymbol{\theta}
=
\begin{bmatrix}
\theta_3 & \theta_4 & \theta_5 & \theta_6 & \theta_7 & \theta_8
\end{bmatrix}^T
```

De invoer `s` is bekend. Er zijn zes onbekende hoeken, dus er zijn zes
onafhankelijke vergelijkingen nodig.

### 8.3 Vectornotatie

Voor een linksegment met lengte `r` en hoek `theta`:

```math
\vec{r}(\theta)
=
r
\begin{bmatrix}
\cos\theta \\
\sin\theta
\end{bmatrix}
```

### 8.4 De drie lussen

Notebook 1 gebruikt drie lussen. Hieronder staan dezelfde vergelijkingen als in
de code, maar als studievorm.

**Lus 1**

```math
F_1 = (r_{3a}+r_{3b})\cos\theta_3 - r_{4a}\cos\theta_4 = 0
```

```math
F_2 = -s + (r_{3a}+r_{3b})\sin\theta_3 - r_{4a}\sin\theta_4 = 0
```

Deze lus koppelt de schuiverlink via link 3 aan link 4.

**Lus 2**

```math
F_3 =
-r_{3b}\cos\theta_3
+ (r_{5a}+r_{5b})\cos\theta_5
+ r_{7a}\cos\theta_7
- r_{4b}\cos\theta_4
= 0
```

```math
F_4 =
-r_{3b}\sin\theta_3
+ (r_{5a}+r_{5b})\sin\theta_5
+ r_{7a}\sin\theta_7
- r_{4b}\sin\theta_4
= 0
```

Deze lus sluit het middelste deel van het mechanisme.

**Lus 3**

```math
F_5 =
-r_{5b}\cos\theta_5
+ r_6\cos\theta_6
+ r_{8a}\cos\theta_8
- (r_{7a}+r_{7b})\cos\theta_7
= 0
```

```math
F_6 =
-r_{5b}\sin\theta_5
+ r_6\sin\theta_6
+ r_{8a}\sin\theta_8
- (r_{7a}+r_{7b})\sin\theta_7
= 0
```

Samen:

```math
\vec{F}(\boldsymbol{\theta}, s) = \vec{0}
```

### 8.5 Waarom `fsolve`?

Het stelsel bevat `sin` en `cos`, dus het is niet-lineair. Daarom gebruiken we
geen lineaire solver, maar:

```python
scipy.optimize.fsolve
```

`fsolve` gebruikt een Newton-achtige iteratie:

```math
\boldsymbol{\theta}_{k+1}
=
\boldsymbol{\theta}_{k}
-
J^{-1}(\boldsymbol{\theta}_{k})
\vec{F}(\boldsymbol{\theta}_{k})
```

waarbij `J` de Jacobiaan is van de sluitingsvergelijkingen.

De notebook doet dit voor elke tijdstap. De oplossing van de vorige tijdstap
wordt gebruikt als startgok voor de volgende tijdstap. Dat heet branch tracking.

Waarom is dat nodig?

Een niet-lineair mechanisme kan meerdere mathematische oplossingen hebben voor
dezelfde `s`. Zonder goede startgok kan de solver naar een andere
configuratietak springen. Door de vorige oplossing te gebruiken blijft de
beweging continu en fysisch logisch.

### 8.6 Sluitingsfout

Na het oplossen controleert de notebook:

```math
\text{residual}_{pos}
=
\|\vec{F}(\boldsymbol{\theta}, s)\|
```

Actuele waarde:

```text
max residual_pos = 5.95e-13 m
```

Dat is zeer klein. De positieanalyse sluit dus numeriek goed.

Mondelinge kernzin:

> De positieanalyse is een niet-lineair sluitingsprobleem. We lossen het per
> tijdstap op met `fsolve` en controleren de oplossing met de sluitingsresidual.

## 9. Dubbele positievalidatie

De notebook doet nog een extra controle: sommige gewrichten worden via twee
verschillende ketens berekend.

Voorbeeld:

- punt `E` via link 3;
- punt `E` via link 4.

Als het mechanisme correct sluit, moeten die punten samenvallen.

De belangrijkste controles:

| Gewricht | Route 1 | Route 2 |
|---|---|---|
| `E` | via link 3 | via link 4 |
| `G` | via link 5 | via link 7 |
| `J` | via link 8 | via link 7 |

Voor link 7 is de consistente reconstructie:

```math
G = H - r_{7a} e_7
```

```math
J = H + r_{7b} e_7
```

met:

```math
e_7 =
\begin{bmatrix}
\cos\theta_7 \\
\sin\theta_7
\end{bmatrix}
```

Dit is belangrijk omdat `H` een tussenpunt op link 7 is. `G` ligt aan de ene
kant van `H`, `J` aan de andere kant. Daarom is het niet correct om `J` te
reconstrueren met een willekeurige somlengte vanaf `H`.

Deze validatie is nuttig omdat ze geometrische fouten toont die een kleine
`fsolve`-residual soms minder duidelijk maakt.

## 10. Snelheidsanalyse

### 10.1 Theorie uit Les 2

Uit de positieanalyse weten we:

```math
\vec{F}(\boldsymbol{\theta}, s) = \vec{0}
```

Omdat deze vergelijking op elk tijdstip geldt, geldt ook:

```math
\frac{d}{dt}\vec{F}(\boldsymbol{\theta}, s) = \vec{0}
```

De linklengtes zijn constant. Alleen de hoeken en `s` veranderen in de tijd.

Voor een term:

```math
r\cos\theta(t)
```

geldt:

```math
\frac{d}{dt}[r\cos\theta]
=
-r\sin\theta \, \dot{\theta}
```

Voor:

```math
r\sin\theta(t)
```

geldt:

```math
\frac{d}{dt}[r\sin\theta]
=
r\cos\theta \, \dot{\theta}
```

### 10.2 Lineair stelsel

Na differentieren krijg je een lineair stelsel in de onbekende hoeksnelheden:

```math
A(\boldsymbol{\theta}) \boldsymbol{\omega}
=
\mathbf{b}
```

met:

```math
\boldsymbol{\omega}
=
\begin{bmatrix}
\dot{\theta}_3 &
\dot{\theta}_4 &
\dot{\theta}_5 &
\dot{\theta}_6 &
\dot{\theta}_7 &
\dot{\theta}_8
\end{bmatrix}^T
```

In onze tekenconventie staat de bekende schuiversnelheid in het rechterlid:

```math
\mathbf{b}
=
\begin{bmatrix}
0 \\
\dot{s} \\
0 \\
0 \\
0 \\
0
\end{bmatrix}
```

De matrix `A` is:

```math
A =
\begin{bmatrix}
-(r_{3a}+r_{3b})\sin\theta_3 & r_{4a}\sin\theta_4 & 0 & 0 & 0 & 0 \\
(r_{3a}+r_{3b})\cos\theta_3 & -r_{4a}\cos\theta_4 & 0 & 0 & 0 & 0 \\
r_{3b}\sin\theta_3 & r_{4b}\sin\theta_4 & -(r_{5a}+r_{5b})\sin\theta_5 & 0 & -r_{7a}\sin\theta_7 & 0 \\
-r_{3b}\cos\theta_3 & -r_{4b}\cos\theta_4 & (r_{5a}+r_{5b})\cos\theta_5 & 0 & r_{7a}\cos\theta_7 & 0 \\
0 & 0 & r_{5b}\sin\theta_5 & -r_6\sin\theta_6 & (r_{7a}+r_{7b})\sin\theta_7 & -r_{8a}\sin\theta_8 \\
0 & 0 & -r_{5b}\cos\theta_5 & r_6\cos\theta_6 & -(r_{7a}+r_{7b})\cos\theta_7 & r_{8a}\cos\theta_8
\end{bmatrix}
```

De code bouwt deze matrix in:

```python
compute_A_matrix(...)
```

De oplossing gebeurt met:

```python
np.linalg.solve(A, b)
```

Niet met `fsolve`, want het snelheidsstelsel is lineair.

Mondelinge kernzin:

> De snelheidsanalyse is de tijdsafgeleide van de positie-sluitingsvergelijkingen.
> Daardoor krijg je een lineair stelsel in de hoeksnelheden.

### 10.3 Snelheid van punt K

De hoeksnelheden zijn niet direct genoeg. We willen ook de snelheid van punt `K`.

Voor een punt `P` op een starre link ten opzichte van punt `Q` geldt in 2D:

```math
\vec{v}_P
=
\vec{v}_Q
+
\dot{\theta} \, \text{perp}(\vec{r}_{QP})
```

waar:

```math
\text{perp}
\begin{bmatrix}
x \\
y
\end{bmatrix}
=
\begin{bmatrix}
-y \\
x
\end{bmatrix}
```

Voor de keten naar `K`:

```math
\vec{v}_B = \begin{bmatrix}0 \\ -\dot{s}\end{bmatrix}
```

```math
\vec{v}_D = \vec{v}_B + \dot{\theta}_3 \, \text{perp}(\vec{r}_{BD})
```

```math
\vec{v}_F = \vec{v}_D + \dot{\theta}_5 \, \text{perp}(\vec{r}_{DF})
```

```math
\vec{v}_I = \vec{v}_F + \dot{\theta}_6 \, \text{perp}(\vec{r}_{FI})
```

```math
\vec{v}_K = \vec{v}_I + \dot{\theta}_8 \, \text{perp}(\vec{r}_{IK})
```

Actuele maximale snelheid:

```text
max ||Kdot|| = 0.4410 m/s
```

## 11. Versnellingsanalyse

### 11.1 Theorie uit Les 2

We starten van het snelheidsstelsel:

```math
A(\boldsymbol{\theta}) \boldsymbol{\omega}
=
\mathbf{b}
```

Differentieren naar de tijd geeft:

```math
\dot{A}\boldsymbol{\omega} + A\boldsymbol{\alpha}
=
\dot{\mathbf{b}}
```

Dus:

```math
A\boldsymbol{\alpha}
=
\dot{\mathbf{b}} - \dot{A}\boldsymbol{\omega}
```

Dat schrijven we als:

```math
A(\boldsymbol{\theta}) \boldsymbol{\alpha}
=
\mathbf{C}(\boldsymbol{\theta}, \boldsymbol{\omega}, \dot{s}, \ddot{s})
```

met:

```math
\boldsymbol{\alpha}
=
\begin{bmatrix}
\ddot{\theta}_3 &
\ddot{\theta}_4 &
\ddot{\theta}_5 &
\ddot{\theta}_6 &
\ddot{\theta}_7 &
\ddot{\theta}_8
\end{bmatrix}^T
```

Belangrijk:

> De matrix `A` is dezelfde als bij de snelheidsanalyse. Alleen het rechterlid
> verandert.

### 11.2 Waar komen de centripetale termen vandaan?

Neem de tijdsafgeleide van een typische snelheidsterm:

```math
-r\sin\theta \, \dot{\theta}
```

Dan:

```math
\frac{d}{dt}
\left[-r\sin\theta \, \dot{\theta}\right]
=
-r\cos\theta \, \dot{\theta}^2
-r\sin\theta \, \ddot{\theta}
```

De term met `ddtheta` hoort bij de onbekende hoekversnellingen en komt links in
`A alpha`. De term met `dtheta^2` is bekend uit de snelheidsanalyse en komt in
het rechterlid `C`.

Voor lus 1 geeft dat bijvoorbeeld:

```math
C_1 =
(r_{3a}+r_{3b})\cos\theta_3 \, \dot{\theta}_3^2
- r_{4a}\cos\theta_4 \, \dot{\theta}_4^2
```

```math
C_2 =
\ddot{s}
+(r_{3a}+r_{3b})\sin\theta_3 \, \dot{\theta}_3^2
- r_{4a}\sin\theta_4 \, \dot{\theta}_4^2
```

Dit principe herhaalt zich voor de andere lussen.

Mondelinge kernzin:

> Bij de versnellingsanalyse blijft `A` dezelfde matrix. De extra termen komen
> door de afgeleide van de hoeksnelheidstermen en bevatten `omega^2`.

### 11.3 Versnelling van punt K

Voor een punt `P` op een starre link ten opzichte van punt `Q`:

```math
\vec{a}_P
=
\vec{a}_Q
+
\ddot{\theta}\,\text{perp}(\vec{r}_{QP})
-
\dot{\theta}^2 \vec{r}_{QP}
```

De tweede term is tangentiele versnelling. De derde term is centripetale
versnelling.

Voor punt `K` ten opzichte van punt `I`:

```math
\ddot{K}_x
=
\ddot{I}_x
- \ddot{\theta}_8 y_{IK}
- \dot{\theta}_8^2 x_{IK}
```

```math
\ddot{K}_y
=
\ddot{I}_y
+ \ddot{\theta}_8 x_{IK}
- \dot{\theta}_8^2 y_{IK}
```

Actuele maximale versnelling:

```text
max ||Kddot|| = 0.1745 m/s^2
```

Die waarde is laag. Dat is logisch, want de beweging is rustig en de schuiver
wordt glad aangestuurd.

## 12. Conditionering en singulariteiten

### 12.1 Theorie uit Les 2

Les 2 bespreekt dode punten en singulariteiten. Bij een dood punt ligt de
mechanische overbrenging ongunstig. Bij een echte singulariteit is het
snelheidsstelsel niet meer uniek oplosbaar.

Voor ons stelsel:

```math
A\boldsymbol{\omega} = \mathbf{b}
```

is de vraag: hoe dicht zit `A` bij singulier?

Dat meten we met het conditiegetal:

```math
\kappa(A) = \text{cond}(A)
```

In termen van singuliere waarden:

```math
\text{cond}(A)
=
\frac{\sigma_{\max}(A)}{\sigma_{\min}(A)}
```

Interpretatie:

- `cond(A)` dicht bij 1: numeriek en mechanisch gunstig;
- hogere `cond(A)`: gevoeligere overbrenging;
- `cond(A) -> infinity`: echte singulariteit.

Een fout in het rechterlid kan versterkt worden met ongeveer:

```math
\frac{\|\delta \boldsymbol{\omega}\|}{\|\boldsymbol{\omega}\|}
\lesssim
\text{cond}(A)
\frac{\|\delta \mathbf{b}\|}{\|\mathbf{b}\|}
```

Dit is geen exacte krachtformule, maar wel een gevoeligheidsmaat.

### 12.2 Toepassing in Notebook 1

Notebook 1 berekent `cond(A)` op elke tijdstap. In de actuele run:

```text
cond(A)_max = 68.36
```

Dat is verhoogd, maar niet extreem en niet oneindig. De notebook meldt daarom
geen echte singulariteit in het gebruikte traject.

De grootste `cond(A)` ligt bij de gesloten stand. Dat betekent:

- het mechanisme is daar gevoeliger;
- kleine bewegingen of fouten kunnen sterker doorwerken;
- daarom vertraagt `condition_scurve` in die zones.

### 12.3 Drempel voor kritische zones

De notebook gebruikt een drempel:

```math
\text{threshold}
=
\max(10^3, P_{95}(\text{cond}(A)))
```

waarbij `P95` het 95ste percentiel is. Daardoor werkt de methode ook bij andere
parameterkeuzes.

In deze actuele hoofdcase is `cond(A)_max = 68.36`, dus onder `1000`. De
interpretatie is:

- geen echte kritische zone volgens de absolute drempel;
- wel een duidelijke relatieve gevoeligheid bij gesloten stand;
- de beweging blijft daarom bewust rustig.

Mondelinge kernzin:

> Een hoge `cond(A)` betekent niet automatisch dat het mechanisme blokkeert. Het
> betekent dat de snelheidsoplossing gevoeliger is. Pas bij een singuliere matrix
> krijg je een echt dood punt.

## 13. Animatie en grafische controle

De animatie is geen aparte fysische simulatie. Ze gebruikt de berekende hoeken
en reconstrueert de punten:

- `B`, `C`, `D`, `E`, `F`, `G`, `H`, `I`, `J`, `K`;
- alle links;
- de schuiverpositie.

Waarom is de animatie nuttig?

- Ze toont of de configuratietak fysisch logisch blijft.
- Ze toont de gesloten en open stand.
- Ze maakt zichtbaar dat de finale open stand hellend is.
- Ze helpt om pieken in `cond(A)` te koppelen aan een geometrische stand.

De animatie bewijst niet op zichzelf dat de analyse correct is. Daarvoor zijn
de sluitingsfouten en de dubbele positievalidatie nodig.

## 14. Output naar Notebook 2

Notebook 1 schrijft:

```text
notebook1_kinematica_results.npz
```

Belangrijke keys:

| Key | Betekenis |
|---|---|
| `t`, `Ts` | tijdvector en tijdstap |
| `s`, `ds`, `dds` | schuiverpositie, snelheid, versnelling |
| `theta3` ... `theta8` | linkhoeken |
| `dtheta3` ... `dtheta8` | hoeksnelheden |
| `ddtheta3` ... `ddtheta8` | hoekversnellingen |
| `Kx`, `Ky` | positie van punt K |
| `Kdot_x`, `Kdot_y`, `Kdot_norm` | snelheid van punt K |
| `Kddot_x`, `Kddot_y`, `Kddot_norm` | versnelling van punt K |
| `cond` | conditionering van `A` |
| `residual_pos` | sluitingsresidual |
| `s_open`, `s_closed` | actuele eindstanden |
| `motion_profile` | gebruikte bewegingswet |
| geometrieparameters | linklengtes voor latere reconstructie |

Dit bestand is de brug tussen kinematica en dynamica. Notebook 2 en Notebook 3
moeten deze waarden gebruiken, zodat alle latere krachten dezelfde geometrie en
dezelfde beweging gebruiken.

Workflow:

1. Wijzig parameters in Notebook 1.
2. Run Notebook 1 volledig.
3. Run Notebook 2.
4. Run Notebook 3 en loadcases.
5. Run Notebook 4.

Als je Notebook 1 verandert maar Notebook 2 of 3 niet opnieuw runt, krijg je een
inconsistente analyse.

## 15. Wat tonen de belangrijkste figuren in Notebook 1?

Deze tekst vervangt de aparte figurenuitleg niet, maar geeft wel de context.

| Figuur | Betekenis | Nuttig voor vraag |
|---|---|---|
| Traject `s`, `ds`, `dds` | Toont de opgelegde invoer en de gladde start/stop | Vraag 4 |
| Positievalidatie E/G/J | Toont dat dubbele ketens sluiten | Controle van methode |
| Animatie | Toont configuratie en hellende open stand | Vraag 1, 4, 6 |
| Snelheid punt K | Toont outputbeweging van de rib | Vraag 4, 8 |
| Versnelling punt K | Toont dynamische input voor Notebook 2 | Vraag 4, 8 |
| `cond(A)` tegen tijd en `s` | Toont gevoelige zones en singulariteitscontrole | Vraag 6 |
| Samenvatting | Geeft snelle numerieke checks | Alle vervolgnotebooks |

## 16. Typische mondelinge vragen over Notebook 1

### Waarom is dit een vlak mechanisme?

Alle links bewegen in een vlak. De rotatieassen van de scharnieren staan loodrecht
op dat vlak. Daarom mogen we met 2D-vectoren werken.

### Waarom zijn krachten hier nog niet nodig?

Kinematica hangt alleen af van geometrie en opgelegde beweging. Krachten bepalen
hoe zwaar het is om die beweging te realiseren, maar niet welke posities de
links innemen bij een opgelegde `s`.

### Waarom gebruik je `fsolve` voor positie maar `solve` voor snelheid?

De positievergelijkingen bevatten sinus en cosinus van onbekende hoeken en zijn
niet-lineair. De snelheids- en versnellingsvergelijkingen zijn lineair in de
onbekende hoeksnelheden en hoekversnellingen.

### Waarom is `cond(A)` relevant?

`A` is de matrix die de invoersnelheid koppelt aan de hoeksnelheden. Als `A`
slecht geconditioneerd is, wordt die relatie gevoelig. Dat wijst op een
mechanisch ongunstige configuratie.

### Waarom is de open stand aangepast naar `s_open = 0.600 m`?

Omdat de oude bijna horizontale stand later te grote horizontale
schuiverreacties gaf. De nieuwe open stand is nog steeds functioneel, maar
constructief veel beter verdedigbaar.

### Waarom moet Notebook 1 eerst opnieuw gerund worden?

Omdat alle latere notebooks de kinematica uit het `.npz`-bestand lezen. Als dat
bestand verouderd is, zijn de dynamische en motorresultaten niet meer
consistent.

## 17. Centrale samenvatting

Notebook 1 past de theorie uit Les 1, Les 2 en Les 5 toe op het
paraplu-stangenmechanisme.

De stappen zijn:

1. Beschrijf het mechanisme als vlak stangenmechanisme met een vrijheidsgraad.
2. Kies `s(t)` als invoer.
3. Maak een glad en condition-afhankelijk bewegingsprofiel.
4. Los per tijdstap de positie op met sluitingsvergelijkingen en `fsolve`.
5. Differentieer de sluitingsvergelijkingen voor snelheid.
6. Differentieer nogmaals voor versnelling.
7. Gebruik dezelfde matrix `A` voor snelheid, versnelling en conditionering.
8. Valideer de posities via residuals en dubbele ketens.
9. Sla alle kinematica op voor de dynamische notebooks.

De actuele run is numeriek betrouwbaar:

- sluitingsfout rond `5.95e-13 m`;
- geen echte singulariteit;
- `cond(A)_max = 68.36`;
- snelheid en versnelling blijven binnen de opgelegde limieten;
- de open stand is hellend en dus beter voor de latere constructieve belasting.

Als je Notebook 1 mondeling moet uitleggen, begin dan niet bij de code. Begin
bij de theorie:

> Mobiliteit `M = 1`, dus we leggen `s(t)` op. De hoeken volgen uit
> sluitingsvergelijkingen. Door differentieren krijgen we snelheid en
> versnelling. De matrix `A` geeft ook de conditionering. Daarna geven we de
> resultaten door aan de dynamica.
