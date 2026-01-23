# Rapport Technique CEP v2 - Revue CTO

## Projet: P202602AO - Generateur CEP UBS/IRDL Pontivy

**Date:** 23 janvier 2026
**Version:** 2.1 (CORRIGEE)
**Statut:** REVISION CRITIQUE - Attente validation CTO

---

## ALERTE CRITIQUE

![Conclusion CTO](https://raw.githubusercontent.com/jpbrasile/images/main/conclusion_cto_CRITIQUE.png)

**Le mode RESISTIF avec electrodes TECAPEEK ne fonctionne PAS a haute conductivite.**

- E_theorique = 20-31 kV/cm
- **E_REEL = 0.01 kV/cm** (quasi NUL!)
- Efficacite < 0.1% a sigma = 50 mS/cm

**Le mode CAPACITIF (DBD) est la SEULE solution viable pour les milieux conducteurs.**

---

## 1. Probleme Identifie: Chute de Tension dans les Electrodes

### 1.1 Analyse E_reel vs E_theorique

![E reel vs theorique](https://raw.githubusercontent.com/jpbrasile/images/main/E_reel_vs_theorique_CRITIQUE.png)

**Commentaire:** Ce graphique montre l'ecart catastrophique entre le champ theorique (E = V/gap) et le champ reel (E = V_gap/gap) en mode resistif. A haute conductivite, le champ reel tend vers zero car toute la tension est dissipee dans les electrodes TECAPEEK.

### 1.2 Cause: Distribution des Resistances

![Distribution resistances](https://raw.githubusercontent.com/jpbrasile/images/main/distribution_resistances.png)

**Commentaire:** A haute conductivite (50 mS/cm), R_milieu = 0.06 Ohm tandis que R_electrode = 120 Ohm. Le ratio R_elec/R_med depasse 2000x! La quasi-totalite de la tension (99.95%) est dissipee dans les electrodes, pas dans le milieu a traiter.

### 1.3 Efficacite de Transfert de Tension

![Efficacite transfert](https://raw.githubusercontent.com/jpbrasile/images/main/efficacite_transfert_tension.png)

**Commentaire:** L'efficacite (V_gap/V_appliquee) chute rapidement avec la conductivite. Pour tous les volumes, l'efficacite passe sous 1% des que sigma > 5-10 mS/cm. Le mode resistif est donc limite aux milieux tres peu conducteurs.

---

## 2. Solution: Mode Capacitif (DBD)

### 2.1 Comparaison Resistif vs Capacitif

![Resistif vs Capacitif](https://raw.githubusercontent.com/jpbrasile/images/main/resistif_vs_capacitif_CONCLUSION.png)

**Commentaire:** A sigma = 50 mS/cm, le mode resistif produit E ~ 0.01 kV/cm (inutilisable) tandis que le mode capacitif produit E = 25-42 kV/cm (efficace). Le mode capacitif est la seule solution viable pour les milieux conducteurs.

### 2.2 Effet Memoire DBD

![Effet memoire DBD](https://raw.githubusercontent.com/jpbrasile/images/main/effet_memoire_dbd.png)

**Commentaire:** L'effet memoire DBD permet d'atteindre 50 kV effectif avec un generateur +/-25 kV. Les charges accumulees sur le dielectrique lors du pulse positif s'ajoutent au pulse negatif suivant, doublant la tension effective.

### 2.3 Domaine de Validite des Modes

![Domaine validite](https://raw.githubusercontent.com/jpbrasile/images/main/domaine_validite_modes.png)

**Commentaire:**
- **Zone verte (Capacitif):** Couvre tout le domaine CCTP (V: 80-500mL, sigma: 1-50 mS/cm)
- **Zone jaune (Resistif OK):** Limitee a sigma < 5 mS/cm
- **Zone rouge (Resistif inefficace):** E_reel ~ 0, inutilisable

---

## 3. Architecture Revisee

### 3.1 Schema Cellule Mode Capacitif

![Schema cellule](https://raw.githubusercontent.com/jpbrasile/images/main/schema_cellule_anneau_garde.png)

**Commentaire:** La cellule utilise des electrodes PEEK isolant (3mm) pour le mode capacitif. Les anneaux de garde permettent d'ajuster le volume sans changer le gap. Le gap minimum est de 10mm pour respecter E_max = 50 kV/cm.

### 3.2 Specifications Revisees

![Specifications revisees](https://raw.githubusercontent.com/jpbrasile/images/main/resume_specifications_REVISE.png)

**Commentaire:** Le mode CAPACITIF (DBD) devient le mode PRINCIPAL. Le mode resistif est relegue en mode secondaire, reserve aux milieux peu conducteurs (sigma < 5 mS/cm).

---

## 4. Resultats Simulation Corrigee

### 4.1 Mode Resistif @ sigma = 50 mS/cm

| Cellule | Volume | E_theorique | E_REEL | Efficacite | Statut |
|---------|--------|-------------|--------|------------|--------|
| Petite | 10 mL | 31.2 kV/cm | 0.01 kV/cm | 0.0% | **INUTILISABLE** |
| Petite | 80 mL | 31.2 kV/cm | 0.01 kV/cm | 0.0% | **INUTILISABLE** |
| Grande | 100 mL | 20.8 kV/cm | 0.01 kV/cm | 0.0% | **INUTILISABLE** |
| Grande | 500 mL | 20.8 kV/cm | 0.01 kV/cm | 0.05% | **INUTILISABLE** |

### 4.2 Mode Capacitif (DBD) @ sigma = 50 mS/cm

| Cellule | Volume | E_REEL | I | Marge I | Statut |
|---------|--------|--------|---|---------|--------|
| Grande | 80 mL | 41.7 kV/cm | 0.5 A | 100% | **OK** |
| Grande | 290 mL | 41.7 kV/cm | 1.8 A | 99% | **OK** |
| Grande | 500 mL | 41.7 kV/cm | 3.2 A | 98% | **OK** |

### 4.3 Mode Resistif - Domaine de Validite

| sigma (mS/cm) | R_med (Ohm) | R_elec (Ohm) | E_reel (kV/cm) | Efficacite |
|---------------|-------------|--------------|----------------|------------|
| 1 | 2.88 | 60 | 0.49 | 2.3% |
| 5 | 0.58 | 60 | 0.10 | 0.5% |
| 10 | 0.29 | 60 | 0.05 | 0.2% |
| 50 | 0.06 | 60 | 0.01 | 0.05% |

**Conclusion:** Le mode resistif n'est utilisable que pour sigma < 5 mS/cm, et meme la l'efficacite reste faible (~2%).

---

## 5. Decisions CTO Requises

### Decision 1: Mode Principal (PRIORITAIRE)

- [ ] **Confirmer le mode CAPACITIF (DBD) comme mode PRINCIPAL**
- [ ] Conserver le mode resistif comme option pour milieux peu conducteurs

### Decision 2: Architecture Cellules

- [ ] **Cellule unique gap=12mm** (mode capacitif, V: 80-500 mL)
- [ ] Abandonner la petite cellule gap=8mm (incompatible capacitif)

### Decision 3: Validation Experimentale

- [ ] Valider l'effet memoire DBD experimentalement avant production
- [ ] Confirmer les performances en mode capacitif sur milieux reels

---

## 6. Specifications Finales Proposees

### Mode CAPACITIF (DBD) - MODE PRINCIPAL

| Parametre | Valeur |
|-----------|--------|
| Electrodes | PEEK isolant, 3 mm |
| Tension effective | 50 kV (effet memoire) |
| Gap minimum | 10 mm |
| Champ E | 25-42 kV/cm |
| Conductivite | 1-50 mS/cm (FULL CCTP) |
| Volume | 80-500 mL |

### Mode RESISTIF - MODE SECONDAIRE (limite)

| Parametre | Valeur |
|-----------|--------|
| Electrodes | TECAPEEK ELS, 25 mm |
| Tension | 25 kV |
| Gap | 5-8 mm |
| **Conductivite max** | **5 mS/cm** |
| Efficacite | < 3% |

---

## 7. Prochaines Etapes

1. **Validation CTO** du changement de mode principal
2. **Tests experimentaux** mode capacitif sur milieux reels
3. **Validation effet memoire DBD** en laboratoire
4. **Dimensionnement final** cellule mode capacitif
5. **Prototype** cellule pilote

---

## Annexes

### A. Fichiers de Simulation

- `electrode_simulation_v2.jl` - Code simulation principal (CORRIGE)
- `generate_report_images_v2.jl` - Generation images
- `results_v2_option_C.csv` - Resultats exportes

### B. Formules Corrigees

**Tension reelle dans le gap (mode resistif):**
```
V_gap = I × R_med = V × R_med / (R_med + 2×R_elec)
```

**Champ electrique REEL:**
```
E_reel = V_gap / gap  (PAS V/gap!)
```

**Efficacite de transfert:**
```
Efficacite = V_gap / V_appliquee × 100%
```

### C. Images du Rapport

| Image | Description |
|-------|-------------|
| conclusion_cto_CRITIQUE.png | Synthese probleme et solution |
| E_reel_vs_theorique_CRITIQUE.png | Ecart E_theo vs E_reel |
| distribution_resistances.png | Repartition R_med vs R_elec |
| efficacite_transfert_tension.png | Efficacite vs conductivite |
| resistif_vs_capacitif_CONCLUSION.png | Comparaison des modes |
| effet_memoire_dbd.png | Principe effet memoire DBD |
| domaine_validite_modes.png | Zones de validite |
| schema_cellule_anneau_garde.png | Architecture cellule |
| resume_specifications_REVISE.png | Specs revisees |

---

*Rapport genere par simulation Julia - electrode_simulation_v2.jl (CORRIGE)*
*Correction majeure: prise en compte chute de tension dans electrodes resistives*
