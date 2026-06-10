# Notebook 1 - Code-uitleg

Deze tekst hoort bij `Notebook 1.ipynb`. Het doel is dat je de code kan
uitleggen tijdens het studeren of mondeling: welke parameters staan waar, welke
functies worden gebruikt, waarom die functies nodig zijn, welke theorie ze
uitvoeren en welke arrays ze doorgeven aan de volgende notebooks.

De plotcode zelf wordt niet regel per regel uitgelegd. Bij plotcellen staat
vooral welke berekende grootheden gebruikt worden en wat de figuur bewijst.

## 0. Overzicht van de codecellen

| Codecel | Inhoud | Belangrijkste output |
|---:|---|---|
| 4 | Imports, parameters, bewegingsprofiel, condition-map | `t`, `s`, `ds`, `dds`, condition-map |
| 6 | Hulpfuncties voor vectoren, sluitingsvergelijkingen en volledige kinematica | functies `rotate_vector`, `loop_closure_eqs`, `kinematics_umbrella` |
| 7 | Uitvoeren van positie-, snelheid- en versnellingsanalyse | `theta`, `dtheta`, `ddtheta`, `cond`, `residual_pos` |
| 9 | Dubbele positievalidatie | foutcurves voor `E`, `G`, `J` |
| 11 | Animatie-instellingen | figuurvenster, frame-indices |
| 12 | Animatiefunctie | visuele reconstructie van het mechanisme |
| 14 | Matrix `A` expliciet opbouwen | functie `compute_A_matrix` |
| 15 | Snelheidscontrole en snelheid punt `K` | `vel_residual`, `Kdot_x`, `Kdot_y`, `Kdot_norm` |
| 16 | Snelheidsfiguren | plots van `dtheta8` en `Kdot` |
| 18 | Versnelling punt `K` | `Kddot_x`, `Kddot_y`, `Kddot_norm` |
| 19 | Versnellingsfiguren | plots van `ddtheta8` en `Kddot` |
| 21 | Conditionering en singulariteiten | `critical_groups`, `cond_threshold` |
| 23 | Opslag naar `.npz` | `notebook1_kinematica_results.npz` |
| 25 | Compacte samenvatting | eindcontrole in tekstvorm |

## 1. Datastroom door Notebook 1

De notebook werkt in deze volgorde:

```text
parameters
  -> actuatortraject s(t), ds(t), dds(t)
  -> positieanalyse theta_i(t)
  -> snelheidsanalyse dtheta_i(t)
  -> versnellingsanalyse ddtheta_i(t)
  -> punt K: positie, snelheid, versnelling
  -> cond(A), residuals, validatie
  -> opslag naar notebook1_kinematica_results.npz
```

Als een parameter bovenaan verandert, moet de volledige notebook opnieuw gerund
worden. Anders kunnen latere cellen oude arrays blijven gebruiken.

## 2. Codecel 4 - Imports, parameters en traject

### 2.1 Imports

De cel start met:

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.optimize import fsolve
from scipy.interpolate import PchipInterpolator
from matplotlib.animation import FuncAnimation
from IPython.display import HTML
```

Betekenis:

- `numpy`: vectoren, matrices, trigonometrie, arrays;
- `matplotlib`: figuren en animatie;
- `fsolve`: niet-lineaire sluitingsvergelijkingen oplossen;
- `PchipInterpolator`: monotone interpolatie voor de condition-afhankelijke
  snelheidsfactor;
- `FuncAnimation` en `HTML`: animatie in de notebook.

`fsolve` is nodig omdat de positieanalyse niet-lineair is. `PchipInterpolator`
is gekozen omdat de snelheidsfactor als functie van `s` netjes en zonder rare
overshoot geinterpoleerd moet worden.

### 2.2 Geometrische parameters

De belangrijkste geometrie staat bovenaan:

```python
L1 = 2.5  # [m]

r3a = 0.507375
r3b = 0.428535
r4a = 0.941041
r4b = 0.655989
r5a = 0.251430
r5b = 0.404558
r6  = 0.680427
r7a = 0.428535
r7b = 0.251892
r8a = 0.404558
r8b = 0.499793
```

Wat deze parameters betekenen:

- `L1`: lengte van de mast in de visualisatie;
- `r3a`, `r3b`: twee segmenten van link 3;
- `r4a`, `r4b`: twee segmenten van link 4;
- `r5a`, `r5b`: twee segmenten van link 5;
- `r6`: lengte van link 6;
- `r7a`, `r7b`: segmenten van link 7 rond punt `H`;
- `r8a`, `r8b`: segmenten van link 8 tot aan punt `K`.

Belangrijk: een link met `a` en `b` is nog altijd een starre link. De opsplitsing
is alleen nodig omdat er een scharnierpunt of outputpunt op een tussenpositie
ligt.

### 2.3 Schuivergrenzen

Actuele finale waarden:

```python
s_open = 0.600000
s_closed = 1.875000
```

Interpretatie:

- `s` wordt naar beneden gemeten vanaf punt `C`;
- `s_closed` is de gesloten stand;
- `s_open` is de open stand;
- openen betekent dat `s` daalt.

De oude bijna horizontale open stand was `s_open = 0.125 m`. Die wordt niet meer
als finale hoofdcase gebruikt, omdat ze later te grote mast- en schuiverreacties
gaf.

### 2.4 Bewegingsrichting

```python
motion_direction = "open"
s_start_custom = s_closed
s_end_custom = s_open
```

De notebook kan drie richtingen aan:

- `"open"`: van `s_closed` naar `s_open`;
- `"close"`: van `s_open` naar `s_closed`;
- `"custom"`: eigen begin- en eindwaarde.

De huidige hoofdcase is `"open"`.

Daarna kiest de code:

```python
if motion_direction == "open":
    s_start = s_closed
    s_end = s_open
elif motion_direction == "close":
    s_start = s_open
    s_end = s_closed
elif motion_direction == "custom":
    s_start = s_start_custom
    s_end = s_end_custom
```

Waarom dit nuttig is:

- Notebook 1 kan later ook sluitbewegingen of tussenstanden simuleren;
- `custom` is nodig voor parameterstudies rond gedeeltelijk openen, omdat je
  dan niet telkens de definities van `s_open` en `s_closed` moet aanpassen;
- de rest van de analyse gebruikt alleen `s_start` en `s_end`;
- daardoor blijft de code algemeen.

Voor de finale hoofdcase gebruiken we `"open"`, maar `custom` blijft bewust in
de code. Een realistische overdekking hoeft niet altijd volledig open of dicht
te bewegen. Als je bijvoorbeeld wil onderzoeken of een minder open stand de
mastbelasting verlaagt, kan dat met:

```python
motion_direction = "custom"
s_start_custom = s_closed
s_end_custom = 0.800
```

De latere notebooks volgen dan automatisch het nieuwe traject, zolang Notebook
1 opnieuw gerund wordt en de `.npz` opnieuw wordt weggeschreven.

De check:

```python
s_min, s_max = sorted([s_open, s_closed])
if not (s_min <= s_start <= s_max and s_min <= s_end <= s_max):
    raise ValueError(...)
if np.isclose(s_start, s_end):
    raise ValueError(...)
```

is nodig omdat de geometrische tak alleen gevalideerd is tussen `s_open` en
`s_closed`. Zonder deze check zou een custom traject per ongeluk buiten het
geldige bereik kunnen gaan. Dan kan `fsolve` falen, naar een andere tak springen
of een fysisch ongeldige stand geven. De check op `s_start == s_end` voorkomt
een beweging met nul slag, waarbij snelheden, tijdschaling en latere
dynamica-interpretatie zinloos worden.

### 2.5 Trajectparameters

```python
motion_profile = "condition_scurve"
t_move_desired = 20.0
t_hold = 4.0
Ts = 0.05
auto_extend_t_move = True
actuator_v_limit = 0.25
actuator_a_limit = 0.10
```

Betekenis:

- `motion_profile`: type bewegingswet;
- `t_move_desired`: gewenste beweegtijd;
- `t_hold`: extra tijd na einde beweging, waarin de stand blijft staan;
- `Ts`: tijdstap van de simulatie;
- `auto_extend_t_move`: verlengt de beweegtijd als snelheids- of
  versnellingslimiet overschreden wordt;
- `actuator_v_limit`: maximale schuiversnelheid;
- `actuator_a_limit`: maximale schuiverversnelling.

Bij de finale run wordt de effectieve bewegingstijd:

```text
t_move_effective = 20.00 s
```

De gewenste `20 s` blijft binnen de ingestelde snelheids- en
versnellingslimieten, dus de effectieve bewegingstijd blijft ook `20.00 s`.

Waarom `20 s` als gewenste waarde?

- Het is snel genoeg dat de overdekking praktisch bruikbaar blijft.
- Het is traag genoeg om geen agressieve beweging van de schuiver en de doek te
  krijgen.
- De uiteindelijke tijd wordt niet blind opgelegd: `auto_extend_t_move`
  controleert de limieten en zou verlengen als dat nodig was.

Een langere tijd is technisch mogelijk. Dat verlaagt vooral de
inertiekrachten, de versnellingen en het piekvermogen. Het verlaagt niet de
zwaartekrachtarbeid en het neemt ook de statische houdkrachten niet weg. In
Notebook 3 en 4 blijkt dat zwaartekracht, wrijving en structurele reacties veel
belangrijker zijn dan inertie. Daarom is het niet zinvol om de beweging extreem
traag te maken; dat zou vooral gebruiksgemak kosten.

Waarom deze snelheids- en versnellingslimieten?

```text
actuator_v_limit = 0.25 m/s
actuator_a_limit = 0.10 m/s^2
```

Deze waarden zijn geen harde motorgrenzen, maar ontwerp-richtwaarden voor een
rustige beweging:

- `0.25 m/s` houdt de schuiverbeweging zichtbaar rustig en voorkomt een te
  snelle doekbeweging;
- `0.10 m/s^2` beperkt versnelling en dus inertiepieken;
- samen zorgen ze dat Notebook 4 geen onrealistisch agressieve
  vermogenspieken moet dimensioneren.

Als deze limieten strenger worden, wordt `t_move_effective` langer. Als ze
losser worden, kan de beweging korter, maar stijgen piekvermogen,
inertiekrachten en mogelijk trillingsgevoeligheid.

De tijdschaalstudie in Notebook 4 laat zien dat langer bewegen vooral het
piekvermogen verlaagt. De maatgevende kracht, het houdkoppel en de structurele
reacties blijven bijna gelijk, omdat die vooral door zwaartekracht, wrijving en
geometrie bepaald worden.

### 2.6 Condition-aware parameters

```python
condition_slow_gain = 1.25
condition_min_speed_factor = 0.25
condition_map_points = 301
```

Betekenis:

- `condition_slow_gain`: hoe sterk de snelheid daalt bij hoge `cond(A)`;
- `condition_min_speed_factor`: laagste toegelaten lokale snelheidsfactor;
- `condition_map_points`: aantal punten waarmee de condition-map over de slag
  wordt opgebouwd.

De code controleert:

```python
if not (0.0 < condition_min_speed_factor <= 1.0):
    raise ValueError(...)
```

Dit voorkomt ongeldige snelheidsfactoren.

Waarom precies deze waarden?

- `condition_min_speed_factor = 0.25`: zelfs in de slechtste zone blijft er nog
  beweging. De schuiver valt dus niet stil in de buurt van een gevoelige stand.
- `condition_slow_gain = 1.25`: de vertraging neemt duidelijk toe bij hoge
  `cond(A)`, maar niet zo agressief dat bijna de hele beweging onnodig traag
  wordt.
- `condition_map_points = 301`: voldoende fijn voor een gladde condition-map
  over de slag, zonder de notebook merkbaar traag te maken.
- `trajectory_shape_points = 3001`: fijnere interne grid voor de
  tijdsherverdeling, zodat `ds` en `dds` glad blijven.

Als `condition_min_speed_factor` hoger wordt, vertraagt het mechanisme minder
in moeilijke zones. Dat maakt de beweging sneller, maar minder voorzichtig bij
hoge `cond(A)`. Als de factor lager wordt, wordt de beweging daar rustiger,
maar de totale tijd neemt toe. Als `condition_slow_gain` groter wordt, wordt de
vertraging sterker geconcentreerd rond de slechtste standen. Bij een lagere
gain wordt de vertraging gelijkmatiger verdeeld.

Als `condition_map_points` te laag staat, kan de speed factor schokkerig of
onnauwkeurig worden. Als het veel hoger staat, verandert het resultaat bijna
niet meer, maar kost het meer rekentijd. `301` is daarom een pragmatische
keuze.

### 2.7 Startgok voor `fsolve`

```python
initial_theta_guess = np.array([...])
```

Deze startgok bepaalt welke configuratietak `fsolve` eerst zoekt. Dit is
belangrijk bij niet-lineaire mechanismen, omdat dezelfde `s` meerdere wiskundige
standen kan hebben. Als de startgok verkeerd is, kan de solver naar een andere
tak springen.

Deze waarden zijn dus niet zomaar numerieke versiering. Ze leggen de solver in
de buurt van de fysisch bedoelde configuratie. Daarna gebruikt de code telkens
de vorige gevonden oplossing als nieuwe startgok. Zo volgt de simulatie dezelfde
tak doorheen de volledige beweging.

Als je de geometrie sterk verandert, moet je deze startgok mogelijk aanpassen.
Bij kleine wijzigingen blijft ze meestal werken, omdat `track_branch` de
oplossing stap voor stap verder volgt.

### 2.8 `static_loop_closure_eqs(theta, s_k)`

Deze functie gebruikt alleen `theta` en `s_k` en geeft de zes
sluitingsvergelijkingen terug:

```python
def static_loop_closure_eqs(theta, s_k):
    ...
    return np.array([F1, F2, F3, F4, F5, F6])
```

Waarom "static" in de naam?

Deze functie gebruikt geen tijdsafhankelijke snelheden of versnellingen. Ze is
een pure positie-sluitingsfunctie. Ze wordt gebruikt om:

- de condition-map op te bouwen;
- bij een willekeurige `s` de juiste hoeken te vinden;
- de residual van de sluitingsvergelijkingen te controleren.

Die error checks zijn nuttig omdat een lage plotkwaliteit of een mooie animatie
niet genoeg bewijst dat de kinematica correct is. De residuals en dubbele
positievalidatie controleren numeriek of de gesloten lussen echt sluiten. Dat is
belangrijk omdat Notebook 2, 3 en 4 volledig op deze posities, snelheden en
versnellingen verder bouwen.

### 2.9 `static_velocity_matrix(theta)`

Deze functie bouwt de matrix `A` van het snelheidsstelsel:

```python
def static_velocity_matrix(theta):
    ...
    return A
```

Deze matrix komt uit de tijdsafgeleide van de sluitingsvergelijkingen:

```text
A(theta) * omega = b
```

In cel 4 wordt deze matrix al gebruikt voor `cond(A)`, nog voor de volledige
tijdsimulatie loopt.

### 2.10 `smooth_shape(u, profile)`

Deze functie geeft voor een genormaliseerde parameter `u`:

```python
h, dh, ddh = smooth_shape(u, profile)
```

Daarin is:

- `h`: dimensieloze positie;
- `dh`: dimensieloze snelheid;
- `ddh`: dimensieloze versnelling.

Voor `smooth_345` gebruikt de code:

```python
h = 10*u**3 - 15*u**4 + 6*u**5
dh = 30*u**2 - 60*u**3 + 30*u**4
ddh = 60*u - 180*u**2 + 120*u**3
```

Voor `smooth_4567`:

```python
h = 35*u**4 - 84*u**5 + 70*u**6 - 20*u**7
dh = 140*u**3 - 420*u**4 + 420*u**5 - 140*u**6
ddh = 420*u**2 - 1680*u**3 + 2100*u**4 - 840*u**5
```

Waarom deze functie?

De code kan snel wisselen tussen bewegingswetten zonder de rest van de
kinematica te veranderen.

### 2.11 `cumulative_trapezoid(y, x)`

Deze kleine hulpfunctie integreert numeriek met de trapeziumregel. Ze wordt
gebruikt om een nieuwe tijdsverdeling te maken voor `condition_scurve`.

Concept:

```text
meer integrale bijdrage = meer tijd in die zone
```

Dus als de snelheidsfactor laag is, krijgt die zone relatief meer tijd.

### 2.12 `interp_by_s(...)`

Deze functie interpoleert een grootheid als functie van `s`, ook als de
`s`-grid dalend is. Dat is nodig omdat openen van groot naar klein `s` gaat.

Zonder deze helper kunnen interpolaties fout lopen als `s_grid` niet oplopend
is.

### 2.13 `track_branch(s_values, theta_start)`

Deze functie loopt over een reeks schuiverstanden `s_values` en lost voor elke
stand de hoeken op:

```python
sol, _, ier, _ = fsolve(...)
```

Voor elke `s_k` slaat ze op:

- `theta_map[k]`: gevonden hoeken;
- `cond_map[k]`: condition number van `A`;
- `residual_map[k]`: sluitingsresidual;
- `failures`: aantal solverstappen zonder perfecte convergentie.

De cruciale regel is conceptueel:

```python
theta_guess = sol
```

De vorige oplossing wordt de startgok voor de volgende stap. Daardoor blijft de
code op dezelfde configuratietak.

### 2.14 `compute_condition_map()`

Deze functie bouwt de condition-map:

```python
s_map = np.linspace(s_closed, s_open, condition_map_points)
cond_map, residual_map, theta_map, failures = track_branch(...)
```

Waarom altijd van `s_closed` naar `s_open`?

Zo gebruikt een sluit- of custombeweging dezelfde geometrische tak als de
openbeweging. Dat maakt de resultaten consistenter.

### 2.15 `solve_theta_on_tracked_branch(s_target)`

Deze functie zoekt eerst in de condition-map de dichtstbijzijnde bekende stand
en gebruikt die als startgok voor `fsolve`.

Doel:

- robuust een hoekoplossing vinden voor een willekeurige `s_target`;
- vermijden dat `fsolve` plots een andere configuratietak kiest.

### 2.16 `smoothstep(q)`

```python
return q**3 * (10.0 - 15.0*q + 6.0*q**2)
```

Deze functie maakt een gladde overgang tussen 0 en 1. Ze wordt gebruikt om de
condition score niet hoekig of discontinu te maken.

Dit is dezelfde 3-4-5-vorm als bij `smooth_345`. Hier wordt ze niet gebruikt als
bewegingswet voor `s(t)`, maar als gladmakende mapping van de genormaliseerde
condition score.

### 2.17 `speed_factor_from_condition(cond_values)`

Deze functie zet `cond(A)` om naar een snelheidsfactor:

```python
cond_score = (log(cond) - log(cond_min)) / (log(cond_max) - log(cond_min))
cond_score = smoothstep(cond_score)
factor = 1 - (1 - condition_min_speed_factor) * cond_score**condition_slow_gain
```

Interpretatie:

- lage `cond(A)` geeft factor dicht bij 1;
- hoge `cond(A)` geeft lagere factor;
- de factor zakt nooit onder `condition_min_speed_factor`.

Dit is de kern van `condition_scurve`.

### 2.18 `make_speed_factor_interpolator(...)`

Deze functie maakt een interpolatiefunctie van de snelheidsfactor als functie
van `s`.

Waarom `PchipInterpolator`?

Omdat die vormbehoudend is en minder snel overshoot geeft dan een gewone hoge
orde interpolatie. Dat is nuttig voor een factor die tussen 0.25 en 1 moet
blijven.

### 2.19 `build_actuator_trajectory(t_move_local)`

Deze functie maakt de echte tijdreeksen:

```python
s_local, ds_local, dds_local, tau
```

Voor `smooth_345` en `smooth_4567` is dat direct:

```python
s = s_start + delta_s * h
ds = delta_s / t_move * dh
dds = delta_s / t_move**2 * ddh
```

Voor `condition_scurve` gebeurt extra werk:

1. maak een gladde `smooth_4567` basisbaan;
2. bepaal langs die baan de lokale snelheidsfactor;
3. maak een reparameterisatie zodat moeilijke zones meer tijd krijgen;
4. bereken `s`, `ds`, `dds` met de kettingregel.

Deze functie is belangrijk omdat ze de enige bron is voor het opgelegde
actuatortraject.

Historiek van de bewegingswetten:

1. **Sinusvormig profiel:** eenvoudig en herkenbaar uit de lessen, maar minder
   handig om randvoorwaarden en conditionering expliciet te sturen.
2. **Polynoomprofiel:** beter controleerbaar, omdat begin- en eindvoorwaarden
   op positie, snelheid en versnelling rechtstreeks in de vorm zitten.
3. **Smooth-profielen:** `smooth_345` en `smooth_4567` zorgen voor zachte
   start/stop zonder snelheids- en versnellingssprongen.
4. **Condition-scurve:** finale keuze. Dit behoudt de gladde `smooth_4567`
   basis, maar verdeelt de tijd anders: trager waar `cond(A)` hoog is.

De gewone smooth-profielen blijven dus niet omdat ze de hoofdkeuze zijn, maar
omdat ze nuttig zijn als vergelijkings- en fallbackopties. Ze maken de notebook
ook transparanter: je kan aantonen dat `condition_scurve` een uitbreiding is op
een klassieke gladde bewegingswet, geen volledig andere kinematische baan.

### 2.20 `required_time_scale(ds_local, dds_local)`

Deze functie controleert of de snelheids- en versnellingslimieten overschreden
worden.

Snelheid schaalt met:

```text
1 / tijd
```

Versnelling schaalt met:

```text
1 / tijd^2
```

Daarom gebruikt de code conceptueel:

```python
scale_v = max_abs_ds / actuator_v_limit
scale_a = sqrt(max_abs_dds / actuator_a_limit)
scale = max(1.0, scale_v, scale_a)
```

Als `auto_extend_t_move = True`, wordt `t_move` vermenigvuldigd met deze schaal.

### 2.21 Trajectfiguur

De plotcode aan het einde van cel 4 toont:

- `s(t)`;
- `ds(t)`;
- `dds(t)`;
- `cond(A)` en snelheidsfactor als functie van `s`.

De relevante analysevariabelen zijn:

```python
t, s, ds, dds
s_condition_map
cond_condition_map
speed_factor_condition_map
```

De plotcode zelf is alleen visualisatie. De analyse zit in de functies hierboven.

## 3. Codecel 6 - Kinematische hulpfuncties

### 3.1 `rotate_vector(z, theta)`

```python
def rotate_vector(z, theta):
    rotation_matrix = np.array([
        [np.cos(theta), -np.sin(theta)],
        [np.sin(theta),  np.cos(theta)]
    ])
    return np.dot(rotation_matrix, z)
```

Deze functie roteert een lokale linkvector naar het vaste assenstelsel.

Voorbeeld:

```python
rotate_vector(np.array([r3a, 0.0]), theta3)
```

geeft de globale vector van een segment op link 3.

Waarom nodig?

De geometrie van elke link is lokaal eenvoudig: lengte langs de lokale x-as.
Door te roteren met `theta` krijg je de echte globale positie.

### 3.2 `loop_closure_eqs(theta_init, s_k, ...)`

Deze functie is de positie-sluitingsfunctie voor de volledige kinematica.

Ze haalt uit `theta_init`:

```python
theta3, theta4, theta5, theta6, theta7, theta8
```

Daarna bouwt ze:

```python
F1, F2, F3, F4, F5, F6
```

en geeft terug:

```python
return np.array([F1, F2, F3, F4, F5, F6])
```

Dit is dezelfde theorie als in de studietekst:

```text
som van vectoren rond elke gesloten lus = 0
```

Deze functie wordt door `fsolve` gebruikt om de onbekende hoeken te vinden.

### 3.3 `kinematics_umbrella(...)`

Dit is de centrale rekenfunctie van Notebook 1. Ze doet in een lus over alle
tijdstappen:

1. positieanalyse met `fsolve`;
2. residualcontrole;
3. snelheidsanalyse via `A omega = B`;
4. condition number `cond(A)`;
5. versnellingsanalyse via `A alpha = C`;
6. opslaan van alle resultaten.

De belangrijkste input:

```python
r3a, r3b, r4a, r4b, r5a, r5b, r6, r7a, r7b, r8a
s, ds, dds
theta_guess
```

De belangrijkste output:

```python
theta3 ... theta8
dtheta3 ... dtheta8
ddtheta3 ... ddtheta8
cond
residual_pos
```

### 3.4 Positieanalyse in `kinematics_umbrella`

Per tijdstap:

```python
x, _, ier, message = fsolve(
    lambda x: loop_closure_eqs(...),
    theta_guess,
    full_output=True
)
```

`x` bevat de zes gevonden hoeken.

`ier` is de convergentievlag. Als `ier != 1`, is `fsolve` niet perfect
geconvergeerd. De code kan dan een waarschuwing geven.

Na oplossing:

```python
theta_guess = x
```

Zo wordt de oplossing van de huidige tijdstap de startgok voor de volgende
tijdstap.

### 3.5 Snelheidsanalyse in `kinematics_umbrella`

Na de positieanalyse bouwt de code de matrix `A` en het rechterlid `B`.

Conceptueel:

```python
omega = np.linalg.solve(A, B)
```

Daarna worden de elementen van `omega` opgeslagen als:

```python
dtheta3[k], ..., dtheta8[k]
```

De code berekent ook:

```python
cond[k] = np.linalg.cond(A)
```

Deze `cond` wordt later gebruikt voor singulariteitscontrole.

### 3.6 Versnellingsanalyse in `kinematics_umbrella`

De code bouwt een rechterlid `C` met:

- `dds[k]`;
- termen met `dtheta_i[k]**2`;
- sinus- en cosinustermen van de huidige hoeken.

Daarna:

```python
alpha = np.linalg.solve(A, C)
```

Dezelfde matrix `A` wordt dus opnieuw gebruikt.

De output wordt opgeslagen als:

```python
ddtheta3[k], ..., ddtheta8[k]
```

Dit is exact de theorie:

```text
A omega = B
A alpha = C
```

## 4. Codecel 7 - Kinematica uitvoeren

Deze cel maakt eerst lege arrays:

```python
theta3 = np.zeros_like(t)
...
dtheta3 = np.zeros_like(t)
...
ddtheta3 = np.zeros_like(t)
...
cond = np.zeros_like(t)
residual_pos = np.zeros_like(t)
```

Waarom vooraf arrays maken?

- efficienter dan telkens lijsten uitbreiden;
- alle arrays hebben exact dezelfde lengte als `t`;
- makkelijk door te geven aan latere notebooks.

Daarna wordt de centrale functie aangeroepen:

```python
[theta3, theta4, ..., cond, residual_pos] = kinematics_umbrella(...)
```

De belangrijkste input is:

```python
s, ds, dds
initial_theta_guess
```

De belangrijkste output is alle kinematica van het mechanisme.

Daarna maakt de cel een figuur met:

- linkhoeken;
- hoeksnelheden;
- hoekversnellingen;
- sluitingsresidual.

Plotcode zelf is minder belangrijk. De figuur gebruikt vooral:

```python
theta3 ... theta8
dtheta3 ... dtheta8
ddtheta3 ... ddtheta8
residual_pos
```

De printregels:

```python
print(f"Maximale closure residual: {np.max(residual_pos):.3e}")
```

zijn belangrijk als numerieke kwaliteitscheck.

## 5. Codecel 9 - Positievalidatie via dubbele ketens

Deze cel reconstrueert gewrichten op twee manieren.

Eerst worden arrays gemaakt:

```python
E3_pos, E4_pos
G5_pos, G7_pos
J8_pos, J7_pos
```

Daarna wordt per tijdstap de geometrie opgebouwd:

```python
C = np.array([0.0, 0.0])
B = C + np.array([0.0, -s[k]])
D = B + rotate_vector(np.array([r3a, 0.0]), theta3[k])
F_pt = D + rotate_vector(np.array([r5a, 0.0]), theta5[k])
I_pt = F_pt + rotate_vector(np.array([r6, 0.0]), theta6[k])
H = C + rotate_vector(np.array([r4a + r4b, 0.0]), theta4[k])
```

Daarna:

```python
E3_pos[k] = D + rotate_vector(np.array([r3b, 0.0]), theta3[k])
E4_pos[k] = C + rotate_vector(np.array([r4a, 0.0]), theta4[k])
```

Punt `E` wordt dus via link 3 en via link 4 berekend.

Voor `G`:

```python
G5_pos[k] = F_pt + rotate_vector(np.array([r5b, 0.0]), theta5[k])
G7_pos[k] = H + rotate_vector(np.array([-r7a, 0.0]), theta7[k])
```

Voor `J`:

```python
J8_pos[k] = I_pt + rotate_vector(np.array([r8a, 0.0]), theta8[k])
J7_pos[k] = H + rotate_vector(np.array([r7b, 0.0]), theta7[k])
```

Belangrijk voor link 7:

```text
G = H - r7a * e7
J = H + r7b * e7
```

Deze validatie is belangrijk omdat ze controleert of de punten echt samenvallen,
niet alleen of `fsolve` een lage residual geeft.

De plotcode toont absolute en relatieve fouten per gewricht. Wat je moet
onthouden:

- de analyse is goed als de fouten rond machineprecisie liggen;
- grote fouten zouden wijzen op een verkeerde puntconstructie of verkeerde
  segmentlengte.

## 6. Codecel 11 en 12 - Animatie

### 6.1 Cel 11: instellingen

Cel 11 zet:

```python
plt.ioff()
fig2, ax = plt.subplots()
```

en bepaalt:

- hoeveel frames getoond worden;
- welke tijdindices gebruikt worden;
- de grenzen van het tekenvenster.

De animatie-instellingen veranderen de analyse niet. Ze bepalen alleen hoe het
mechanisme getekend wordt.

### 6.2 Cel 12: `update(frame)`

De functie `update(frame)` reconstrueert voor een gekozen tijdstap alle punten:

```python
C, B, D, E, F, G, H, I, J, K
```

Daarna tekent ze de links tussen die punten.

Belangrijk voor de analyse:

- de animatie gebruikt dezelfde `theta_i` en `s` als de berekeningen;
- ze is dus een visualisatie van de oplossing, geen nieuwe simulatie;
- ze helpt om te zien of de configuratietak logisch blijft;
- ze toont dat de finale open stand hellend is.

De matplotlib-regels zelf hoef je niet mondeling te kennen. Je moet wel kunnen
zeggen dat de animatie de berekende geometrie reconstrueert.

## 7. Codecel 14 - Matrix `A`

Deze cel definieert:

```python
def compute_A_matrix(theta, r3a, r3b, r4a, r4b, r5a, r5b, r6, r7a, r7b, r8a):
    ...
    return A
```

Deze functie is eigenlijk dezelfde `A` als in de snelheidsanalyse van
`kinematics_umbrella`, maar hier apart beschikbaar voor controles en latere
uitleg.

Input:

- `theta`: vector met `theta3` tot `theta8`;
- geometrische parameters.

Output:

- `A`: 6x6 matrix van het snelheidsstelsel.

Waarom apart?

- het maakt de snelheidscontrole leesbaar;
- dezelfde matrix kan gebruikt worden voor `cond(A)`;
- het vermijdt dat `A` op verschillende plaatsen met andere formules wordt
  opgebouwd.

## 8. Codecel 15 - Snelheidscontrole en snelheid van punt K

### 8.1 `perp(v)`

```python
def perp(v):
    return np.array([-v[1], v[0]])
```

Dit is de 2D-vorm van een rotatie over 90 graden. Ze wordt gebruikt voor:

```text
omega x r
```

In 2D:

```text
omega x (x, y) = omega * (-y, x)
```

### 8.2 Snelheidsresidual

De cel bouwt per tijdstap:

```python
theta_k = [...]
omega_k = [...]
A = compute_A_matrix(...)
B = np.array([0.0, ds[k], 0.0, 0.0, 0.0, 0.0])
vel_residual[k] = np.linalg.norm(A @ omega_k - B)
```

Dit controleert of de eerder berekende hoeksnelheden echt voldoen aan:

```text
A omega = B
```

Een kleine residual betekent dat de snelheidsanalyse consistent is.

### 8.3 Snelheid van punt K

De cel bouwt de snelheid op langs de keten:

```python
vB = np.array([0.0, -ds[k]])
vD = vB + dtheta3[k] * perp(rBD)
vF = vD + dtheta5[k] * perp(rDF)
vI = vF + dtheta6[k] * perp(rFI)
vK = vI + dtheta8[k] * perp(rIK)
```

Daarna:

```python
Kdot_x[k] = vK[0]
Kdot_y[k] = vK[1]
Kdot_norm[k] = np.linalg.norm(vK)
```

Deze waarden zijn belangrijk voor:

- beoordelen hoe snel de overdekking opent;
- latere dynamica;
- vraag 4 over bewegingsprofiel;
- vraag 8 over inertie.

Actueel:

```text
max |v_K| = 0.4410 m/s
```

## 9. Codecel 16 - Snelheidsfiguren

Deze cel plot:

- `dtheta8`;
- `Kdot_x`;
- `Kdot_y`;
- `Kdot_norm`;
- dezelfde grootheden als functie van `s`;
- alle hoeksnelheden.

De plotcode zelf is niet belangrijk. Belangrijk is welke arrays geplot worden:

```python
dtheta8
Kdot_x, Kdot_y, Kdot_norm
dtheta3 ... dtheta8
```

Waarom nuttig?

- je ziet of de outputbeweging rustig blijft;
- je ziet waar punt `K` het snelst beweegt;
- je kan de beweging koppelen aan het gekozen schuivertraject.

## 10. Codecel 18 - Versnelling van punt K

Deze cel berekent:

```python
Kddot_x
Kddot_y
Kddot_norm
```

Ze gebruikt:

- `theta8`;
- `dtheta8`;
- `ddtheta8`;
- de positie van punt `I`;
- de snelheid en versnelling van de tussenpunten `B`, `D`, `F`, `I`.

Voor punt `K` gebruikt de code:

```python
Kddot_x[k] = ax_I - ddtheta8_k * y_IK - dtheta8_k**2 * x_IK
Kddot_y[k] = ay_I + ddtheta8_k * x_IK - dtheta8_k**2 * y_IK
```

Theorie:

```text
a_K = a_I + alpha8 x r_IK - omega8^2 r_IK
```

De termen met `ddtheta8` zijn tangentieel. De termen met `dtheta8**2` zijn
centripetaal.

Actueel:

```text
max |a_K| = 0.1745 m/s^2
```

Deze versnelling is klein, wat past bij de rustige beweging.

## 11. Codecel 19 - Versnellingsfiguren

Deze cel plot:

- `ddtheta8`;
- `Kddot_x`;
- `Kddot_y`;
- `Kddot_norm`;
- dezelfde grootheden als functie van `s`;
- alle hoekversnellingen.

Belangrijk is niet de plotcode, maar de betekenis:

- de versnelling van punt `K` is de dynamische input voor Notebook 2;
- pieken in versnelling kunnen leiden tot inertiekrachten;
- bij de huidige trage beweging blijven die klein.

## 12. Codecel 21 - Conditionering en singulariteiten

### 12.1 Basisstatistieken

De cel begint met:

```python
cond_finite = cond[np.isfinite(cond)]
```

Daarna print ze:

```python
min cond(A)
gemiddelde cond(A)
max cond(A)
```

Actueel:

```text
max cond(A) = 68.36
```

### 12.2 Drempel voor kritische configuraties

```python
cond_threshold_abs = 1e3
cond_threshold_percentile = np.percentile(cond_finite, 95)
cond_threshold = max(cond_threshold_abs, cond_threshold_percentile)
```

Waarom deze drempel?

- `1e3` is een absolute grens voor echt slechte conditionering;
- het 95ste percentiel geeft een relatieve grens als een ander ontwerp andere
  schaalwaarden heeft;
- de `max` zorgt dat de analyse niet te snel alarm slaat.

### 12.3 Kritische zones groeperen

```python
critical_idx = np.where(cond > cond_threshold)[0]
```

Opeenvolgende indices boven de drempel worden gegroepeerd in:

```python
critical_groups
```

Per groep zoekt de code:

- beginindex;
- eindindex;
- piekindex;
- `s` en `t` bij de piek;
- maximale `cond(A)`.

In de actuele hoofdcase ligt `cond(A)` onder `1e3`, dus er is geen echte
kritische zone volgens deze absolute grens.

### 12.4 `_add_drempel(...)`

Dit is een kleine plothelper. Ze tekent de drempel als die in beeld ligt, of
plaatst tekst als de drempel buiten de y-as valt.

Deze functie is puur voor visualisatie.

### 12.5 Conditioneringsfiguren

De cel plot:

- `cond(A)` als functie van `t`;
- `cond(A)` als functie van `s`.

Deze figuren zijn nuttig voor vraag 6:

```text
Heeft het ontwerp dode punten of singulariteiten?
```

Kerninterpretatie:

- geen echte singulariteit;
- wel verhoogde gevoeligheid bij de gesloten stand;
- daarom gebruikt het traject `condition_scurve`.

## 13. Codecel 23 - Opslag voor Notebook 2

Deze cel schrijft:

```python
notebook1_kinematica_results.npz
```

met:

```python
np.savez(results_path, ...)
```

Belangrijkste opgeslagen groepen:

### 13.1 Tijd en traject

```python
t, Ts
s, ds, dds
motion_direction
motion_profile
s_open, s_closed
s_start, s_end
t_move_desired, t_move_effective
t_hold
```

Dit zorgt dat latere notebooks exact dezelfde beweging gebruiken.

### 13.2 Trajectlimieten

```python
auto_extend_t_move
actuator_v_limit
actuator_a_limit
condition_slow_gain
condition_min_speed_factor
```

Deze metadata is belangrijk om later te weten hoe het traject gekozen werd.

### 13.3 Hoeken

```python
theta3 ... theta8
dtheta3 ... dtheta8
ddtheta3 ... ddtheta8
```

Dit zijn de belangrijkste kinematische outputs voor Notebook 2 en 3.

### 13.4 Punt K

```python
Kx, Ky
Kdot_x, Kdot_y, Kdot_norm
Kddot_x, Kddot_y, Kddot_norm
```

Deze waarden worden gebruikt om de beweging van de rib/output te analyseren.

### 13.5 Controlevariabelen

```python
cond
residual_pos
```

Hiermee kunnen latere notebooks of teksten checken of de kinematische run
betrouwbaar was.

### 13.6 Geometrische parameters

Alle linklengtes worden opgeslagen. Daardoor kunnen Notebook 2 en 3 de punten en
zwaartepunten reconstrueren zonder de geometrie opnieuw hardcoded anders te
definieren.

## 14. Codecel 25 - Samenvatting

Deze cel zoekt:

```python
i_cond_max = int(np.nanargmax(cond))
i_speed_max = int(np.nanargmax(Kdot_norm))
i_acc_max = int(np.nanargmax(Kddot_norm))
```

Daarna print ze:

- schuiverbereik;
- maximale sluitingsfout;
- maximale snelheid van punt `K`;
- maximale versnelling van punt `K`;
- min/gem/max van `cond(A)`;
- waar de grootste `cond(A)` optreedt;
- aantal kritische zones;
- pad naar het `.npz`-bestand.

Waarom deze cel nuttig is:

- snelle controle na `Run All`;
- direct zien of een parameterwijziging problemen gaf;
- cijfers kunnen gebruikt worden in de mondelinge verdediging.

## 15. Wat is analysecode en wat is alleen plotcode?

### Analysecode

Dit moet je echt begrijpen:

- parameterdefinitie;
- bewegingsprofiel;
- `static_loop_closure_eqs`;
- `static_velocity_matrix`;
- `track_branch`;
- `speed_factor_from_condition`;
- `build_actuator_trajectory`;
- `loop_closure_eqs`;
- `kinematics_umbrella`;
- `compute_A_matrix`;
- `perp`;
- snelheid en versnelling van punt `K`;
- `cond(A)`-analyse;
- `.npz`-opslag.

### Plotcode

Dit moet je niet regel per regel kennen:

- `plt.subplots`;
- `ax.plot`;
- `set_xlabel`, `set_ylabel`, `legend`, `grid`;
- animatie-opmaak.

Wat je wel moet weten:

- welke variabelen geplot worden;
- waarom die figuur nuttig is;
- welke conclusie je uit de figuur haalt.

## 16. Mogelijke opschoning van Notebook 1

De code is inhoudelijk coherent. Ik zou niet vlak voor de deadline grote
refactors doen. Wel zijn er enkele mogelijke latere opschoonpunten:

1. De plotcellen kunnen later compacter gemaakt worden met helperfuncties.
2. De matrix `A` wordt in meerdere contexten opgebouwd; dat is begrijpelijk,
   maar kan later nog meer gecentraliseerd worden.
3. De animatiecode is vrij lang, maar logisch omdat alle punten expliciet
   getekend worden.
4. De condition-map en de volledige tijdsimulatie gebruiken verwante
   sluitingsfuncties. Dat is niet fout; het maakt de trajectopbouw robuuster.

Voor de finale versie is het belangrijkste dat de code uitlegbaar blijft en dat
de `.npz`-output consistent is met Notebook 2, 3 en 4. Dat is nu het geval.

## 17. Korte uitleg per codecel voor mondeling

Als je snel door Notebook 1 moet gaan:

1. **Cel 4:** definieert parameters en maakt het schuivertraject. Hier zit de
   keuze `s_open = 0.600 m` en `condition_scurve`.
2. **Cel 6:** definieert de sluitingsvergelijkingen en centrale kinematische
   solver.
3. **Cel 7:** voert de kinematica effectief uit en controleert de residual.
4. **Cel 9:** valideert dat gewrichten via dubbele ketens samenvallen.
5. **Cel 11-12:** visualiseert het mechanisme als animatie.
6. **Cel 14-15:** controleert het snelheidsstelsel en berekent snelheid van
   punt `K`.
7. **Cel 18:** berekent versnelling van punt `K`.
8. **Cel 21:** analyseert `cond(A)` en mogelijke singulariteiten.
9. **Cel 23:** schrijft alle resultaten weg naar `.npz`.
10. **Cel 25:** geeft de numerieke eindcontrole.

De centrale boodschap:

> De code volgt de theorie uit de lessen: parameters en bewegingswet, dan
> positie via sluitingsvergelijkingen, snelheid via differentiatie, versnelling
> via nogmaals differentieren, conditionering via de matrix `A`, en opslag voor
> de dynamische notebooks.
