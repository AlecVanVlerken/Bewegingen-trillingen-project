# Notebook 3 - Code-uitleg

Deze tekst hoort bij `Notebook 3.ipynb`. Het doel is dat je de code kan
uitleggen tijdens het studeren of mondeling: welke data geladen wordt, welke
parameters worden gebruikt, hoe zwaartekracht en wrijving in het Newton-Euler
stelsel terechtkomen, welke controles worden uitgevoerd en welke resultaten naar
de volgende notebooks gaan.

De plotcode zelf wordt niet regel per regel uitgelegd. Bij plotcellen staat
vooral welke berekende grootheden gebruikt worden en wat de figuur bewijst.

## 0. Overzicht van de codecellen

| Codecel | Inhoud | Belangrijkste output |
|---:|---|---|
| 3 | Data laden uit Notebook 1 en 2 | kinematica, massa's, inerties, `F_drive_s_inertia` |
| 5 | Parameters voor zwaartekracht, wrijving en aandrijving | `g`, `mu_slider`, `mu_pin`, `drive_travel_per_rev_nb3` |
| 7 | Rigid-body kinematica | gewrichtspunten, zwaartepunten, `K_pos`, `K_vel`, `K_acc` |
| 9 | Newton-Euler stelsel met bekende krachten | `build_inverse_dynamics_system(...)` |
| 11 | Wrijvingsmodel en iteratieve solver | `compute_slider_friction_s`, `compute_pin_friction`, `solve_case` |
| 13 | Cases oplossen en decompositie | `F_drive_s_total`, `F_gravity_component`, `F_friction_component` |
| 15 | Energiebalans-validatie | `energy_balance_error`, `Ekin`, `P_gravity_val`, `P_friction_val` |
| 17 | Krachtdecompositiefiguren | grafieken voor inertie, zwaartekracht, wrijving en normaalkracht |
| 19 | Statische houdanalyse | `F_hold_s_curve`, `R_Ax_hold_curve`, `T_hold_lock_required_curve` |
| 21 | Vermogen en actuatorbelasting | `P_act_total`, `E_act_total`, `T_motor_total` |
| 23 | Arbeids-surplus | `A_theta_full`, `A_max_full`, motorinschatting |
| 25 | Framebelasting en onbalans | `F_A_total_norm`, `F_C_total_norm`, `F_frame_total_norm` |
| 28 | Opslag naar `.npz` | `notebook3_gravity_friction_results.npz` |

## 1. Datastroom door Notebook 3

De notebook werkt in deze volgorde:

```text
notebook1_kinematica_results.npz
  -> tijd, schuivertraject, hoeken, snelheden, versnellingen

notebook2_dynamica_results.npz
  -> massa's, inerties, inertie-only referentiekracht

parameters
  -> zwaartekracht, schuiverwrijving, pinwrijving

rigid-body reconstructie
  -> punten, zwaartepunten, versnellingen

Newton-Euler met bekende krachten
  -> case zonder zwaartekracht/wrijving
  -> case met zwaartekracht
  -> case met zwaartekracht + wrijving

validaties en ontwerpresultaten
  -> energiebalans
  -> houdkracht
  -> vermogen
  -> framebelasting
  -> notebook3_gravity_friction_results.npz
```

Belangrijk:

- Notebook 3 berekent geen nieuwe kinematica;
- massa's en inerties komen uit Notebook 2;
- de inertiecheck moet overeenkomen met Notebook 2;
- de nieuwe inhoud van Notebook 3 zit in zwaartekracht, wrijving en statische
  houdanalyse.

## 2. Codecel 3 - Setup en data laden

Deze cel laadt twee bestanden:

```python
kin = np.load("notebook1_kinematica_results.npz")
dyn = np.load("notebook2_dynamica_results.npz")
```

Notebook 1 levert:

- `t`, `s`, `ds`, `dds`;
- `theta3` tot `theta8`;
- `dtheta3` tot `dtheta8`;
- `ddtheta3` tot `ddtheta8`;
- `Kx`, `Ky`, `Kdot_*`, `Kddot_*`;
- geometrische parameters zoals `r3a`, `r3b`, enzovoort.

Notebook 2 levert:

- `masses`;
- `inertias`;
- `payload_mass_K`;
- `line_mass_density`;
- `slider_mass`;
- `total_model_mass`;
- `F_drive_s`, de inertie-only referentie.

De code controleert eerst of de nodige keys bestaan. Dat voorkomt dat Notebook 3
stilzwijgend met een oud of onvolledig `.npz`-bestand werkt.

### Massa-informatie

Notebook 3 neemt het massamodel uit Notebook 2 over:

```python
mass_array = dyn["masses"].astype(float)
inertia_array = dyn["inertias"].astype(float)
masses = {link_id: mass_array[link_id - 2] for link_id in range(2, 9)}
inertias = {link_id: inertia_array[link_id - 2] for link_id in range(2, 9)}
```

De extra rod-parameters worden ook geladen als ze bestaan:

```python
rod_outer_diameter
rod_wall_thickness
rod_tube_mass_per_m
rod_fittings_line_mass_allowance
```

Die zijn niet nodig om de Newton-Euler-solver te laten werken, maar ze maken de
herkomst van `line_mass_density` verdedigbaar.

### Inertie-referentie

```python
F_drive_s_inertia = dyn["F_drive_s"]
```

Deze array is belangrijk. Later lost Notebook 3 opnieuw een inertie-only case op.
Het verschil met `F_drive_s_inertia` moet ongeveer nul zijn. Zo bewijs je dat de
nieuwe notebook de Notebook-2-dynamica niet breekt.

## 3. Codecel 5 - Parameters

### 3.1 Zwaartekracht

```python
with_gravity = True
g = 9.81
```

`with_gravity` is vooral een duidelijke schakelaar voor de interpretatie. De
werkelijke solve-cases geven later expliciet `include_gravity=True` of `False`
mee.

### 3.2 Schuiverwrijving

```python
mu_slider = 0.08
mu_slider_static = 0.12
c_slider = 0.0
v_eps = 1e-3
```

Betekenis:

- `mu_slider`: dynamische Coulombwrijving tussen schuiver en geleiding;
- `mu_slider_static`: statische wrijvingsgrens voor houdanalyse;
- `c_slider`: viskeuze wrijving;
- `v_eps`: kleine snelheidsschaal voor de `tanh`-benadering.

`c_slider = 0.0` is bewust: de analyse blijft dicht bij Coulombwrijving uit de
lessen. Viscose demping kan later aangezet worden, maar is niet de hoofdcase.

De waarden zijn gekozen als realistische orde-grootte voor een outdoor
schuiver/collar met glijblokken of rollen. Droge kunststof-op-staal
lineaire geleidingen zitten typisch in de orde `mu = 0.05-0.23`; ideale
kogellagers zitten veel lager, maar ons systeem heeft ook afdichtingen,
voorspanning, vuil en imperfecte uitlijning. Daarom is `mu_slider = 0.08`
conservatiever dan een ideaal rollager, maar nog steeds realistisch voor een
lage-wrijving geleiding. De statische waarde `0.12` ligt hoger omdat
losbreekwrijving meestal groter is dan glijwrijving.

De bronorde komt uit fabrikanttabellen voor glijgeleidingen en lagers: igus
drylin/iglidur, JTEKT/Koyo lagerkennis en SKF composietglijlagers. Voor een
definitief productontwerp kies je daarna een echte rail, glijblok of rol en
controleer je de datasheetwaarde voor dat exacte onderdeel.

### 3.3 Pinwrijving

```python
include_pin_friction = True
mu_pin = 0.05
pin_radius = 0.006
omega_eps = 1e-3
```

Pinwrijving wordt als remmend moment gemodelleerd. Ze hangt af van:

- wrijvingscoefficient;
- penradius;
- normaalkracht in het scharnier;
- relatieve hoeksnelheid.

`omega_eps` doet voor rotatie hetzelfde als `v_eps` voor translatie: het maakt de
overgang rond nul snelheid glad.

### 3.4 Iteratieparameters

```python
friction_iterations = 20
friction_tol = 1e-8
```

Wrijving hangt af van reactiekrachten, en reactiekrachten zijn onbekenden. Daarom
moet de oplossing itereren:

```text
oplossen zonder/ met vorige wrijving
  -> reacties schatten
  -> wrijving herberekenen
  -> opnieuw oplossen
```

`friction_tol` bepaalt wanneer de vaste-puntsiteratie voldoende geconvergeerd is.

### 3.5 Actuator-equivalent

```python
actuator_efficiency = 0.78
actuator_safety_factor = 1.50
drive_pulley_radius_nb3 = 0.025
drive_travel_per_rev_nb3 = 2 * np.pi * drive_pulley_radius_nb3
```

Dit is een lokale poelie/tandriem-inschatting in Notebook 3. Ze is nuttig voor
orde-grootte van koppel en vermogen, maar niet de finale motorselectie.

Finale motorselectie gebeurt in Notebook 4. De oude naam `screw_lead` blijft
alleen nog als compatibiliteitskey in de `.npz` bestaan; de actieve betekenis is
nu de lineaire riemverplaatsing per poelieomwenteling.

## 4. Codecel 7 - Rigid-body kinematica

Deze cel is sterk gelijkend op Notebook 2. Ze reconstrueert alle punten en
zwaartepunten uit de Notebook-1-kinematica.

### 4.1 `perp`

```python
def perp(v):
    ...
```

Deze functie draait een 2D-vector 90 graden:

```text
(x, y) -> (-y, x)
```

Ze wordt gebruikt voor:

```math
\omega \times r = \omega \, perp(r)
```

en:

```math
\alpha \times r = \alpha \, perp(r)
```

### 4.2 `rigid_point`

```python
def rigid_point(ref_pos, ref_vel, ref_acc, theta, omega, alpha_val, local_vector):
```

Deze functie rekent een lokaal punt op een link om naar:

- globale positie;
- globale snelheid;
- globale versnelling.

De formules zijn:

```math
v_P = v_O + \omega \times r_{OP}
```

```math
a_P = a_O + \alpha \times r_{OP} - \omega^2 r_{OP}
```

### 4.3 Punten en zwaartepunten

De cel maakt:

- `C_pos`, `B_pos`;
- gewrichtspunten `D`, `E`, `F`, `G`, `H`, `I`, `J`, `K`;
- dictionaries `cg_pos`, `cg_vel`, `cg_acc`, `alpha`.

Die dictionaries worden later gebruikt in Newton-Euler:

```python
masses[link_id] * cg_acc[link_id][k]
inertias[link_id] * alpha[link_id][k]
```

De cel controleert ook de reconstructie van `K` en de sluitverschillen van `E`,
`G` en `J`. Als die fout groot wordt, is de puntreconstructie fout en zijn de
krachten niet betrouwbaar.

## 5. Codecel 9 - Inverse dynamica met bekende krachten

Deze cel bouwt het algemene Newton-Euler-stelsel.

### 5.1 `moment_2d`

```python
def moment_2d(r, F):
    return r[0] * F[1] - r[1] * F[0]
```

Dit is:

```math
M_z = xF_y - yF_x
```

### 5.2 `link_acc`, `link_alpha`, `point_acc_K`

Deze functies kiezen tussen dynamisch en statisch rekenen:

- dynamisch: gebruik echte versnellingen uit de kinematica;
- statisch: gebruik nul versnelling.

Voor de houdanalyse wordt `static=True` gebruikt. Dan berekent de notebook de
kracht om de positie vast te houden zonder bewegingsversnelling.

### 5.3 `add_link_equations`

Deze functie voegt drie vergelijkingen toe voor een link:

```math
\sum F_x = m a_x
```

```math
\sum F_y = m a_y
```

```math
\sum M_{cg} = J\alpha
```

Ze kan omgaan met:

- onbekende krachten;
- bekende krachten;
- bekende momenten;
- puntlasten zoals de massa in `K`;
- onbekende momenten zoals `M_A`.

Bekende krachten worden naar het rechterlid gebracht. Daardoor verandert de
matrixstructuur niet wanneer zwaartekracht of wrijving wordt toegevoegd.

### 5.4 `gravity_force`

```python
def gravity_force(link_id):
    return masses[link_id] * g_vec
```

`g_vec` is `[0, -g]`. Deze bekende kracht wordt toegevoegd aan elke link wanneer
`include_gravity=True`.

Voor puntmassa `K` wordt de zwaartekracht meegenomen via:

```python
K_eff = payload_mass_K * (point_acc_K(k, static) - g_vec)
```

Als `include_gravity=False`, wordt alleen `m_K a_K` gebruikt.

### 5.5 `build_inverse_dynamics_system`

```python
def build_inverse_dynamics_system(k, include_gravity=True,
                                  slider_friction_s=0.0,
                                  pin_moments_by_link=None,
                                  static=False):
```

Deze functie bouwt `A` en `b` voor een tijdstap.

Belangrijke argumenten:

- `include_gravity`: gewicht wel/niet opnemen;
- `slider_friction_s`: bekende schuiverwrijving in s-richting;
- `pin_moments_by_link`: bekende pinwrijvingsmomenten per link;
- `static`: versnellingen nul maken voor houdanalyse.

De functie roept `add_link_equations` op voor link 2 tot en met 8. Aan het eind
controleert ze of exact `n_unknowns` vergelijkingen zijn toegevoegd.

## 6. Codecel 11 - Wrijvingsmodel en solver

### 6.1 `omega_link`

```python
omega_link = {0: zero, 2: zero, 3: dtheta3, ...}
```

Deze dictionary koppelt elke link aan zijn hoeksnelheid. Link 0 is grond en link
2 is de schuiver, dus die hebben rotatiesnelheid nul.

### 6.2 `joint_definitions`

De notebook definieert per scharnier:

- welke twee links verbonden zijn;
- welke onbekende krachtcomponenten bij het gewricht horen.

Die informatie is nodig om:

- de normaalkracht in het gewricht te bepalen;
- de relatieve hoeksnelheid te bepalen;
- pinwrijving te berekenen.

### 6.3 `reaction_norm_from_solution`

```python
def reaction_norm_from_solution(w, fx_name, fy_name):
```

Deze functie haalt uit de oplossingsvector `w` een gewrichtskracht en berekent
de norm:

```math
|R| = \sqrt{R_x^2 + R_y^2}
```

Die norm is de basis voor pinwrijving.

### 6.4 `compute_slider_friction_s`

```python
F_coulomb = -mu_slider * N_slider * tanh(ds[k] / v_eps)
F_viscous = -c_slider * ds[k]
```

met:

```python
N_slider = abs(R_Ax)
```

De schuiverwrijving hangt dus af van de horizontale geleidingsreactie. Ze werkt
tegen de schuiverbeweging.

### 6.5 `compute_pin_friction`

Deze functie loopt over alle `joint_definitions` en berekent per gewricht een
remmend moment:

```math
M = \mu r |R| tanh(\omega_{rel}/\omega_\epsilon)
```

De momenten worden per link opgeslagen in `pin_moments_by_link`. De ene link
krijgt het tegengestelde moment van de andere link, volgens actie-reactie.

### 6.6 `solve_case`

```python
def solve_case(include_gravity=True, include_friction=False, static=False):
```

Deze functie lost de volledige tijdreeks op.

Als `include_friction=False`:

- er wordt eenmalig opgelost;
- schuiverwrijving en pinwrijving zijn nul.

Als `include_friction=True`:

1. los op met huidige wrijvingsschatting;
2. bereken nieuwe schuiver- en pinwrijving;
3. los opnieuw op;
4. herhaal tot `friction_tol` of maximaal `friction_iterations`.

De output is een dictionary met onder andere:

- `w_all`;
- `dyn_residual`;
- `F_slider_friction_s`;
- `N_slider`;
- `joint_power_loss`;
- `friction_iterations_used`.

## 7. Codecel 13 - Oplossingen en controles

De notebook lost drie cases op:

```python
case_inertia_check = solve_case(include_gravity=False, include_friction=False)
case_gravity = solve_case(include_gravity=True, include_friction=False)
case_total = solve_case(include_gravity=True, include_friction=True)
```

Daarna worden de oplossingen uitgepakt met:

```python
vars_inertia_check = unpack_case(case_inertia_check)
vars_gravity = unpack_case(case_gravity)
vars_total = unpack_case(case_total)
```

De aandrijfkracht is:

```python
F_drive_s = -F_act_y
```

De decompositie:

```python
F_drive_s_inertia_check = -vars_inertia_check["F_act_y"]
F_drive_s_gravity = -vars_gravity["F_act_y"]
F_drive_s_total = -vars_total["F_act_y"]
F_gravity_component = F_drive_s_gravity - F_drive_s_inertia
F_friction_component = F_drive_s_total - F_drive_s_gravity
```

Belangrijk:

- `F_drive_s_inertia_check` moet gelijk zijn aan Notebook 2;
- `F_gravity_component` is het extra effect van gewicht;
- `F_friction_component` is het extra effect van schuiver- en pinwrijving.

De cel print ook:

- maximale krachtcomponenten;
- residuen;
- wrijvingssigncheck.

De wrijvingssigncheck controleert of wrijving energie dissipeert en niet
onbedoeld energie toevoegt.

## 8. Codecel 15 - Energiebalans

Deze cel controleert:

```math
P_{act} =
\frac{dE_{kin}}{dt}
+ P_g
+ P_{fric}
```

De code gebruikt:

```python
P_actuator_val = F_drive_s_total * ds
```

`F_s * ds` is vermogen.

De kinetische energie bevat:

- translatie van alle links;
- rotatie van alle stangen;
- translatie van puntmassa `K`.

Het zwaartekrachtvermogen wordt berekend met:

```python
P_gravity_val += m * g * v_y
```

Het wrijvingsvermogen bevat:

- schuiverwrijving;
- pinwrijvingsverliezen.

`energy_balance_error` is het verschil tussen links en rechts. Een kleine fout
betekent dat krachten, vermogen en energie consistent zijn.

## 9. Codecel 17 - Krachtdecompositiefiguren

Deze cel maakt figuren van eerder berekende arrays. De plotcode zelf is niet
inhoudelijk belangrijk, maar de gebruikte grootheden wel:

- `F_drive_s_inertia`;
- `F_gravity_component`;
- `F_friction_component`;
- `F_drive_s_total`;
- `F_slider_drive_component`;
- `F_pin_friction_component`;
- `case_total["N_slider"]`.

De figuren bewijzen:

- inertie is klein;
- zwaartekracht domineert;
- schuiverwrijving is relevant;
- pinwrijving is kleiner;
- de schuiverreactie bepaalt de schuiverwrijving.

## 10. Codecel 19 - Statische houdanalyse

Deze cel sorteert de beweging op schuiverstand:

```python
hold_curve_indices = np.argsort(s)
hold_s_curve = s[hold_curve_indices]
```

Daarna lost ze voor representatieve standen een statische case op:

```python
static=True
```

Dat betekent:

- versnellingen nul;
- snelheden niet gebruikt voor inertie;
- zwaartekracht blijft actief;
- wrijving/houdcapaciteit wordt apart bekeken.

Belangrijke outputs:

- `F_hold_s_curve`;
- `R_Ax_hold_curve`;
- `static_slider_capacity_curve`;
- `T_hold_motor_curve`;
- `T_hold_lock_required_curve`.

De statische schuiverwrijvingsgrens:

```python
static_slider_capacity_curve = mu_slider_static * abs(R_Ax_hold_curve)
```

Deze waarde is een theoretische capaciteit, geen veilige rem. Daarom blijft een
rem of vergrendeling nodig.

## 11. Codecel 21 - Vermogen en actuatorbelasting

Deze cel gebruikt de totale aandrijfkracht:

```python
F_required_s_total = F_drive_s_total.copy()
P_act_total = F_required_s_total * ds
```

Daarna integreert ze vermogen tot arbeid:

```python
E_act_total[1:] = cumsum(trapeziumintegratie van P_act_total)
```

Voor het poelie-equivalent:

```python
motor_speed_rps = ds / drive_travel_per_rev_nb3
T_motor_total = F_required_s_total * drive_travel_per_rev_nb3 / (2*pi*actuator_efficiency)
```

Omdat:

```math
screw\_lead = 2\pi r
```

is dit equivalent aan:

```math
T = \frac{F r}{\eta}
```

Ook hier geldt: dit is een Notebook-3 quick check. Notebook 4 is de finale
motoranalyse.

## 12. Codecel 23 - Arbeids-surplus

De cel berekent:

```python
P_load_full = F_required_s_total * ds
P_avg_full = P_load_full.mean()
A_theta_full = cumsum((P_load_full - P_avg_full) * Ts)
A_max_full = A_theta_full.max() - A_theta_full.min()
```

Dit volgt de Les-4 definitie:

```math
A(t)=\int (P-\bar{P})dt
```

`A_max_full` is een maat voor de energiefluctuatie. Voor dit trage
positioneermechanisme is dat vooral nuttig als vergelijking, niet als reden om
een vliegwiel te kiezen.

## 13. Codecel 25 - Framebelasting en onbalans

Deze cel combineert:

- schuiverreactie;
- actuatorreactie;
- schuiverwrijving;
- framekracht in C.

Belangrijke arrays:

```python
F_A_total
F_C_total
F_frame_total
F_frame_total_norm
```

`F_frame_total` is niet hetzelfde als Notebook-2-onbalans. Notebook 2 keek
alleen naar inertie. Notebook 3 bevat ook gewicht en wrijving.

De figuren zijn nuttig om te tonen:

- welke belasting naar schuiver/mast gaat;
- welke belasting naar punt C gaat;
- hoe groot de netto framebelasting is;
- waarom lokale reacties groter kunnen zijn dan globale onbalans.

## 14. Codecel 28 - Opslag

De laatste codecel schrijft:

```python
notebook3_gravity_friction_results.npz
```

Belangrijke opgeslagen keys:

| Key | Betekenis |
|---|---|
| `F_drive_s_total` | totale aandrijfkracht met zwaartekracht en wrijving |
| `F_drive_s_inertia` | inertie-only referentie uit Notebook 2 |
| `F_drive_s_gravity` | kracht met zwaartekracht maar zonder wrijving |
| `F_gravity_component` | geisoleerde zwaartekrachtbijdrage |
| `F_friction_component` | geisoleerde wrijvingsbijdrage |
| `F_slider_drive_component` | bijdrage van schuiverwrijving |
| `F_pin_friction_component` | bijdrage van pinwrijving |
| `F_hold_s_curve` | statische houdkracht over het bereik |
| `P_act_total`, `E_act_total` | vermogen en arbeid |
| `A_max_full` | arbeids-surplus |
| `F_A_total_norm`, `F_C_total_norm`, `F_frame_total_norm` | frame- en steunbelasting |
| `mu_slider`, `mu_pin`, `pin_radius` | wrijvingsparameters |
| `rod_*` | herkomst van de stanglijnmassa |

Notebook 4 kan dit bestand lezen als `load_case = "baseline"`.

## 15. Wat moet je kunnen uitleggen bij deze code?

### Waarom worden meerdere cases opgelost?

Om de totale kracht op te splitsen in inertie, zwaartekracht en wrijving. Zonder
die decompositie zie je alleen de eindwaarde, niet wat de oorzaak is.

### Waarom is de solver iteratief?

Omdat wrijving afhangt van reactiekrachten, en die reacties zijn zelf onbekenden
van het Newton-Euler-stelsel.

### Waarom blijft de matrixstructuur gelijk?

Omdat zwaartekracht en wrijving bekende krachten zijn. Ze veranderen het
rechterlid `b`, niet de lijst met onbekenden.

### Waarom is `F_drive_s = -F_act_y`?

Omdat `s` positief naar beneden is en globale `y` positief omhoog.

### Waarom is de houdanalyse nodig?

Omdat de motor/rem niet alleen tijdens beweging belast wordt. Ook in stilstand
moet de paraplu in positie blijven.

### Waarom is Notebook 4 nog nodig?

Notebook 3 berekent de mechanische belasting. Notebook 4 vertaalt die naar
motor, poelie, rem, riem/kabel, aandrijfas, veiligheid en kost.

## 16. Typische fouten bij aanpassen

- Notebook 3 runnen zonder Notebook 1 en 2 opnieuw te runnen na parameterwijziging.
- De massa's in Notebook 3 apart hardcoden in plaats van uit Notebook 2 laden.
- Zwaartekracht op puntmassa `K` vergeten.
- Wrijving met het verkeerde teken toevoegen.
- `R_Ax` zien als riemkracht. Het is een horizontale geleidingsreactie.
- De statische schuiverwrijvingsgrens zien als veilige vergrendeling.
- De quick motorsectie in Notebook 3 verwarren met finale motorselectie in
  Notebook 4.

## 17. Korte samenvatting

De code van Notebook 3 doet het volgende:

1. Ze laadt de kinematica uit Notebook 1 en de inertie/massa uit Notebook 2.
2. Ze reconstrueert punten en zwaartepunten.
3. Ze bouwt hetzelfde Newton-Euler-stelsel als Notebook 2.
4. Ze voegt zwaartekracht, schuiverwrijving en pinwrijving toe als bekende
   krachten/momenten.
5. Ze lost aparte cases op om inertie, zwaartekracht en wrijving te isoleren.
6. Ze valideert met residu en energiebalans.
7. Ze berekent houdkracht, vermogen, arbeids-surplus en framebelasting.
8. Ze schrijft een Notebook-4-compatibel `.npz`-bestand weg.

De belangrijkste codeconclusie is dat de inertiecomponent klein blijft en dat de
belasting vooral uit zwaartekracht en wrijving komt.
