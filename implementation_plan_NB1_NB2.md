# Implementatieplan — Notebook 1 - Examen & Notebook 2

> **Status:** ter review door projectgenoot — nog geen code aangepast.
>
> **Scope:** Alleen Notebook 1 - Examen.ipynb en Notebook 2.ipynb worden gewijzigd.
> Notebook 3.ipynb is reeds bijgewerkt en wordt hier niet aangeraakt.
> De sluitingsvergelijkingen, de kinematische solver en de inverse-dynamica-kern
> worden **niet** gewijzigd.

---

## Overzicht van alle wijzigingen

| # | Notebook | Cel-ID | Type | Prioriteit | Samenvatting |
|---|---|---|---|---|---|
| 1 | NB1 | `orig-020` | Bug-fix | **Hoog** | Verwijder dubbele `Kdot` berekening |
| 2 | NB1 | `orig-024` | Bug-fix | **Hoog** | Verwijder dubbele `Kddot` berekening |
| 3 | NB1 | `orig-010` | Verbetering | Medium | Precompute `dt` in branch-tracking |
| 4 | NB1 | nieuw na `orig-012` | Toevoeging | Medium | Multi-gewricht validatie E, G, J (log-schaal) |
| 5 | NB1 | `orig-020` | Toevoeging | Medium | Plot alle 6 hoeksnelheden |
| 6 | NB1 | `orig-024` | Toevoeging | Medium | Plot alle 6 hoekversnellingen |
| 7 | NB1 | nieuw vóór `exam-parameters` | Toevoeging | Laag | Topologie-tabel links en gewrichten |
| 8 | NB2 | `dyn-title` | Toevoeging | **Hoog** | Expliciete waarschuwing inertie-only |
| 9 | NB2 | nieuw na `dyn-power-code` | Toevoeging | Medium | Arbeids-surplus A_max (Les 4) |
| 10 | NB2 | nieuw na `dyn-check-code` | Toevoeging | Medium | Energiebalans-validatie `dEkin/dt = P_act` |
| 11 | NB2 | `dyn-save-code` | Toevoeging | Laag | A_max en energiebalans opslaan in `.npz` |

---

## Notebook 1 — Gedetailleerde wijzigingen

---

### Wijziging 1 — cel `orig-020`: Verwijder dubbele K-snelheidsberekening

**Probleem:**
Cel `orig-018` berekent `Kdot_x`, `Kdot_y`, `Kdot_norm` via de correcte
kettingpropagatie (vB → vD → vF → vI → vK). Cel `orig-020` herberekent
exact dezelfde drie variabelen via een projectieformule en overschrijft ze
**stilzwijgend**. De plots in `orig-020` tonen daardoor niet de gevalideerde
kettingwaarden maar de projectiewaarden.

**Wat wordt verwijderd** (bovenaan `orig-020`):
```python
Kdot_x = np.zeros_like(t)
Kdot_y = np.zeros_like(t)
Kdot_norm = np.zeros_like(t)

for k in range(len(t)):
    r8tot = r8a + r8b
    Kdot_x[k] = (
        -r3a * np.sin(theta3[k]) * dtheta3[k]
        -r5a * np.sin(theta5[k]) * dtheta5[k]
        -r6  * np.sin(theta6[k]) * dtheta6[k]
        -r8tot * np.sin(theta8[k]) * dtheta8[k]
    )
    Kdot_y[k] = (
        -ds[k]
        + r3a * np.cos(theta3[k]) * dtheta3[k]
        ...
    )
    Kdot_norm[k] = np.sqrt(Kdot_x[k]**2 + Kdot_y[k]**2)
```

**Wat blijft:** De vier subplots (snelheid vs. tijd en vs. s) — ongewijzigd,
maar nu gevoed vanuit de correcte waarden uit `orig-018`.

---

### Wijziging 2 — cel `orig-024`: Verwijder dubbele K-versnellingsberekening

**Probleem:** Identiek aan wijziging 1 maar voor versnelling.
Cel `orig-022` berekent `Kddot_x/y/norm` correct via de volledige keten
met de versnelling van punt I. Cel `orig-024` herberekent ze via een
projectieformule en overschrijft ze.

**Wat wordt verwijderd** (bovenaan `orig-024`):
```python
Kddot_x = np.zeros_like(t)
Kddot_y = np.zeros_like(t)
Kddot_norm = np.zeros_like(t)

for k in range(len(t)):
    r8tot = r8a + r8b
    Kddot_x[k] = (
        -r3a * np.cos(theta3[k]) * dtheta3[k]**2
        -r3a * np.sin(theta3[k]) * ddtheta3[k]
        ...
    )
    ...
```

**Wat blijft:** De vier subplots (versnelling vs. tijd en vs. s) — ongewijzigd.

---

### Wijziging 3 — cel `orig-010`: Precompute `dt` in branch-tracking

**Probleem:** In de `kinematics_umbrella`-functie staat bij elke tijdstap:
```python
theta3_init = theta3[k] + (t[1] - t[0]) * dtheta3[k]
```
De uitdrukking `t[1] - t[0]` is een constante maar wordt 241× opnieuw
berekend.

**Wat verandert:** Één regel toevoegen vóór de `for`-lus:
```python
dt = t[1] - t[0]  # uniforme tijdstap voor branch-tracking voorspelling
```
En de vijf `theta_X_init`-regels aanpassen van `(t[1] - t[0])` naar `dt`.

Geen functionele impact — puur leesbaarheid en lichte efficiëntiewinst.

---

### Wijziging 4 — nieuw cel-paar na `orig-012`: Multi-gewricht positievalidatie

**Probleem:** De huidige positievalidatie bestaat uit één scalaire
uitvoer: `max closure residual = 5.463e-13`. Dit controleert of de
sluitingsvergelijkingen voldaan zijn, maar niet of de positiepropagatie-code
correct werkt. Een teken-/indexfout in de puntberekening zou hier onzichtbaar
blijven.

**Benodigde data:** Alle benodigde variabelen zijn al beschikbaar na `orig-012`:

| Gewricht | Keten 1 | Keten 2 |
|---|---|---|
| E | `E3_pos` (via B, theta3) | `E4_pos` (via C, theta4) |
| G | `G5_pos` (via D, theta5) | `G7_pos` (via H, theta7) |
| J | `J8_pos` (via I, theta8) | `J7_pos` (via H, theta7) |

**Nieuwe cel 1 (markdown):**
Korte uitleg van de twee-keten-validatiemethode.

**Nieuwe cel 2 (code):**
```python
fig_val, axes = plt.subplots(3, 2, figsize=(14, 9), constrained_layout=True)
fig_val.suptitle("Positievalidatie: absolute en relatieve fout per gewricht")

joints_val = [
    ("E", E3_pos, E4_pos),
    ("G", G5_pos, G7_pos),
    ("J", J8_pos, J7_pos),
]

for i, (name, pos1, pos2) in enumerate(joints_val):
    abs_err = np.abs(pos1 - pos2)
    denom   = np.where(np.abs(pos1) > 1e-12, pos1, 1e-12)
    rel_err = np.abs(abs_err / denom)

    axes[i, 0].plot(t, abs_err[:, 0], label=f'{name}_x')
    axes[i, 0].plot(t, abs_err[:, 1], label=f'{name}_y')
    axes[i, 0].set_yscale("log"); axes[i, 0].grid(True); axes[i, 0].legend()
    axes[i, 0].set_ylabel("Abs. fout [m]")

    axes[i, 1].plot(t, rel_err[:, 0], label=f'{name}_x')
    axes[i, 1].plot(t, rel_err[:, 1], label=f'{name}_y')
    axes[i, 1].set_yscale("log"); axes[i, 1].grid(True); axes[i, 1].legend()
    axes[i, 1].set_ylabel("Rel. fout [-]")

for ax in axes[-1, :]:
    ax.set_xlabel("t [s]")
plt.show()
```

---

### Wijziging 5 — cel `orig-020`: Plot alle 6 hoeksnelheden

**Probleem:** De snelheidsplots tonen alleen `dtheta8` en snelheidscomponenten
van punt K. De overige vijf hoeksnelheden zijn nooit gevisualiseerd.

**Wat wordt toegevoegd:** Aan het einde van `orig-020`, na de bestaande
2×2 subplots, een nieuw 2×3 subplot:

```python
fig_omega, axes_omega = plt.subplots(2, 3, figsize=(13, 7), constrained_layout=True)
fig_omega.suptitle("Hoeksnelheden van alle links (als functie van de tijd)")

dtheta_all = [dtheta3, dtheta4, dtheta5, dtheta6, dtheta7, dtheta8]
labels_omega = [r'$\omega_3$', r'$\omega_4$', r'$\omega_5$',
                r'$\omega_6$', r'$\omega_7$', r'$\omega_8$']

for ax, dth, lbl in zip(axes_omega.flat, dtheta_all, labels_omega):
    ax.plot(t, dth, label=lbl)
    ax.set_ylabel(f'{lbl} [rad/s]')
    ax.set_xlabel('t [s]')
    ax.grid(True); ax.legend()

plt.show()
```

---

### Wijziging 6 — cel `orig-024`: Plot alle 6 hoekversnellingen

**Probleem:** Zelfde als wijziging 5 maar voor versnelling.

**Wat wordt toegevoegd:** Analoge 2×3 subplot aan het einde van `orig-024`:

```python
fig_alpha, axes_alpha = plt.subplots(2, 3, figsize=(13, 7), constrained_layout=True)
fig_alpha.suptitle("Hoekversnellingen van alle links (als functie van de tijd)")

ddtheta_all = [ddtheta3, ddtheta4, ddtheta5, ddtheta6, ddtheta7, ddtheta8]
labels_alpha = [r'$\alpha_3$', r'$\alpha_4$', r'$\alpha_5$',
                r'$\alpha_6$', r'$\alpha_7$', r'$\alpha_8$']

for ax, ddth, lbl in zip(axes_alpha.flat, ddtheta_all, labels_alpha):
    ax.plot(t, ddth, label=lbl, color='darkred')
    ax.set_ylabel(f'{lbl} [rad/s²]')
    ax.set_xlabel('t [s]')
    ax.grid(True); ax.legend()

plt.show()
```

---

### Wijziging 7 — nieuw cel vóór `exam-parameters`: Topologie-tabel

**Probleem:** De notebook springt direct naar parameters zonder ooit te
beschrijven welke link welke gewrichten verbindt.

**Nieuwe markdown-cel:**

```markdown
## Topologie van het paraplu-mechanisme

Het mechanisme bestaat uit 8 links (inclusief de vaste grond) en
10 gewrichten (B, C, D, E, F, G, H, I, J, K).

| Link | Van → Naar | Type | Rol |
|------|-----------|------|-----|
| 1 | vaste mast | grond | referentie |
| 2 | (schuiver langs mast) | prismatisch | invoer s(t) |
| 3 | B → D → E | ternair | koppelt schuiver aan link 4 |
| 4 | C → E → H | ternair | ruggengraat, vast scharnier in C |
| 5 | D → F → G | ternair | eerste radiale uitbreiding |
| 6 | F → I | binair | koppelstang |
| 7 | G → H → J | ternair | synchronisatiestang |
| 8 | I → J → K | ternair | buitenste rib — K is outputpunt |

C is het enige vaste rotatiepunt. Punt K is de tip van het parapludoek.
```

---

## Notebook 2 — Gedetailleerde wijzigingen

---

### Wijziging 8 — cel `dyn-title`: Expliciete waarschuwing inertie-only

**Probleem:** De huidige tekst vermeldt wel dat zwaartekracht weggelaten is,
maar pas na een lange alinea en zonder visuele nadruk. Een lezer die de
uitvoer ziet (`max |F_drive_s| = 144 N`) kan dat abusievelijk als realistische
ontwerpbelasting interpreteren, terwijl de werkelijke kracht ~20× groter is.

**Wat wordt toegevoegd:** Aan het einde van `dyn-title`:

```markdown
> **⚠ LET OP — inertie-only analyse**
>
> Zwaartekracht, wrijving en statische belastingen zijn **niet** opgenomen
> in Notebook 2. De berekende krachten zijn uitsluitend inertieel en
> onderschatten de werkelijke actuatorbelasting met een factor ~20.
>
> | Bijdrage | Max. waarde | Aandeel |
> |---|---|---|
> | Inertie (dit notebook) | ~3–144 N* | ~4% |
> | Zwaartekracht (NB3) | ~63 N | ~83% |
> | Wrijving (NB3) | ~10–40 N | ~13% |
>
> \* afhankelijk van massa-instellingen en simulatiesnelheid.
>
> **Gebruik Notebook 3** voor de volledige belastingsanalyse.
```

---

### Wijziging 9 — nieuw cel-paar na `dyn-power-code`: A_max (Les 4)

**Probleem:** Notebook 2 berekent geen arbeids-surplus, terwijl dit de
Les 4-methode is voor motordimensionering van periodieke mechanismen.

**Nieuwe cel 1 (markdown):**

```markdown
## Arbeids-surplus en motordimensionering (Les 4) — inertie-only

Les 4 introduceert de arbeids-surplus $A_\text{max}$ als energiebuffer-eis:

$$A_\text{max} = \max_t\!\int_0^t(P-\bar P)\,dt - \min_t\!\int_0^t(P-\bar P)\,dt$$

**Kanttekening (Les 4):** $A_\text{max}$ is geen inherente eigenschap van het
mechanisme — de waarde verandert met de bewegingswet. Hier is dat de
halve-cosinus van Notebook 1.

Deze waarden zijn inertie-only. Zie Notebook 3 voor $A_\text{max}$ met
zwaartekracht en wrijving (de realistische ontwerpwaarden).
```

**Nieuwe cel 2 (code):**
```python
P_load_nb2  = F_drive_s * ds
P_avg_nb2   = P_load_nb2.mean()
P_peak_nb2  = np.abs(P_load_nb2).max()
P_rms_nb2   = np.sqrt(np.mean(P_load_nb2**2))
F_peak_nb2  = np.max(np.abs(F_drive_s[active_motion_mask]))
F_rms_nb2   = np.sqrt(np.mean(F_drive_s[active_motion_mask]**2))

A_theta_nb2 = np.cumsum((P_load_nb2 - P_avg_nb2) * Ts)
A_max_nb2   = A_theta_nb2.max() - A_theta_nb2.min()

T_motor_peak_les4 = actuator_safety_factor * F_peak_nb2 * screw_lead / (2*np.pi*actuator_efficiency)
T_motor_rms_les4  = actuator_safety_factor * F_rms_nb2  * screw_lead / (2*np.pi*actuator_efficiency)
P_motor_rated_req = 1.5 * P_rms_nb2
P_motor_peak_req  = 1.3 * P_peak_nb2

print("=" * 58)
print("  ARBEIDS-SURPLUS EN MOTORDIMENSIONERING (Les 4) — inertie-only")
print("=" * 58)
print(f"P_avg  = {P_avg_nb2:.3f} W   P_peak = {P_peak_nb2:.3f} W   P_rms = {P_rms_nb2:.3f} W")
print(f"F_peak = {F_peak_nb2:.3f} N   F_rms  = {F_rms_nb2:.3f} N")
print(f"A_max  = {A_max_nb2:.4f} J  (energiebuffer, inertie-only)")
print()
print(f"Aanbevolen motor (inertie-only — zie NB3 voor werkelijke waarden):")
print(f"  Nominaal vermogen  >= {P_motor_rated_req:.2f} W  (1.5 x P_rms)")
print(f"  Piekvermogen       >= {P_motor_peak_req:.2f} W  (1.3 x P_peak)")
print(f"  Piek motorkoppel   >= {T_motor_peak_les4:.4f} Nm")
print(f"  RMS  motorkoppel   >= {T_motor_rms_les4:.4f} Nm")

# Les 4-stijl plot
fig_amax, ax_amax = plt.subplots(1, 2, figsize=(12, 4), constrained_layout=True)
fig_amax.suptitle("Arbeids-surplus (Les 4) — inertie-only")

ax_amax[0].plot(t, P_load_nb2)
ax_amax[0].axhline(P_avg_nb2, color='gray', ls='--', lw=1, label=f'P_gem = {P_avg_nb2:.3f} W')
ax_amax[0].set_title("Ingangsvermogen"); ax_amax[0].set_xlabel("t [s]")
ax_amax[0].set_ylabel("P [W]"); ax_amax[0].grid(True); ax_amax[0].legend()

ax_amax[1].plot(t, A_theta_nb2, 'k', lw=1.5)
ax_amax[1].axhline(A_theta_nb2.max(), color='red',  ls='--', lw=1, label=f'max = {A_theta_nb2.max():+.3f} J')
ax_amax[1].axhline(A_theta_nb2.min(), color='blue', ls='--', lw=1, label=f'min = {A_theta_nb2.min():+.3f} J')
ax_amax[1].fill_between(t, A_theta_nb2, A_theta_nb2.min(), alpha=0.12, color='red')
ax_amax[1].set_title(f"Arbeids-surplus $A_{{max}}$ = {A_max_nb2:.3f} J")
ax_amax[1].set_xlabel("t [s]"); ax_amax[1].set_ylabel("∫(P−P̄)dt [J]")
ax_amax[1].grid(True); ax_amax[1].legend()
plt.show()
```

---

### Wijziging 10 — nieuw cel-paar na `dyn-check-code`: Energiebalans-validatie

**Probleem:** De huidige controle verifieert de kracht- en momentbalans.
Een energiebalanscontrole is een globale, onafhankelijke validatie die het
hele Newton-Euler stelsel in één getal samenvat.

Voor inertie-only (geen zwaartekracht, geen wrijving) geldt:

$$P_\text{actuator}(t) = \frac{d}{dt}\sum_i\left(\tfrac{1}{2}m_i v_{cg,i}^2 + \tfrac{1}{2}J_i\omega_i^2\right)$$

**Nieuwe cel 1 (markdown):** Uitleg van de stelling + notitie dat bij
inertie-only de gravitatie- en wrijvingstermen wegvallen.

**Nieuwe cel 2 (code):**
```python
# Kinetische energie (translatie + rotatie) van alle links + puntmassa K
Ekin_nb2 = np.zeros(n_steps)
for link_id in range(2, 9):
    Ekin_nb2 += (0.5 * masses[link_id] * np.sum(cg_vel[link_id]**2, axis=1)
                 + 0.5 * inertias[link_id] * alpha[link_id]**2)
Ekin_nb2 += 0.5 * payload_mass_K * np.sum(K_vel**2, axis=1)

# Numerieke afgeleide via centrale differenties
dEkin_dt_nb2 = np.gradient(Ekin_nb2, t)

# Energiebalans: P_act - dEkin/dt  (moet ≈ 0)
energy_balance_error_nb2 = F_drive_s * ds - dEkin_dt_nb2

denom_nb2 = np.where(np.abs(F_drive_s * ds) > 1e-4, F_drive_s * ds, np.nan)
energy_balance_rel_nb2 = np.abs(energy_balance_error_nb2 / denom_nb2)

fig_eb2, ax_eb2 = plt.subplots(1, 2, figsize=(12, 4), constrained_layout=True)
fig_eb2.suptitle("Energiebalans-validatie — inertie-only (NB2)")

ax_eb2[0].plot(t, np.abs(energy_balance_error_nb2))
ax_eb2[0].set_title("|P_act − dEkin/dt|"); ax_eb2[0].set_yscale("log")
ax_eb2[0].set_xlabel("t [s]"); ax_eb2[0].set_ylabel("fout [W]"); ax_eb2[0].grid(True)

ax_eb2[1].plot(t, energy_balance_rel_nb2)
ax_eb2[1].set_title("Relatieve fout"); ax_eb2[1].set_yscale("log")
ax_eb2[1].set_xlabel("t [s]"); ax_eb2[1].set_ylabel("fout [-]"); ax_eb2[1].grid(True)

plt.show()
print(f"Energiebalans max abs. fout : {np.nanmax(np.abs(energy_balance_error_nb2)):.3e} W")
print(f"Energiebalans max rel. fout : {np.nanmax(energy_balance_rel_nb2):.3e}")
print("(Restfout door numerieke differentiatie van Ekin; bij analytische afleiding: machineprecisie.)")
```

---

### Wijziging 11 — cel `dyn-save-code`: Nieuwe keys toevoegen aan `.npz`

**Wat verandert:** In de `np.savez`-aanroep de volgende keys toevoegen:

```python
# Arbeids-surplus (Les 4)
A_max_nb2=A_max_nb2,
A_theta_nb2=A_theta_nb2,
P_avg_nb2=P_avg_nb2,
P_peak_nb2=P_peak_nb2,
P_rms_nb2=P_rms_nb2,
F_peak_nb2=F_peak_nb2,
F_rms_nb2=F_rms_nb2,
T_motor_peak_les4=T_motor_peak_les4,
T_motor_rms_les4=T_motor_rms_les4,
# Energiebalans-validatie
energy_balance_error_nb2=energy_balance_error_nb2,
Ekin_nb2=Ekin_nb2,
```

En de samenvattingsprint uitbreiden met:
```python
print(f"arbeids-surplus A_max (Les 4) : {A_max_nb2:.4f} J")
print(f"max energiebalans-fout        : {np.nanmax(np.abs(energy_balance_error_nb2)):.3e} W")
```

---

## Wat er NIET verandert

- Sluitingsvergelijkingen (`loop_closure_eqs`)
- Kinematische solver (`kinematics_umbrella`)
- Snelheids- en versnellingsmatrix A
- Massaparameters in NB2 (`line_mass_density = 0.55`, `slider_mass = 1.50`, `payload_mass_K = 3.00`)
- De `.npz`-koppeling NB1 → NB2 → NB3 (alleen NB2's `.npz` krijgt extra keys)
- Notebook 3 (reeds bijgewerkt, niet aanraken)

---

## Noot: discrepantie F_drive NB2 vs. NB3

NB2 print `max |F_drive_s| = 144 N`, NB3 print `max |F_s| inertie = 3.17 N`
voor dezelfde massa-instellingen. Dit is **geen bug** maar het gevolg van
verschillende simulatieconfiguraties:

- NB2-run: `t_end = 12 s`, `t_move = 8 s` → matige versnellingen
- NB3-run (opgeslagen `.npz`): `simulatieduur = 34.05 s` → tragere beweging,
  kleinere versnellingen, kleinere inertiële krachten

Beide berekeningen zijn correct voor hun eigen kinematica-invoer.
De `.npz` die NB3 inlaadt, was van een andere NB1-run dan de `.npz`
die NB2 heeft gebruikt voor zijn eigen uitvoer. Zorg dat NB1, NB2 en NB3
altijd na elkaar gedraaid worden (Run All in volgorde).
