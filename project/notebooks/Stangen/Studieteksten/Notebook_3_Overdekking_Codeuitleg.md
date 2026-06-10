# Notebook 3 - Overdekking - Code-uitleg

Deze tekst hoort bij `Notebook 3 - Overdekking.ipynb`. Het doel is dat je de
code kan uitleggen tijdens het studeren of mondeling: welke data geladen wordt,
welke parameters instelbaar zijn, hoe het overdekkingsmassamodel wordt gemaakt,
hoe dezelfde Newton-Euler-dynamica uit Notebook 3 opnieuw wordt gebruikt, hoe
de voorbalk- en mastcontrole werken, hoe de trekveerloadcase wordt toegevoegd
en welke resultaten naar Notebook 4 gaan.

De plotcode zelf wordt niet regel per regel uitgelegd. Bij plotcellen staat
vooral welke berekende grootheden gebruikt worden en wat de figuur moet
aantonen.

## 0. Overzicht van de codecellen

| Codecel | Inhoud | Belangrijkste output |
|---:|---|---|
| 2 | Setup en kinematica laden uit Notebook 1 | `t`, `s`, `ds`, `dds`, hoeken, geometrie, `canopy_depth` |
| 4 | Overdekkingsparameters en massamodel | `payload_mass_K_equivalent`, `masses`, `inertias`, voorbalk-/doekmassa |
| 6 | Structurele weercontrole voorbalk | `beam_*`, `weather_*`, profielscreening, governing loadcase |
| 8 | Belastingsparameters | `mu_slider`, `mu_pin`, aandrijfparameters, trekveerinstellingen |
| 10 | Rigid-body kinematica | punten, zwaartepunten, snelheden, versnellingen |
| 12 | Newton-Euler stelsel | `build_inverse_dynamics_system(...)` |
| 14 | Wrijvingsmodel en iteratieve solver | `compute_slider_friction_s`, `compute_pin_friction`, `solve_case` |
| 16 | Baseline zonder trekveer, openen/sluiten | `F_drive_s_total_open`, `F_drive_s_total_close`, decompositie |
| 18 | Energiebalans-validatie | `energy_balance_error`, `Ekin`, vermogensbalans |
| 20 | Krachtdecompositiefiguren | gebruikt `F_drive_s_*`, `N_slider`, wrijvingscomponenten |
| 22 | Statische houdanalyse | `F_hold_s_curve`, `R_Ax_hold_curve`, `T_hold_lock_required_curve` |
| 24 | Actuatorvermogen en poelie-equivalent | `P_act_total`, `E_act_total`, `T_motor_total` |
| 26 | Arbeids-surplus en motordimensionering | `A_max_full`, `P_motor_peak_req`, `T_motor_peak_sizing` |
| 28 | Framebelasting en onbalans | `F_A_total`, `F_C_total`, `F_frame_total` |
| 30 | Mast- en schuiverbelasting | `mast_couple_moment`, `F_guide_design`, mastprofielcheck |
| 32 | Opslag baseline | `notebook3_overdekking_results.npz` |
| 34 | Optionele trekverenloadcase | `notebook3_overdekking_trekveren_results.npz` |
| 36 | 3D-animatie | `ani3d`, 3D-weergave van twee mechanismen en doek |

## 1. Datastroom door Notebook 3 - Overdekking

De notebook werkt in deze volgorde:

```text
notebook1_kinematica_results.npz
  -> tijd, schuivertraject, hoeken, snelheden, versnellingen, geometrie

overdekkingsparameters
  -> breedte, aantal mechanismen, doekmassa, voorbalkprofiel

massamodel
  -> linkmassa's, inerties, equivalente K-massa per mechanisme

voorbalkcontrole
  -> doorbuiging, spanning, torsie, weerloadcases

Newton-Euler
  -> inverse dynamica zonder/ met zwaartekracht en wrijving

open/sluitanalyse
  -> openingsrichting en sluitrichting met juiste wrijvingsteken

optionele trekveren
  -> extra bekende kracht op de schuiver

opslag
  -> notebook3_overdekking_results.npz
  -> notebook3_overdekking_trekveren_results.npz
```

Belangrijk:

- de notebook berekent geen nieuwe kinematica;
- de notebook gebruikt dezelfde kinematische baan als Notebook 1;
- het massamodel is eigen aan de overdekking;
- de inverse dynamica is dezelfde Newton-Euler-aanpak als in Notebook 2 en 3;
- Notebook 4 leest de `.npz`-output voor motor, riem, rem en aandrijfas.

## 2. Codecel 2 - Setup en kinematica laden

Deze cel laadt:

```python
kinematics_path = Path("notebook1_kinematica_results.npz")
kin = np.load(kinematics_path)
```

Daaruit komen:

- tijd: `t`;
- schuiverbaan: `s`, `ds`, `dds`;
- hoeken: `theta3` tot `theta8`;
- hoeksnelheden: `dtheta3` tot `dtheta8`;
- hoekversnellingen: `ddtheta3` tot `ddtheta8`;
- punt `K`: `Kx`, `Ky`, `Kdot_*`, `Kddot_*`;
- geometrie: `L1`, `r3a`, `r3b`, enzovoort;
- conditionering: `cond`;
- slaggrenzen: `s_open`, `s_closed`, `stroke`.

### Waarom alleen Notebook 1 laden?

Deze overdekkingsnotebook gebruikt niet letterlijk het massamodel uit Notebook 2.
Dat is bewust. Notebook 2 hoort bij het enkelvoudige mechanisme met een kleine
puntmassa in `K`. De overdekking heeft een eigen voorbalk, doekmassa,
breedte en structurele belastingen.

De kinematica blijft wel dezelfde:

```text
zwaardere massa -> andere krachten
zwaardere massa -> niet automatisch andere s(t)
```

De opgelegde beweging wordt dus volledig bepaald door Notebook 1.

### `canopy_depth`

De code bepaalt de uitval/diepte uit de K-positie:

```python
canopy_depth = float(np.nanmax(Kx) - np.nanmin(C_pos_x))
```

In praktijk is dit de horizontale projectie van het mechanisme. Deze waarde
wordt later gebruikt voor doekoppervlak en weerbelasting.

## 3. Codecel 4 - Overdekkingsparameters en massamodel

Dit is een van de belangrijkste cellen van de notebook. Ze bepaalt hoe de
brede overdekking wordt vertaald naar massa's per mechanisme.

### 3.1 Instelbare hoofdparameters

Belangrijke parameters:

```python
canopy_width = 6.0
mechanism_count_total = 2
fabric_areal_density = 0.35
fabric_mass_to_K_fraction = 1.0
fittings_mass_per_K = 1.5
```

Betekenis:

- `canopy_width`: breedte van de overdekking;
- `mechanism_count_total`: aantal identieke mechanismen over de breedte;
- `fabric_areal_density`: massa per vierkante meter doek;
- `fabric_mass_to_K_fraction`: welk deel van de doekmassa conservatief naar
  de K-punten wordt gebracht;
- `fittings_mass_per_K`: beslag, klemmen en lokale verbindingen rond K.

### 3.2 Voorbalkprofiel

De huidige hoofdcase gebruikt:

```python
front_beam_profile = "200x100x5"
```

De code bevat een profielcatalogus:

```python
beam_profile_catalog = {
    "80x40x3": ...,
    "100x50x3": ...,
    ...
    "200x100x5": ...
}
```

Daaruit volgen:

- doorsnedeoppervlak `beam_area`;
- traagheidsmoment `beam_I`;
- massa per meter;
- totale voorbalkmassa.

De huidige voorbalkmassa is ongeveer:

```text
front_beam_mass_total = 46.98 kg
```

### 3.3 Hulpfuncties voor de balk

De cel definieert onder andere:

```python
rectangular_tube_area(height, width, thickness)
rectangular_tube_I_vertical(height, width, thickness)
beam_deflection_for(width, profile_key)
```

Deze functies zijn eenvoudige profiel- en balkformules:

- `rectangular_tube_area`: doorsnedeoppervlak van een rechthoekige koker;
- `rectangular_tube_I_vertical`: sterk-as traagheidsmoment;
- `beam_deflection_for`: eerste schatting van doorbuiging voor een gekozen
  breedte en profiel.

Ze worden gebruikt om profielkeuzes te vergelijken zonder telkens handmatig
formules te herhalen.

### 3.4 Equivalent payload in K

De kernformule is:

```python
payload_mass_K_equivalent =
    front_beam_mass_total / mechanism_count_total
  + fabric_mass_to_K_fraction * fabric_mass_total / mechanism_count_total
  + fittings_mass_per_K
```

Voor de huidige hoofdcase:

```text
payload_mass_K_equivalent = 27.32 kg per mechanisme
```

Waarom zo?

- de voorbalk hangt aan de uiteinden van de mechanismen;
- de doekmassa werkt verspreid, maar voor motorbelasting wordt ze conservatief
  deels in `K` gelegd;
- beslag en verbindingen zitten lokaal rond de voorrand/K-zone.

Dit is geen exacte eindige-elementenverdeling. Het is een eerste
ontwerpmodel dat conservatief is voor de aandrijfbelasting.

### 3.5 Stang- en schuivermassa

Net als in Notebook 2/3 worden de stangen fysisch opgebouwd uit een aluminium
buis:

```python
rod_outer_diameter = 0.030
rod_wall_thickness = 0.002
rod_material_density = 2700.0
rod_fittings_line_mass_allowance = 0.075
```

Daaruit wordt:

```python
line_mass_density = rod_tube_mass_per_m + rod_fittings_line_mass_allowance
```

De schuiver heeft:

```python
slider_mass = 1.50
```

De massa's per link worden daarna:

```python
masses[3] = line_mass_density * L3
...
masses[8] = line_mass_density * L8
```

De puntmassa in K wordt apart toegevoegd aan link 8 via het momentenevenwicht.
Ze wordt niet gewoon als extra massa over de hele link uitgesmeerd.

### 3.6 Traagheidsmomenten

Voor staven wordt gebruikt:

```python
J = m L^2 / 12
```

Dat is de standaard benadering voor een slanke staaf rond het eigen zwaartepunt.
De schuiver krijgt `J = 0`, omdat hij alleen transleert.

## 4. Codecel 6 - Structurele weercontrole voor de voorbalk

Deze cel is geen inverse dynamica. Ze is een aparte structurele eerste-orde
controle van de voorbalk.

### 4.1 Belangrijke parameters

```python
enable_weather_structural_check = True
deflection_limit_ratio = 300
deflection_limit_abs = 0.020
twist_limit_deg = 2.0
aluminium_yield_strength = 150e6
stress_safety_factor = 1.5
```

Betekenis:

- `deflection_limit_ratio = 300`: limiet zoals `L/300`;
- `deflection_limit_abs = 0.020`: absolute limiet van 20 mm;
- `twist_limit_deg`: maximale torsiehoek;
- `aluminium_yield_strength`: vloeigrensorde van aluminium;
- `stress_safety_factor`: veiligheidsfactor op spanning.

### 4.2 Weerparameters

```python
wind_basic_velocity = 26.0
wind_peak_pressure_factor = 1.8
wind_cp_down = 0.8
wind_cp_uplift = -1.2
ground_snow_load = 0.50e3
snow_shape_coefficient = 0.8
rain_water_depth_cases_mm = [0, 10, 25]
```

Deze waarden zijn instelbare ontwerpwaarden. De notebook gebruikt ze om
Eurocode-geinspireerde loadcases te maken, maar dit is geen gecertificeerde
Eurocodeberekening.

### 4.3 Profielgrootheden

De code berekent:

- sterk-as traagheidsmoment `beam_I_strong`;
- zwak-as traagheidsmoment `beam_I_weak`;
- sectiemoduli;
- shear area;
- torsieconstante;
- schuifmodulus `G`.

Functies:

```python
rectangular_tube_I_weak(...)
rectangular_tube_torsion_constant(...)
```

Die zijn nodig voor:

- buiging;
- dwarskracht;
- torsie;
- twist.

### 4.4 `evaluate_beam_weather_case`

Deze functie berekent voor één loadcase:

```python
q_line
V_max
M_max
delta
sigma_bending
tau_shear
tau_torsion
von_mises
twist
utilization
```

De gebruikte balkformules zijn:

```math
V_\text{max} = qL/2
```

```math
M_\text{max} = qL^2/8
```

```math
\delta_\text{max} = 5qL^4/(384EI)
```

De torsie komt uit een excentriciteit:

```python
front_beam_load_eccentricity = 0.04
```

Dus als wind of regen niet exact door het schuifcentrum werkt, ontstaat een
torsiemoment.

### 4.5 `evaluate_profile_weather_screen`

Deze functie herhaalt de weercontrole voor meerdere profielkeuzes. Zo kan de
notebook tonen waarom een lichter profiel minder verdedigbaar is dan
`200x100x5`.

Belangrijk:

- dit is nuttig voor de ontwerpkeuze;
- het is geen optimalisatie-algoritme;
- de gebruiker kan het profiel bovenaan aanpassen en opnieuw runnen.

### 4.6 Belangrijkste resultaten

Voor de huidige hoofdcase:

```text
governing case = wind uplift
max doorbuiging = 14.98 mm
max von Mises = 27.70 MPa
max twist = 0.034 deg
max utilization = 0.75
beam_structural_ok = True
```

Dit betekent: de voorbalk is binnen deze eerste-orde aannames acceptabel.

## 5. Codecel 8 - Belastingsparameters

Deze cel zet de fysieke belastingsparameters voor de dynamica.

### 5.1 Zwaartekracht

```python
g = 9.81
g_vec = np.array([0.0, -g])
```

De y-as is verticaal omhoog. Zwaartekracht werkt dus in negatieve y-richting.

### 5.2 Schuiverwrijving

```python
mu_slider = 0.08
mu_slider_static = 0.12
c_slider = 0.0
v_eps = 1e-3
```

Betekenis:

- `mu_slider`: dynamische Coulombwrijving van schuiver/collar;
- `mu_slider_static`: statische wrijvingsgrens voor houdanalyse;
- `c_slider`: viskeuze term, hier bewust nul;
- `v_eps`: gladde overgangssnelheid voor `tanh`.

De waarden zijn gekozen als realistische orde voor een outdoor geleiding met
rollen of glijblokken. Ze zijn hoger dan ideale kogellagers, maar lager dan
slechte droge metaal-op-metaalgeleiding.

### 5.3 Pinwrijving

```python
include_pin_friction = True
mu_pin = 0.05
pin_radius = 0.006
omega_eps = 1e-3
```

Pinwrijving wordt later als remmend moment in de momentvergelijkingen gezet.

### 5.4 Iteratieparameters

```python
friction_iterations = 20
friction_tol = 1e-8
```

Wrijving hangt af van reactiekrachten. Daarom wordt iteratief opgelost. Deze
parameters bepalen hoeveel vaste-puntsiteraties maximaal gebeuren en wanneer
de oplossing geconvergeerd is.

### 5.5 Aandrijfquickcheck

```python
actuator_efficiency = 0.78
actuator_safety_factor = 1.50
drive_pulley_radius_nb3 = 0.025
drive_travel_per_rev_nb3 = 2*pi*drive_pulley_radius_nb3
```

Dit is een lokale poelie/riem quick check in Notebook 3. De finale motor- en
poeliekeuze gebeurt in Notebook 4.

De oude naam:

```python
screw_lead = drive_travel_per_rev_nb3
```

blijft alleen als compatibiliteitsalias in de `.npz`. Het ontwerp gebruikt geen
schroefspindel als hoofdarchitectuur.

### 5.6 Trekveerinstellingen

```python
compute_spring_assist_case = True
use_spring_assist_for_main_output = False
spring_count_per_mechanism = 2
spring_design_mode = "fraction_of_baseline_hold"
spring_assist_fraction_open = 0.60
spring_assist_fraction_closed = 0.70
spring_max_assist_fraction = 0.80
```

Betekenis:

- de baseline zonder veer blijft de hoofdcase;
- er wordt wel een aparte trekveerloadcase uitgerekend;
- de veer wordt automatisch gedimensioneerd als fractie van de baseline
  houdkracht;
- `spring_max_assist_fraction` voorkomt volledige compensatie.

## 6. Codecel 10 - Rigid-body kinematica

Deze cel reconstrueert alle punten, zwaartepunten, snelheden en versnellingen.

### 6.1 Hulpfuncties

```python
def perp(v):
    ...
```

Geeft een vector die 90 graden gedraaid is. Deze functie wordt gebruikt voor
rotatiekinematica.

```python
def rigid_point(P0, v0, a0, theta, dtheta, ddtheta, r):
    ...
```

Deze functie berekent positie, snelheid en versnelling van een punt dat op een
starre link ligt op afstand `r`.

Ze gebruikt de vlakke starre-lichaamformules:

```math
v_P = v_O + \omega \times r
```

```math
a_P = a_O + \alpha \times r - \omega^2 r
```

### 6.2 Punten en zwaartepunten

De code bouwt onder andere:

- `C_pos`;
- `B_pos`;
- `E_pos`;
- `G_pos`;
- `J_pos`;
- `K_pos`;
- zwaartepunten van links 2 tot 8.

Ook worden snelheden en versnellingen opgeslagen in dictionaries:

```python
cg_pos
cg_vel
cg_acc
```

en puntdata:

```python
points_pos
points_vel
points_acc
```

### 6.3 Waarom opnieuw reconstrueren?

Notebook 1 levert de kinematische oplossing, maar de dynamica heeft per link
zwaartepunten en puntversnellingen nodig. Daarom reconstrueert deze notebook
alle relevante punten uit dezelfde hoeken en linklengtes.

## 7. Codecel 12 - Inverse dynamica

Dit is de centrale dynamische code.

### 7.1 `moment_2d`

```python
def moment_2d(r, F):
    return r[0] * F[1] - r[1] * F[0]
```

Dit is het vlakke moment:

```math
M_z = r_x F_y - r_y F_x
```

Het wordt gebruikt in de momentvergelijkingen rond het zwaartepunt.

### 7.2 Linkversnellingen

Functies:

```python
link_acc(k, link_id)
link_alpha(k, link_id)
point_acc_K(k)
```

Deze halen per tijdstap de juiste translatieversnelling en hoekversnelling op.

Voor link 8 komt de puntmassa in `K` extra in het evenwicht. Daarom is
`point_acc_K(k)` nodig.

### 7.3 `add_link_equations`

Deze functie voegt per link drie Newton-Eulervergelijkingen toe aan het
lineaire stelsel:

```math
\sum F_x = m a_x
```

```math
\sum F_y = m a_y
```

```math
\sum M_{cg} = J\alpha
```

In code worden onbekende krachten in matrix `A` gezet. Bekende krachten zoals
gewicht, wrijving en veer gaan naar rechterlid `b`.

Voor link 8 wordt de puntmassa in `K` apart toegevoegd. Daardoor telt ook het
moment van de K-massa rond het linkzwaartepunt mee.

### 7.4 `gravity_force`

```python
def gravity_force(m):
    return m * g_vec
```

Geeft de gewichtskracht voor een massa.

### 7.5 `build_inverse_dynamics_system`

Deze functie bouwt voor één tijdstap het hele stelsel:

```python
A, b = build_inverse_dynamics_system(
    k,
    include_gravity=True,
    slider_friction_s=...,
    pin_moments_by_link=...,
    include_spring=False,
    spring_force_up=0.0,
    static=False
)
```

Belangrijke opties:

- `include_gravity`: zwaartekracht toevoegen of niet;
- `slider_friction_s`: bekende schuiverwrijving in s-richting;
- `pin_moments_by_link`: bekende pinwrijvingsmomenten;
- `include_spring`: trekveer als bekende kracht op de schuiver;
- `spring_force_up`: grootte van die opwaartse veerkracht;
- `static`: versnellingen nul zetten voor houdanalyse.

De matrixstructuur blijft dezelfde. Alleen het rechterlid verandert door
extra bekende krachten.

## 8. Codecel 14 - Wrijvingsmodel en iteratieve solver

Deze cel berekent wrijving en lost cases iteratief op.

### 8.1 `reaction_norm_from_solution`

Deze functie haalt een scharnierreactie uit de oplossing `w` en neemt de norm.
Dat is nodig omdat pinwrijving afhangt van de grootte van de reactiekracht.

### 8.2 `compute_slider_friction_s`

De schuiverwrijving is:

```python
F_coulomb = -mu_slider * N_slider * tanh(ds_eff / v_eps)
F_viscous = -c_slider * ds_eff
```

waar:

```python
N_slider = abs(R_Ax)
```

Dus de horizontale reactie op de geleiding bepaalt de schuiverwrijving.

`ds_eff` is belangrijk omdat de notebook ook sluiten kan berekenen. Bij sluiten
moet het teken van de snelheid omkeren, dus de wrijving ook.

### 8.3 `compute_pin_friction`

Per scharnier:

```python
M_base = mu_pin * pin_radius * N_joint * tanh(omega_rel / omega_eps)
```

Dit geeft een remmend moment op de betrokken links. Het moment werkt tegen de
relatieve hoeksnelheid.

### 8.4 `solve_case`

Dit is de algemene solver:

```python
solve_case(
    include_gravity=True,
    include_friction=True,
    include_spring=False,
    spring_force_up_series=None,
    ds_override=None,
    dds_override=None,
    static=False
)
```

De solver:

1. maakt een beginschatting zonder wrijving;
2. lost `A w = b` op;
3. berekent reactiekrachten;
4. berekent schuiver- en pinwrijving;
5. lost opnieuw op;
6. herhaalt tot `friction_tol` of maximaal `friction_iterations`.

De output is een dictionary met onder andere:

- `w_all`: alle onbekenden per tijdstap;
- `residual`: sluitingsresidu van het lineaire stelsel;
- `cond`: conditionering van het dynamisch stelsel;
- `F_slider_friction_s`;
- `N_slider`;
- `joint_friction_moments`;
- `joint_normal_forces`;
- `joint_power_loss`.

## 9. Codecel 16 - Baseline zonder trekveer

Deze cel lost de hoofdloadcases op.

### 9.1 Cases

```python
case_inertia_check = solve_case(include_gravity=False, include_friction=False)
case_gravity = solve_case(include_gravity=True, include_friction=False)
case_total = solve_case(include_gravity=True, include_friction=True)
case_total_close = solve_case(... sluitrichting ...)
```

Betekenis:

- `case_inertia_check`: controle dat de solver dezelfde inertie geeft als
  Notebook 2;
- `case_gravity`: inertie + zwaartekracht, zonder wrijving;
- `case_total`: inertie + zwaartekracht + wrijving;
- `case_total_close`: dezelfde baan, maar in sluitrichting.

### 9.2 `unpack_case`

Deze functie haalt onbekenden uit `w_all`:

```python
vars_total = unpack_case(case_total)
```

Daaruit komen bijvoorbeeld:

- `F_act_y`;
- `R_Ax`;
- `C_x`;
- `C_y`;
- interne scharnierkrachten.

De aandrijfkracht in `s`-richting wordt:

```python
F_drive_s_total = -vars_total["F_act_y"]
```

Dit minteken komt door de tekenconventie: `s` is positief naar beneden, terwijl
globale y positief omhoog is.

### 9.3 Krachtdecompositie

De code maakt:

```python
F_gravity_component = F_drive_s_gravity - F_drive_s_inertia
F_friction_component = F_drive_s_total - F_drive_s_gravity
```

Zo kan je de totale kracht opsplitsen in:

- inertie;
- zwaartekracht;
- wrijving.

Voor de huidige baseline:

```text
max |F_drive_s_total| = 372.24 N per mechanisme
```

### 9.4 Openen en sluiten

De cel maakt ook open- en sluitprofielen:

```python
F_drive_s_total_open
F_drive_s_total_close
P_act_total_open
P_act_total_close
```

Dat is essentieel omdat wrijving van richting verandert en trekveren sluiten
kunnen beinvloeden.

## 10. Codecel 18 - Energiebalans-validatie

Deze cel controleert of vermogen en energie consistent zijn.

De basis is:

```python
P_actuator_val = F_drive_s_total * ds
```

Daarna worden vermogensbijdragen opgebouwd:

- verandering in kinetische energie;
- zwaartekrachtvermogen;
- wrijvingsvermogen;
- actuatorvermogen.

Het residu:

```python
energy_balance_error
```

moet klein zijn. Als dit groot zou zijn, klopt er waarschijnlijk iets niet in:

- tekenconventie;
- puntmassa in `K`;
- wrijvingsrichting;
- arbeid/vermogen;
- versnellingen.

Deze check is belangrijker dan alleen visueel mooie grafieken, omdat ze
controleert of de dynamica energetisch logisch is.

## 11. Codecel 20 - Krachtdecompositiefiguren

Deze cel maakt grafieken, maar de analysevariabelen zijn:

- `F_drive_s_inertia`;
- `F_drive_s_gravity`;
- `F_drive_s_total`;
- `F_gravity_component`;
- `F_friction_component`;
- `N_slider`;
- `F_slider_friction_s`;
- pinwrijvingscomponenten.

De figuren bewijzen:

- inertie is kleiner dan zwaartekracht;
- wrijving is niet verwaarloosbaar;
- de schuiverreactie bepaalt de schuiverwrijving;
- de totale aandrijfkracht komt vooral uit statische belasting.

Je hoeft de plotcode zelf niet te kennen. Je moet wel kunnen zeggen welke
grootheden erin zitten.

## 12. Codecel 22 - Statische houdanalyse

Deze cel berekent de houdkracht voor verschillende schuiverstanden.

Voor elke stand:

```python
A_hold, b_hold = build_inverse_dynamics_system(
    k,
    include_gravity=True,
    static=True
)
w_hold = np.linalg.solve(A_hold, b_hold)
```

`static=True` betekent:

- snelheden nul;
- versnellingen nul;
- alleen statische evenwichtsbelasting.

Belangrijke outputs:

```python
F_hold_s_curve
R_Ax_hold_curve
static_slider_capacity_curve
T_hold_lock_required_curve
```

Waarom nuttig?

De overdekking moet ook in tussenstanden kunnen blijven staan. De notebook
berekent dus niet alleen bewegingskracht, maar ook houdkracht.

Belangrijk:

> De statische schuiverwrijving wordt berekend, maar niet als veilige rem
> gebruikt. Een rem of vergrendeling blijft nodig.

## 13. Codecel 24 - Vermogen en actuatorbelasting

Deze cel vertaalt kracht naar vermogen en poeliekoppel.

### 13.1 Vermogen

```python
P_act_total = F_required_s_total * ds
```

Omdat `ds` positief of negatief kan zijn, kan vermogen positief of negatief
zijn:

- positief: motor levert mechanisch vermogen;
- negatief: belasting drijft terug of remt/regeneratief.

### 13.2 Energie

```python
E_act_total = cumulative_integral(P_act_total, t)
```

De cumulatieve arbeid toont hoeveel energie tijdens de beweging wordt geleverd
of terugkomt.

### 13.3 Poelie-equivalent

```python
motor_speed_rps = ds / drive_travel_per_rev_nb3
T_motor_total = F_required_s_total * drive_travel_per_rev_nb3 / (2*pi*eta)
```

Dit is een lokale quick check. Notebook 4 doet de echte motorselectie met
veiligheidsfactoren, poeliekeuze en mechanismeaantal.

## 14. Codecel 26 - Arbeids-surplus en motordimensionering

Deze cel gebruikt het idee uit Les 4: arbeids-surplus.

De belasting:

```python
P_load_full = F_required_s_total * ds
```

Gemiddeld vermogen:

```python
P_avg_full
```

Arbeids-surplus:

```python
A_theta_full = integral(P_load_full - P_avg_full)
A_max_full = max(A_theta_full) - min(A_theta_full)
```

Interpretatie:

- `A_max_full` toont hoeveel energie tijdelijk boven/onder het gemiddelde
  vermogen zit;
- bij een continu cyclische machine kan dat relevant zijn voor vliegwiel;
- bij deze overdekking is een vliegwiel niet logisch, maar de grootheid blijft
  nuttig om energievariatie en motordimensionering te bespreken.

De cel berekent ook:

```python
T_motor_peak_sizing
T_motor_rms_sizing
P_motor_peak_req
P_motor_rated_req
```

Deze zijn indicatief; de finale motorselectie staat in Notebook 4.

## 15. Codecel 28 - Framebelasting en onbalans

Deze cel bouwt de krachten op die het frame/mast ziet.

Belangrijke arrays:

```python
F_A_total
F_C_total
F_frame_total
F_A_total_norm
F_C_total_norm
F_frame_total_norm
```

`F_A_total` is de lokale belasting aan de schuiver/actuatorzone.
`F_C_total` is de reactie bij het vaste framepunt.
`F_frame_total` is de netto externe framebelasting.

Belangrijk onderscheid:

- onbalans uit Notebook 2 is inertieel;
- framebelasting in deze notebook bevat gewicht en wrijving;
- lokale reacties kunnen groot zijn, zelfs als netto framekracht kleiner is.

De code bewaart ook:

```python
F_shak_norm_inertia
```

als inertie-only referentie.

## 16. Codecel 30 - Mast- en schuiverbelasting

Deze cel is toegevoegd omdat de finale overdekking niet alleen een motorprobleem
is. De schuiver en mast moeten horizontale reacties dragen.

### 16.1 Belangrijke parameters

```python
mast_bracket_spacing_candidates = [...]
mast_reference_bracket_spacing = 1.0
guide_safety_factor = 2.0
loaded_roller_count = 4
guide_reference_roller_capacity = 3000.0
```

Betekenis:

- `mast_bracket_spacing_candidates`: mogelijke afstanden tussen muur-/framebeugels;
- `guide_safety_factor`: veiligheidsfactor op geleidingsreactie;
- `loaded_roller_count`: aantal rollen/glijblokken dat de last deelt;
- `guide_reference_roller_capacity`: referentiecapaciteit per rol/glijblok.

### 16.2 Schuiverreactie

```python
R_Ax_local = vars_total["R_Ax"]
slider_side_reaction_peak = max(abs(R_Ax_local))
```

Voor de huidige hoofdcase:

```text
slider_side_reaction_peak = 1.04 kN per mechanisme
```

Met veiligheidsfactor:

```python
F_guide_design = guide_safety_factor * slider_side_reaction_peak
```

Huidig:

```text
F_guide_design = 2.08 kN
```

### 16.3 Mastmoment

Eerste-orde:

```python
mast_couple_moment = abs(R_Ax_local) * s
```

Huidig:

```text
mast_couple_moment_peak = 625 Nm
```

De beugelkracht wordt geschat met:

```python
F_bracket = M_mast / bracket_spacing
```

Bij `1 m`:

```text
mast_reference_bracket_force = 626 N
```

### 16.4 Mastprofielcheck

De functie:

```python
evaluate_mast_guide_design(...)
```

controleert mastprofielen uit een catalogus. Ze berekent:

- sectiemodulus;
- buigspanning;
- benutting;
- massa per meter;
- pass/fail.

Huidig geselecteerd profiel:

```text
selected_mast_profile = 100x100x5
utilization = 0.070
```

Dit is een eerste-orde check. Het vervangt geen echte verankeringsberekening,
maar het toont dat de mastreacties in een verdedigbare orde liggen.

## 17. Codecel 32 - Opslag baseline

Deze cel schrijft:

```python
notebook3_overdekking_results.npz
```

De output bevat dezelfde hoofdkeys als Notebook 3, zodat Notebook 4 ermee kan
werken:

- `F_drive_s_total`;
- `F_drive_s_inertia`;
- `F_drive_s_gravity`;
- `F_friction_component`;
- `F_hold_s_curve`;
- `R_Ax_total`;
- `F_A_total_norm`;
- `F_C_total_norm`;
- `F_frame_total_norm`;
- `P_act_total`;
- `E_act_total`.

Daarnaast bevat de output overdekkingsspecifieke keys:

- `canopy_width`;
- `canopy_depth`;
- `mechanism_count_total`;
- `front_beam_mass_total`;
- `fabric_mass_total`;
- `payload_mass_K_equivalent`;
- `beam_*`;
- `weather_*`;
- `mast_*`;
- `guide_*`.

Ook worden nieuwe open/sluitkeys opgeslagen:

- `F_drive_s_total_open`;
- `F_drive_s_total_close`;
- `P_act_total_open`;
- `P_act_total_close`;
- `direction_names`;
- `motor_design_direction`.

Notebook 4 gebruikt die keys om automatisch de zwaarste richting te kiezen.

## 18. Codecel 34 - Optionele trekveren

Deze cel rekent een aparte loadcase met trekveren uit.

### 18.1 Spring sizing

De baseline houdkracht wordt gebruikt:

```python
upward_hold_required_curve = max(-F_hold_s_baseline_curve, 0)
```

Daaruit worden doelkrachten gekozen:

```python
spring_force_open_target
spring_force_closed_target
```

Met:

```python
spring_assist_fraction_open = 0.60
spring_assist_fraction_closed = 0.70
spring_max_assist_fraction = 0.80
```

De directe veerstijfheid:

```python
spring_direct_rate_per_spring_N_per_mm = 0.010
spring_k_per_spring = 1000 * spring_direct_rate_per_spring_N_per_mm
spring_k_total = spring_count_per_mechanism * spring_k_per_spring
```

Huidig:

```text
spring_force_open_total = 172.74 N per mechanisme
spring_force_closed_total = 198.24 N per mechanisme
spring_k_total = 20.00 N/m
spring_energy_delta = 236.50 J
```

Dit is een gewone directe trekveer: geen extra kabel, poelie of hefboom in het
veermodel. De veer volgt dezelfde slag als de schuiver:

```text
fysieke veerstijfheid per veer = 0.010 N/mm = 10 N/m
fysieke veerweg over slag      = 1275 mm
fysieke veerkracht per veer    = 86.37 -> 99.12 N
```

De veer is dus lang en relatief slap. Dat is de prijs voor een eenvoudige
directe montage over de volledige schuiverslag.

### 18.2 Veerkracht als bekende kracht

De veer trekt omhoog:

```python
spring_force_up_total
```

In `build_inverse_dynamics_system` wordt dit een bekende kracht op de schuiver:

```python
known.append((np.array([0.0, spring_force_up]), B_pos[k]))
```

De veer verandert dus:

- niet de kinematica;
- niet de massa's;
- niet de matrixstructuur;
- wel het rechterlid van `A w = b`.

### 18.3 Cases met veer

De cel lost opnieuw:

- zwaartekracht + veer zonder wrijving;
- zwaartekracht + veer + wrijving;
- openrichting;
- sluitrichting;
- houdkracht met veer.

Daarna worden dezelfde outputs gemaakt als baseline, maar dan voor de
veerloadcase.

### 18.4 Opslag trekveren

Als `compute_spring_assist_case = True`, schrijft de cel:

```python
notebook3_overdekking_trekveren_results.npz
```

Belangrijke extra keys:

- `spring_force_up_total`;
- `spring_force_s_total`;
- `spring_force_open_total`;
- `spring_force_closed_total`;
- `spring_k_total`;
- `spring_physical_rate_per_spring_N_per_mm`;
- `spring_motion_ratio`;
- `spring_physical_extension_closed`;
- `spring_force_open_physical_per_spring`;
- `spring_force_closed_physical_per_spring`;
- `spring_energy_stored`;
- `spring_energy_delta`;
- `F_drive_s_baseline`;
- `F_hold_s_baseline_curve`.

Deze output is ook Notebook-4-compatibel.

## 19. Codecel 36 - 3D-animatie

Deze cel maakt een 3D-animatie met Matplotlib.

Belangrijke instellingen:

```python
animation_frame_step = 12
```

Functies:

```python
points_at(idx, z)
update_3d(frame_i)
```

`points_at` neemt de 2D-punten uit Notebook 1/3 en plaatst ze op een
z-positie:

- links;
- rechts;
- eventueel meerdere mechanismen als `mechanism_count_total` wordt aangepast.

De animatie tekent:

- de twee stangenmechanismen;
- de masten;
- de schuivers;
- de voorbalk tussen de K-zones;
- een transparant doekvlak.

Belangrijk:

- de animatie is visuele ondersteuning;
- de krachten komen niet uit de animatie;
- de animatie gebruikt dezelfde kinematica als de dynamica.

## 20. Belangrijkste functies samengevat

| Functie | Waarvoor dient ze? |
|---|---|
| `rectangular_tube_area` | doorsnedeoppervlak kokerprofiel |
| `rectangular_tube_I_vertical` | sterk-as traagheidsmoment voorbalk |
| `beam_deflection_for` | snelle doorbuigingsschatting voor profiel/breedte |
| `rectangular_tube_I_weak` | zwak-as traagheidsmoment |
| `rectangular_tube_torsion_constant` | torsieconstante voor gesloten koker |
| `evaluate_beam_weather_case` | volledige weerloadcase voor de voorbalk |
| `evaluate_profile_weather_screen` | profielvergelijking voor voorbalk |
| `perp` | 90 graden rotatie in vlakke kinematica |
| `rigid_point` | positie/snelheid/versnelling van punt op star lichaam |
| `moment_2d` | vlak moment `r x F` |
| `add_link_equations` | Newton-Euler-vergelijkingen aan `A w = b` toevoegen |
| `build_inverse_dynamics_system` | volledig dynamisch stelsel opstellen |
| `compute_slider_friction_s` | schuiverwrijving uit `R_Ax` en `ds` |
| `compute_pin_friction` | pinwrijvingsmomenten uit scharnierreacties |
| `solve_case` | iteratief oplossen van een dynamische loadcase |
| `evaluate_mast_guide_design` | mast- en geleidingsprofielen eerste-orde controleren |
| `points_at` | 3D-animatiepunten per mechanisme maken |
| `update_3d` | animatieframe bijwerken |

## 21. Belangrijkste parameters samengevat

| Parameter | Betekenis | Huidige waarde |
|---|---|---:|
| `canopy_width` | breedte overdekking | `6.0 m` |
| `mechanism_count_total` | aantal mechanismen | `2` |
| `fabric_areal_density` | doekmassa per oppervlak | `0.35 kg/m^2` |
| `fabric_mass_to_K_fraction` | doekmassa naar K | `1.0` |
| `front_beam_profile` | gekozen voorbalk | `200x100x5` |
| `payload_mass_K_equivalent` | equivalente K-massa | `27.32 kg` |
| `mu_slider` | dynamische schuiverwrijving | `0.08` |
| `mu_slider_static` | statische schuiverwrijving | `0.12` |
| `mu_pin` | pinwrijving | `0.05` |
| `drive_pulley_radius_nb3` | referentiepoelie quick check | `25 mm` |
| `compute_spring_assist_case` | trekveerloadcase berekenen | `True` |
| `spring_count_per_mechanism` | trekveren per mechanisme | `2` |
| `spring_max_assist_fraction` | max veercompensatie | `0.80` |
| `wind_basic_velocity` | windbasisnelheid | `26 m/s` |
| `front_beam_load_eccentricity` | excentriciteit weerlast | `0.04 m` |
| `guide_safety_factor` | veiligheidsfactor geleiding | `2.0` |
| `loaded_roller_count` | dragende rollen/glijblokken | `4` |

## 22. Wat moet je kunnen uitleggen?

### Waarom komt de kinematica uit Notebook 1?

Omdat dit inverse dynamica is. De beweging wordt opgelegd. De massa's bepalen
de krachten, niet de baan.

### Waarom heeft de overdekking een eigen massamodel?

Omdat de finale toepassing een voorbalk en doek heeft. De oude `3 kg` puntmassa
uit het enkelvoudige mechanisme is niet representatief voor een 6 m brede
overdekking.

### Waarom wordt de voorbalk apart gecontroleerd?

Omdat motorbelasting en structurele doorbuiging twee verschillende dingen zijn.
Een lichte balk verlaagt motorkracht, maar kan te veel doorbuigen of torsie
krijgen onder wind.

### Waarom is de wrijvingsoplossing iteratief?

Omdat wrijving afhangt van reactiekrachten, terwijl die reactiekrachten pas uit
de oplossing van het dynamisch stelsel komen.

### Waarom openen en sluiten apart?

Omdat wrijving omkeert met bewegingsrichting en de trekveer bij sluiten opnieuw
wordt opgespannen.

### Waarom blijft een rem nodig?

Omdat statische wrijving onzeker is. Tussenstanden moeten gehouden worden door
een motorrem, zelfremmende transmissie of mechanische vergrendeling.

### Waarom is `R_Ax` zo belangrijk?

`R_Ax` is de horizontale geleidingsreactie. De riem draagt die niet. Ze bepaalt:

- schuiverwrijving;
- lokale belasting op rollen/glijblokken;
- mastmoment;
- beugelkrachten.

### Waarom is de trekveer niet gewoon gratis winst?

De trekveer helpt openen en houden, maar bij sluiten moet ze opnieuw energie
opnemen. De zwaartekracht helpt daarbij, maar wrijving blijft verlies.

## 23. Typische valkuilen in deze notebook

- Denken dat de overdekking nieuwe kinematica berekent. Dat doet ze niet.
- `F_s` verwarren met globale y-kracht. `s` is positief naar beneden.
- De K-puntmassa vergeten in het momentenevenwicht van link 8.
- Denken dat de riem ook horizontale reacties draagt. Dat is fout.
- Alleen de openingsrichting bekijken en sluiten vergeten.
- Trekveren als gratis energiebron voorstellen.
- Voorbalkdoorbuiging verwarren met motorbelasting.
- De weercontrole voorstellen als gecertificeerde Eurocodeberekening.
- De statische schuiverwrijving als veilige rem gebruiken.
- De 3D-animatie zien als fysische solver. Ze is alleen visualisatie.

## 24. Samenvatting van de code

De code doet in essentie dit:

1. Laad de Notebook-1-kinematica.
2. Bouw een overdekkingsmassamodel uit voorbalk, doek, stangen en K-massa.
3. Controleer de voorbalk op gewicht, regen, sneeuw en wind.
4. Reconstrueer alle punten en zwaartepunten.
5. Bouw per tijdstap hetzelfde Newton-Euler-stelsel als in Notebook 3.
6. Voeg zwaartekracht, wrijving en eventueel trekveer als bekende krachten toe.
7. Los de baseline zonder veer op voor openen en sluiten.
8. Controleer energie, houdkracht, vermogen en framebelasting.
9. Controleer mast, schuivergeleiding en beugelkrachten.
10. Schrijf een Notebook-4-compatibel `.npz`-bestand.
11. Reken optioneel een tweede `.npz` met trekveren uit.
12. Maak een 3D-animatie als visuele ondersteuning.

Kernzin:

> De overdekkingscode is geen nieuwe dynamische methode. Het is dezelfde
> Newton-Euler inverse dynamica, maar met een realistischer massamodel,
> structurele randcontroles en een Notebook-4-compatibele loadcase voor de
> finale brede toepassing.
