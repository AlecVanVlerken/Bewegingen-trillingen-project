# Hoofdstuk 1 – Kinematische analyse van het paraplu-mechanisme

Deze studietekst werkt de kinematische analyse van het paraplu-mechanisme volledig
uit, van de eerste intuïtie tot aan de concrete matrixvergelijkingen. Ze volgt de
structuur van Notebook 1 maar legt elk stap volledig uit. Het doel is dat je na het
lezen niet alleen weet *hoe* je de analyse uitvoert, maar ook *waarom* elke stap
geldig is.

---

## 1. Wat analyseert kinematica, en waarom hier?

Kinematica beschrijft beweging zonder naar krachten te kijken. We willen weten:
hoeveel draaien de stangen? Hoe snel beweegt een punt op de rib? Hoe groot is de
versnelling halverwege een slag? Die grootheden zijn de basis voor de dynamische
analyse in Notebook 2, waar krachten en momenten wél een rol spelen.

Het paraplu-mechanisme heeft één invoer: de schuiverpositie $s(t)$ langs de mast.
Alles wat het mechanisme doet — de hoeken van alle stangen, de snelheid van de
ribpunten, de versnellingen — volgt uitsluitend uit die ene invoer. Kinematica legt
exact die relatie bloot.

> ⚠️ **Belangrijk voor mondeling:** De scheiding tussen kinematica en dynamica is
> fundamenteel. Op het mondeling wordt dikwijls gevraagd: "Welke grootheden zijn
> puur kinematisch?" — antwoord: posities, hoeken, snelheden, versnellingen. Krachten
> en momenten zijn dynamisch.

---

## 2. Beschrijving van het mechanisme

Het paraplu-mechanisme is gemodelleerd als een **vlak stangenmechanisme** met starre
links en ideale gewrichten. "Vlak" betekent dat alle bewegingen in één vlak liggen.
"Starre links" betekent dat de stangen zelf niet buigen of uitrekken.

De links zijn genummerd:

| Link | Rol |
|------|-----|
| 1 | Vaste grondlink (de mast) |
| 2 | Schuiver — beweegt langs de mast, stuurt het mechanisme |
| 3–8 | Beweeglijke stangen die de ribben vormen |

Het assenstelsel heeft zijn oorsprong in punt **C** (bevestigingspunt van de mast
aan de grond), met de $x$-as horizontaal en de $y$-as verticaal omhoog. De schuiver
zit op de mast en beweegt omlaag wanneer $s$ toeneemt.

**Punt K** is het uiteinde van de buitenste rib (link 8). Dit is het
*output*-punt van het mechanisme: we willen weten hoe K beweegt wanneer de schuiver
verschuift.

De verbinding schuiver–mast is een prismatisch gewricht (vertaalt langs één as); alle
andere verbindingen zijn roterende gewrichten (draaien om één punt).

---

## 3. Parameters en mobiliteitsanalyse

### 3.1 Geometrische parameters

De lengte van link $i$ wordt opgegeven als twee deelstukken $r_{ia}$ en $r_{ib}$. Dat
zijn **geen** twee afzonderlijke staven — het zijn twee segmenten van dezelfde starre
link, gemeten vanaf respectievelijk het eerste en het tweede gewricht. Het onderscheid
is nodig omdat tussenpunten (scharnierpunten) niet altijd aan het uiteinde van een
link zitten.

| Parameter | Waarde (m) | Betekenis |
|-----------|-----------|-----------|
| $L_1$ | 2.5 | Mastlengte |
| $r_{3a}$ | 0.507 | Link 3, segment a |
| $r_{3b}$ | 0.429 | Link 3, segment b |
| $r_{4a}$ | 0.941 | Link 4, segment a |
| $r_{4b}$ | 0.656 | Link 4, segment b |
| $r_{5a}$ | 0.251 | Link 5, segment a |
| $r_{5b}$ | 0.405 | Link 5, segment b |
| $r_6$ | 0.680 | Link 6 (volledig) |
| $r_{7a}$ | 0.429 | Link 7, segment a |
| $r_{7b}$ | 0.252 | Link 7, segment b |
| $r_{8a}$ | 0.405 | Link 8, segment a |
| $r_{8b}$ | 0.500 | Link 8, segment b |

Het schuiverbereik loopt van $s_{\text{open}} = 0.125\,\text{m}$ (open toestand) tot
$s_{\text{closed}} = 1.875\,\text{m}$ (gesloten toestand).

### 3.2 Mobiliteitsformule (Grübler–Kutzbach)

> ⚠️ **Belangrijk voor mondeling:** De mobiliteitsformule is een standaard examenvraag.
> Ken de formule, wat elke term betekent, en hoe je hem toepast.

De **mobiliteit** $M$ van een vlak mechanisme is het aantal onafhankelijke invoeren
dat je nodig hebt om het mechanisme volledig te sturen.

$$\boxed{M = 3(n - 1) - 2f_1 - f_2}$$

Waarbij:
- $n$ = aantal links (inclusief de grondlink)
- $f_1$ = aantal gewrichten met **één** vrijheidsgraad (roterende gewrichten,
  prismatische gewrichten)
- $f_2$ = aantal gewrichten met **twee** vrijheidsgraden (zeldzaam in vlakke mechanismen)

**Waarom $3(n-1)$?** Een vrij lichaam in het vlak heeft 3 vrijheidsgraden: 2
translaties en 1 rotatie. Bij $n$ links zou je $3n$ vrijheidsgraden hebben als niets
verbonden was. De grondlink is vast, dus trek je $3$ graden af: $3(n-1)$.

**Waarom $-2f_1$?** Elk gewricht met één vrijheidsgraad legt twee beperkingen op
(het raakt twee lichamen, en elk lichaam verliest twee van zijn drie vrijheidsgraden).

Voor het paraplu-mechanisme geldt $M = 1$: de schuiverpositie $s(t)$ is de enige
onafhankelijke invoer. Dat is noodzakelijk voor een goed aangedreven mechanisme.

> ⚠️ **Valkuil:** Telfouten in $n$ of $f_1$ zijn de meest voorkomende fout. Twijfel
> je, teken dan het mechanisme en nummer elk link en elk gewricht apart.

---

## 4. Positieanalyse via sluitingsvergelijkingen

### 4.1 Intuïtie: wat is een sluitingsvergelijking?

Stel je voor dat je een robotarm uittekent: je begint in punt C, gaat via een reeks
verbonden staven, en moet uiteindelijk op hetzelfde beginpunt uitkomen. Die eis — dat
de keten gesloten is — heet de **sluitingsvoorwaarde**.

Formeel: in een gesloten kinematische keten moeten de vectoren die je door opeenvolgende
links aflegt, optellen tot nul:

$$\sum_i \vec{r}_i = \vec{0}$$

Elke vectorvergelijking in het vlak levert twee scalaire vergelijkingen ($x$- en
$y$-component). Drie onafhankelijke lussen leveren dus zes vergelijkingen — precies
genoeg voor de zes onbekende hoeken $\theta_3$ t.e.m. $\theta_8$.

> ⚠️ **Belangrijk voor mondeling:** Je moet kunnen uitleggen *waarom* drie lussen
> nodig zijn. Het antwoord: het mechanisme heeft 6 onbekende hoeken, en elke vectorlus
> levert 2 vergelijkingen.

### 4.2 De drie vectorlussen

Elke link $i$ wordt beschreven als een vector met lengte $r_i$ en richting $\theta_i$:

$$\vec{r}_i = r_i \begin{pmatrix} \cos\theta_i \\ \sin\theta_i \end{pmatrix}$$

**Lus 1** verbindt de schuiver B met zichzelf via links 3 en 4:

$$F_1 = (r_{3a} + r_{3b})\cos\theta_3 - r_{4a}\cos\theta_4 = 0$$
$$F_2 = -s + (r_{3a} + r_{3b})\sin\theta_3 - r_{4a}\sin\theta_4 = 0$$

*Uitleg:* Beginnend vanuit B (de schuiver, op hoogte $-s$ onder C), gaan we langs
link 3 naar een knooppunt en dan terug via link 4. De som van de horizontale
verplaatsingen moet nul zijn, en de som van de verticale verplaatsingen ook.
De term $-s$ staat voor de verticale offset van B ten opzichte van C.

**Lus 2** verbindt een middenpunt van link 3 via links 5 en 7 terug naar link 4:

$$F_3 = -r_{3b}\cos\theta_3 + (r_{5a}+r_{5b})\cos\theta_5 + r_{7a}\cos\theta_7 - r_{4b}\cos\theta_4 = 0$$
$$F_4 = -r_{3b}\sin\theta_3 + (r_{5a}+r_{5b})\sin\theta_5 + r_{7a}\sin\theta_7 - r_{4b}\sin\theta_4 = 0$$

**Lus 3** verbindt een middenpunt van link 5 via links 6 en 8 terug naar link 7:

$$F_5 = -r_{5b}\cos\theta_5 + r_6\cos\theta_6 + r_{8a}\cos\theta_8 - (r_{7a}+r_{7b})\cos\theta_7 = 0$$
$$F_6 = -r_{5b}\sin\theta_5 + r_6\sin\theta_6 + r_{8a}\sin\theta_8 - (r_{7a}+r_{7b})\sin\theta_7 = 0$$

Samen vormen deze zes vergelijkingen het **niet-lineair stelsel**:

$$\vec{F}(\theta_3, \theta_4, \theta_5, \theta_6, \theta_7, \theta_8,\, s) = \vec{0}$$

### 4.3 Numerieke oplossing met fsolve

Stelsel $\vec{F} = \vec{0}$ is niet-lineair (er staan $\sin$ en $\cos$ in) en heeft
geen gesloten oplossing. We lossen het numeriek op met `scipy.optimize.fsolve`, een
Newton-Raphson-achtige methode.

**Stap voor stap:**

1. Kies een startpunt $\theta^{(0)}$ (de initiële gok).
2. `fsolve` itereert: $\theta^{(k+1)} = \theta^{(k)} - J^{-1} \vec{F}(\theta^{(k)})$,
   waarbij $J$ de Jacobiaan is.
3. Stop wanneer $\|\vec{F}(\theta)\| < \text{tolerantie}$.

**Kwaliteitscheck — sluitingsfout:** Na elke oplossing berekent de code:

$$\text{residual\_pos} = \|\vec{F}(\theta_{\text{opgelost}}, s)\|$$

Een sluitingsfout in de orde van machineprecisie ($\approx 10^{-14}$) bevestigt dat
de gevonden hoeken werkelijk de sluiting respecteren. Een grotere fout wijst op een
niet-geconvergeerde oplossing.

> ⚠️ **Belangrijk voor mondeling:** De sluitingsfout is de *numerieke residual van de
> sluitingsvergelijkingen* na oplossing. Klein = goed. Als de prof vraagt "hoe
> controleer je of je positie-analyse correct is?" → antwoord: bereken de residual
> $\|\vec{F}\|$.

### 4.4 Configuratiebehoud via branch tracking

Een niet-lineair stelsel kan meerdere oplossingen hebben — voor een gegeven $s$
kunnen de stangen meerdere fysisch mogelijke standen aannemen. Dit heet een
**configuratietak** (configuration branch).

Om te vermijden dat de solver bij opeenvolgende tijdstappen naar een andere
configuratietak springt, wordt de oplossing van tijdstap $k$ gebruikt als startpunt
voor tijdstap $k+1$. Bovendien wordt een lineaire predictie toegepast:

$$\theta^{(0)}_{k+1} = \theta_k + \Delta t \cdot \dot\theta_k$$

Dit geeft een betere startgok en zorgt voor een continue, fysisch correcte beweging.

> ⚠️ **Valkuil op mondeling:** Verwar geen configuratietak met een singulariteit.
> Bij een configuratietak bestaan meerdere geldige oplossingen; bij een singulariteit
> is er geen unieke oplossing.

---

## 5. Bewegingsprofiel van de schuiver

### 5.1 Waarom een glad profiel?

De schuiver kan theoretisch met constante snelheid bewegen (trapeziumvorm), maar dat
geeft oneindig grote versnellingen bij de overgang. In de praktijk — en voor correcte
dynamische analyse — moeten snelheid en versnelling nul zijn aan het begin en einde
van de slag.

Een polynomiaal profiel garandeert gladheid. De genormaliseerde tijd $\tau \in [0,1]$
beschrijft de slag van start naar einde.

### 5.2 Smooth-345 en Smooth-4567

**Smooth-345** (derde-orde polynoom van de snelheid):

$$h(\tau) = 10\tau^3 - 15\tau^4 + 6\tau^5$$

Hierbij is $h(0)=0$, $h(1)=1$, en de snelheid $\dot{h}$ en versnelling $\ddot{h}$
zijn nul aan de eindpunten.

**Smooth-4567** (vierde-orde polynoom van de snelheid, zachter):

$$h(\tau) = 35\tau^4 - 84\tau^5 + 70\tau^6 - 20\tau^7$$

Dit profiel heeft ook nulderde orde (ruck) aan de eindpunten, waardoor de beweging
zachter start en stopt.

De schuiverpositie, -snelheid en -versnelling als functie van de tijd zijn dan:

$$s(t) = s_{\text{start}} + \Delta s \cdot h(\tau), \qquad \tau = \frac{t}{t_{\text{move}}}$$
$$\dot{s}(t) = \frac{\Delta s}{t_{\text{move}}} \cdot h'(\tau)$$
$$\ddot{s}(t) = \frac{\Delta s}{t_{\text{move}}^2} \cdot h''(\tau)$$

### 5.3 Condition-aware traject

Het **condition-scurve** profiel past de snelheid aan op basis van de conditiegetal
van matrix A. In zones waar het mechanisme slecht geconditioneerd is (zie §8),
beweegt de schuiver trager. Zo worden hoge belastingen vermeden.

De snelheidsfactor per schuiverpositie wordt bepaald via:

$$f(s) = 1 - (1 - f_{\min}) \cdot \tilde{\kappa}(s)^{\gamma}$$

waarbij $\tilde{\kappa}(s)$ een genormaliseerde maat is voor $\log(\text{cond}(A))$
en $\gamma = \texttt{condition\_slow\_gain}$. De smoother `smoothstep` zorgt voor een
vloeiende overgang.

Het resultaat: meer tijd in moeilijke configuraties → kleinere hoeksnelheden → kleinere
inertiële effecten in Notebook 2.

---

## 6. Snelheidsanalyse

### 6.1 Intuïtie: van positie naar snelheid

De snelheidsanalyse volgt direct uit de positieanalyse door **differentiatie naar de
tijd**. Als de sluitingsvergelijkingen op elk moment nul zijn, dan is hun tijdsafgeleide
ook nul.

Het sleutelpunt is dat de linklengtes $r_i$ **constant** zijn — ze zijn geometrische
parameters, geen functies van de tijd. Alleen de hoeken $\theta_i$ en de schuiver
$s$ veranderen met de tijd.

### 6.2 Afleiding van het snelheidsstelsel

> ⚠️ **Belangrijk voor mondeling:** Dit is de centrale methode van de kinematische
> analyse. Je moet de stappen kunnen reproduceren en uitleggen.

**Stap 1:** Neem de tijdsafgeleide van elke sluitingsvergelijking $F_i = 0$.

Gebruik de kettingregel. Voor een term $r\cos\theta(t)$:
$$\frac{d}{dt}[r\cos\theta] = -r\sin\theta \cdot \dot\theta$$

Voor een term $r\sin\theta(t)$:
$$\frac{d}{dt}[r\sin\theta] = r\cos\theta \cdot \dot\theta$$

→ *Justificatie:* kettingregel, constante linklengtes.

**Stap 2:** Schrijf de afgeleide vergelijkingen als een lineair stelsel.

Alle termen met $\dot\theta_i$ komen aan de linkerkant; de bekende term met $\dot{s}$
gaat naar rechts:

$$A(\theta) \cdot \boldsymbol{\omega} = \mathbf{b} \cdot \dot{s}$$

waarbij $\boldsymbol{\omega} = [\dot\theta_3, \dot\theta_4, \dot\theta_5, \dot\theta_6,
\dot\theta_7, \dot\theta_8]^T$ en $\mathbf{b} = [0, 1, 0, 0, 0, 0]^T$.

**Stap 3:** Los het lineaire stelsel op.

$$\boldsymbol{\omega} = A^{-1} \mathbf{b} \cdot \dot{s}$$

→ *Dit is een lineair stelsel* (niet niet-lineair zoals bij posities), dus `np.linalg.solve`
volstaat.

### 6.3 Structuur van matrix A

De matrix A is een $6\times6$ matrix die afhangt van de actuele hoeken:

$$A = \begin{pmatrix}
-(r_{3a}+r_{3b})\sin\theta_3 & r_{4a}\sin\theta_4 & 0 & 0 & 0 & 0 \\
(r_{3a}+r_{3b})\cos\theta_3 & -r_{4a}\cos\theta_4 & 0 & 0 & 0 & 0 \\
r_{3b}\sin\theta_3 & r_{4b}\sin\theta_4 & -(r_{5a}+r_{5b})\sin\theta_5 & 0 & -r_{7a}\sin\theta_7 & 0 \\
-r_{3b}\cos\theta_3 & -r_{4b}\cos\theta_4 & (r_{5a}+r_{5b})\cos\theta_5 & 0 & r_{7a}\cos\theta_7 & 0 \\
0 & 0 & r_{5b}\sin\theta_5 & -r_6\sin\theta_6 & (r_{7a}+r_{7b})\sin\theta_7 & -r_{8a}\sin\theta_8 \\
0 & 0 & -r_{5b}\cos\theta_5 & r_6\cos\theta_6 & -(r_{7a}+r_{7b})\cos\theta_7 & r_{8a}\cos\theta_8
\end{pmatrix}$$

Het rechterlid is:

$$\mathbf{B} = \begin{pmatrix} 0 \\ \dot{s} \\ 0 \\ 0 \\ 0 \\ 0 \end{pmatrix}$$

**Hoe lees je de structuur van A?**

- Rijen 1–2 komen van lus 1 (vergelijkingen $F_1'$ en $F_2'$): alleen $\dot\theta_3$
  en $\dot\theta_4$ zijn niet nul.
- Rijen 3–4 komen van lus 2: $\dot\theta_3$, $\dot\theta_4$, $\dot\theta_5$ en
  $\dot\theta_7$.
- Rijen 5–6 komen van lus 3: $\dot\theta_5$, $\dot\theta_6$, $\dot\theta_7$ en
  $\dot\theta_8$.

De sparsity van A weerspiegelt de topologie van het mechanisme: lussen zijn
slechts met een beperkt aantal links verbonden.

> ⚠️ **Belangrijk voor mondeling:** De prof kan vragen: "Hoe bouw je de A-matrix op?"
> Antwoord: door de tijdsafgeleide van elke sluitingsvergelijking te nemen, de
> $\sin$- en $\cos$-termen te groeperen per $\dot\theta_i$, en die als kolom op te
> schrijven.

### 6.4 Snelheid van punt K via puntconstructie

De hoeksnelheden geven de interne beweging van het mechanisme. Voor de output — punt K
aan het uiteinde van link 8 — moeten we de snelheid langs de keten opbouwen.

**Methode: aaneenschakeling van snelheidsvectoren**

Voor een punt P op een roterende link geldt:
$$\vec{v}_P = \vec{v}_Q + \dot\theta \cdot \vec{e}_\perp \times \vec{r}_{QP}$$

In 2D: als $\vec{r}_{QP} = (x, y)$, dan is $\dot\theta \cdot \vec{e}_\perp \times \vec{r}_{QP}
= \dot\theta \cdot (-y, x)$.

**Stap voor stap van B naar K:**

$$\vec{v}_B = (0,\; -\dot{s})$$

$$\vec{v}_D = \vec{v}_B + \dot\theta_3 \cdot \text{perp}(\vec{r}_{BD})$$

$$\vec{v}_F = \vec{v}_D + \dot\theta_5 \cdot \text{perp}(\vec{r}_{DF})$$

$$\vec{v}_I = \vec{v}_F + \dot\theta_6 \cdot \text{perp}(\vec{r}_{FI})$$

$$\vec{v}_K = \vec{v}_I + \dot\theta_8 \cdot \text{perp}(\vec{r}_{IK})$$

met $\text{perp}(x, y) = (-y, x)$.

De norm $\|\vec{v}_K\|$ geeft de totale snelheid van punt K op elk tijdstip.

> ⚠️ **Belangrijk voor mondeling:** De puntconstructie is een elegante maar foutgevoelige
> methode. Zorg dat je de juiste hoeksnelheid ($\dot\theta_i$ van de link waarop het
> punt zit) gebruikt, en de juiste relatieve positievector (van het vorige punt tot
> het punt in kwestie).

**Kwaliteitscheck:** Het snelheidsresidu bevestigt de juistheid:

$$\|A \cdot \boldsymbol{\omega} - \mathbf{B}\| \approx 0$$

Een residu kleiner dan $10^{-10}$ is normaal bij correcte berekeningen.

---

## 7. Versnellingsanalyse

### 7.1 Intuïtie: nog een differentiatielaag

De versnellingsanalyse volgt uit de **tijdsafgeleide van het snelheidsstelsel**. We
beginnen van $A \cdot \boldsymbol{\omega} = \mathbf{B}$ en differentiëren opnieuw:

$$\frac{d}{dt}[A \cdot \boldsymbol{\omega}] = \frac{d}{dt}[\mathbf{B}]$$

$$\dot{A} \cdot \boldsymbol{\omega} + A \cdot \dot{\boldsymbol{\omega}} = \dot{\mathbf{B}}$$

$$A \cdot \boldsymbol{\alpha} = \dot{\mathbf{B}} - \dot{A} \cdot \boldsymbol{\omega}$$

Hierbij is $\boldsymbol{\alpha} = [\ddot\theta_3, \ldots, \ddot\theta_8]^T$ de vector
van hoekversnellingen.

### 7.2 Het versnellingsstelsel

> ⚠️ **Belangrijk voor mondeling:** Dezelfde matrix A wordt hergebruikt. Alleen het
> rechterlid verandert. Dit is een cruciaal inzicht.

Het stelsel heeft de vorm:

$$A(\theta) \cdot \boldsymbol{\alpha} = \mathbf{C}$$

waarbij **dezelfde matrix A** gebruikt wordt als bij de snelheidsanalyse. Het
rechterlid $\mathbf{C}$ bevat:

1. De **invoerversnelling** $\ddot{s}$ (in de tweede rij)
2. **Centripetale termen** proportioneel met $\dot\theta_i^2$ (in alle rijen)

### 7.3 Herkomst van de centripetale termen

Wanneer we de tijdsafgeleide nemen van $-r\sin\theta \cdot \dot\theta$ (een term uit
de snelheidsmatrix), krijgen we:

$$\frac{d}{dt}[-r\sin\theta \cdot \dot\theta] = -r\cos\theta \cdot \dot\theta^2 - r\sin\theta \cdot \ddot\theta$$

De term $-r\sin\theta \cdot \ddot\theta$ gaat naar links (als onderdeel van $A \cdot
\boldsymbol{\alpha}$). De term $-r\cos\theta \cdot \dot\theta^2$ blijft rechts als
onderdeel van $\mathbf{C}$.

Concreet is het rechterlid voor lus 1:

$$C_1 = (r_{3a}+r_{3b})\cos\theta_3 \cdot \dot\theta_3^2 - r_{4a}\cos\theta_4 \cdot \dot\theta_4^2$$

$$C_2 = \ddot{s} + (r_{3a}+r_{3b})\sin\theta_3 \cdot \dot\theta_3^2 - r_{4a}\sin\theta_4 \cdot \dot\theta_4^2$$

Het patroon is duidelijk: elke $\sin$-term in A geeft een $\cos\cdot\dot\theta^2$ in C,
en vice versa, met hetzelfde teken (maar dan naar rechts verplaatst).

> ⚠️ **Valkuil:** De centripetale termen staan aan de **rechterkant** van het stelsel,
> niet in A. A verandert niet.

### 7.4 Versnelling van punt K

De versnelling van K wordt opgebouwd langs dezelfde keten als de snelheid, maar bevat
nu ook centripetale bijdragen.

**Versnelling van een punt P op een roterende link vanuit Q:**

$$\vec{a}_P = \vec{a}_Q + \underbrace{\ddot\theta \cdot (-y_{QP},\, x_{QP})}_{\text{tangentieel}} - \underbrace{\dot\theta^2 \cdot (x_{QP},\, y_{QP})}_{\text{centripetaal}}$$

In componentvorm, voor punt K ten opzichte van I (via de volledige lengte $r_8 = r_{8a}+r_{8b}$):

$$\ddot{K}_x = \ddot{I}_x - \ddot\theta_8 \cdot y_{IK} - \dot\theta_8^2 \cdot x_{IK}$$
$$\ddot{K}_y = \ddot{I}_y + \ddot\theta_8 \cdot x_{IK} - \dot\theta_8^2 \cdot y_{IK}$$

**Dit zegt ons:** Zelfs als de hoekversnelling $\ddot\theta_8$ klein is, kan de
centripetale term $\dot\theta_8^2 \cdot r_8$ groot zijn bij hoge hoeksnelheid. Daarom
kan de versnelling van K piekeren in zones waar het mechanisme snel draait, ook al
is de hoekversnelling bescheiden.

> ⚠️ **Belangrijk voor mondeling:** Wanneer wordt versnelling groot? Twee gevallen:
> (1) grote hoekversnelling (tangentieel), (2) grote hoeksnelheid met grote radius
> (centripetaal). Beide bijdragen zijn aanwezig in $\|\ddot{\vec{K}}\|$.

**Alternatieve berekening (directe som langs keten):**

De code bevat ook een directe formulering:

$$\ddot{K}_x = -r_{3a}\cos\theta_3\,\dot\theta_3^2 - r_{3a}\sin\theta_3\,\ddot\theta_3
- r_{5a}\cos\theta_5\,\dot\theta_5^2 - r_{5a}\sin\theta_5\,\ddot\theta_5
- r_6\cos\theta_6\,\dot\theta_6^2 - r_6\sin\theta_6\,\ddot\theta_6
- r_{8\text{tot}}\cos\theta_8\,\dot\theta_8^2 - r_{8\text{tot}}\sin\theta_8\,\ddot\theta_8$$

Dit is een directe uitwerking van de keten van B naar K waarbij telkens de
$x$-bijdrage van elk linksegment toegevoegd wordt.

---

## 8. Conditionering en singulariteiten

### 8.1 Wat is het conditiegetal?

Het conditiegetal $\kappa(A) = \text{cond}(A)$ van een matrix is een maat voor hoe
gevoelig de oplossing van $A\mathbf{x} = \mathbf{b}$ is voor kleine veranderingen in
$A$ of $\mathbf{b}$.

Formeel: als $A\mathbf{x} = \mathbf{b}$ en $(A + \delta A)(\mathbf{x} + \delta\mathbf{x})
= \mathbf{b}$, dan geldt:

$$\frac{\|\delta\mathbf{x}\|}{\|\mathbf{x}\|} \leq \text{cond}(A) \cdot \frac{\|\delta A\|}{\|A\|}$$

Een **laag** conditiegetal (dicht bij 1) betekent: het stelsel is numeriek stabiel.
Een **hoog** conditiegetal (bv. $10^6$) betekent: kleine fouten in de invoer kunnen
grote fouten in de output veroorzaken.

> ⚠️ **Belangrijk voor mondeling:** "Wat is het conditiegetal?" — het is de verhouding
> van de grootste naar de kleinste singuliere waarde van A. Het geeft aan hoe "ver"
> de matrix van singulariteit verwijderd is.

### 8.2 Mechanische betekenis

**Wanneer cond(A) groot is:**

De matrix A dreigt singulier te worden. Mechanisch betekent dit dat het mechanisme een
configuratie nadert waar:
- Links op één lijn komen (doorgeslagen positie)
- De beweging van de schuiver nauwelijks wordt overgedragen naar de links
- Kleine verstoringen in $\dot{s}$ leiden tot grote hoeksnelheden

Dit heet een **dood punt** (dead point) of **singuliere configuratie**. In zulke
stands kan het mechanisme vastklemmen of oncontroleerbaar bewegen.

**Wanneer A exact singulier is:**

Het stelsel $A\boldsymbol{\omega} = \mathbf{B}$ heeft geen unieke oplossing. De
hoeksnelheden zijn onbepaald. Het mechanisme heeft tijdelijk méér vrijheidsgraden
(of nul vrijheidsgraden) — afhankelijk van de richting van de singulariteit.

> ⚠️ **Valkuil:** Een hoog conditiegetal impliceert *niet* dat het mechanisme
> geblokkeerd is. Het betekent dat de numerieke en mechanische gevoeligheid groot is.
> Pas bij $\text{cond}(A) \to \infty$ is er een ware singulariteit.

### 8.3 Identificatie van kritische zones

De code berekent $\text{cond}(A)$ op elk tijdstip en vergelijkt met een drempel:

$$\text{drempel} = \max\left(10^3,\; \text{percentiel}_{95}\right)$$

De drempel combineert een absolute limiet met een relatieve maat die aanpast aan de
specifieke geometrie. Opeenvolgende tijdstappen boven de drempel worden gegroepeerd
tot één **kritische zone**.

Voor elke kritische zone rapporteert de code:
- De schuiverpositie $s$ bij de piekwaarde van $\text{cond}(A)$
- Het tijdstip $t$
- De maximale waarde van $\text{cond}(A)$ in die zone

**Wat te doen bij kritische zones?**
1. Het condition-scurve traject vertraagt automatisch de schuiver in die zones.
2. In de dynamische analyse (Notebook 2) verwacht je grotere krachten in die zones.
3. Controleer of de singulariteit fysisch vermijdbaar is door de geometrie aan te
   passen.

> ⚠️ **Belangrijk voor mondeling:** Combineer altijd de grafiek van $\text{cond}(A)$
> als functie van $s$ met de animatie. Kijk welke geometrische configuratie overeenkomt
> met de piek. Dat geeft je de mechanische intuïtie.

---

## 9. Koppeling naar Notebook 2

De kinematische resultaten worden opgeslagen in een `.npz`-bestand. Dit bestand bevat:

- Tijdvector $t$ en schuivertraject $s$, $\dot{s}$, $\ddot{s}$
- Hoeken $\theta_3$ t.e.m. $\theta_8$
- Hoeksnelheden $\dot\theta_3$ t.e.m. $\dot\theta_8$
- Hoekversnellingen $\ddot\theta_3$ t.e.m. $\ddot\theta_8$
- Positie, snelheid en versnelling van punt K
- Conditiegetal $\text{cond}(A)$ en sluitingsfout
- Alle geometrische parameters

Dit bestand dient als de **enige brug** tussen kinematica en dynamica. Wanneer je een
parameter wijzigt in Notebook 1 en alles opnieuw uitvoert, schrijft het `.npz`-bestand
de nieuwe waarden over. Notebook 2 leest dan altijd de meest recente kinematica in.

> ⚠️ **Valkuil:** Als je Notebook 1 gedeeltelijk uitvoert (niet "Run All"), kan het
> `.npz`-bestand verouderd zijn. Notebook 2 zal dan dynamische resultaten berekenen
> op basis van een inconsistente kinematica. Altijd "Run All" voor Notebook 1 als je
> een parameter gewijzigd hebt.

---

## 10. Examensamenvatting: overzicht van de methoden

| Analyse | Stelsel | Oplossing | Sleutelmatrix |
|---------|---------|-----------|---------------|
| Positie | $\vec{F}(\boldsymbol{\theta}, s) = \vec{0}$ (niet-lineair) | `fsolve` (Newton-Raphson) | — (Jacobiaan intern) |
| Snelheid | $A(\boldsymbol{\theta}) \cdot \boldsymbol{\omega} = \mathbf{b}\dot{s}$ (lineair) | `linalg.solve` | $A$ |
| Versnelling | $A(\boldsymbol{\theta}) \cdot \boldsymbol{\alpha} = \mathbf{C}(\boldsymbol{\omega}, \ddot{s})$ (lineair) | `linalg.solve` | $A$ (zelfde!) |
| Conditionering | $\kappa = \text{cond}(A)$ | — | $A$ |

**Wat te kennen voor het mondeling:**

1. **Vectorlusmethode:** hoe stel je de sluitingsvergelijkingen op voor een gegeven
   mechanisme?
2. **Differentiatie naar de tijd:** hoe ga je van positievergelijkingen naar
   snelheidsvergelijkingen?
3. **Hergebruik van A:** waarom is de snelheidsmatrix ook de versnellingsmatrix?
4. **Centripetale termen:** wat zijn ze, waar komen ze vandaan, aan welke kant staan ze?
5. **Puntconstructie:** hoe bepaal je de snelheid/versnelling van een outputpunt?
6. **Conditiegetal:** wat meet het, wat betekent een hoge waarde mechanisch?
7. **Sluitingsfout:** hoe controleer je de kwaliteit van de positie-oplossing?
8. **Mobiliteitsformule Grübler-Kutzbach:** formule, betekenis van elke term.

> ⚠️ **Centrale examenvraag:** "Leg de kinematische analyse van een stangenmechanisme
> uit, van positie tot versnelling." — begin met de vectorlus (positie), differentieer
> naar tijd (snelheid, lineair stelsel $A\boldsymbol{\omega} = \mathbf{B}$), differentieer
> nogmaals (versnelling, zelfde A met nieuw rechterlid $\mathbf{C}$). Eindig met de
> puntconstructie voor het outputpunt.
