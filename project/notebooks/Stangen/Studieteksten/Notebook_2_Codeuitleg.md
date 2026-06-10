# Notebook 2 - Code-uitleg

Deze tekst hoort bij `Notebook 2.ipynb`. Het doel is dat je de code kan
uitleggen tijdens het studeren of mondeling: welke parameters staan waar, welke
functies worden gebruikt, waarom die functies nodig zijn, welke theorie ze
uitvoeren en welke arrays worden doorgegeven aan latere notebooks.

De plotcode zelf wordt niet regel per regel uitgelegd. Bij plotcellen staat
vooral welke berekende grootheden gebruikt worden en wat de figuur bewijst.

## 0. Overzicht van de codecellen

| Codecel | Inhoud | Belangrijkste output |
|---:|---|---|
| 3 | Imports en data laden uit Notebook 1 | `t`, `s`, `ds`, `dds`, hoeken, snelheden, versnellingen, geometrie |
| 5 | Dynamische parameters | `masses`, `inertias`, `payload_mass_K`, actuatorparameters |
| 7 | Rigid-body kinematica | gewrichtspunten, zwaartepunten, `K_pos`, `K_vel`, `K_acc` |
| 9 | Inverse dynamica opbouwen en oplossen | `w_all`, `F_drive_s`, `R_Ax`, `C_x`, `C_y`, gewrichtskrachten |
| 11 | Globale dynamische controle | `force_balance_error`, `moment_balance_error` |
| 13 | Energiebalans-validatie | `Ekin_nb2`, `energy_balance_error_nb2` |
| 15 | Krachten en onbalans | `F_shak`, `M_shak_C`, reactiekrachtnormen |
| 17 | Actuatorvermogen en poelie-/riem-equivalent | `P_act`, `E_act`, `T_motor`, `motor_speed_rpm` |
| 19 | Arbeids-surplus volgens Les 4 | `A_theta_nb2`, `A_max_nb2`, inertie-only motorinschatting |
| 21 | Opslag naar `.npz` | `notebook2_dynamica_results.npz` |

## 1. Datastroom door Notebook 2

De notebook werkt in deze volgorde:

```text
notebook1_kinematica_results.npz
  -> tijd, schuivertraject, hoeken, hoekafgeleiden en geometrie
  -> massa's en massatraagheden
  -> reconstructie punten en zwaartepunten
  -> Newton-Euler stelsel A w = b per tijdstap
  -> gewrichtskrachten, schuiverkracht en frame-reacties
  -> globale dynamische controles
  -> energiebalans en onbalans
  -> actuatorvermogen en arbeids-surplus
  -> notebook2_dynamica_results.npz
```

Belangrijk: Notebook 2 berekent geen eigen traject. De beweging is volledig
afkomstig van Notebook 1. Als `s_open`, `s_closed`, `t_move_desired`,
`motion_profile` of geometrie in Notebook 1 veranderen, moet Notebook 1 eerst
opnieuw uitgevoerd worden. Daarna volgt Notebook 2 automatisch die nieuwe
kinematica.

## 2. Codecel 3 - Imports en kinematica laden

### 2.1 Imports

De cel start met:

```python
from pathlib import Path
import numpy as np
import matplotlib.pyplot as plt
```

Betekenis:

- `Path`: robuust werken met bestandsnamen;
- `numpy`: arrays, vectornormen, lineaire algebra, numerieke afgeleiden;
- `matplotlib`: figuren.

Er wordt hier geen `scipy` gebruikt. Notebook 2 lost alleen lineaire stelsels op
met `np.linalg.solve`. De niet-lineaire positieanalyse is al in Notebook 1
gedaan.

### 2.2 Bestand uit Notebook 1

```python
kinematics_path = Path("notebook1_kinematica_results.npz")
if not kinematics_path.exists():
    raise FileNotFoundError(...)
```

Deze check voorkomt dat Notebook 2 met ontbrekende of oude data start. Notebook
2 is afhankelijk van `notebook1_kinematica_results.npz`.

### 2.3 Required keys

De lijst `required_keys` bevat alle arrays die Notebook 2 nodig heeft:

```python
required_keys = [
    "t", "Ts", "s", "ds", "dds",
    "theta3", ..., "theta8",
    "dtheta3", ..., "dtheta8",
    "ddtheta3", ..., "ddtheta8",
    "Kx", "Ky", "Kdot_x", "Kdot_y", ...,
    "cond", "residual_pos",
    "L1", "r3a", ..., "r8b",
]
```

Deze check is belangrijk voor compatibiliteit. Als Notebook 1 ooit anders zou
opslaan, krijgt Notebook 2 meteen een duidelijke foutmelding:

```python
missing = [key for key in required_keys if key not in data.files]
if missing:
    raise KeyError(...)
```

Zonder deze check zou de notebook later kunnen falen op een minder duidelijke
plaats.

### 2.4 Arrays uitpakken

De code pakt daarna alle grootheden uit:

```python
t = data["t"]
s = data["s"]
ds = data["ds"]
dds = data["dds"]
theta3 = data["theta3"]
...
```

Betekenis:

- `t`: tijdvector;
- `s`: schuiverpositie, positief naar beneden;
- `ds`: schuiversnelheid;
- `dds`: schuiverversnelling;
- `theta3` tot `theta8`: linkhoeken;
- `dtheta3` tot `dtheta8`: hoeksnelheden;
- `ddtheta3` tot `ddtheta8`: hoekversnellingen;
- `Kx`, `Ky`: positie van outputpunt K uit Notebook 1;
- `Kdot_*`, `Kddot_*`: snelheid en versnelling van K;
- `cond`: conditionering van de kinematische matrix uit Notebook 1;
- `residual_pos`: sluitingsfout uit Notebook 1;
- `r3a` tot `r8b`: geometrische lengtes.

`n_steps` en `T_cycle` worden afgeleid:

```python
n_steps = len(t)
T_cycle = float(t[-1] - t[0])
```

De check:

```python
if T_cycle <= 0:
    raise ValueError(...)
```

zorgt dat de tijdvector fysisch geldig is.

## 3. Codecel 5 - Dynamische parameters

### 3.1 Linklengtes

De geometrische segmenten uit Notebook 1 worden omgezet naar volledige
linklengtes:

```python
L3 = r3a + r3b
L4 = r4a + r4b
L5 = r5a + r5b
L6 = r6
L7 = r7a + r7b
L8 = r8a + r8b
```

`L2 = 0.0` omdat link 2 de schuiver is. Die wordt niet als roterende staaf
gemodelleerd.

### 3.2 Massa's

De belangrijkste parameters zijn:

```python
slider_mass = 1.50
rod_outer_diameter = 0.030
rod_wall_thickness = 0.002
rod_material_density = 2700.0
rod_fittings_line_mass_allowance = 0.075
line_mass_density = rod_tube_mass_per_m + rod_fittings_line_mass_allowance
payload_mass_K = 3.00
mass_scale = 1.0
inertia_scale = 1.0
```

Betekenis:

- `slider_mass`: massa van de collar/schuiver rond de mast;
- `rod_outer_diameter`: buitendiameter van de ronde aluminium buis;
- `rod_wall_thickness`: wanddikte van de buis;
- `rod_material_density`: dichtheid van aluminium;
- `rod_fittings_line_mass_allowance`: extra lijnmassa voor scharnieren, bouten,
  eindstukken en lokale verstevigingen;
- `line_mass_density`: massa per meter stang, berekend uit buisprofiel plus
  beslagtoeslag;
- `payload_mass_K`: equivalente puntmassa in K;
- `mass_scale`: globale schaalfactor om alle massa's te verhogen of verlagen;
- `inertia_scale`: schaalfactor voor de rotatie-inerties.

Waarom deze waarden:

- `slider_mass = 1.50 kg` is een realistische eerste inschatting voor een
  schuiver/collar met lagering, bouten en bevestiging aan de aandrijving;
- `line_mass_density ≈ 0.550 kg/m` volgt uit een ronde aluminium buis van
  `30x2 mm`, met een extra beslagtoeslag van `0.075 kg/m`;
- `payload_mass_K = 3.00 kg` is een vereenvoudigde puntmassa voor doek/rand/rib
  bij het enkelvoudige basismechanisme.

Deze waarden zijn niet hetzelfde als de finale overdekkingsmassa aan K. In
`Notebook 3 - Overdekking` wordt `payload_mass_K` opnieuw berekend uit
voorbalkmassa, doekmassa en beslag. De stangmassa en schuivermassa blijven wel
consequent dezelfde soort parameters.

De formule in de code is:

```python
rod_inner_diameter = rod_outer_diameter - 2.0 * rod_wall_thickness
rod_tube_area = np.pi / 4.0 * (rod_outer_diameter**2 - rod_inner_diameter**2)
rod_tube_mass_per_m = rod_tube_area * rod_material_density
line_mass_density = rod_tube_mass_per_m + rod_fittings_line_mass_allowance
```

Daarmee is de lijnmassa niet zomaar hardcoded, maar gekoppeld aan een fysisch
profiel dat je later kan aanpassen.

De massa's worden opgeslagen in een dictionary:

```python
masses = {
    2: slider_mass * mass_scale,
    3: line_mass_density * L3 * mass_scale,
    ...
}
```

Waarom een dictionary?

- elke link heeft een nummer;
- `masses[link_id]` is leesbaar in de Newton-Euler-opbouw;
- latere notebooks gebruiken dezelfde linknummering.

### 3.3 Massatraagheden

Voor de stangen:

```python
J = m L^2 / 12
```

In code:

```python
inertias = {
    2: 0.0,
    3: masses[3] * L3**2 / 12 * inertia_scale,
    ...
}
```

`inertias[2] = 0.0` omdat de schuiver niet roteert. Hij beweegt alleen
translerend langs de mast.

### 3.4 Actuatorparameters

Notebook 2 bevat een poelie-/riem-equivalent:

```python
actuator_efficiency = 0.78
actuator_safety_factor = 1.50
drive_pulley_radius_nb2 = 0.025
drive_travel_per_rev_nb2 = 2 * np.pi * drive_pulley_radius_nb2
```

Betekenis:

- `actuator_efficiency`: rendement voor de eenvoudige poelie-/rieminschatting;
- `actuator_safety_factor`: veiligheidsfactor op piekwaarden;
- `drive_pulley_radius_nb2`: referentiepoelieradius voor de inertie-only quick check;
- `drive_travel_per_rev_nb2`: lineaire riemverplaatsing per poelieomwenteling.

Waarom is `actuator_efficiency` nodig?

De formule voor motorkoppel komt uit vermogensbehoud. Ideaal zou gelden:

```math
F_s \dot{s} = T \omega
```

In een echte aandrijving gaat vermogen verloren in:

- riem-, lager- of reductiewrijving;
- lagers;
- reductiekast;
- motorverliezen.

Daarom wordt gedeeld door een rendement. Bij een rendement kleiner dan 1 moet
de motor meer koppel leveren dan in een ideaal systeem. `0.78` is hier een
eenvoudige eerste waarde voor riem + reductie in de inertie-only
poelie-/riemvergelijking. Voor de finale aandrijving gebruikt Notebook 4 de
eigen aandrijfparameters.

Belangrijke nuance:

> Deze actuatorparameters zijn niet de finale motorarchitectuur. Ze dienen in
> Notebook 2 vooral om de inertie-only kracht om te zetten naar een eenvoudige
> koppel- en toerentalinschatting. De finale aandrijving staat in Notebook 4.

## 4. Codecel 7 - Rigid-body kinematica

Deze cel reconstrueert alle punten en zwaartepunten die nodig zijn voor de
Newton-Euler-vergelijkingen.

### 4.1 Functie `perp`

```python
def perp(v):
    v = np.asarray(v)
    return np.column_stack((-v[:, 1], v[:, 0])) if v.ndim == 2 else np.array([-v[1], v[0]])
```

Deze functie draait een 2D-vector 90 graden tegenwijzerzin:

```text
(x, y) -> (-y, x)
```

Ze werkt zowel voor een enkele vector als voor een array van vectoren.

Waarom nodig?

In vlakke starre-lichaamkinematica schrijf je:

```math
\omega \times \mathbf{r} = \omega \, \mathrm{perp}(\mathbf{r})
```

en:

```math
\alpha \times \mathbf{r} = \alpha \, \mathrm{perp}(\mathbf{r})
```

### 4.2 Functie `rigid_point`

```python
def rigid_point(ref_pos, ref_vel, ref_acc, theta, omega, alpha, local_vector):
    ...
```

Input:

- `ref_pos`: positie van referentiepunt O;
- `ref_vel`: snelheid van O;
- `ref_acc`: versnelling van O;
- `theta`: hoek van de link;
- `omega`: hoeksnelheid van de link;
- `alpha`: hoekversnelling van de link;
- `local_vector`: vector van O naar punt P in lokale linkcoordinaten.

De functie zet de lokale vector eerst om naar globale coordinaten:

```python
r_global = [
    local_x*cos(theta) - local_y*sin(theta),
    local_x*sin(theta) + local_y*cos(theta)
]
```

Daarna gebruikt ze:

```python
pos = ref_pos + r_global
vel = ref_vel + omega[:, None] * perp(r_global)
acc = ref_acc + alpha[:, None] * perp(r_global) - (omega[:, None] ** 2) * r_global
```

Dit is exact de starre-lichaamformule uit de theorie.

### 4.3 Vaste punten C en schuiverpunt B

Punt `C` is het vaste framepunt:

```python
C_pos = zeros2.copy()
C_vel = zeros2.copy()
C_acc = zeros2.copy()
```

Punt `B` is de schuiver:

```python
B_pos = np.column_stack((zero, -s))
B_vel = np.column_stack((zero, -ds))
B_acc = np.column_stack((zero, -dds))
```

Waarom mintekens?

- globale `y` is positief omhoog;
- `s` is positief naar beneden;
- dus `y_B = -s`.

Daarom:

```math
\dot{y}_B = -\dot{s}
```

en:

```math
\ddot{y}_B = -\ddot{s}
```

### 4.4 Gewrichtspunten reconstrueren

Met `rigid_point` worden de punten gereconstrueerd:

- `D` op link 3;
- `E` als gemiddelde van de reconstructie via link 3 en link 4;
- `H` op link 4;
- `F` op link 5;
- `G` als gemiddelde van link 5 en link 7;
- `I` op link 6;
- `J` als gemiddelde van link 7 en link 8;
- `K` op link 8.

Voorbeeld:

```python
D_pos, D_vel, D_acc = rigid_point(B_pos, B_vel, B_acc, theta3, dtheta3, ddtheta3, [r3a, 0.0])
```

Dat betekent: punt `D` ligt op link 3, op afstand `r3a` vanaf `B`.

Waarom worden `E`, `G` en `J` soms gemiddeld?

Deze punten liggen op gesloten lussen. Ze kunnen via twee kanten worden
gereconstrueerd. Door het gemiddelde te nemen vermijd je dat zeer kleine
numerieke sluitingsfouten zichtbaar worden in de dynamica.

### 4.5 Zwaartepunten

De dictionaries:

```python
cg_pos, cg_vel, cg_acc, alpha = {}, {}, {}, {}
```

houden per link bij:

- zwaartepuntpositie;
- zwaartepuntsnelheid;
- zwaartepuntversnelling;
- hoekversnelling.

Voor een uniforme staaf ligt het zwaartepunt op halve lengte:

```python
cg_pos[3], cg_vel[3], cg_acc[3] = rigid_point(..., [L3 / 2, 0.0])
```

Voor link 7 is de lokale vector speciaal:

```python
[(r7b - r7a) / 2, 0.0]
```

Waarom?

Link 7 wordt in de geometrie rond punt `H` gebruikt met een stuk naar `G` en een
stuk naar `J`. Punt `G` ligt aan de ene kant van `H`, punt `J` aan de andere
kant. Het zwaartepunt ligt dus niet op `L7/2` vanaf `H` in dezelfde richting,
maar op het midden tussen `-r7a` en `+r7b`.

### 4.6 Kinematicachecks

De cel controleert:

```python
max |K_reconstructie - K_Notebook1|
```

en:

```python
max sluitverschil E/G/J
```

Deze checks zijn nodig omdat Notebook 2 zelf punten opnieuw reconstrueert. Als
hier een fout zit, zijn alle krachten in de inverse dynamica verdacht.

## 5. Codecel 9 - Inverse dynamica opbouwen en oplossen

Dit is de kern van Notebook 2.

### 5.1 Onbekenden

De onbekenden staan in:

```python
unknown_labels = [
    "R_Ax", "F_act_y", "M_A",
    "C_x", "C_y",
    "B_x", "B_y",
    ...
    "J_x", "J_y",
]
```

Daarna wordt een index gemaakt:

```python
unknown_index = {name: i for i, name in enumerate(unknown_labels)}
```

Waarom?

Het lineaire stelsel gebruikt een vector `w`. Met `unknown_index["C_x"]` weet de
code in welke kolom van matrix `A` de onbekende `C_x` staat.

### 5.2 Functie `moment_2d`

```python
def moment_2d(r, F):
    return r[0] * F[1] - r[1] * F[0]
```

Dit is het vlakke moment:

```math
M_z = x F_y - y F_x
```

De functie wordt gebruikt voor:

- momenten van onbekende gewrichtskrachten;
- momenten van puntmassa K;
- globale momentcontrole.

### 5.3 Functie `add_link_equations`

```python
def add_link_equations(A, b, row, m, J, a_cg, alpha_cg, cg, forces, moments=None, point_masses=None):
```

Deze functie voegt voor een link drie vergelijkingen toe:

```math
\sum F_x = m a_x
```

```math
\sum F_y = m a_y
```

```math
\sum M_{cg} = J \alpha
```

Input:

- `A`: matrix van het lineaire stelsel;
- `b`: rechterlid;
- `row`: huidige rij in het stelsel;
- `m`, `J`: massa en massatraagheid van de link;
- `a_cg`: versnelling van het zwaartepunt;
- `alpha_cg`: hoekversnelling;
- `cg`: positie van het zwaartepunt;
- `forces`: lijst van onbekende krachten op de link;
- `moments`: lijst van onbekende momenten;
- `point_masses`: extra puntmassa's, zoals `K` op link 8.

Een kracht wordt ingevoerd als:

```python
(fx_name, fy_name, sign, point)
```

Voorbeeld:

```python
("B_x", "B_y", -1.0, B_pos[k])
```

Betekenis:

- onbekende krachtcomponenten zijn `B_x` en `B_y`;
- ze grijpen aan in punt `B`;
- teken `-1.0` betekent dat deze kracht op deze link tegengesteld werkt aan de
  positieve definitie.

De functie vult de krachtvergelijkingen:

```python
A[row_fx, j] += sign
A[row_fy, j] += sign
```

en de momentvergelijking:

```python
A[row_m, j] += -sign * r[1]   # bijdrage van F_x
A[row_m, j] +=  sign * r[0]   # bijdrage van F_y
```

Dat komt rechtstreeks uit:

```math
M = x F_y - y F_x
```

### 5.4 Puntmassa's in `add_link_equations`

Voor puntmassa's wordt niet een onbekende kracht toegevoegd. De inertiekracht
van de puntmassa is bekend:

```python
F_point = point_mass * point_acc
```

Die wordt toegevoegd aan het rechterlid:

```python
F_inertia = F_inertia + F_point
M_inertia = M_inertia + moment_2d(point_pos - cg, F_point)
```

In Notebook 2 wordt dit gebruikt voor `payload_mass_K` op link 8.

### 5.5 Rechterlid `b`

Aan het einde van `add_link_equations`:

```python
b[row_fx] = F_inertia[0]
b[row_fy] = F_inertia[1]
b[row_m] = M_inertia
```

De linkerzijde van het stelsel bevat alle onbekende krachten. Het rechterlid
bevat de bekende inertietermen.

### 5.6 Functie `build_inverse_dynamics_system`

```python
def build_inverse_dynamics_system(k, masses_local=None, inertias_local=None):
```

Deze functie bouwt voor tijdstap `k` de volledige matrix `A` en vector `b`.

Waarom `masses_local` en `inertias_local`?

Daardoor kan dezelfde functie eventueel gebruikt worden met aangepaste massa's
of inerties zonder alle code te herschrijven. De standaard is:

```python
masses_local = masses
inertias_local = inertias
```

Per link roept de functie `add_link_equations` aan.

### 5.7 Link 2: schuiver

Voor de schuiver:

```python
[("R_Ax", None, 1.0, B_pos[k]),
 (None, "F_act_y", 1.0, B_pos[k]),
 ("B_x", "B_y", 1.0, B_pos[k])]
```

Op de schuiver werken:

- horizontale geleidingsreactie `R_Ax`;
- verticale actuatorreactie `F_act_y`;
- gewrichtskracht van link 3 in `B`;
- geleidingsmoment `M_A`.

De schuiver is link 2 en heeft geen rotatie-inertie in dit model.

### 5.8 Links 3 tot 8

Voor elke stang worden de gewrichtskrachten toegevoegd met hun juiste
actie-reactieteken.

Voorbeeld link 3:

```python
[("B_x", "B_y", -1.0, B_pos[k]),
 ("D_x", "D_y", 1.0, D_pos[k]),
 ("E_x", "E_y", 1.0, E_pos[k])]
```

Op link 3 werken:

- kracht van schuiverpunt B met tegengesteld teken;
- kracht van link 5 in D;
- kracht van link 4 in E.

Voor link 8 wordt extra de puntmassa in K meegegeven:

```python
point_masses=[(payload_mass_K, K_acc[k], K_pos[k])]
```

Daarmee zit de versnelling van de payload correct in de dynamica.

### 5.9 Aantal vergelijkingen controleren

Na alle links:

```python
if row != n_unknowns:
    raise RuntimeError(...)
```

Dit voorkomt dat er per ongeluk te weinig of te veel vergelijkingen in het
stelsel staan.

### 5.10 Oplossen over alle tijdstappen

De arrays worden klaargezet:

```python
A_all = np.zeros((n_steps, n_unknowns, n_unknowns))
b_all = np.zeros((n_steps, n_unknowns))
w_all = np.zeros((n_steps, n_unknowns))
dyn_residual = np.zeros(n_steps)
dyn_cond = np.zeros(n_steps)
```

Per tijdstap:

```python
A_k, b_k = build_inverse_dynamics_system(k)
dyn_cond[k] = np.linalg.cond(A_k)
w_all[k] = np.linalg.solve(A_k, b_k)
dyn_residual[k] = np.linalg.norm(A_k @ w_all[k] - b_k)
```

Betekenis:

- `dyn_cond`: conditionering van de dynamische matrix;
- `np.linalg.solve`: lost `A_k w_k = b_k` op;
- `dyn_residual`: numerieke restfout van de oplossing.

### 5.11 Onbekenden als losse arrays

Na het oplossen:

```python
for name, idx in unknown_index.items():
    globals()[name] = w_all[:, idx]
```

Daardoor ontstaan arrays zoals:

- `R_Ax`;
- `F_act_y`;
- `C_x`;
- `C_y`;
- `E_x`;
- `J_y`;
- enzovoort.

Dit maakt latere berekeningen en plots leesbaarder.

Daarna:

```python
F_actuator_y = F_act_y
F_drive_s = -F_actuator_y
M_guide_A = M_A
```

Belangrijk teken:

- `F_actuator_y` is positief naar boven;
- `s` is positief naar beneden;
- daarom is de aandrijfkracht in s-richting `F_drive_s = -F_actuator_y`.

## 6. Codecel 11 - Globale dynamische controle

Deze cel controleert of de oplossing fysisch klopt wanneer alle links samen als
een systeem worden beschouwd.

### 6.1 Externe krachten

```python
F_A_on_mechanism = np.column_stack((R_Ax, F_actuator_y))
F_C_on_mechanism = np.column_stack((C_x, C_y))
F_external_total = F_A_on_mechanism + F_C_on_mechanism
```

De externe krachten op het mechanisme zijn:

- kracht in de schuiver/actuator bij `A/B`;
- framekracht in `C`.

Interne gewrichtskrachten vallen weg als je alle links samen neemt.

### 6.2 Inertiekrachten optellen

Voor elke link:

```python
F_i = masses[link_id] * cg_acc[link_id][k]
F_inertia_total[k] += F_i
```

Voor het moment rond `C`:

```python
M_inertia_about_C[k] += moment_2d(cg_pos[link_id][k] - C_pos[k], F_i) + inertias[link_id] * alpha[link_id][k]
```

Daarna wordt ook de puntmassa in K toegevoegd:

```python
F_K = payload_mass_K * K_acc[k]
```

### 6.3 Moment van externe krachten

```python
M_external_about_C[k] = M_guide_A[k] + moment_2d(B_pos[k] - C_pos[k], F_A_on_mechanism[k])
```

Omdat de framekracht in `C` aangrijpt in het momentpunt `C`, levert die geen
moment rond `C`.

### 6.4 Foutmaten

```python
force_balance_error = np.linalg.norm(F_external_total - F_inertia_total, axis=1)
moment_balance_error = np.abs(M_external_about_C - M_inertia_about_C)
```

Als deze fouten groot zijn, is er waarschijnlijk:

- een tekenfout;
- een fout aangrijpingspunt;
- een ontbrekende massa;
- een fout in de reconstructie van de punten.

De huidige fouten zijn numeriek klein, wat de Newton-Euler-opbouw valideert.

## 7. Codecel 13 - Energiebalans-validatie

Deze cel controleert de oplossing via energie.

### 7.1 Kinetische energie

De kinetische energie wordt opgebouwd uit:

```python
Ekin_nb2 = 0.5*m*v_cg^2 + 0.5*J*omega^2
```

Voor link 2:

```python
Ekin_nb2 += 0.5 * masses[2] * np.sum(cg_vel[2]**2, axis=1)
```

Voor links 3 tot 8:

```python
Ekin_nb2 += 0.5*m*v_cg^2 + 0.5*J*omega^2
```

Voor de puntmassa K:

```python
Ekin_nb2 += 0.5 * payload_mass_K * np.sum(K_vel**2, axis=1)
```

### 7.2 Afgeleide van kinetische energie

```python
dEkin_dt_nb2 = np.gradient(Ekin_nb2, t)
```

`np.gradient` geeft een numerieke afgeleide. Daarom is de energiebalans niet
exact nul, maar de fout moet klein zijn.

### 7.3 Vermogenscontrole

```python
energy_balance_error_nb2 = F_drive_s * ds - dEkin_dt_nb2
```

Omdat Notebook 2 geen zwaartekracht of wrijving bevat, moet gelden:

```math
F_s \dot{s} = \frac{dE_{kin}}{dt}
```

Belangrijk: de code gebruikt `F_drive_s * ds`, dus kracht maal snelheid. Dat is
vermogen. Het is niet `F_s * s`, want kracht maal positie zou arbeid of energie
zijn, geen momentaan vermogen.

Hierbij is:

- `F_drive_s`: gegeneraliseerde kracht langs de schuivercoordinaat `s`;
- `ds`: schuiversnelheid `dot{s}`;
- `F_drive_s * ds`: mechanisch vermogen dat de actuator aan het mechanisme
  levert of eruit opneemt.

Deze controle is nuttig omdat ze anders is dan de krachtbalans. Ze controleert
of het vermogen van de berekende aandrijfkracht overeenkomt met de verandering
van kinetische energie.

### 7.4 Relatieve fout

```python
denom_nb2 = np.where(np.abs(F_drive_s * ds) > 1e-4, F_drive_s * ds, np.nan)
energy_balance_rel_nb2 = np.abs(energy_balance_error_nb2 / denom_nb2)
```

De relatieve fout wordt alleen berekend wanneer het vermogen groot genoeg is.
Anders zou je delen door bijna nul, wat een misleidend grote relatieve fout
geeft.

## 8. Codecel 15 - Krachten en onbalans

### 8.1 Reactiekrachtnormen

De cel berekent normen van geselecteerde krachten:

```python
R_C_norm = norm([C_x, C_y])
R_A_norm = norm(F_A_on_mechanism)
R_E_norm = norm([E_x, E_y])
R_J_norm = norm([J_x, J_y])
```

Deze waarden zijn nuttig om te zien welke verbindingen dynamisch het zwaarst
belast worden in de inertie-only analyse.

Waarom worden maar enkele normen geplot?

De code lost wel alle onbekende gewrichtskrachten op. Ze zitten allemaal in
`w_all` en kunnen via `unknown_labels` teruggevonden worden. De figuur toont
alleen een selectie (`A`, `C`, `E`, `J`) omdat:

- `A` en `C` de hoofdverbindingen met schuiver en frame zijn;
- `E` en `J` representatieve interne gewrichten zijn;
- alle gewrichten tegelijk plotten de figuur onleesbaar zou maken.

Voor de shaking forces worden niet alleen die geselecteerde links gebruikt. De
shaking force komt uit de totale externe kracht op het volledige mechanisme:

```python
F_external_total = F_A_on_mechanism + F_C_on_mechanism
```

Interne gewrichtskrachten vallen globaal weg door actie-reactie.

### 8.2 Onbalanskracht

```python
F_shak = -F_external_total
```

Dit is de kracht die het bewegende mechanisme op het frame uitoefent. Het
minteken komt door actie-reactie: als het frame een kracht op het mechanisme
uitoefent, oefent het mechanisme de tegengestelde kracht op het frame uit.

Daarna:

```python
F_shak_x = F_shak[:, 0]
F_shak_y = F_shak[:, 1]
F_shak_norm = np.linalg.norm(F_shak, axis=1)
```

### 8.3 Onbalansmoment

```python
M_shak_C = -M_external_about_C
```

Dit is het inertiele moment dat het mechanisme op het frame uitoefent rond punt
`C`.

Wat betekent dit fysisch?

`F_shak` en `M_shak_C` zijn geen krachten die je extra op het mechanisme zet.
Ze zijn de reactie die het bewegende mechanisme aan zijn frame geeft door
versnelling van massa's. Bij grote waarden kan een machine gaan trillen, lawaai
maken, bouten en lagers vermoeien of een zwaardere fundering nodig hebben.

Bij ons zijn ze klein omdat de beweging traag en glad is. In een sneller
industrieel mechanisme worden shaking forces groot door zware links, hoge
versnellingen, asymmetrische massa's of hoge cyclische snelheid. Dan gebruikt
men typisch contramassa's, symmetrische mechanismen, stijvere frames, demping of
een rustiger bewegingswet.

### 8.4 Wat bewijzen de figuren?

De figuren tonen:

- inertiele aandrijfkracht als functie van `s`;
- reactie in framepunt `C`;
- schuiver/actuatorreacties;
- enkele interne reacties;
- onbalanskrachtcomponenten;
- onbalansmoment rond `C`;
- verloop van onbalans in de tijd.

Voor de verdediging is vooral belangrijk:

> De inertiele onbalans is klein door het rustige traject. Grote frame- en
> mastbelastingen in de finale overdekking komen dus niet hoofdzakelijk uit
> inertie, maar uit zwaartekracht, wrijving en structurele lasten.

## 9. Codecel 17 - Actuatorvermogen en poelie-/riem-equivalent

### 9.1 Benodigde schuifkracht

```python
F_required_s = F_drive_s.copy()
```

In Notebook 2 is de benodigde schuifkracht gelijk aan de inertiele
aandrijfkracht. Er zijn nog geen extra krachten door zwaartekracht of wrijving.

### 9.2 Actuatorvermogen

```python
P_act = F_required_s * ds
```

Dit is vermogen in de schuivercoordinaat:

```math
P = F_s \dot{s}
```

Daarna wordt de energie via trapeziumintegratie berekend:

```python
E_act[1:] = np.cumsum(0.5 * (P_act[1:] + P_act[:-1]) * np.diff(t))
```

### 9.3 Actieve beweging en hold-fase

De code gebruikt toleranties:

```python
motion_v_tol = 1e-8
motion_a_tol = 1e-8
```

Daarmee worden twee masks gemaakt:

```python
active_motion_mask = (abs(ds) > tol) | (abs(dds) > tol)
hold_mask_dyn = (abs(ds) <= tol) & (abs(dds) <= tol)
```

Waarom nodig?

- tijdens echte beweging wil je piek- en RMS-krachten bepalen;
- tijdens hold is de inertiele kracht in Notebook 2 nul;
- bij numerieke afronding wil je niet dat minuscule waarden als beweging tellen.

### 9.4 Dynamische piek- en RMS-kracht

```python
F_dynamic_peak = max(abs(F_required_s[active_motion_mask]))
F_dynamic_rms = sqrt(mean(F_required_s[active_motion_mask]**2))
```

Piek is belangrijk voor maximale belasting. RMS is belangrijk voor thermische of
gemiddelde motorbelasting.

De dynamische piek wordt bepaald over de tijdstappen waarin het mechanisme echt
beweegt:

```python
active_motion_mask = (abs(ds) > motion_v_tol) | (abs(dds) > motion_a_tol)
```

Daarna neemt de code:

```python
max(abs(F_required_s[active_motion_mask]))
```

Voor Notebook 2 is dit de piek van de inertie-only aandrijfkracht. In de
hold-fase is die kracht nul, omdat er zonder zwaartekracht en wrijving geen
statische belasting is.

### 9.5 Spindel-equivalent

```python
motor_speed_rps = ds / drive_travel_per_rev_nb2
motor_speed_rpm = 60.0 * motor_speed_rps
omega_motor = 2 * pi * motor_speed_rps
```

Als de poelie/riem per omwenteling `drive_travel_per_rev_nb2` meter verplaatst, dan is:

```math
n = \dot{s}/p
```

waar `p` de riemverplaatsing per poelieomwenteling is.

Het motorkoppel:

```python
T_motor = F_required_s * drive_travel_per_rev_nb2 / (2 * np.pi * actuator_efficiency)
```

komt uit vermogensbehoud:

```math
F_s \dot{s} = T \omega
```

met rendement `eta`.

### 9.6 Positief en regeneratief vermogen

```python
P_positive = np.maximum(P_act, 0.0)
P_negative = np.minimum(P_act, 0.0)
```

Betekenis:

- `P_positive`: actuator levert vermogen aan het mechanisme;
- `P_negative`: mechanisme levert vermogen terug of moet afgeremd worden.

Notebook 2 gebruikt deze waarden voor een eenvoudige inertie-only inschatting:

```python
P_positive_peak = safety_factor * max(P_positive) / efficiency
P_regen_peak = safety_factor * abs(min(P_negative)) * efficiency
```

Deze waarden zijn klein en niet maatgevend voor de finale motor.

## 10. Codecel 19 - Arbeids-surplus en motordimensionering

Deze cel past het arbeids-surplus uit Les 4 toe op de inertie-only belasting.

### 10.1 Vermogensgrootheden

```python
P_load_nb2 = F_drive_s * ds
P_avg_nb2 = P_load_nb2.mean()
P_peak_nb2 = abs(P_load_nb2).max()
P_rms_nb2 = sqrt(mean(P_load_nb2**2))
```

Betekenis:

- `P_load_nb2`: momentaan inertieel vermogen;
- `P_avg_nb2`: gemiddeld vermogen over de volledige run;
- `P_peak_nb2`: piekwaarde;
- `P_rms_nb2`: effectieve waarde.

Ook de krachten worden samengevat:

```python
F_peak_nb2 = max(abs(F_drive_s[active_motion_mask]))
F_rms_nb2 = sqrt(mean(F_drive_s[active_motion_mask]**2))
```

### 10.2 Arbeids-surplus

```python
A_theta_nb2 = np.cumsum((P_load_nb2 - P_avg_nb2) * Ts_nb2)
A_max_nb2 = A_theta_nb2.max() - A_theta_nb2.min()
```

Dit volgt de formule:

```math
A(t) = \int_0^t (P - \bar{P})\,dt
```

en:

```math
A_{max} = \max(A) - \min(A)
```

In Notebook 2 is dit een maat voor de tijdelijke energiefluctuatie door inertie.

### 10.3 Inertie-only motorinschatting

```python
T_motor_peak_les4 = safety * F_peak * lead / (2*pi*eta)
T_motor_rms_les4 = safety * F_rms * lead / (2*pi*eta)
P_motor_rated_req = 1.5 * P_rms
P_motor_peak_req = 1.3 * P_peak
```

Deze waarden zijn bewust gelabeld als inertie-only. Ze zijn nuttig om de methode
uit Les 4 toe te passen, maar ze zijn niet de uiteindelijke motorselectie.

Finale motorselectie:

```text
Notebook 4
```

met een Notebook-3-loadcase inclusief zwaartekracht, wrijving, overdekking en
eventueel trekveren.

## 11. Codecel 21 - Opslag naar `.npz`

De laatste codecel schrijft de resultaten weg:

```python
results_path = Path("notebook2_dynamica_results.npz").resolve()
np.savez(results_path, ...)
```

Belangrijke opgeslagen keys:

| Key | Betekenis |
|---|---|
| `t`, `s`, `ds`, `dds` | traject uit Notebook 1 |
| `w_all` | volledige vector met alle onbekenden per tijdstap |
| `unknown_labels` | namen van de kolommen in `w_all` |
| `dyn_residual`, `dyn_cond` | numerieke kwaliteit van het dynamisch stelsel |
| `F_actuator_y`, `F_drive_s` | actuator-/schuifkracht |
| `R_Ax`, `C_x`, `C_y`, `M_A` | hoofdreacties aan schuiver en frame |
| `F_shak_x`, `F_shak_y`, `F_shak_norm` | inertiele onbalanskracht |
| `M_shak_C` | inertieel onbalansmoment rond C |
| `P_act`, `E_act` | vermogen en arbeid |
| `T_motor`, `motor_speed_rpm` | poelie-/riem-equivalent |
| `masses`, `inertias` | massamodel |
| `payload_mass_K`, `line_mass_density`, `slider_mass` | belangrijke massa-parameters |
| `rod_outer_diameter`, `rod_wall_thickness`, `rod_tube_mass_per_m` | fysische herkomst van de stanglijnmassa |
| `A_max_nb2`, `A_theta_nb2` | arbeids-surplus volgens Les 4 |
| `energy_balance_error_nb2`, `Ekin_nb2` | energiebalanscontrole |

Deze `.npz` is belangrijk omdat Notebook 3 en latere notebooks kunnen nagaan of
hun inertiecomponent overeenkomt met Notebook 2.

## 12. Wat moet je kunnen uitleggen bij deze code?

### Waarom worden punten opnieuw gereconstrueerd?

Notebook 1 levert de hoeken en het traject. Notebook 2 heeft de posities,
snelheden en versnellingen van zwaartepunten nodig. Die volgen uit de
starre-lichaamformules.

### Waarom is `F_drive_s = -F_actuator_y`?

Omdat `s` positief naar beneden is en globale `y` positief naar boven. Een
opwaartse actuatorkracht is dus negatief in de positieve `s`-richting.

### Waarom is er een puntmassa in K?

Omdat een deel van doek/rand/rib-massa aan het uiteinde wordt meegenomen. Die
massa heeft een eigen versnelling en veroorzaakt ook een moment op link 8.

### Waarom zijn de Notebook-2-krachten klein?

Omdat alleen inertie wordt berekend en het traject traag is. Zwaartekracht en
wrijving ontbreken nog.

### Waarom blijft de kinematica gelijk als de massa verandert?

Omdat Notebook 2 inverse dynamica gebruikt. De beweging is opgelegd. Massa's
veranderen de benodigde krachten, niet de opgelegde baan. In een echte machine
moet de motor natuurlijk sterk genoeg zijn om die baan ook werkelijk te volgen.

### Waarom is de poelie-/riemsectie niet de finale motor?

Omdat Notebook 2 alleen een eenvoudige inertie-only actuatoromzetting doet. De
finale aandrijving is motor + riem/kabel/poelie/rem en wordt in Notebook 4
gedimensioneerd.

## 13. Typische fouten bij aanpassen van de code

- Notebook 2 runnen zonder Notebook 1 opnieuw te runnen na een parameterwijziging.
- Een nieuwe key in Notebook 1 gebruiken maar `required_keys` in Notebook 2 niet
  aanpassen.
- De puntmassa in K wel in de krachtbalans zetten maar niet in de momentbalans.
- Het teken van `F_drive_s` verwarren met `F_actuator_y`.
- `R_Ax` interpreteren als riemkracht. Dat is fout: `R_Ax` is een horizontale
  geleidingsreactie.
- De inertie-only motorwaarden uit Notebook 2 verwarren met de finale
  motorwaarden uit Notebook 4.
- `dyn_cond` verwarren met `cond(A)` uit Notebook 1. Notebook 1 kijkt naar de
  kinematische snelheidsmatrix; Notebook 2 naar de dynamische
  Newton-Euler-matrix.

## 14. Korte samenvatting

De code van Notebook 2 doet het volgende:

1. Ze laadt de volledige kinematica uit Notebook 1.
2. Ze kiest massa's en massatraagheden.
3. Ze reconstrueert de zwaartepunten met starre-lichaamkinematica.
4. Ze bouwt per tijdstap een Newton-Euler-stelsel `A w = b`.
5. Ze lost alle gewrichts- en reactiekrachten op.
6. Ze controleert de oplossing met globale balans en energiebalans.
7. Ze berekent inertiele onbalans, vermogen en arbeids-surplus.
8. Ze schrijft alles weg naar `notebook2_dynamica_results.npz`.

De belangrijkste inhoudelijke conclusie uit de code is dat de inertiekrachten
klein zijn voor de huidige rustige beweging. Notebook 2 is daarom vooral de
methodische basis waarop de latere zwaartekracht-, wrijvings-, trekveer- en
overdekkingsanalyses verder bouwen.
