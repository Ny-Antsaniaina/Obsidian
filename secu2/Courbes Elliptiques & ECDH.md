
## 1.C'est quoi une courbe elliptique ?

Une courbe elliptique a la forme :

$$y^2 = x^3 + ax + b \pmod{p}$$

Dans l'exercice : **y² = x³ + 2x + 3 (mod 97)**

> **mod 97** signifie qu'on travaille dans un espace fini : tous les résultats sont ramenés entre 0 et 96.

---

## 2. Comment trouver le point G(3,6) ?

On **vérifie** qu'un point appartient à la courbe en remplaçant x et y.

### Vérification de G(3, 6) :

**Côté gauche :** $$y^2 = 6^2 = 36$$

**Côté droit :** $$x^3 + 2x + 3 = 3^3 + 2(3) + 3 = 27 + 6 + 3 = 36$$

$$36 \equiv 36 \pmod{97} \quad ✓$$

> G(3,6) est bien sur la courbe ! On le **choisit** comme point de base public.

---

## 3. Comment "multiplier" un point ? (ex: 25·G)

Multiplier un point = **l'additionner avec lui-même** plusieurs fois.

```
25·G = G + G + G + ... + G  (25 fois)
```

On utilise l'algorithme **"double-and-add"** (comme la multiplication rapide) :

```
25 = 11001 en binaire

→ On part de G
→ On double, on additionne selon les bits
→ Beaucoup plus rapide que 25 additions
```

### Formule d'addition de deux points P(x₁,y₁) + Q(x₂,y₂) :

Si P ≠ Q : $$\lambda = \frac{y_2 - y_1}{x_2 - x_1} \pmod{p}$$

Si P = Q (doublement) : $$\lambda = \frac{3x_1^2 + a}{2y_1} \pmod{p}$$

Puis : $$x_3 = \lambda^2 - x_1 - x_2 \pmod{p}$$ $$y_3 = \lambda(x_1 - x_3) - y_1 \pmod{p}$$

> Les divisions sont en réalité des **inverses modulaires** (algorithme d'Euclide étendu)

---

## 4. Déroulé complet de l'exercice

```
┌─────────────────────────────────────────┐
│  ÉTAPE 0 : Accord public                │
│  Courbe : y²= x³+2x+3 (mod 97)         │
│  Point de base : G(3,6)                 │
└─────────────────────────────────────────┘
           ↓                    ↓
┌──────────────────┐  ┌──────────────────────┐
│       MOI        │  │     MON CAMARADE     │
│  clé privée a=25 │  │  clé privée b=17     │
│                  │  │                      │
│ A = 25·G=(80,10) │  │ B = 17·G=(87,70)     │
└──────────────────┘  └──────────────────────┘
           ↓ échange clés publiques ↓
┌──────────────────┐  ┌──────────────────────┐
│ S = 25·B         │  │ S = 17·A             │
│ = 25·(87,70)     │  │ = 17·(80,10)         │
│ = (32,90) ✓      │  │ = (32,90) ✓          │
└──────────────────┘  └──────────────────────┘
           ↓
    SECRET COMMUN = (32, 90)
```

---

## 5. Pourquoi c'est sécurisé ?

|Action|Difficulté|
|---|---|
|Calculer A = a·G|**Facile** (rapide)|
|Retrouver **a** depuis A et G|**Impossible** en pratique|

C'est le **problème du logarithme discret sur courbe elliptique** (ECDLP).

> Même avec un ordinateur puissant, si p est très grand (ex: 256 bits comme Bitcoin), il faudrait des **milliards d'années** pour retrouver la clé privée.

---

## 6. Résumé des étapes à faire dans l'exercice

```
1. Vérifier que G est sur la courbe
   → remplacer x=3, y=6 dans y²=x³+2x+3 (mod 97)

2. Choisir sa clé privée a (ex: 25)

3. Calculer sa clé publique A = a·G
   → additions répétées sur la courbe

4. Échanger les clés publiques

5. Calculer S = a·B (ou b·A)
   → les deux donnent le même point !
```