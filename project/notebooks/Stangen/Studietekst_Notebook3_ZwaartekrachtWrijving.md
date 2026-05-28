# Hoofdstuk 3 – Inverse dynamica met zwaartekracht en wrijving

Deze studietekst werkt de uitbreidingen van Notebook 3 volledig uit. Notebook 2 had
een puur inertiële analyse. Hier voegen we zwaartekracht, schuiverwrijving,
scharnierwrijving en statische houdanalyse toe. Ten slotte behandelen we de
arbeid-energiestelling als validatie en de arbeids-surplus als motordimensioneringsgrootheid
(les 4). Het doel is elk nieuw element te begrijpen: waar het vandaan komt, hoe
het in het stelsel terechtkomt, en wat het mechanisch betekent.

**Workflow:** Notebook 3 laadt zowel `notebook1_kinematica_results.npz` (kinematica uit
Notebook 1) als `notebook2_dynamica_results.npz` (massa's, inerties en inertiële resultaten
uit Notebook 2). Daardoor blijven alle drie de notebooks consistent met dezelfde geometrie,
hetzelfde schuivertraject en dezelfde dynamische parameters. De vereiste uitvoeringsvolgorde
is: eerst Notebook 1, dan Notebook 2, dan Notebook 3.

---

## 1. Wat verandert er ten opzichte van Notebook 2?

In Notebook 2 was het rechterlid van het Newton-Euler stelsel puur inertieel:

$$\mathbf{b} = \begin{pmatrix} m \ddot{x}_{cg} \\ m \ddot{y}_{cg} \\ J_{cg}\alpha \end{pmatrix}$$

De matrix $A$ en de onbekenden $\mathbf{w}$ bleven ongewijzigd. In Notebook 3 worden
**bekende externe krachten** (zwaartekracht, wrijving) naar de rechterkant gebracht.
De linkermatrix $A$ verandert niet — de onbekenden blijven dezelfde 21 krachten.

De structuur is altijd:

$$A \mathbf{w} = \mathbf{b}_{\text{inertie}} + \mathbf{b}_{\text{zwaartekracht}} + \mathbf{b}_{\text{wrijving}}$$

> ⚠️ **Belangrijk voor mondeling:** Bekende krachten gaan naar rechts. Onbekende
> krachten blijven links. Dit is de kern van de inverse-dynamica aanpak bij meerdere
> belastingen.

De drie bijdragen kunnen apart berekend worden en opgeteld:

$$F_{s,\text{totaal}} = F_{s,\text{inertie}} + F_{s,\text{zwaartekracht}} + F_{s,\text{wrijving}}$$

Dat maakt validatie eenvoudig: je kunt elke term apart controleren.

---

## 2. Zwaartekracht in de inverse dynamica

### 2.1 Zwaartekracht als bekende kracht

De zwaartekracht werkt op elk lichaam in de negatieve $y$-richting:

$$\vec{F}_{g,i} = m_i \vec{g} = m_i \begin{pmatrix} 0 \\ -g \end{pmatrix}$$

met $g = 9{,}81\,\text{m/s}^2$.

Ze is **volledig bekend** — geen onbekenden. Daarom gaat ze naar de **rechterkant**
van de Newton-Euler vergelijkingen.

### 2.2 Hoe verschuift de zwaartekracht naar het rechterlid?

Begin van de krachtenbalans voor link $i$:

$$\sum F_{\text{onbekend}} + \vec{F}_{g,i} = m_i \vec{a}_{cg,i}$$

Verschuif de bekende zwaartekracht naar rechts:

$$\sum F_{\text{onbekend}} = m_i \vec{a}_{cg,i} - \vec{F}_{g,i} = m_i(\vec{a}_{cg,i} - \vec{g})$$

**Dit zegt ons:** bij het samenstellen van het rechterlid vervang je $\vec{a}_{cg}$
effectief door $\vec{a}_{cg} - \vec{g}$. Dat is ook de reden waarom de puntmassa K
in de code behandeld wordt als:

$$\vec{F}_{\text{eff},K} = m_K (\vec{a}_K - \vec{g})$$

> ⚠️ **Valkuil:** Zwaartekracht werkt ook op de puntmassa K — niet alleen op de
> links. Vergeet je dit, dan ontbreekt de gewichtslast van het doek/randprofiel
> volledig in de krachtenbalans van link 8.

### 2.3 Effect op het momentenevenwicht

De zwaartekracht aangrijpend in het zwaartepunt van link $i$ geeft ook een bijdrage
aan het moment om het zwaartepunt. Maar omdat de kracht precies in het zwaartepunt
aangrijpt, is de arm nul:

$$M_{g,i} = \vec{r}_{cg \to cg} \times \vec{F}_{g,i} = \vec{0} \times \vec{F}_{g,i} = 0$$

Zwaartekracht draagt dus **niet** bij aan het momentenevenwicht om het zwaartepunt.
Maar ze beïnvloedt wél de gewrichtskrachten, die op hun beurt momenten geven in de
vergelijkingen van aangrenzende links.

---

## 3. Wrijvingsmodellen

### 3.1 Algemeen principe: Coulombwrijving

> ⚠️ **Belangrijk voor mondeling:** Coulombwrijving is het standaard wrijvingsmodel uit
> de lessen. Formule, richting, normale kracht — dit moet je perfect kennen.

Coulombwrijving heeft een vaste grootte evenredig met de normaaldruk, en een richting
tegensteld aan de beweging:

$$F_{\text{wrijving}} = -\mu \cdot N \cdot \text{sgn}(\dot{q})$$

waarbij:
- $\mu$ de wrijvingscoëfficiënt is
- $N$ de normaaldruk (loodrecht op het glijvlak)
- $\dot{q}$ de relatieve snelheid in het glijvlak

In de code wordt $\text{sgn}(\dot{q})$ benaderd door $\tanh(\dot{q}/v_\epsilon)$ voor
numerieke smoothheid. Dit vermijdt een discontinuïteit bij $\dot{q} = 0$.

### 3.2 Schuiverwrijving

De schuiver beweegt langs de mast. De mast legt een horizontale normaaldruk op de
schuiver: dat is de geleidingsreactie $R_{Ax}$ (de horizontale scharnierreactie van
punt A op de schuiver). De wrijvingskracht werkt verticaal, in de $s$-richting:

$$F_{\text{wrijving,slider}} = -\mu_{\text{slider}} \cdot |R_{Ax}| \cdot \tanh\!\left(\frac{\dot{s}}{v_\epsilon}\right)$$

**Tekenconventie:** de wrijving werkt altijd tegen de beweging van de schuiver.
Als de schuiver opent ($\dot{s} < 0$), is $\tanh(\dot{s}/v_\epsilon) < 0$, dus
$F_{\text{wrijving,slider}} > 0$ — de wrijving werkt in de richting van toenemende $s$
(remmend).

De schuiverwrijving gaat als bekende kracht naar het rechterlid van de vergelijkingen
van link 2 (de schuiver):

$$b_{2,y} = m_2 \ddot{y}_{cg,2} - m_2 g - (-F_{\text{wrijving,slider}})$$

> ⚠️ **Valkuil:** De schuiverwrijving is een kracht in de verticale richting op de
> schuiver. Ze heeft echter geen loodrechte component, dus ze draagt niet bij aan de
> normale kracht $R_{Ax}$ zelf — dat zou een cirkelredenering zijn.

### 3.3 Scharnierwrijving (buswrijving)

Elk roterend scharnier heeft een penradius $r_{\text{pin}}$ en een wrijvingscoëfficiënt
$\mu_{\text{pin}}$. De normale kracht in het scharnier is de resultante van de
gewrichtskracht $\|\vec{R}_{\text{joint}}\|$. Het wrijvingsmoment op de pen is:

$$M_{\text{pin}} = \mu_{\text{pin}} \cdot r_{\text{pin}} \cdot \|\vec{R}_{\text{joint}}\| \cdot \tanh\!\left(\frac{\omega_{\text{rel}}}{\omega_\epsilon}\right)$$

waarbij $\omega_{\text{rel}} = \dot\theta_a - \dot\theta_b$ het verschil in hoeksnelheid
is tussen de twee verbonden links $a$ en $b$.

**Tekenconventie voor het moment op de twee links:**
- Link $a$ krijgt: $M_{\text{op a}} = -M_{\text{pin}}$ (remmend op $a$)
- Link $b$ krijgt: $M_{\text{op b}} = +M_{\text{pin}}$ (remmend op $b$)

Dit is consistent met actie-reactie: de twee momenten hebben tegengesteld teken en
werken in de richting die de relatieve rotatie remt.

> ⚠️ **Belangrijk voor mondeling:** Scharnierwrijving geeft een moment op de twee
> verbonden links, geen kracht. Het moment staat in het derde rij-equivalent van de
> Newton-Euler vergelijkingen (de momentenbalans $\sum M_{cg} = J\alpha$).

### 3.4 Iteratieve oplossing (vaste-puntsiteratie)

**Het probleem:** de wrijvingskrachten hangen af van $R_{Ax}$ en
$\|\vec{R}_{\text{joint}}\|$, die zelf onbekenden zijn. Het stelsel is daardoor
**impliciet** in de wrijving.

**De oplossing:** vaste-puntsiteratie (fixed-point iteration):

1. Los het stelsel op zonder wrijving → eerste schatting $\mathbf{w}^{(0)}$
2. Bereken wrijvingskrachten op basis van $\mathbf{w}^{(0)}$
3. Los het stelsel opnieuw op met die wrijving → $\mathbf{w}^{(1)}$
4. Herhaal tot $\|\mathbf{w}^{(k+1)} - \mathbf{w}^{(k)}\| < \text{tolerantie}$

De code gebruikt maximaal 20 iteraties met tolerantie $10^{-8}$. Typisch convergeert
het in 8–12 iteraties.

**Waarom convergeert dit?** De wrijvingskrachten zijn proportioneel aan de normale
krachten, die op hun beurt slechts zwak afhangen van de wrijving. De
koppeling is een kleine perturbatie, geen dominante term — de iteratie is stabiel.

> ⚠️ **Belangrijk voor mondeling:** Dit is een voorbeeld van een impliciet
> wrijvingsmodel. Het alternatief is een direct wrijvingsmodel waarbij de
> wrijvingskracht een expliciete functie is van de toestand — dat vereist geen
> iteratie maar is soms minder nauwkeurig.

---

## 4. Validatie: de arbeid-energiestelling

### 4.1 De stelling

> ⚠️ **Belangrijk voor mondeling:** De arbeid-energiestelling is een fundamentele wet
> die als globale controle dient. Ken de formulering, de drie termen, en wat elk
> term fysisch betekent.

De arbeid-energiestelling stelt dat het vermogen van de actuator gelijk is aan de
som van drie bijdragen:

$$\boxed{P_{\text{act}}(t) = \frac{dE_{\text{kin}}}{dt} + P_{\text{zwaartekracht}}(t) + P_{\text{wrijving}}(t)}$$

Elk term apart:

**1. Actuatorvermogen:**
$$P_{\text{act}} = F_s(t) \cdot \dot{s}(t)$$

**2. Tijdsafgeleide van de kinetische energie:**
$$\frac{dE_{\text{kin}}}{dt}, \qquad E_{\text{kin}} = \sum_{i=2}^{8}\left(\frac{1}{2}m_i\|\vec{v}_{cg,i}\|^2 + \frac{1}{2}J_{cg,i}\omega_i^2\right) + \frac{1}{2}m_K\|\vec{v}_K\|^2$$

**3. Vermogen geleverd tegen de zwaartekracht:**
$$P_{\text{zwaartekracht}} = \sum_{i=2}^{8} m_i g\, \dot{y}_{cg,i} + m_K g\, \dot{y}_K$$

*Uitleg:* als een massa opstijgt ($\dot{y} > 0$), stijgt de potentiële energie en moet
de actuator meer vermogen leveren. Als een massa daalt ($\dot{y} < 0$), helpt de
zwaartekracht en is minder actuatorvermogen nodig. Dit is equivalent aan
$P_{zwk} = +dU_{pot}/dt$.

**4. Gedissipeerd wrijvingsvermogen:**
$$P_{\text{wrijving}} = -F_{\text{wrijving,slider}} \cdot \dot{s} + \sum_j |M_{\text{pin},j} \cdot \omega_{\text{rel},j}|$$

Dit is altijd positief (dissipatief): wrijving vreet vermogen.

### 4.2 Als controle

Als de inverse-dynamica-implementatie correct is, moeten linker- en rechterlid op elk
tijdstip overeenkomen. De code berekent de absolute fout:

$$\epsilon = \left|P_{\text{act}} - \left(\frac{dE_{\text{kin}}}{dt} + P_{\text{zwaartekracht}} + P_{\text{wrijving}}\right)\right|$$

Een fout in de orde van machineprecisie ($<10^{-8}\,\text{W}$) bevestigt dat de
implementatie thermodynamisch consistent is. Kleine fouten kunnen ook optreden door
de numerieke differentiatie van $E_{\text{kin}}$ via centrale differenties.

---

## 5. Krachtdecompositie

### 5.1 Drie afzonderlijke berekeningen

De notebook löst het stelsel driemaal op:

| Geval | Zwaartekracht | Wrijving |
|-------|--------------|---------|
| `case_inertia_check` | nee | nee |
| `case_gravity` | ja | nee |
| `case_total` | ja | ja |

Dit laat toe elke bijdrage te isoleren:

$$F_{s,\text{zwaartekracht}} = F_{s,\text{gravity}} - F_{s,\text{inertie}}$$
$$F_{s,\text{wrijving}} = F_{s,\text{totaal}} - F_{s,\text{gravity}}$$

### 5.2 Grootte-ordes voor dit mechanisme

De notebook rapporteert de volgende typische waarden:

| Bijdrage | Maximale $|F_s|$ | Aandeel |
|----------|-----------------|---------|
| Inertieel (Notebook 2) | ~3 N | klein |
| Zwaartekracht | ~63 N | dominant |
| Schuiverwrijving | zie Notebook 3-samenvatting | groot |
| Scharnierwrijving | ~2 N | klein |

**Conclusie:** dit is een **quasi-statisch** mechanisme. De zwaartekracht domineert;
de inertietermijnen zijn ~20× kleiner. Dit heeft een directe ontwerpsconsequentie:
lagere massa of betere geleiding heeft veel meer effect dan het verlengen van de
bewegingstijd.

> ⚠️ **Belangrijk voor mondeling:** "Welke bijdrage domineert in de aandrijfkracht?"
> → zwaartekracht en schuiverwrijving, daarna inertie.
> Dit is mechanisme-specifiek en verschilt bij hogere snelheden of zwaardere links.

---

## 6. Statische houdanalyse

### 6.1 Definitie

In de stilstand ($\dot{s} = 0$, $\ddot{s} = 0$, $\dot\theta_i = 0$, $\ddot\theta_i = 0$)
zijn alle inertiële termen nul. Er blijft alleen zwaartekracht over. De vereiste
**houdkracht** $F_{\text{hold}}$ is de actuatorkracht die nodig is om het mechanisme
op positie $s$ te houden.

Technisch: dezelfde 21×21 inverse-dynamica oplossing, maar met `static=True` in de
code, zodat alle acceleraties op nul gezet worden:

$$A \mathbf{w}_{\text{statisch}} = \mathbf{b}_{\text{zwaartekracht alleen}}$$

### 6.2 Formule voor de houdkracht

$$F_{\text{hold}}(s) = -F_{act,y,\text{statisch}}(s)$$

De houdkracht varieert met de schuiverpositie $s$ — in bepaalde standen draagt het
mechanisme zijn eigen gewicht effectiever dan in andere.

### 6.3 Wrijving als vergrendeling: waarom het niet veilig is

Theoretisch levert schuiverwrijving een capaciteit:

$$F_{\text{wrijving,statisch,max}} = \mu_{\text{statisch}} \cdot |R_{Ax}|$$

Over het volledige schuiverbereik geldt dat:

$$F_{\text{wrijving,statisch,max}} < |F_{\text{hold}}|$$

**De geleiding kan de positie niet op eigen wrijving vasthouden.** Dit is een
ontwerpconsequentie: je hebt een extra vergrendeling nodig.

> ⚠️ **Belangrijk voor mondeling:** "Kan wrijving de paraplu op positie houden?"
> → nee, niet betrouwbaar. Wrijving varieert door slijtage, vuil en smering. Voor
> veilige houdkracht is een zelfremmende spindel of mechanische vergrendeling nodig.

### 6.4 Zelfremmende schroefspindel

Een schroefspindel is **zelfremmend** wanneer de spoedhelling $\lambda$ kleiner is
dan de wrijvingshoek:

$$\tan\lambda < \mu_{\text{pin}} \qquad \Leftrightarrow \qquad \frac{p}{\pi d} < \mu$$

met $p$ de spoed (m/omwenteling) en $d$ de nominale diameter van de spindel.

**Intuïtie:** als de helling te steil is ($\tan\lambda > \mu$), kan de axiale last de
spindel doen terugdraaien. Bij een flauwe helling ($\tan\lambda < \mu$) blokkeert de
wrijving elke terugbeweging — de spindel is mechanisch zelfremmend, geen extra rem nodig.

Voor $p = 5\,\text{mm}$ en een typische diameter van $20\,\text{mm}$:
$$\tan\lambda = \frac{5\,\text{mm}}{\pi \cdot 20\,\text{mm}} \approx 0{,}08$$

Bij $\mu_{\text{pin}} > 0{,}08$ is de spindel zelfremmend.

> ⚠️ **Valkuil:** een zelfremmende spindel verliest zijn zelfremming als de spindel
> smeert (lagere $\mu$) of als er trillingsbelasting is. Controleer altijd onder de
> meest ongunstige smeringsconditie.

---

## 7. Vermogen en actuatorbelasting

### 7.1 Actuatorvermogen (volledige belasting)

$$P_{\text{act}}(t) = F_{s,\text{totaal}}(t) \cdot \dot{s}(t)$$

Nu zijn zwaartekracht en wrijving inbegrepen. Het profiel is asymmetrisch:
- **Openen** ($\dot{s} < 0$, massa's dalen): zwaartekracht **helpt**, actuator levert
  minder of negatief vermogen.
- **Sluiten** ($\dot{s} > 0$, massa's stijgen): zwaartekracht **werkt tegen**, actuator
  levert meer vermogen.

Dit is het **regeneratief karakter** van het mechanisme: bij openen kan de actuator
energie terugwinnen (negatief vermogen = remmen), bij sluiten moet hij meer leveren.

### 7.2 Motordimensionering — drie criteria

Voor een definitieve motorselektie worden drie criteria gehanteerd:

**1. Continu nominaal vermogen** (thermisch):
$$P_{\text{nominaal}} \geq 1{,}5 \times P_{\text{rms}}$$

Het motorwikkeling mag niet oververhitten bij langdurig gebruik.

**2. Piek vermogencapaciteit** (kortdurend):
$$P_{\text{piek,motor}} \geq 1{,}3 \times P_{\text{piek}}$$

De motor moet korte piekvraag kunnen leveren zonder in beveiliging te gaan.

**3. Koppelcriteria via schroefspindel:**
$$T_{\text{piek}} = k_s \cdot \frac{F_{\text{piek}} \cdot p}{2\pi\eta}, \qquad T_{\text{rms}} = k_s \cdot \frac{F_{\text{rms}} \cdot p}{2\pi\eta}$$

met veiligheidsfactor $k_s = 1{,}5$ en rendement $\eta = 0{,}80$.

> ⚠️ **Belangrijk voor mondeling:** Ken de drie criteria en de formule voor het
> motorkoppel via de spindel. Leg ook uit waarom je de veiligheidsfactor toepast op
> het koppel (onzekerheden in wrijving, slijtage, externe lasten).

---

## 8. Arbeids-surplus en motordimensionering (Les 4)

### 8.1 Waarom arbeids-surplus?

Voor een periodiek werkende machine (pers, motor, compressor) is de belasting cyclisch.
In sommige fases levert de belasting meer vermogen dan gemiddeld; in andere fases
minder. Het verschil moet gebufferd worden — door een vliegwiel of de motorregelaar.

De **arbeids-surplus** $A_{\max}$ kwantificeert die benodigde energiebuffer.

### 8.2 Definitie (Les 4 — Bruyninckx)

$$\boxed{A_{\max} = \max_t \int_0^t \bigl(P(t') - \bar{P}\bigr)\,dt' \;-\; \min_t \int_0^t \bigl(P(t') - \bar{P}\bigr)\,dt'}$$

Waarbij:
- $P(t) = F_s(t)\,\dot{s}(t)$ het ogenblikkelijke actuatorvermogen is
- $\bar{P}$ het tijdsgemiddelde over de volledige beweging is
- De integraal de **gecumuleerde afwijking** van het gemiddelde voorstelt

**Intuïtie:** stel je voor dat je het cumulatieve energieverschil $(P - \bar{P})$
bijhoudt als een "saldo". Als het mechanisme tijdelijk meer vraagt dan gemiddeld,
stijgt het saldo. Als het minder vraagt, daalt het. $A_{\max}$ is het totale
schommelbereik van dat saldo — de benodigde energiebuffer.

### 8.3 Berekening

De discrete versie:

$$A_\theta(t_k) = \sum_{j=0}^{k} (P(t_j) - \bar{P}) \cdot \Delta t$$

$$A_{\max} = \max_k A_\theta(t_k) - \min_k A_\theta(t_k)$$

### 8.4 Kanttekeningen

- $A_{\max}$ is **niet** een inherente eigenschap van het mechanisme alleen; ze hangt
  af van de gekozen bewegingswet én de aanwezigheid van dissipatie (wrijving).
- Voor een positioneermechanisme (niet-cyclisch) heeft $A_{\max}$ een andere
  interpretatie dan voor een continu draaiende machine: hier meet het de energiebuffer
  die de regelaar of condensator moet kunnen leveren.

> ⚠️ **Valkuil:** Verwar $A_{\max}$ niet met de totale netto energie $E_{\text{net}} =
> \int_0^T P\,dt$. Die geeft de totale energiekost over de slag. $A_{\max}$ geeft de
> maximale piekbehoefte ten opzichte van het gemiddelde — relevant voor de
> dimensionering van de energieopslag of de keuze van het vermogensprofiel.

---

## 9. Framebelasting versus onbalans

### 9.1 Het verschil

- **Onbalans (Notebook 2):** puur inertieel — de resultante die het mechanisme op het
  frame overdraagt door versnellingen. Klein bij trage bewegingen.
- **Framebelasting (Notebook 3):** totale reactie op het frame inclusief zwaartekracht.
  Bijna statisch gewicht plus inertieel supplement.

### 9.2 Interne krachtsoverdracht

De steunreacties in punt A (schuiver) en punt C (framebevestiging van link 4) zijn elk
duidelijk groter dan de netto externe framekracht.

**Hoe is dit mogelijk?** Het mechanisme heeft meerdere lussen. Interne gewrichtskrachten
kunnen groot zijn terwijl ze bij optelling grotendeels wegvallen. De netto
buitenbelasting is de resterende niet-gecompenseerde kracht, maar de individuele
scharnierkrachten kunnen veel groter zijn door de inwendige krachtsoverdracht.

> ⚠️ **Belangrijk voor mondeling:** Lagerdimensionering moet gebaseerd zijn op de
> **lokale steunreacties**, niet op de netto framekracht. Een kleine netto resultante
> garandeert geen kleine lagerbelasting.

---

## 10. Numerieke parameters en hun invloed

| Parameter | Waarde | Effect |
|-----------|--------|--------|
| $\mu_{\text{slider}}$ | 0,05 | Coulombwrijving schuiver (kinematisch) |
| $\mu_{\text{slider,statisch}}$ | 0,08 | Statische wrijving schuiver |
| $\mu_{\text{pin}}$ | 0,05 | Scharnierwrijving per scharnier |
| $r_{\text{pin}}$ | 6 mm | Penradius — schaal van het wrijvingsmoment |
| $v_\epsilon$ | 1 mm/s | Smoothing-drempel schuiverwrijving |
| $\omega_\epsilon$ | 1 mrad/s | Smoothing-drempel scharnierwrijving |
| $\eta$ | 0,80 | Rendement schroefspindel |
| $k_s$ | 1,50 | Veiligheidsfactor koppel |

De $\tanh$-regularisering vermijdt een numerieke discontinuïteit bij nulsnelheid.
De waarden $v_\epsilon$ en $\omega_\epsilon$ zijn klein genoeg om de fysische wrijving
goed te benaderen maar groot genoeg voor numerieke stabiliteit.

---

## 11. Examensamenvatting

| Onderwerp | Sleutelformule | Wat te kennen |
|-----------|---------------|---------------|
| Zwaartekracht in Newton-Euler | $\mathbf{b} \mathrel{+}= m_i \vec{g}$ (rechterlid) | Bekende krachten gaan naar rechts |
| Coulombwrijving schuiver | $F = -\mu N \tanh(\dot{s}/v_\epsilon)$ | Richting, normale kracht $= R_{Ax}$ |
| Scharnierwrijving | $M = \mu_{\text{pin}} r_{\text{pin}} N \tanh(\omega_{\text{rel}}/\omega_\epsilon)$ | Actie-reactie op twee links |
| Iteratieve oplossing | vaste-puntsiteratie | Waarom itereren? Convergentie? |
| Arbeid-energiestelling | $P_{\text{act}} = \dot{E}_{\text{kin}} + P_{\text{zwk}} + P_{\text{wrijving}}$ | Drie termen, validatierol |
| Statische houdanalyse | $\vec{a} = 0$, $\vec{\alpha} = 0$ | Wanneer wrijving tekortschiet |
| Zelfremmende spindel | $\tan\lambda < \mu \;\Leftrightarrow\; p/(\pi d) < \mu$ | Ontwerp-eis voor vergrendeling |
| Arbeids-surplus (Les 4) | $A_{\max} = \max A_\theta - \min A_\theta$ | Definitie, berekening, interpretatie |
| Motorkoppel | $T = Fp/(2\pi\eta)$ | Met veiligheidsfactor |

**Wat te kennen voor het mondeling:**

1. **Bekende krachten:** zwaartekracht gaat naar het rechterlid van Newton-Euler —
   hoe en waarom.
2. **Coulombwrijving:** formule, richting, normale kracht — voor schuiver en scharnier.
3. **Iteratieve wrijving:** waarom nodig, hoe het convergeert.
4. **Arbeid-energiestelling:** drie termen en hun fysische betekenis als validatie.
5. **Statische houdanalyse:** methode ($\vec{a} = 0$), en de ontwerpconsequentie.
6. **Zelfremmende spindel:** conditie $\tan\lambda < \mu$, consequenties bij slijtage.
7. **Arbeids-surplus $A_{\max}$:** definitie, discrete berekening, gebruik bij
   motorselektie.
8. **Grootte-ordes:** inertie $\ll$ wrijving $<$ zwaartekracht voor dit mechanisme.
9. **Lagerdimensionering:** altijd op lokale steunreacties, niet op netto framekracht.

> ⚠️ **Centrale examenvraag:** "Hoe voeg je zwaartekracht toe aan de inverse dynamica?"
> → zwaartekracht is een bekende kracht, gaat naar het rechterlid als
> $-m\vec{g}$ (verschuiving van links naar rechts), zodat de matrix $A$ en de
> onbekenden ongewijzigd blijven.

> ⚠️ **Centrale examenvraag:** "Wat is de arbeids-surplus en waarvoor dient ze?"
> → maat voor de benodigde energiebuffer bij een motor; gelijk aan het verschil
> tussen maximum en minimum van de gecumuleerde afwijking $\int(P-\bar{P})dt$.
