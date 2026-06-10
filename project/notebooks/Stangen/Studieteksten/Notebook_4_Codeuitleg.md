# Notebook 4 - Code-uitleg

Deze tekst hoort bij `Notebook 4.ipynb`. Het doel is dat je de code kan
uitleggen zonder elke plotregel te moeten kennen. De nadruk ligt op:

- welke data uit Notebook 3 wordt geladen;
- welke parameters bovenaan instelbaar zijn;
- hoe kracht, vermogen, koppel, rem en motor worden berekend;
- hoe precisie, as, frequentie, kost en energie worden gecontroleerd;
- welke resultaten naar het `.npz`-bestand worden geschreven.

De plotcode zelf is niet belangrijk om regel per regel te kennen. Bij
plotcellen staat vooral welke berekende grootheden gebruikt worden en waarom de
figuur nuttig is.

## 0. Overzicht van de codecellen

| Codecel | Inhoud | Belangrijkste output |
|---:|---|---|
| 2 | Loadcase kiezen en data laden | `F_drive_s_total`, open/sluitdata, `mechanism_count` |
| 4 | Aandrijfparameters instellen | poelies, rendementen, veiligheidsfactoren, motorclasses |
| 6 | Kracht, vermogen en koppel berekenen | `line_force_design`, `T_pulley_design`, `P_peak_motor_input` |
| 8 | Houdkracht en rem | `T_hold_brake_design_curve`, `recommended_brake_torque` |
| 10 | Poelieradius en reductie vergelijken | `radius_rows`, `gear_rows` |
| 12 | Motorklasse kiezen | `selected_motor_class_name`, marges |
| 14 | Precisieanalyse | encoderresolutie, backlash, elastische vervorming |
| 16 | Tijdschaalanalyse | effect van sneller/trager bewegen |
| 18 | Meerdere mechanismen | schaalgedrag van kracht, koppel en vermogen |
| 20 | Gemeenschappelijke aandrijfas | torsiehoek, schuifspanning, lagerbelasting |
| 22 | Frequentie-inhoud | spectra van versnelling en aandrijfkracht |
| 24 | Aandrijfschets, kost en energie | energie/cyclus, jaarverbruik, kostorde |
| 26 | Resultaten opslaan | `notebook4_aandrijving_*.npz` |

## 1. Datastroom door Notebook 4

Notebook 4 begint altijd met een `.npz`-bestand uit een Notebook-3-loadcase.

```text
Notebook 3-loadcase
  -> F_drive_s_total, F_hold_s_curve, ds, s, t, energie, reacties
  -> Notebook 4
  -> motor, poelie, riem/kabel, rem, encoder, as, kost, energie
```

De notebook heeft geen eigen kinematische solver en geen eigen Newton-Euler
stelsel. Dat is bewust:

- kinematica hoort in Notebook 1;
- inverse dynamica hoort in Notebook 2/3;
- praktische aandrijving hoort in Notebook 4.

Daardoor kan dezelfde Notebook 4 gebruikt worden voor:

- het enkelvoudige basismodel;
- het enkelvoudige model met trekveren;
- de brede overdekking;
- de brede overdekking met trekveren;
- een custom loadcase.

## 2. Codecel 2 - Loadcase kiezen en data laden

De belangrijkste instelling is:

```python
load_case = "overdekking_trekveren"
custom_results3_filename = "notebook3_gravity_friction_results.npz"
```

De mapping is:

```python
load_case_files = {
    "baseline": "notebook3_gravity_friction_results.npz",
    "trekveren": "notebook3_trekveren_results.npz",
    "overdekking": "notebook3_overdekking_results.npz",
    "overdekking_trekveren": "notebook3_overdekking_trekveren_results.npz",
}
```

Als `load_case = "custom"` wordt gebruikt, leest de notebook het bestand uit
`custom_results3_filename`.

### Waarom deze selector nodig is

Notebook 4 is bedoeld als algemene aandrijfmodule. De motorberekening moet dus
niet hard gekoppeld zijn aan een specifiek Notebook 3-bestand.

Voor de huidige finale analyse gebruiken we:

```text
overdekking_trekveren
```

maar de baseline zonder trekveren blijft nuttig als vergelijking.

### Vereiste data

De notebook controleert of de belangrijkste keys aanwezig zijn, zoals:

```python
t
s
ds
dds
F_drive_s_total
F_drive_s_inertia
F_hold_s_curve
```

Voor nieuwere loadcases worden ook open/sluitkeys gelezen:

```python
F_drive_s_total_open
F_drive_s_total_close
P_act_total_open
P_act_total_close
E_positive_open
E_positive_close
```

Als die bestaan, gebruikt Notebook 4 automatisch de zwaarste richting. Dat is
vooral belangrijk bij trekveren. Een veer helpt bij openen, maar kan bij sluiten
juist extra belasting of remwerking veroorzaken.

### Mechanism count

Als het `.npz`-bestand `mechanism_count_total` bevat, gebruikt Notebook 4 dat
als standaard aantal aangedreven mechanismen:

```python
loadcase_mechanism_count = int(data["mechanism_count_total"])
```

Voor de overdekking is dat `2`. Daardoor worden krachten en vermogens
automatisch opgeschaald naar de totale motorbelasting.

## 3. Codecel 4 - Ontwerpkeuzes voor de aandrijving

Deze cel bevat de belangrijkste instelbare parameters.

### Aandrijvingstype

```python
drive_type = "belt_cable"
```

De notebook is uitgewerkt voor een tandriem- of kabel/riemaandrijving langs de
mast. De oude schroefspindelgedachte is niet meer de finale architectuur.

Fysisch betekent dit:

- motor drijft een poelie of as aan;
- gesloten riem/kabel loopt langs de mast;
- schuiver/collar zit vast aan een aangedreven riemtak;
- retourtak loopt vrij terug;
- riem levert verticale kracht;
- geleiding draagt horizontale kracht en kantelmoment.

### Aantal mechanismen

```python
mechanism_count_override = None
mechanism_count = ...
```

Als `mechanism_count_override = None`, gebruikt de notebook het aantal uit de
loadcase. Voor de overdekking is dat `2`.

Als je bijvoorbeeld wil testen wat een motor moet leveren voor drie identieke
mechanismen, kan dit handmatig overschreven worden. Dan moet je wel opletten:
de mechanica van de voorbalk en de massa's moeten dan ook in Notebook 3 -
Overdekking consequent aangepast worden.

### Poelieradius

```python
auto_select_pulley = True
preferred_pulley_radius = 0.025
pulley_radius_candidates = np.array([...])
minimum_practical_pulley_radius = 0.025
manual_pulley_radius = 0.025
```

De poelieradius bepaalt de omzetting van lijnkracht naar koppel:

```text
T = F r / eta
```

En ook de snelheid:

```text
rpm = |ds| / (2 pi r) * 60
```

De huidige automatische selectie kiest `25 mm`. Dat is tegelijk de voorkeursradius en de minimum praktische radius voor buitengebruik. Een kleinere poelie zou minder koppel vragen, maar buigt de riem sterker en geeft minder robuuste levensduur.

### Rendementen

```python
belt_stage_efficiency = 0.92
drive_efficiency = 0.78
gear_efficiency = 0.85
```

`belt_stage_efficiency` wordt gebruikt voor de omzetting van lijnkracht naar poeliekoppel. `drive_efficiency` is de globale schatting voor motor/reductor/riemvermogen. Dit is bewust conservatief omdat er verliezen zijn in:

- riem/kabel;
- poelies;
- lagers;
- uitlijning;
- schuiverklem;
- buitengebruik en vervuiling.

`gear_efficiency` wordt gebruikt om het motorkoppel aan de motoras in te
schatten na de reductie.

### Veiligheidsfactoren

```python
drive_safety_factor = 2.0
brake_safety_factor = 2.0
```

De aandrijving wordt niet exact op de berekende piekwaarde gekozen. Er zit een
factor 2 op bewegingskracht en remkracht omdat het model niet alle praktische
effecten bevat.

### Praktische minimale lijnkracht

```python
line_force_floor_single = 200.0
line_force_floor_double = 300.0
line_force_floor_multi_per_mechanism = 150.0
```

Deze ondergrens voorkomt dat de notebook een te licht aandrijfsysteem voorstelt
als de berekende krachten toevallig laag zijn. Voor een buitenconstructie is een
minimale robuustheid nodig.

### Motorparameters

```python
motor_voltage = 48.0
controller_current_margin = 1.50
motor_nominal_speed_rpm = 2000.0
motor_peak_speed_rpm = 2000.0
```

De notebook gebruikt hiermee een realistische klasse voor een 48 V BLDC/servo
met reductor, encoder en rem. De pieksnelheid wordt conservatief gelijk genomen
aan de bruikbare ontwerpsnelheid, niet aan een optimistische no-load snelheid.
De motorstroom wordt als orde van grootte bepaald uit vermogen, spanning en
marge.

### Reductie

```python
gear_ratio_candidates = np.array([10, 16, 20, 25, 28, 35, 40, 50, 70, 80, 100])
```

De notebook kiest automatisch een reductie die genoeg koppel geeft zonder de
motor boven de pieksnelheid te duwen. In de huidige case is dat:

```text
25:1
```

### Precisieparameters

```python
encoder_counts_per_rev = 1024
encoder_quadrature_factor = 4
backlash_estimate_mm = 1.5
position_tolerance_mm = 5.0
effective_drive_stiffness_N_per_m = 1.5e5
```

Deze waarden zijn niet bedoeld als exact catalogusontwerp. Ze geven wel een
redelijke orde van grootte om te zien of de gekozen aandrijving precies genoeg
kan positioneren.

### Kostparameters

```python
open_close_cycles_per_day = 1.0
operating_days_per_year = 220.0
electricity_price_eur_per_kwh = 0.35
```

De energiekost wordt daarmee geschat. Omdat de beweging traag en zeldzaam is,
blijkt de energiekost zeer klein.

### Motorclass arrays

De notebook vergelijkt eenvoudige motorklassen:

```python
100 W
250 W
500 W
750 W
```

met bijhorende uitgangskoppels, remkoppels, kostordes en encoder/rem-vlaggen.
Dit is geen exacte productselectie, maar een selectie van een technische klasse.

### Asopties

De gemeenschappelijke as wordt getest met:

```python
solid_30
solid_35
solid_40
tube_40x5
tube_50x5
tube_60x5
```

Voor elke as wordt massa, schuifspanning en torsiehoek berekend.

## 4. Codecel 6 - Kracht, vermogen en koppel

Deze cel is de kern van Notebook 4.

Eerst wordt de kracht per mechanisme opgeschaald:

```python
F_s_drive = mechanism_count * F_s_one
F_hold_drive_curve = mechanism_count * F_hold_s_curve
```

Voor de overdekking met twee mechanismen:

```text
F_totaal = F_links + F_rechts
```

Daarna worden open- en sluitrichting apart bekeken als die data aanwezig is:

```python
F_s_drive_open
F_s_drive_close
```

De notebook bepaalt de zwaarste richting. In de huidige case is dat openen.

### Ontwerplijnkracht

De ontwerpwaarde wordt:

```python
line_force_design = max(
    drive_safety_factor * line_force_peak_operating,
    line_force_floor
)
```

Voor de huidige case:

```text
line_force_peak_motion = 398.95 N
line_force_design = 797.91 N
```

### Poeliesnelheid

De poeliesnelheid volgt uit de schuiversnelheid:

```python
pulley_speed_rps = ds_drive / (2*pi*r)
pulley_speed_rpm = pulley_speed_rps * 60
```

Met `r = 25 mm` wordt:

```text
gemiddelde uitgangssnelheid = 24.53 rpm
piek uitgangssnelheid = 75.06 rpm
```

### Poeliekoppel

De koppelvergelijking is:

```python
T_pulley_active = F_s_drive * r / belt_stage_efficiency
T_pulley_design = line_force_design * r / belt_stage_efficiency
```

Voor de huidige case:

```text
T_pulley_design = 21.68 Nm
```

### Vermogen

De schuiververmogens komen uit:

```python
P_slider_drive = F_s_drive * ds_drive
```

Omdat het teken afhankelijk is van conventies, kijkt de notebook naar positieve
vermogenspieken voor de motorbelasting. Het motorinputvermogen wordt gedeeld
door het rendement en met marge gebruikt voor de motorklasse.

Voor de huidige case:

```text
piek schuiververmogen totaal = 57.06 W
piek motorinputvermogen = 146.31 W
aanbevolen motorvermogen = 219.46 W
```

## 5. Codecel 8 - Houdkracht, rem en tussenstanden

Deze cel gebruikt de statische houdkracht uit Notebook 3:

```python
F_hold_drive_curve = mechanism_count * F_hold_s_curve
```

De benodigde remkoppels worden:

```python
T_hold_output_curve = abs(F_hold_drive_curve) * r
T_hold_brake_design_curve = brake_safety_factor * T_hold_output_curve
```

Ook het equivalente motorschachtkoppel na reductie wordt bepaald:

```python
T_hold_motor_shaft_curve =
    T_hold_brake_design_curve / (selected_gear_ratio * gear_efficiency)
```

Waarom dit belangrijk is:

- de overdekking moet halfopen kunnen blijven staan;
- de motor mag niet constant stroom moeten leveren;
- bij spanningsuitval moet de constructie niet zomaar zakken;
- schuiverwrijving is geen betrouwbare rem.

Voor de huidige case:

```text
max remkoppel aan uitgang = 11.57 Nm
```

## 6. Codecel 10 - Poelieradius en reductie vergelijken

Deze cel vult twee tabellen:

```python
radius_rows
gear_rows
```

### `radius_rows`

Voor elke kandidaat-poelieradius wordt bekeken:

- gemiddelde rpm;
- piek-rpm;
- ontwerpkoppel;
- remkoppel;
- verplaatsing per omwenteling;
- haalbaarheid.

De figuur laat zien waarom de poelieradius een echte trade-off is.

### `gear_rows`

Voor elke kandidaat-reductie wordt bekeken:

- geschatte motorpieksnelheid;
- motorkoppel;
- haalbaarheid tegenover `motor_peak_speed_rpm`.

De huidige reductie is:

```text
25:1
```

Die ligt goed voor snelheid en koppel bij de huidige poelieradius.

## 7. Codecel 12 - Motorclass en kostmarge

Deze cel kiest een motorklasse uit eenvoudige arrays:

```python
motor_class_power_w
motor_class_output_torque_nm
motor_class_brake_torque_nm
motor_class_has_encoder
motor_class_has_brake
```

Een motorklasse is geschikt als:

```python
vermogen >= recommended_motor_power_peak
koppel >= recommended_output_torque
rem >= recommended_brake_torque
encoder aanwezig
rem aanwezig
```

Voor de huidige case:

```text
selected_motor_class_name = "500 W 48V BLDC + rem + gearbox"
```

De marges zijn:

```text
vermogen: 2.28
koppel:   2.31
rem:      1.73
```

Die marges maken de keuze verdedigbaar voor buitengebruik en onzekerheden.

## 8. Codecel 14 - Precisieanalyse

Deze cel vertaalt encoderresolutie naar lineaire schuiverresolutie.

De verplaatsing per uitgangsomwenteling is:

```python
line_per_output_rev = 2*pi*r
```

De resolutie met motorencoder en reductie is:

```python
linear_resolution_motor_encoder =
    line_per_output_rev / (gear_ratio * encoder_counts_effective)
```

De outputencoderresolutie zonder reductievoordeel is:

```python
linear_resolution_output_encoder =
    line_per_output_rev / encoder_counts_effective
```

Daarnaast wordt riem-/aandrijfelasticiteit geschat:

```python
elastic_deflection = F / effective_drive_stiffness_N_per_m
```

Voor de huidige case zijn de resoluties zeer klein tegenover de toegelaten
positioneringsfout. De echte beperkingen zijn daarom vooral:

- backlash;
- riemrek;
- speling in koppelingen;
- torsie van de as;
- schuivergeleiding.

## 9. Codecel 16 - Tijdschaalanalyse

Deze cel onderzoekt wat er gebeurt als hetzelfde traject sneller of trager
wordt uitgevoerd.

```python
time_scale_factors = [0.60, 0.80, 1.00, 1.25, 1.50, 2.00]
```

De kracht wordt opgesplitst in:

```python
non_inertial_component
inertia_component
```

Daarna wordt inertie geschaald als:

```python
F_inertia_scaled = F_inertia / lambda**2
```

Waarom `1/lambda^2`?

- als de beweging `lambda` keer trager wordt, wordt snelheid ongeveer
  `1/lambda`;
- versnelling wordt ongeveer `1/lambda^2`;
- inertiekracht is `m a`.

De niet-inertiele component wordt niet op dezelfde manier kleiner, omdat
zwaartekracht en Coulombwrijving vooral van positie en richting afhangen.

De figuur is nuttig voor de vraag over trajectkeuze:

- sneller bewegen verhoogt vermogen en inertie;
- trager bewegen verlaagt piekvermogen;
- zwaartekrachtarbeid blijft grotendeels aanwezig.

## 10. Codecel 18 - Krachtgeneratie en symmetrische uitvoering

Deze cel vergelijkt het effect van meerdere identieke mechanismen:

```python
counts = np.unique([1, 2, mechanism_count])
```

Voor elk aantal wordt geschat:

- totale lijnkracht;
- ontwerpkoppel;
- piekvermogen;
- lokale zijreactie;
- ruw buigend moment.

Belangrijke interpretatie:

- meer mechanismen betekent meer totale verticale aandrijfkracht;
- de motor moet dan meer leveren;
- maar lokale structurele lasten kunnen beter verdeeld worden;
- een symmetrische of parallelle uitvoering is mechanisch gunstiger voor de
  brede overdekking.

Voor de finale case met twee mechanismen moet de motor beide schuivers samen
aandrijven.

## 11. Codecel 20 - Gemeenschappelijke aandrijfas en synchronisatie

Deze cel controleert of een gemeenschappelijke as realistisch is.

De aslengte wordt voor de overdekking genomen als:

```python
shaft_span = canopy_width
```

De as draagt het ontwerpkoppel:

```python
shaft_design_torque = T_pulley_design
```

Voor elke asoptie berekent de code:

- buiten- en binnendiameter;
- polair traagheidsmoment `J`;
- massa per meter;
- schuifspanning;
- torsiehoek;
- OK/niet OK.

De gebruikte theorie:

```text
tau = T r / J
theta = T L / (G J)
```

Voor de huidige case selecteert de notebook:

```text
tube_40x5
```

met:

```text
torsiehoek = 0.55 deg
schuifspanning = 2.52 MPa
```

De code berekent ook een eerste orde lagerbelasting:

```python
shaft_radial_bearing_load_est =
    belt_bearing_load_factor * local_line_force_design
```

Dit is nog geen lagercatalogusberekening. Het is wel genoeg om te tonen dat de
as niet zomaar verwaarloosd is.

## 12. Codecel 22 - Frequentie-inhoud

Deze cel bevat de functie:

```python
def one_sided_spectrum(y, dt):
```

Die functie:

- haalt het gemiddelde uit het signaal;
- gebruikt een Hann-window;
- berekent de FFT;
- geeft een eenzijdig amplitude-spectrum terug.

De code past dit toe op:

```python
dds_drive
F_s_drive
```

Daarna zoekt ze dominante krachtfrequenties en vergelijkt die met een geschatte
eerste buigfrequentie van de voorbalk.

De eerste buigfrequentie wordt benaderd als een eenvoudig opgelegde balk:

```text
f1 ~ sqrt(E I / (m L^4))
```

Voor de huidige case:

```text
aandrijfreferentie = 0.042 Hz
voorbalkfrequentie = 15.6 Hz
scheidingsfactor = 375
```

Dit is geen volledige modale analyse, maar wel een nuttige check dat de rustige
aandrijving niet in de buurt van de eerste voorbalkmodus zit.

## 13. Codecel 24 - Aandrijfschets, kost en energie

Deze cel berekent de energie per open+sluitcyclus:

```python
energy_per_open_close_mech =
    energy_positive_open_one + energy_positive_close_one

energy_per_open_close_total =
    mechanism_count * energy_per_open_close_mech / drive_efficiency
```

Voor de huidige case:

```text
energy_per_open_close_total = 476.5 J
```

Daarna wordt de jaarenergie:

```python
annual_motion_energy_kwh =
    energy_per_open_close_total
    * open_close_cycles_per_day
    * operating_days_per_year
    / 3.6e6
```

En de energiekost:

```python
annual_energy_cost_eur =
    (annual_motion_energy_kwh + annual_standby_energy_kwh)
    * electricity_price_eur_per_kwh
```

Voor de huidige aannames:

```text
annual_energy_cost_eur = 0.01 euro/jaar
```

De kostorde wordt:

```python
installed_drive_cost_min_eur =
    motor_cost_min_eur + drive_hardware_cost_min_eur

installed_drive_cost_max_eur =
    motor_cost_max_eur + drive_hardware_cost_max_eur
```

Voor de overdekking:

```text
1759-2699 euro
```

Daarnaast raamt dezelfde cel de constructie buiten de motor:

```text
constructie buiten motor = 5272-10239 euro
motor + constructie samen = 6631-11938 euro
```

Die constructiekost bevat voorbalk, doek, masten/steunen, stangen, schuiver,
geleiding, gemeenschappelijke as, riemen, poelies, lagers, trekveren, beugels,
ankers en corrosiebescherming. Het blijft een materiaal- en componentraming,
geen volledige plaatsings- of keuringsprijs.

De figuur in deze cel is een schema van:

- een motor;
- een gemeenschappelijke as;
- twee lokale poelies;
- twee riem/kabellussen;
- twee schuivers;
- aparte geleiding.

## 14. Codecel 26 - Resultaten opslaan

Deze cel kiest eerst de bestandsnaam:

```python
if load_case == "trekveren":
    output_filename = "notebook4_aandrijving_trekveren_results.npz"
elif load_case == "overdekking":
    output_filename = "notebook4_aandrijving_overdekking_results.npz"
elif load_case == "overdekking_trekveren":
    output_filename = "notebook4_aandrijving_overdekking_trekveren_results.npz"
...
```

Daarna schrijft `np.savez(...)` alle belangrijke resultaten weg.

Belangrijke outputgroepen:

### Loadcase en traject

```python
load_case
source_results3_filename
mechanism_count
has_bidirectional
motor_design_direction
t
s
ds
dds
```

### Aandrijving

```python
drive_pulley_radius
drive_efficiency
selected_gear_ratio
line_force_design
T_pulley_design
P_peak_motor_input
I_peak_est
```

### Houdkracht en rem

```python
F_hold_drive_curve
T_hold_output_curve
T_hold_brake_design_curve
recommended_brake_torque
```

### Motorclass

```python
selected_motor_class_name
selected_motor_power_class
selected_output_torque_class
selected_brake_torque_class
motor_power_margin
motor_torque_margin
motor_brake_margin
```

### As

```python
shaft_selected_name
shaft_selected_twist_deg
shaft_selected_tau
shaft_radial_bearing_load_est
```

### Frequentie

```python
freq_acc
amp_acc
freq_force
amp_force
beam_first_bending_freq
frequency_separation_ratio
```

### Kost en energie

```python
energy_per_open_close_total
annual_motion_energy_kwh
annual_energy_cost_eur
installed_drive_cost_min_eur
installed_drive_cost_max_eur
```

Deze output maakt het mogelijk om later tabellen, samenvattingen of
antwoordteksten automatisch op actuele resultaten te baseren.

## 15. Welke parameters zou je typisch aanpassen?

De belangrijkste parameters om mee te spelen zijn:

| Parameter | Effect |
|---|---|
| `load_case` | kiest welke Notebook-3-belasting wordt gebruikt |
| `mechanism_count_override` | test meer/minder parallel aangedreven mechanismen |
| `preferred_pulley_radius` | verandert koppel/snelheid/precisie trade-off |
| `drive_efficiency` | maakt aandrijving optimistischer of conservatiever |
| `drive_safety_factor` | verhoogt/verlaagt ontwerpkracht |
| `brake_safety_factor` | verhoogt/verlaagt remvereiste |
| `gear_ratio_candidates` | bepaalt welke reducties mogelijk zijn |
| `position_tolerance_mm` | verandert precisie-eis |
| `effective_drive_stiffness_N_per_m` | schat riem/as/constructiestijfheid |
| `shaft_options` | test andere asprofielen |
| `open_close_cycles_per_day` | verandert energie- en kostinschatting |

Parameters die je niet zomaar los mag aanpassen:

- `mechanism_count` zonder ook de overdekkingsmassa in Notebook 3 te bekijken;
- `drive_efficiency` extreem hoog zonder reden;
- `line_force_floor` te laag voor buitengebruik;
- rem uitschakelen terwijl tussenstanden nodig zijn.

## 16. Wat moet je mondeling kunnen uitleggen?

De belangrijkste punten zijn:

1. Notebook 4 leest de belasting uit Notebook 3 en berekent geen nieuwe
   kinematica.
2. De zwaarste richting wordt automatisch gekozen als open/sluitdata aanwezig
   is.
3. De motor wordt gekozen op kracht, koppel, vermogen, rem en precisie.
4. De riem/kabel draagt alleen verticale aandrijfkracht.
5. De schuivergeleiding en mast dragen horizontale reacties.
6. Een rem is nodig om tussenstanden veilig vast te houden.
7. Een enkele motor met gemeenschappelijke as is haalbaar als de as hoog of
   achter de constructie ligt en torsie beperkt blijft.
8. De energiekost is klein; robuustheid en aankoopkost zijn bepalend.
9. De frequentiecheck is een eerste orde controle, geen volledige modale
   analyse.
10. De motorclass is een richtklasse, geen definitieve productselectie.

## 17. Huidige eindconclusie uit de code

Voor de finale loadcase `overdekking_trekveren` komt Notebook 4 uit op:

```text
2 mechanismen
poelieradius: 25 mm
reductie: 25:1
ontwerplijnkracht: 797.9 N
ontwerpkoppel uitgang: 21.7 Nm
vereiste rem: 11.57 Nm
motorklasse: 500 W 48V BLDC + rem + gearbox
gemeenschappelijke as: tube_40x5
energie per open+sluitcyclus: 476.5 J
concrete motor/reductor-kit: 1359-1699 euro
totale aandrijfhardware: 1759-2699 euro
constructie buiten motor: 5272-10239 euro
motor + constructie samen: 6631-11938 euro
```

Dat is een verdedigbaar voorontwerp voor een trage, buiten geplaatste
zonwerende overdekking met twee synchroon aangedreven schuivers.
