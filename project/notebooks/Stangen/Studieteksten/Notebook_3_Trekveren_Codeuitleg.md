# Notebook 3 - Trekveren - Code-uitleg

Deze tekst hoort bij `Notebook 3 - Trekveren.ipynb`. De notebook gebruikt bijna
dezelfde codebasis als `Notebook 3.ipynb`, maar voegt een trekveerpakket toe als
bekende externe kracht op de schuiver.

De gedeelde codeblokken blijven inhoudelijk gelijk:

- data laden uit Notebook 1 en 2;
- massa's en inerties gebruiken;
- rigid-body kinematica;
- Newton-Euler stelsel;
- zwaartekracht;
- schuiverwrijving;
- pinwrijving;
- energiebalans;
- houdanalyse;
- vermogen en arbeids-surplus;
- opslag naar `.npz`.

Als een formule in `Notebook 3.ipynb` aangepast wordt, moet dezelfde wijziging
ook hier gebeuren, behalve waar deze notebook expliciet de veer toevoegt.

## 0. Overzicht van de codecellen

| Codecel | Inhoud | Belangrijkste output |
|---:|---|---|
| 3 | Data laden uit Notebook 1, 2 en baseline Notebook 3 | kinematica, massa's, baseline zonder veer |
| 5 | Parameters voor wrijving, actuator en trekveren | `mu_slider`, `mu_pin`, `spring_*` |
| 7 | Veermodel en automatische sizing | `spring_force_up_total`, `spring_energy_stored` |
| 9 | Rigid-body kinematica | punten, zwaartepunten, `K_pos`, `K_vel`, `K_acc` |
| 11 | Newton-Euler stelsel met veerkracht | `build_inverse_dynamics_system(... include_spring ...)` |
| 13 | Wrijvingsmodel en iteratieve solver | `solve_case(... include_spring ...)` |
| 15 | Cases oplossen en krachtdecompositie | `F_drive_s_total`, `F_spring_assist_component` |
| 17 | Energiebalans met veer | `energy_balance_error`, `Ekin`, vermogensbalans |
| 19 | Kracht- en energievergelijking | baseline versus trekveren |
| 21 | Statische houdanalyse | `F_hold_s_curve`, `T_hold_lock_required_curve` |
| 23 | Vermogen en aandrijfvoorbereiding | quick motorcheck zonder/met veer |
| 25 | Arbeids-surplus | `A_max_full`, `A_max_baseline`, `spring_energy_delta` |
| 27 | Framebelasting en veeranker | `F_spring_anchor_norm`, framekrachten |
| 30 | Opslag naar `.npz` | `notebook3_trekveren_results.npz` |

## 1. Datastroom door Notebook 3 - Trekveren

De datastroom is:

```text
notebook1_kinematica_results.npz
  -> kinematica

notebook2_dynamica_results.npz
  -> massa's, inerties, inertie-only referentie

notebook3_gravity_friction_results.npz
  -> baseline zonder veer

veerparameters
  -> veerkracht als functie van s
  -> veerenergie

Newton-Euler met veer
  -> zwaartekracht + wrijving + trekveer
  -> vergelijking met baseline

notebook3_trekveren_results.npz
  -> Notebook-4-compatibele loadcase met extra spring-keys
```

Belangrijk:

- de veer verandert `s(t)` niet;
- de veer verandert massa's en inerties niet;
- de veer wordt alleen als bekende opwaartse kracht op de schuiver toegevoegd;
- de baseline zonder veer wordt ingeladen om eerlijk te vergelijken.

## 2. Codecel 3 - Setup en data laden

Deze cel laadt drie bestanden:

```python
kin = np.load("notebook1_kinematica_results.npz")
dyn = np.load("notebook2_dynamica_results.npz")
baseline = np.load("notebook3_gravity_friction_results.npz")
```

Notebook 1 levert kinematica. Notebook 2 levert massa's en inerties. De baseline
uit Notebook 3 levert de krachten zonder veer.

### Baseline-data

Belangrijke baseline-arrays:

```python
F_drive_s_baseline = baseline["F_drive_s_total"]
F_drive_s_gravity_baseline = baseline["F_drive_s_gravity"]
F_gravity_component_baseline = baseline["F_gravity_component"]
F_friction_component_baseline = baseline["F_friction_component"]
P_act_baseline = baseline["P_act_total"]
F_hold_s_baseline_curve = baseline["F_hold_s_curve"]
```

Waarom nodig?

De veernotebook moet niet alleen een nieuwe oplossing maken. Ze moet ook tonen:

- hoeveel de aandrijfkracht daalt;
- hoeveel de houdkracht daalt;
- hoeveel vermogen/arbeid verandert;
- wat de veer kost in opgeslagen energie.

Daarvoor is de baseline zonder veer nodig.

### Massa's en rod-parameters

Net als Notebook 3 laadt deze notebook:

- `masses`;
- `inertias`;
- `payload_mass_K`;
- `line_mass_density`;
- `rod_outer_diameter`;
- `rod_wall_thickness`;
- `rod_tube_mass_per_m`;
- `rod_fittings_line_mass_allowance`.

Daardoor blijft de veercase exact hetzelfde mechanische systeem, maar met een
extra bekende veerkracht.

## 3. Codecel 5 - Parameters

Deze cel bevat dezelfde parameters als Notebook 3:

```python
g = 9.81
mu_slider = 0.08
mu_slider_static = 0.12
c_slider = 0.0
mu_pin = 0.05
pin_radius = 0.006
friction_iterations = 20
friction_tol = 1e-8
actuator_efficiency = 0.78
actuator_safety_factor = 1.50
drive_pulley_radius_nb3 = 0.025
```

Die waarden moeten gelijk blijven aan Notebook 3, omdat anders de vergelijking
zonder/met veer niet eerlijk is.

De schuiverwrijving is bewust iets conservatiever gekozen dan een ideaal
rollager. Droge kunststof-op-staal lineaire geleidingen zitten typisch in de
orde `mu = 0.05-0.23`; in een buitenopstelling komen daar uitlijning, vuil en
voorspanning bij. Daarom gebruiken we `mu_slider = 0.08` dynamisch en
`mu_slider_static = 0.12` voor de statische houdanalyse. `c_slider = 0.0`
houdt de analyse bij Coulombwrijving; er wordt geen extra viskeuze demping
ingevoerd.

De aandrijfparameter is hier een poelie-/riem-equivalent. De oude naam
`screw_lead` blijft alleen als compatibiliteitskey in de output bestaan.

### Veerparameters

Extra parameters:

```python
spring_count = 2
spring_design_mode = "fraction_of_baseline_hold"
spring_assist_fraction_open = 0.60
spring_assist_fraction_closed = 0.70
spring_max_assist_fraction = 0.80
spring_force_open_total_manual = 20.0
spring_force_closed_total_manual = 50.0
```

Betekenis:

- `spring_count`: aantal parallelle trekveren;
- `spring_design_mode`: automatische sizing of handmatige krachten;
- `spring_assist_fraction_open`: gewenste assist als fractie van baseline
  houdkracht in open stand;
- `spring_assist_fraction_closed`: idem in gesloten stand;
- `spring_max_assist_fraction`: bovengrens zodat de veer niet te dominant wordt;
- `spring_force_*_manual`: handmatige fallback-waarden.

De checks in deze cel voorkomen ongeldige instellingen:

- minstens een veer;
- geldige mode;
- maximale assistfractie tussen 0 en 1;
- geen negatieve assistfracties.

## 4. Codecel 7 - Veermodel

Dit is de belangrijkste extra cel ten opzichte van Notebook 3.

### 4.1 Open en gesloten stand

```python
s_open = float(np.min(s))
s_closed = float(np.max(s))
stroke = s_closed - s_open
```

Omdat `s` positief naar beneden is:

- `s_open` is de kleinste `s`;
- `s_closed` is de grootste `s`;
- `stroke` is positief.

### 4.2 Baseline-houdkracht als ontwerpbasis

De code maakt een positieve "opwaarts nodig"-curve:

```python
upward_hold_required_curve = np.maximum(-F_hold_s_baseline_curve, 0.0)
```

Waarom het minteken?

`F_hold_s_baseline_curve` staat in de `s`-richting. Een negatieve `F_s` betekent
opwaartse kracht, dus hulp bij openen. Voor veersizing willen we een positieve
opwaartse kracht.

### 4.3 Automatische sizing

Bij:

```python
spring_design_mode = "fraction_of_baseline_hold"
```

kiest de code:

```python
spring_force_open_target =
    spring_assist_fraction_open * upward_hold_required_curve[open_idx]

spring_force_closed_target =
    spring_assist_fraction_closed * upward_hold_required_curve[closed_idx]
```

In de directe-veermode ligt de veerstijfheid niet meer vrij volgens deze twee
doelkrachten. De notebook gebruikt een vaste, realistische lage veerconstante:

```python
spring_direct_rate_per_spring_N_per_mm = 0.010
```

Dus:

```python
spring_k_per_spring = 1000 * spring_direct_rate_per_spring_N_per_mm
spring_k_total = spring_count * spring_k_per_spring
```

Omdat de stijfheid vastligt, kiest de code daarna de open-kracht of voorspanning
zodat de gesloten kracht onder de doelkracht en onder de maximale
assistfractie blijft. Er is dus geen extra veeroverbrenging:
`spring_motion_ratio = 1`.

Voor de huidige single-mechanism case wordt dat ongeveer:

```text
directe veerstijfheid per veer = 0.010 N/mm = 10 N/m
totale veerstijfheid           = 20.0 N/m
fysieke veerweg over slag      = 1275 mm
fysieke veerkracht per veer    = 9.45 -> 22.20 N
```

De veer is dus een lange, relatief slappe trekveer langs de mast. Dat past bij
de eenvoudige fysieke opstelling zonder extra veeroverbrenging.

### 4.4 Max assist cap

De code berekent een assistlimiet:

```python
assist_cap_curve = spring_max_assist_fraction * upward_hold_required_curve
```

Als de gekozen veer ergens boven die limiet komt, wordt ze geschaald met:

```python
spring_scale_factor
```

Waarom?

De veer mag niet te sterk worden. De motor/rem moet controle houden in alle
standen.

### 4.5 Veerkracht en veerenergie

De kracht tijdens de beweging:

```python
spring_force_up_total =
    spring_force_open_total + spring_k_total * (s - s_open)
```

De kracht in `s`-richting:

```python
spring_force_s_total = -spring_force_up_total
```

De opgeslagen veerenergie:

```python
spring_energy_stored =
    spring_force_open_total * (s - s_open)
    + 0.5 * spring_k_total * (s - s_open)**2
```

Belangrijke outputs:

- `spring_force_open_total`;
- `spring_force_closed_total`;
- `spring_k_total`;
- `spring_k_per_spring`;
- `spring_physical_rate_per_spring_N_per_mm`;
- `spring_motion_ratio`;
- `spring_physical_extension_closed`;
- `spring_force_open_physical_per_spring`;
- `spring_force_closed_physical_per_spring`;
- `spring_energy_delta`;
- `spring_assist_fraction_curve`.

## 5. Codecel 9 - Rigid-body kinematica

Deze cel is inhoudelijk dezelfde als codecel 7 van Notebook 3.

Ze maakt:

- `perp`;
- `rigid_point`;
- punten `B`, `C`, `D`, `E`, `F`, `G`, `H`, `I`, `J`, `K`;
- `cg_pos`, `cg_vel`, `cg_acc`, `alpha`.

De veer verandert deze kinematica niet. Dit is belangrijk:

> De trekveer wijzigt de krachten, niet de opgelegde baan.

## 6. Codecel 11 - Newton-Euler met trekveer

Deze cel is bijna dezelfde als Notebook 3, maar de functie heeft extra
argumenten:

```python
def build_inverse_dynamics_system(k,
    include_gravity=True,
    include_spring=False,
    spring_force_up=0.0,
    slider_friction_s=0.0,
    pin_moments_by_link=None,
    static=False):
```

### Veer als bekende kracht

In `known_forces_for(link_id)` staat:

```python
if link_id == 2 and include_spring:
    known.append((np.array([0.0, spring_force_up]), B_pos[k]))
```

Betekenis:

- de veer grijpt aan op link 2, de schuiver;
- aangrijpingspunt is `B_pos[k]`;
- richting is globaal omhoog;
- de kracht is bekend en gaat dus naar het rechterlid.

De rest van het Newton-Euler-stelsel blijft gelijk:

- zwaartekracht als bekende kracht;
- schuiverwrijving als bekende kracht;
- pinwrijving als bekende momenten;
- onbekende reacties blijven in `A`.

## 7. Codecel 13 - Wrijvingsmodel en solver

Deze cel is bijna dezelfde als Notebook 3, maar `solve_case` heeft een extra
argument:

```python
def solve_case(include_gravity=True,
               include_friction=False,
               include_spring=False,
               static=False):
```

Per tijdstap wordt:

```python
spring_force_up_k = float(spring_force_up_total[k]) if include_spring else 0.0
```

Daarna wordt die kracht meegegeven aan `build_inverse_dynamics_system`.

De wrijvingsiteratie blijft hetzelfde:

1. oplossen met huidige wrijving;
2. reacties gebruiken om schuiver- en pinwrijving te berekenen;
3. opnieuw oplossen;
4. herhalen tot convergentie.

Belangrijk:

> De veer verandert de wrijvingsmethode niet. Ze verandert alleen de bekende
> krachtbalans van de schuiver.

## 8. Codecel 15 - Oplossingen en controles

De notebook lost drie cases op:

```python
case_inertia_check =
    solve_case(include_gravity=False, include_friction=False, include_spring=False)

case_gravity_spring =
    solve_case(include_gravity=True, include_friction=False, include_spring=True)

case_total =
    solve_case(include_gravity=True, include_friction=True, include_spring=True)
```

Let op:

- de inertiecheck heeft geen veer;
- `case_gravity_spring` bevat zwaartekracht en veer, maar geen wrijving;
- `case_total` bevat zwaartekracht, veer en wrijving.

De aandrijfkrachten:

```python
F_drive_s_inertia_check = -vars_inertia_check["F_act_y"]
F_drive_s_gravity = -vars_gravity_spring["F_act_y"]
F_drive_s_total = -vars_total["F_act_y"]
```

In deze notebook betekent `F_drive_s_gravity` dus:

```text
inertie + zwaartekracht + trekveer, zonder wrijving
```

Dat is anders dan in Notebook 3 zonder veer. Daarom slaat de notebook ook een
duidelijke key `F_drive_s_gravity_spring` op.

### Veercomponent

```python
F_spring_assist_component =
    F_drive_s_gravity - F_drive_s_gravity_baseline
```

Dit toont hoeveel de veer de kracht verandert ten opzichte van de baseline.

### Openingsreductie

De code bepaalt:

```python
baseline_open_peak
spring_open_peak
opening_peak_reduction
```

Zo kan de notebook aantonen dat de veer effectief de openingspiek verlaagt.

## 9. Codecel 17 - Energiebalans met veer

De energiebalans wordt uitgebreid met veerenergie. Conceptueel:

```math
P_act =
dEkin/dt + P_gravity + P_friction + dE_spring/dt
```

De code gebruikt:

```python
P_actuator_val = F_drive_s_total * ds
```

en neemt ook `spring_energy_stored` mee in de controle. Zo wordt gecontroleerd
dat de motorbelasting consistent is met:

- kinetische energie;
- zwaartekrachtenergie;
- wrijvingsverlies;
- energie die in/uit de veer gaat.

## 10. Codecel 19 - Kracht- en energievergelijking

Deze cel maakt vergelijkende arrays:

```python
force_reduction_abs = abs(F_drive_s_baseline) - abs(F_drive_s_total)
P_act_total_preview = F_drive_s_total * ds
E_act_total_preview = integraal(P_act_total_preview)
```

De figuren tonen:

- veerkracht per stand;
- veerenergie per stand;
- `F_s` zonder en met veer;
- krachtreductie;
- vermogen zonder en met veer;
- cumulatieve arbeid zonder en met veer.

De plotcode zelf is niet belangrijk; de belangrijke boodschap is dat de veer de
motorbelasting verlaagt voor hetzelfde traject.

## 11. Codecel 21 - Statische houdanalyse

De houdanalyse wordt opnieuw uitgevoerd, maar nu met veer:

```python
build_inverse_dynamics_system(...,
    include_gravity=True,
    include_spring=True,
    spring_force_up=float(spring_force_up_total[k]),
    static=True)
```

Belangrijke outputs:

- `F_hold_s_curve`;
- `R_Ax_hold_curve`;
- `static_slider_capacity_curve`;
- `T_hold_motor_curve`;
- `T_hold_lock_required_curve`;
- `T_hold_lock_required_baseline_curve`.

De vergelijking met baseline toont hoeveel de rem-/houdvereiste daalt door de
veer.

Belangrijk:

> Ook met trekveren blijft een rem of mechanische vergrendeling nodig.

## 12. Codecel 23 - Vermogen en aandrijfvoorbereiding

Deze cel berekent:

```python
F_required_s_total = F_drive_s_total.copy()
P_act_total = F_required_s_total * ds
E_act_total = integraal(P_act_total)
```

Daarna vergelijkt ze met baseline:

```python
T_motor_total
T_motor_baseline
P_positive_peak
P_positive_peak_baseline
```

Ook is er een compacte quick check voor een poelie:

```python
drive_pulley_radius_nb3 = 0.025
quick_drive_efficiency = ...
quick_line_force_design = ...
quick_T_pulley_design = ...
quick_T_hold_design = ...
```

Deze quick check is nuttig voor orde-grootte, maar Notebook 4 blijft leidend
voor finale motorselectie.

## 13. Codecel 25 - Arbeids-surplus

De cel berekent arbeids-surplus met veer:

```python
A_theta_full = cumsum((P_load_full - P_avg_full) * Ts)
A_max_full = A_theta_full.max() - A_theta_full.min()
```

En vergelijkt met baseline:

```python
A_theta_baseline
A_max_baseline
spring_energy_delta
```

De figuur met `A_max` en `E_veer` is belangrijk voor de vraag over energieopslag:

- een vliegwiel is niet logisch voor deze trage positionering;
- een trekveer is wel logisch als zwaartekrachtcompensatie;
- de veer verlaagt openingslast maar moet bij sluiten opnieuw worden opgespannen.

## 14. Codecel 27 - Framebelasting en veeranker

Deze cel berekent de extra kracht op het veeranker:

```python
F_spring_global = [0, spring_force_up_total]
F_spring_anchor_global = -F_spring_global
F_spring_anchor_norm = norm(F_spring_anchor_global)
```

De schuiverkracht met veer wordt:

```python
F_A_total =
    [R_Ax, F_act_y]
    + F_slider_global
    + F_spring_global
```

Waarom zit de veer in `F_A_total`?

Omdat de veer als externe kracht op de schuiver werkt. Voor de globale
framebelasting moet ook de reactie op het vaste veeranker worden bekeken.

De figuren tonen:

- schuiverreactie;
- framekracht;
- netto framebelasting;
- veerankerbelasting.

## 15. Codecel 30 - Opslag

De laatste codecel schrijft:

```python
notebook3_trekveren_results.npz
```

Het bestand bevat dezelfde hoofdkeys als Notebook 3, zodat Notebook 4 het kan
lezen:

| Key | Betekenis |
|---|---|
| `F_drive_s_total` | totale aandrijfkracht met veer |
| `F_drive_s_inertia` | inertie-only referentie |
| `F_drive_s_gravity_spring` | zwaartekracht + veer zonder wrijving |
| `F_gravity_component` | zwaartekracht/veer-combinatie t.o.v. inertie |
| `F_friction_component` | wrijvingsbijdrage |
| `F_hold_s_curve` | houdkracht met veer |
| `P_act_total`, `E_act_total` | vermogen en arbeid met veer |
| `F_A_total_norm`, `F_C_total_norm`, `F_frame_total_norm` | framebelasting |

Extra veerkeys:

| Key | Betekenis |
|---|---|
| `spring_force_up_total` | opwaartse veerkracht tijdens beweging |
| `spring_force_s_total` | veerkracht in s-richting |
| `spring_force_open_total` | totale veerkracht open |
| `spring_force_closed_total` | totale veerkracht gesloten |
| `spring_k_total` | totale directe veerstijfheid aan de schuiver |
| `spring_k_per_spring` | directe veerstijfheid per veer |
| `spring_physical_rate_per_spring_N_per_mm` | fysieke trekveerstijfheid per veer |
| `spring_motion_ratio` | `1.0`, want de veer is direct gemonteerd |
| `spring_energy_stored` | opgeslagen veerenergie tijdens beweging |
| `spring_energy_delta` | energieverschil gesloten-open |
| `F_drive_s_baseline` | baseline zonder veer |
| `F_hold_s_baseline_curve` | baseline houdkracht |
| `F_spring_anchor_norm` | belasting op veeranker |

Notebook 4 kan dit bestand lezen met:

```python
load_case = "trekveren"
```

Voor de brede overdekking bestaat een aparte loadcase in `Notebook 3 -
Overdekking`.

## 16. Wat moet je kunnen uitleggen bij deze code?

### Waar komt de veer in het stelsel?

Als bekende kracht op link 2, aangrijpend in punt B, richting globaal omhoog.

### Waarom verandert de kinematica niet?

Omdat de beweging opgelegd blijft vanuit Notebook 1. De veer verandert alleen de
krachten die nodig zijn om die beweging te volgen.

### Waarom wordt de baseline geladen?

Om zonder/ met veer met exact dezelfde massa's, kinematica en wrijving te
vergelijken.

### Waarom wordt de veer automatisch geschaald?

Om te vermijden dat ze meer dan de ingestelde maximale assistfractie levert. De
motor/rem moet controle houden.

### Waarom moet sluiten nog steeds bekeken worden?

Omdat de veer bij sluiten opnieuw wordt uitgerekt. De veer verlaagt de
openingslast, maar de energie moet later terug in de veer.

### Waarom blijft Notebook 4 nodig?

Deze notebook berekent mechanische belasting met veer. Notebook 4 bepaalt de
praktische motor, rem, riem, poelie en veiligheidsfactoren.

## 17. Typische fouten bij aanpassen

- Alleen Notebook 3 aanpassen en de trekverenvariant vergeten.
- De veer in de kinematica proberen te stoppen. Ze hoort in de dynamica.
- Het teken van de veer verwarren: omhoog is positief in `y`, maar negatief in
  `s`.
- De veer als gratis energiebron zien.
- De veer in de riem modelleren in plaats van parallel aan de aandrijving.
- De baseline met andere massa's of andere kinematica vergelijken.
- `F_drive_s_gravity` in deze notebook verwarren met dezelfde naam in Notebook 3:
  hier zit de veer al in die case.

## 18. Korte samenvatting

De code van `Notebook 3 - Trekveren` doet het volgende:

1. Ze laadt kinematica, massa's en baseline zonder veer.
2. Ze dimensioneert twee trekveren op basis van de baseline-houdkracht.
3. Ze berekent veerkracht en veerenergie als functie van `s`.
4. Ze voegt de veer toe als bekende opwaartse kracht op de schuiver.
5. Ze lost dezelfde Newton-Euler-dynamica op als Notebook 3.
6. Ze vergelijkt kracht, vermogen, arbeid en houdkracht zonder/met veer.
7. Ze berekent veerankerbelasting.
8. Ze schrijft een Notebook-4-compatibel `.npz`-bestand weg.

De belangrijkste codeconclusie is dat de trekveer de verticale aandrijf- en
houdkracht sterk verlaagt, zonder de kinematica of het massamodel te veranderen.
