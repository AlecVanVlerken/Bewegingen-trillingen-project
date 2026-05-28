# Hoofdstuk 2 – Inverse dynamica, onbalans en actuatoranalyse

Deze studietekst werkt de dynamische analyse van Notebook 2 volledig uit. Ze bouwt
verder op de kinematische resultaten van Notebook 1 en legt uit hoe je van beweging
naar krachten gaat via de Newton-Euler methode. Het doel is diep begrip: niet alleen
weten hoe het stelsel opgebouwd wordt, maar ook waarom elke term er staat en wat het
resultaat mechanisch betekent.

---

## 1. Inverse dynamica: wat is het en waarvoor dient het?

### 1.1 Het onderscheid met voorwaartse dynamica

Er zijn twee richtingen in de dynamica van mechanismen:

- **Voorwaartse dynamica:** gegeven de krachten, bereken de beweging.
- **Inverse dynamica:** gegeven de beweging, bereken de krachten.

Inverse dynamica is de aanpak die hier gebruikt wordt. We kennen de kinematica al
volledig uit Notebook 1 — posities, snelheden, versnellingen van elk punt op elk
tijdstip. Wat we willen weten is: welke krachten moeten er op het mechanisme inwerken
om precies die beweging te veroorzaken?

Dat geeft antwoord op praktische ontwerpen:
- Hoe groot moet de actuatorkracht zijn?
- Welke krachten draagt het frame?
- Hoe zwaar moet het scharniergewricht bij E zijn?

> ⚠️ **Belangrijk voor mondeling:** "Wat is inverse dynamica?" — de beweging is
> gegeven, de krachten zijn onbekend. Je lost een lineair stelsel op per tijdstap.

### 1.2 Wat bevat Notebook 2 niet?

Zwaartekracht, windbelasting en wrijving zijn in deze versie niet opgenomen. Dat
betekent dat alle berekende krachten **puur inertieel** zijn — ze ontstaan uitsluitend
door de versnelling van massa's. In de stilstandsfase zijn alle krachten dan ook nul
(want $\dot{s} = 0$ en $\ddot{s} = 0$ en geen externe krachten).

Dit is een bewuste keuze: de inertiële analyse is de basis. Zwaartekracht en wind
komen er later als extra termen bovenop.

---

## 2. Dynamische parameters

### 2.1 Massa's

Elke link krijgt een massa toegewezen op basis van een lijnmassadichtheid:

$$m_i = \rho_l \cdot L_i$$

met $\rho_l = 0{,}55\,\text{kg/m}$ (robuuste aluminium stangen) en
$L_i = r_{ia} + r_{ib}$ de totale lengte van link $i$.

| Link | Lengte $L_i$ (m) | Massa $m_i$ (kg) |
|------|-----------------|-----------------|
| 2 (schuiver) | — | 1,50 (apart gekozen) |
| 3 | $r_{3a}+r_{3b}$ | $\rho_l \cdot L_3$ |
| 4 | $r_{4a}+r_{4b}$ | $\rho_l \cdot L_4$ |
| 5 | $r_{5a}+r_{5b}$ | $\rho_l \cdot L_5$ |
| 6 | $r_6$ | $\rho_l \cdot L_6$ |
| 7 | $r_{7a}+r_{7b}$ | $\rho_l \cdot L_7$ |
| 8 | $r_{8a}+r_{8b}$ | $\rho_l \cdot L_8$ |

Bovendien zit er in punt K een **equivalente puntmassa** $m_K = 3{,}00\,\text{kg}$,
die doek, randprofiel en ribconstructie vertegenwoordigt. Deze massa wordt **niet**
bij link 8 opgeteld als een verdeelde massa, maar als een puntmassa in K apart
behandeld in de Newton-Euler vergelijkingen van link 8.

> ⚠️ **Valkuil:** De puntmassa in K beïnvloedt de krachtenbalans én het momentenevenwicht
> van link 8. Als je haar vergeet of verkeerd toevoegt, klopt de dynamica van link 8 niet.

### 2.2 Traagheidsmomenten

De links zijn gemodelleerd als uniforme stangen. Het traagheidsmoment om het
zwaartepunt is:

$$J_{cg,i} = \frac{1}{12} m_i L_i^2$$

**Herkomst:** Voor een uniforme staaf met massa $m$ en lengte $L$ is het
traagheidsmoment om het middenpunt gelijk aan $mL^2/12$. Dit volgt uit de integraal
$\int_{-L/2}^{L/2} \rho_l x^2\,dx = \rho_l L^3/12 = mL^2/12$.

De schuiver (link 2) draait niet ($\alpha_2 = 0$, want hij beweegt alleen langs de
mast), dus zijn traagheidsmoment speelt geen rol.

---

## 3. Zwaartepuntkinematica: posities, snelheden en versnellingen

### 3.1 Waarom zwaartepuntkinematica?

Newton's tweede wet $\sum \vec{F} = m\vec{a}$ en het rotatieanalogon $\sum M_{cg} = J_{cg}\alpha$
gelden voor het **zwaartepunt** van elk lichaam. Voor een uniforme staaf is het
zwaartepunt het geometrische midden.

We kennen de kinematica van de gewrichtspunten (uit Notebook 1). Via de
**starre-lichaamformule** kunnen we daaruit elk gewenst punt berekenen.

### 3.2 De starre-lichaamformule

> ⚠️ **Belangrijk voor mondeling:** Dit is de centrale formule van de puntkinematica.
> Ze koppelt de snelheid en versnelling van een punt op een star lichaam aan die van
> een referentiepunt.

Voor een punt P op een star lichaam, met referentiepunt O:

$$\vec{v}_P = \vec{v}_O + \boldsymbol{\omega} \times \vec{r}_{OP}$$

$$\vec{a}_P = \vec{a}_O + \boldsymbol{\alpha} \times \vec{r}_{OP} - \omega^2 \vec{r}_{OP}$$

In het vlak ($\omega$ en $\alpha$ zijn scalars, $\vec{r}_{OP}$ is een 2D-vector):

$$\vec{v}_P = \vec{v}_O + \omega \cdot \text{perp}(\vec{r}_{OP})$$

$$\vec{a}_P = \vec{a}_O + \alpha \cdot \text{perp}(\vec{r}_{OP}) - \omega^2 \cdot \vec{r}_{OP}$$

met $\text{perp}(x, y) = (-y,\, x)$ (rotatie van 90° in tegenwijzerzin).

**Uitleg van de twee termen in de versnelling:**
- $\alpha \cdot \text{perp}(\vec{r}_{OP})$: tangentiële versnelling — loodrecht op $\vec{r}_{OP}$,
  evenredig met de hoekversnelling.
- $-\omega^2 \cdot \vec{r}_{OP}$: centripetale versnelling — gericht naar O, evenredig
  met het kwadraat van de hoeksnelheid.

### 3.3 Lokale vectoren voor de zwaartepunten

Voor een uniforme staaf is het zwaartepunt op positie $L/2$ langs de lokale as. De
lokale vector van het eerste gewricht naar het zwaartepunt is $[L/2, 0]$ in lokale
coördinaten.

Na rotatie over hoek $\theta$:

$$\vec{r}_{O,cg} = \frac{L}{2} \begin{pmatrix} \cos\theta \\ \sin\theta \end{pmatrix}$$

De code gebruikt een vectoriseerde implementatie `rigid_point` die voor alle tijdstappen
tegelijk berekent. Dit geldt ook voor de gewrichtspunten D, E, F, G, H, I, J, K.

> ⚠️ **Valkuil:** Link 7 zit niet symmetrisch. De segmenten $r_{7a}$ en $r_{7b}$
> zijn niet gelijk. Het zwaartepunt van link 7 zit op afstand $\frac{L_7}{2}$ van het
> eerste gewricht H, maar dan gemeten langs de as van link 7 met de correcte
> oriëntatie (lokale vector: $\frac{r_{7b} - r_{7a}}{2}$ vanuit H, wegens de spiegeling).

---

## 4. Newton-Euler vergelijkingen per link

### 4.1 De drie vergelijkingen per link

> ⚠️ **Belangrijk voor mondeling:** Drie vergelijkingen per link is de kern van de
> inverse dynamica. Je moet ze kunnen opschrijven en verklaren.

Voor elk bewegend lichaam $i$ schrijven we:

$$\sum F_{x,i} = m_i \ddot{x}_{cg,i} \tag{1}$$

$$\sum F_{y,i} = m_i \ddot{y}_{cg,i} \tag{2}$$

$$\sum M_{cg,i} = J_{cg,i} \alpha_i \tag{3}$$

De linkerkant bevat alle **onbekende** krachten die op link $i$ inwerken (gewrichtskrachten
en externe krachten). De rechterkant is volledig **bekend** — het zijn de inertiële
resultanten die we al berekend hebben in §3.

Dit maakt het stelsel lineair in de onbekenden: van niet-lineaire dynamica (met $\sin$,
$\cos$, etc. in de bewegingsvergelijkingen) zijn we overgeschakeld naar een lineair
algebraïsch stelsel per tijdstap.

### 4.2 Tekenconventie voor gewrichtskrachten

Gewrichtskracht $\vec{R}_E$ werkt op link 3 met een bepaald teken, en op link 4 met
het **tegengestelde** teken (actie = -reactie). Zo vallen interne krachten weg bij een
globale controle.

De onbekenden zijn gedefinieerd als de kracht die een aangrenzend lichaam op de link
uitoefent. Bij een gewricht tussen twee links wordt de kracht éénmaal gedefinieerd en
daarna met het juiste teken in de vergelijkingen van beide links gebruikt.

### 4.3 Het volledige stelsel: 21 vergelijkingen, 21 onbekenden

7 links × 3 vergelijkingen = 21 vergelijkingen.

De 21 onbekenden:

| Onbekende | Betekenis |
|-----------|-----------|
| $R_{Ax}$ | Horizontale geleidingsreactie op schuiver (van mast) |
| $F_{act,y}$ | Verticale actuatorkracht op schuiver |
| $M_A$ | Moment van de geleiding op schuiver |
| $C_x, C_y$ | Frame-reactie in punt C (bevestiging link 4 aan grond) |
| $B_x, B_y$ | Gewrichtskracht in B (schuiver–link 3) |
| $D_x, D_y$ | Gewrichtskracht in D (link 3–link 5) |
| $E_x, E_y$ | Gewrichtskracht in E (link 3–link 4) |
| $F_x, F_y$ | Gewrichtskracht in F (link 5–link 6) |
| $G_x, G_y$ | Gewrichtskracht in G (link 5–link 7) |
| $H_x, H_y$ | Gewrichtskracht in H (link 4–link 7) |
| $I_x, I_y$ | Gewrichtskracht in I (link 6–link 8) |
| $J_x, J_y$ | Gewrichtskracht in J (link 7–link 8) |

Totaal: 3 + 2 + 2 + 2 + 2 + 2 + 2 + 2 + 2 + 2 = **21** onbekenden. ✓

### 4.4 Het lineaire stelsel per tijdstap

Per tijdstap $k$ wordt het stelsel opgebouwd als:

$$\underbrace{A_k}_{21\times 21} \cdot \underbrace{\mathbf{w}_k}_{21\times 1} = \underbrace{\mathbf{b}_k}_{21\times 1}$$

De matrix $A_k$ bevat enkel de coëfficiënten van de onbekende krachten (de getallen
die voor $R_{Ax}$, $F_{act,y}$, ... staan). Het rechterlid $\mathbf{b}_k$ bevat de
inertiële resultanten $m_i \ddot{x}_{cg,i}$, $m_i \ddot{y}_{cg,i}$ en $J_{cg,i}\alpha_i$.

De oplossing $\mathbf{w}_k = A_k^{-1} \mathbf{b}_k$ geeft alle 21 onbekende krachten
op tijdstip $k$.

### 4.5 Hoe ziet de bijdrage van één link eruit?

Neem link 3 als voorbeeld. Op link 3 werken:
- In B: de kracht $(-B_x, -B_y)$ van de schuiver op link 3 (actie-reactie)
- In D: de kracht $(D_x, D_y)$ van link 5 op link 3
- In E: de kracht $(E_x, E_y)$ van link 4 op link 3

De drie Newton-Euler vergelijkingen voor link 3:

$$-B_x + D_x + E_x = m_3 \ddot{x}_{cg,3}$$

$$-B_y + D_y + E_y = m_3 \ddot{y}_{cg,3}$$

$$(-B_y)(x_{cg3,B}) - (-B_x)(y_{cg3,B}) + (D_y)(x_{cg3,D}) - \ldots = J_{cg,3} \alpha_3$$

waarbij $x_{cg3,P}$ de $x$-component is van de vector van het zwaartepunt van link 3
naar punt P.

**Momenten:** Het 2D-moment van kracht $\vec{F}$ op punt P om punt Q is:

$$M = (\vec{r}_{QP}) \times \vec{F} = (x_P - x_Q) F_y - (y_P - y_Q) F_x$$

> ⚠️ **Valkuil:** Het moment wordt genomen om het **zwaartepunt** van de link, niet
> om een willekeurig punt. Dit is vereist door de Euler-draaiingsvergelijking
> $\sum M_{cg} = J_{cg} \alpha$.

### 4.6 Puntmassa K in link 8

Link 8 heeft een extra puntmassa $m_K$ in punt K. Deze massa levert een extra bijdrage
aan de krachten- en momentenbalans van link 8:

$$\sum F_{x,8} = m_8 \ddot{x}_{cg,8} + m_K \ddot{x}_K$$

$$\sum F_{y,8} = m_8 \ddot{y}_{cg,8} + m_K \ddot{y}_K$$

$$\sum M_{cg,8} = J_{cg,8} \alpha_8 + \vec{r}_{cg8,K} \times m_K \vec{a}_K$$

Het moment door de puntmassa is het kruisproduct van de arm (van het zwaartepunt van
link 8 naar K) met de kracht ($m_K \vec{a}_K$).

---

## 5. Oplossing en kwaliteitscontrole

### 5.1 Oplossing per tijdstap

Per tijdstap wordt het stelsel opgelost met `numpy.linalg.solve`, wat intern een
LU-decompositie van $A_k$ uitvoert. De conditionering `dyn_cond[k] = cond(A_k)` wordt
bijgehouden om te controleren of het stelsel overal numeriek stabiel is.

Het residu $\|A_k \mathbf{w}_k - \mathbf{b}_k\|$ moet in de orde van machineprecisie
liggen. Een groter residu wijst op een numeriek probleem.

### 5.2 Globale controle: alle links samen

Als je alle Newton-Euler vergelijkingen van alle links optelt, vallen alle **interne**
gewrichtskrachten weg (actie = -reactie). Alleen de **externe** krachten blijven over:

$$\vec{F}_A + \vec{F}_C = \sum_{i=2}^{8} m_i \vec{a}_{cg,i} + m_K \vec{a}_K$$

Dit is de globale krachtenbalans voor het hele systeem. Ze kost niets extra om te
berekenen en is een sterke controle: als ze niet klopt, zit er een fout in de
Newton-Euler vergelijkingen van één of meer links.

Analoog voor het moment om punt C:

$$M_A + \vec{r}_{CA} \times \vec{F}_A = \sum_{i=2}^{8} \left(J_{cg,i}\alpha_i + \vec{r}_{C,cg_i} \times m_i\vec{a}_{cg,i}\right) + \vec{r}_{CK} \times m_K \vec{a}_K$$

> ⚠️ **Belangrijk voor mondeling:** De globale controle is een elegante manier om
> de inverse dynamica te verifiëren. De formule is: som van externe krachten = totale
> inertiële resultante.

---

## 6. Aandrijfkracht en onbalans

### 6.1 Aandrijfkracht

De actuator werkt verticaal op de schuiver. De actuatorkracht in het globale
assenstelsel is $F_{act,y}$ (positief naar boven). Omdat de schuivercoördinaat $s$
**positief naar beneden** gemeten is, is de gegeneraliseerde aandrijfkracht in
$s$-richting:

$$F_s = -F_{act,y}$$

**Waarom het minteken?** Wanneer de actuator omhoog duwt ($F_{act,y} > 0$) werkt hij
in de richting van dalende $s$. De gegeneraliseerde kracht is de kracht in de richting
van toenemende $s$, dus $F_s = -F_{act,y}$.

> ⚠️ **Belangrijk voor mondeling:** De relatie tussen de actuatorkracht en de
> gegeneraliseerde kracht hangt af van de tekenconventie voor $s$. Controleer altijd
> of de richting van $s$ en de richting van de kracht overeen komen.

### 6.2 Onbalanskrachten

De **onbalanskracht** $\vec{F}_{shak}$ is de kracht die het mechanisme op zijn
ophanging (frame) uitoefent:

$$\vec{F}_{shak} = -\left(\vec{F}_A + \vec{F}_C\right)$$

**Intuïtie:** $\vec{F}_A + \vec{F}_C$ zijn de externe krachten van het frame op het
mechanisme. De reactie van het mechanisme op het frame is precies tegengesteld.

Het **onbalansmoment** om punt C:

$$M_{shak,C} = -\left(M_A + \vec{r}_{CA} \times \vec{F}_A\right)$$

> ⚠️ **Valkuil:** Onbalans heeft niets te maken met rotatiesymmetrie of draaiende
> onderdelen. In de context van stangenmechanismen is onbalans de dynamische lading op
> het frame — de kracht en het moment die het mechanisme op zijn steunstructuur overdraagt
> tijdens de beweging.

**Wanneer is onbalans groot?**
- Bij grote versnellingen van zware links
- Bij grote centripetale krachten (hoge hoeksnelheid, lange arm)
- Dicht bij singuliere configuraties (hoge hoeksnelheden in het snelheidsstelsel)

---

## 7. Actuatorvermogen en motorsturing

### 7.1 Vermogen

Het **instantane actuatorvermogen** is:

$$P_{act}(t) = F_s(t) \cdot \dot{s}(t)$$

Dit is de rate waarmee de actuator mechanisch werk levert aan het mechanisme.

- $P_{act} > 0$: actuator levert energie (aandrijven)
- $P_{act} < 0$: actuator neemt energie op (afremmen, regeneratief)

De **energie** over de volledige slag:

$$E_{act} = \int_0^T P_{act}(t)\,dt \approx \sum_k \frac{P_{act}(t_k) + P_{act}(t_{k+1})}{2} \cdot \Delta t$$

(trapeziumregel voor numerieke integratie)

### 7.2 Schroefspindel en motor

De actuator wordt aangedreven door een roterende motor via een schroefspindel. De
schroefspindel zet rotatiebeweging om in lineaire verplaatsing:

$$\dot{s} = p \cdot n \qquad \Rightarrow \qquad n = \frac{\dot{s}}{p}$$

met $p = 0{,}005\,\text{m/omw}$ de **spoed** (lineaire verplaatsing per omwenteling)
en $n$ het motortoerental in omwentelingen per seconde.

In rpm: $N = 60\,n = \frac{60\,\dot{s}}{p}$

Het **motorkoppel** volgt uit vermogensbehoud:

$$P = F_s \cdot \dot{s} = T \cdot \omega_m = T \cdot 2\pi n$$

$$\Rightarrow \quad T = \frac{F_s \cdot p}{2\pi}$$

Met rendement $\eta$ (verlies door wrijving in de spindel):

$$\boxed{T = \frac{F_s \cdot p}{2\pi \eta}}$$

> ⚠️ **Belangrijk voor mondeling:** De relatie $T = Fp/(2\pi\eta)$ is de dimensionale
> koppeling tussen lineaire actuatorkracht en motorkoppel. Ken ook de dimensies:
> $F$ in N, $p$ in m/omw, $T$ in Nm.

**Piek vs. RMS:** Voor motorsizing gebruik je:
- **Piek koppel** (× veiligheidsfactor $f_s = 1{,}5$): bepaalt de mechanische sterktelimiet
- **RMS koppel** (gedurende de beweging): bepaalt de thermische belasting van de motor

### 7.3 Positief en negatief vermogen

Wanneer $P_{act} < 0$, remt de motor af. In een regeneratief systeem kan die energie
teruggewonnen worden. In een niet-regeneratief systeem wordt ze als warmte gedissipeerd.

De code berekent apart de piekvermogen voor aandrijven en voor afremmen, en past de
efficiëntie op de juiste manier toe:
- Aandrijven: $P_{positief,peak} = f_s \cdot \max(P_{act}^+) / \eta$
- Afremmen: $P_{regen,peak} = f_s \cdot |\min(P_{act}^-)| \cdot \eta$

---

## 8. Overzicht van de volledige workflow

```
Notebook 1 (kinematica)
  │
  ├── Positieanalyse: fsolve op F(θ,s) = 0
  │       → θ3...θ8 per tijdstap
  │
  ├── Snelheidsanalyse: A·ω = B·ṡ
  │       → dθ3...dθ8 per tijdstap
  │
  └── Versnellingsanalyse: A·α = C(ω,s̈)
          → ddθ3...ddθ8 per tijdstap
          ↓
          notebook1_kinematica_results.npz
          ↓
Notebook 2 (dynamica — inertie only)
  │
  ├── Zwaartepuntkinematica: rigid_point()
  │       → a_cg,i en α_i voor alle links
  │
  ├── Inverse dynamica: A_dyn·w = b per tijdstap
  │       → 21 onbekende krachten
  │
  ├── Globale controle: F_ext = Σ m·a_cg
  │
  ├── Energiebalans-validatie: P_act = dEkin/dt
  │
  ├── Aandrijfkracht: F_s = -F_act,y
  ├── Onbalans: F_shak = -(F_A + F_C)
  └── Actuatoranalyse: P = F_s·ṡ, T = F_s·p/(2π·η)
          ↓
          notebook2_dynamica_results.npz
          ↓
Notebook 3 (dynamica — zwaartekracht + wrijving)
```

---

## 9. Examensamenvatting: overzicht van de methoden

| Stap | Vergelijking | Onbekenden | Oplossing |
|------|-------------|-----------|-----------|
| Zwaartepuntkinematica | $\vec{a}_{cg} = \vec{a}_O + \alpha\,\text{perp}(\vec{r}) - \omega^2\vec{r}$ | geen (alles bekend) | directe berekening |
| Newton-Euler per link | $\sum F = ma,\; \sum M_{cg} = J\alpha$ | gewrichtskrachten, actuatorkracht | lineair stelsel $A\mathbf{w}=\mathbf{b}$ |
| Globale controle | $\vec{F}_{ext} = \sum m\vec{a}_{cg}$ | — | verificatie |
| Onbalans | $\vec{F}_{shak} = -(\vec{F}_A + \vec{F}_C)$ | — | directe berekening |
| Actuatorvermogen | $P = F_s \dot{s}$ | — | directe berekening |
| Motorkoppel | $T = F_s p / (2\pi\eta)$ | — | directe berekening |

**Wat te kennen voor het mondeling:**

1. **Definitie inverse dynamica:** beweging gegeven, krachten onbekend.
2. **Newton-Euler vergelijkingen:** drie per link, voor het zwaartepunt, welke termen
   staan links (onbekende krachten) en rechts (inertiële resultante).
3. **Starre-lichaamformule:** $\vec{a}_P = \vec{a}_O + \alpha\,\text{perp}(\vec{r}_{OP}) - \omega^2\vec{r}_{OP}$ — twee termen, tangentieel en centripetaal.
4. **Traagheidsmoment uniforme staaf:** $J_{cg} = mL^2/12$ en waarom.
5. **Tekenconventie gewrichtskrachten:** actie–reactie, hoe het stelsel consequent
   blijft.
6. **Puntmassa in K:** hoe die in de Newton-Euler vergelijkingen van link 8 terechtkomt.
7. **Globale controle:** interne krachten vallen weg, externe krachten = totale
   inertiële resultante.
8. **Onbalans:** definitie, formule, mechanische betekenis.
9. **Motorkoppel via schroefspindel:** $T = Fp/(2\pi\eta)$, wat is $p$, wat is $\eta$.
10. **Rendement:** aandrijven (/η) vs. afremmen (×η) — de efficiëntie werkt anders.

> ⚠️ **Centrale examenvraag:** "Hoe voer je de inverse dynamica uit van een vlak
> stangenmechanisme?" — begin met de Newton-Euler vergelijkingen per link (3 per link),
> schrijf het als lineair stelsel $A\mathbf{w} = \mathbf{b}$ (rechterlid = inertiële
> resultante, onbekenden = krachten), verifieer globaal door interne krachten te laten
> wegvallen, en bereken daarna de aandrijfkracht en het actuatorvermogen.

> ⚠️ **Valkuil:** In de inverse dynamica is $A$ de coëfficiëntenmatrix van de
> **krachten**, niet dezelfde matrix A als in de kinematische snelheidsanalyse. Het
> zijn twee fundamenteel verschillende stelsels die toevallig dezelfde naam dragen in
> de code — controleer altijd welke A je bedoelt.
