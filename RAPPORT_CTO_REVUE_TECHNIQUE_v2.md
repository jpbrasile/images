# Rapport Technique CEP v2 - Revue CTO

## Projet: P202602AO - Generateur CEP UBS/IRDL Pontivy

**Date:** 23 janvier 2026
**Version:** 2.0
**Statut:** En attente validation CTO

---

## Resume Executif

Ce rapport presente l'architecture v2 du generateur CEP (Champs Electriques Pulses) conforme au CCTP P202602AO. L'architecture proposee utilise:

- **Generateur bipolaire +/-25 kV**
- **2 cellules batch avec anneaux de garde reglables** (+ option 3eme cellule)
- **Double mode**: Resistif (TECAPEEK) et Capacitif DBD (PEEK isolant)
- **Effet memoire DBD**: 50 kV effectif en mode capacitif

**DECISION CTO REQUISE:** Choix entre 3 options pour la limitation 500 mL en mode resistif.

---

## 1. Architecture Generateur

### 1.1 Specifications Generateur

| Parametre | Valeur | Note |
|-----------|--------|------|
| Amplitude | +/-25 kV | Bipolaire |
| V mode resistif | 25 kV | Direct |
| V mode capacitif | 50 kV | Effet memoire DBD |
| I_max | 160 A | Crete |
| C_stock | 1.92 uF | Stockage energie |
| P_max AC/DC | 4 kW | Entree |

### 1.2 Effet Memoire DBD (Mode Capacitif)

En mode capacitif avec electrodes PEEK isolant, l'effet memoire DBD permet de doubler la tension effective:

1. Pulse +25 kV -> charge du dielectrique
2. Pulse -25 kV -> inversion -> champ total = **50 kV**

Cet effet est fondamental pour atteindre les champs eleves requis par le CCTP (jusqu'a 50 kV/cm).

---

## 2. Architecture Cellules v2

### 2.1 Contrainte E_max

Pour gerer les effets de bord (amplification x1.5-3), nous imposons:

**E_max = 50 kV/cm**

Cela definit les gaps minimaux:
- Mode resistif (25 kV): gap_min = **5 mm**
- Mode capacitif (50 kV): gap_min = **10 mm**

### 2.2 Deux Cellules avec Anneaux de Garde

| Cellule | Gap | Volume | Surface | Cote |
|---------|-----|--------|---------|------|
| Petite | 8 mm | 10-80 mL | 12.5-100 cm2 | 35-100 mm |
| Grande | 12 mm | 80-500 mL | 66.7-416.7 cm2 | 82-204 mm |

**Avantages des anneaux de garde:**
- Volume ajustable sans changer le gap
- Effets de bord rejetes hors zone utile
- Electrodes interchangeables (resistif/capacitif)
- Simplification: 2 cellules couvrent 10-500 mL

### 2.3 Plages de Champ E

| Cellule | Mode Resistif (25 kV) | Mode Capacitif (50 kV) |
|---------|----------------------|------------------------|
| Petite | 1.2 - 31.2 kV/cm | 1.2 - 62.5 kV/cm* |
| Grande | 0.8 - 20.8 kV/cm | 0.8 - 41.7 kV/cm |

*Note: 62.5 kV/cm depasse E_max, donc non utilisable a 50 kV

---

## 3. DECISION CTO: Limitation 500 mL Mode Resistif

### 3.1 Probleme Identifie

Avec la grande cellule (gap=12mm) et un volume de 500 mL:
- Surface = 416.7 cm2
- A sigma=50 mS/cm avec electrode TECAPEEK 25mm: **I = 208 A > 160 A**
- Epaisseur electrode requise pour I<=160A: **32.5 mm** (non pratique)

### 3.2 Trois Options Proposees

#### Option A: Limiter le Volume a 384 mL

| Parametre | Valeur |
|-----------|--------|
| Volume max | 384 mL |
| sigma_max | 50 mS/cm (full CCTP) |
| Nb cellules | 2 |
| E_max | 20.8 kV/cm |
| Couverture vol. | 77% |
| Couverture sigma | 100% |

**Argumentaire:**
- (+) Full range conductivite 1-50 mS/cm maintenu
- (+) Epaisseur electrode realiste (25mm)
- (+) 2 cellules seulement (simplicite)
- (-) Volume max reduit de 23%

**Recommandation:** Adapte si echantillons typiques < 400 mL

---

#### Option B: Reduire sigma_max pour 500 mL

| Parametre | Valeur |
|-----------|--------|
| Volume max | 500 mL |
| sigma_max @ 500mL | ~1 mS/cm |
| Nb cellules | 2 |
| E_max | 20.8 kV/cm |
| Couverture vol. | 100% |
| Couverture sigma | 2% |

**Plage de validite (electrode 25mm):**
- 100 mL -> sigma_max = 50+ mS/cm
- 200 mL -> sigma_max = 50+ mS/cm
- 300 mL -> sigma_max = 50+ mS/cm
- 400 mL -> sigma_max < 50 mS/cm
- 500 mL -> sigma_max ~ 1 mS/cm

**Argumentaire:**
- (+) Volume 500 mL possible
- (+) 2 cellules seulement
- (-) Limitation conductivite severe (exclut digestats conducteurs)

**Recommandation:** Adapte uniquement pour milieux peu conducteurs

---

#### Option C: 3eme Cellule avec Gap=20 mm

| Parametre | Valeur |
|-----------|--------|
| Volume max | 500 mL |
| sigma_max | 50 mS/cm (full CCTP) |
| Nb cellules | 3 |
| E_max | 12.5 kV/cm |
| Couverture vol. | 100% |
| Couverture sigma | 100% |

**Specifications 3eme cellule:**
- Gap: 20 mm
- Volume: 300-500 mL
- Surface: 150-250 cm2
- Courant @ 500mL, 50mS/cm: **125 A < 160 A** OK

**Argumentaire:**
- (+) Full range volume 10-500 mL
- (+) Full range conductivite 1-50 mS/cm
- (+) Couverture CCTP 100%
- (-) Champ E reduit (12.5 kV/cm vs 20.8 kV/cm)
- (-) 3 cellules au lieu de 2 (cout, complexite)

**Recommandation:** Solution complete si budget permet

---

### 3.3 Tableau Comparatif

| Critere | Option A | Option B | Option C | CCTP |
|---------|----------|----------|----------|------|
| Volume max | 384 mL | 500 mL | 500 mL | 500 mL |
| sigma_max @ V_max | 50 mS/cm | ~1 mS/cm | 50 mS/cm | 50 mS/cm |
| Nb cellules | 2 | 2 | 3 | - |
| E_max (kV/cm) | 20.8 | 20.8 | 12.5 | >1 |
| Couverture vol. | 77% | 100% | 100% | 100% |
| Couverture sigma | 100% | 2% | 100% | 100% |

### 3.4 Recommandation

**Option C recommandee** pour couverture CCTP 100%.

Alternative: **Option A** si les echantillons typiques sont < 400 mL.

---

## 4. Resultats Simulation v2

### 4.1 Mode Resistif

| Cellule | Volume | Surface | Epaisseur | E | I | Marge |
|---------|--------|---------|-----------|---|---|-------|
| Petite | 10 mL | 12.5 cm2 | 25 mm | 31.2 kV/cm | 6.2 A | 96% |
| Petite | 45 mL | 56.2 cm2 | 25 mm | 31.2 kV/cm | 28.1 A | 82% |
| Petite | 80 mL | 100 cm2 | 25 mm | 31.2 kV/cm | 50 A | 69% |
| Grande | 80 mL | 66.7 cm2 | 25 mm | 20.8 kV/cm | 33.3 A | 79% |
| Grande | 290 mL | 241.7 cm2 | 25 mm | 20.8 kV/cm | 120.8 A | 25% |
| Grande | 500 mL | - | - | - | **>160 A** | **INVALIDE** |
| Tres Grande (C) | 300 mL | 150 cm2 | 25 mm | 12.5 kV/cm | 74.9 A | 53% |
| Tres Grande (C) | 400 mL | 200 cm2 | 25 mm | 12.5 kV/cm | 99.9 A | 38% |
| Tres Grande (C) | 500 mL | 250 cm2 | 25 mm | 12.5 kV/cm | 124.9 A | 22% |

### 4.2 Mode Capacitif (DBD)

| Cellule | Volume | Surface | Epaisseur | E | I | Marge |
|---------|--------|---------|-----------|---|---|-------|
| Grande | 80 mL | 66.7 cm2 | 3 mm | 41.7 kV/cm | 0.5 A | 100% |
| Grande | 290 mL | 241.7 cm2 | 3 mm | 41.7 kV/cm | 1.8 A | 99% |
| Grande | 500 mL | 416.7 cm2 | 3 mm | 41.7 kV/cm | 3.2 A | 98% |

Note: La petite cellule (gap=8mm) n'est pas utilisable en capacitif (gap < 10mm)

---

## 5. Materiaux Electrodes

### 5.1 Mode Resistif: TECAPEEK ELS nano black

| Propriete | Valeur |
|-----------|--------|
| Resistivite | 100 Ohm.m (milieu gamme 10-1000) |
| Permittivite | 3.3 |
| T max service | 260C |
| Epaisseur | 1-25 mm |

### 5.2 Mode Capacitif: PEEK Standard Isolant

| Propriete | Valeur |
|-----------|--------|
| Resistivite | 10^14 Ohm.m |
| Permittivite | 3.3 |
| T max service | 260C |
| Epaisseur | 0.3-3 mm |

---

## 6. Points de Decision CTO

### Decision 1: Option pour 500 mL (PRIORITAIRE)

- [ ] Option A: V_max = 384 mL, sigma_max = 50 mS/cm (2 cellules)
- [ ] Option B: V_max = 500 mL, sigma_max ~ 1 mS/cm (2 cellules)
- [ ] **Option C: 3eme cellule gap=20mm (RECOMMANDE)**

### Decision 2: Validation Architecture 2 Cellules + Anneaux de Garde

- [ ] Approuve l'architecture avec anneaux de garde reglables
- [ ] Demande modifications

### Decision 3: Validation Effet Memoire DBD

- [ ] Confirme l'utilisation de l'effet memoire pour 50 kV en capacitif
- [ ] Demande validation experimentale prealable

---

## 7. Prochaines Etapes

1. **Validation CTO** des choix d'architecture
2. **Dimensionnement detaille** des cellules retenues
3. **Choix fournisseur** TECAPEEK ELS
4. **Validation thermique** (echauffement en mode continu)
5. **Prototype** cellule pilote

---

## Annexes

### A. Fichiers de Simulation

- `electrode_simulation_v2.jl` - Code simulation principal
- `generate_report_images_v2.jl` - Generation images
- `results_v2_option_A.csv` - Resultats Option A
- `results_v2_option_C.csv` - Resultats Option C

### B. Formules Cles

**Resistance milieu:**
```
R_med = gap / (sigma x Surface)
```

**Resistance electrode TECAPEEK:**
```
R_elec = rho x epaisseur / Surface
```

**Impedance totale mode resistif:**
```
Z = R_med + 2 x R_elec
```

**Courant:**
```
I = V / Z
```

**Champ electrique:**
```
E = V / gap
```

---

*Rapport genere par simulation Julia - electrode_simulation_v2.jl*
