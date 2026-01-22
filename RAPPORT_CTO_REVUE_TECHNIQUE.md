# RAPPORT TECHNIQUE - REVUE CTO
## Dimensionnement Cellules CEP - Projet P202602AO

**Date:** 2026-01-22
**Pour:** Revue CTO
**Projet:** Générateur CEP - Université Bretagne Sud / IRDL Pontivy
**Statut:** En attente de validation

---

## SOMMAIRE

1. [Contexte et objectifs](#1-contexte-et-objectifs)
2. [Hypothèses de modélisation](#2-hypothèses-de-modélisation)
3. [Solutions proposées](#3-solutions-proposées)
4. [Résultats de simulation](#4-résultats-de-simulation)
5. [Points à trancher](#5-points-à-trancher)
6. [Risques identifiés](#6-risques-identifiés)
7. [Prochaines étapes](#7-prochaines-étapes)

---

## 1. CONTEXTE ET OBJECTIFS

### 1.1 Cahier des charges CCTP P202602AO

Le générateur CEP (Champs Électriques Pulsés) doit traiter des produits alimentaires avec les contraintes suivantes:

| Exigence CCTP | Valeur | Notre conformité |
|---------------|--------|------------------|
| Tension max | 50 kV crête-crête (±25 kV) | ✅ OK |
| Courant crête max | 160 A | ✅ OK |
| Largeur impulsion | 10 µs | ✅ OK |
| Chute de tension | < 5% | ✅ OK |
| Conductivité produits | 1 - 50 mS/cm | ✅ OK |
| Mode batch | 10 - 500 mL | ✅ OK |
| Mode continu | 1 - 100 kg/h | ✅ OK |
| Alimentation | 4 kW AC/DC | ✅ OK |

### 1.2 Objectif de l'étude

Dimensionner les cellules de traitement (électrodes + chambre) pour couvrir toute la plage d'utilisation tout en respectant les limites du générateur.

---

## 2. HYPOTHÈSES DE MODÉLISATION

### 2.1 Hypothèses électriques

| Paramètre | Hypothèse | Justification | Niveau confiance |
|-----------|-----------|---------------|------------------|
| **Résistivité TECAPEEK** | ρ = 100 Ω·m | Milieu de gamme log (10³-10⁵ Ω·cm) | ⚠️ MOYEN |
| **Modèle circuit** | R_série (R_med + 2×R_elec) | Mode résistif pur, néglige capacité | ✅ BON |
| **Tension appliquée** | V = 25 kV (amplitude) | Mode bipolaire ±25 kV | ✅ CONFIRMÉ |
| **Forme d'onde** | Créneaux rectangulaires | Néglige transitoires | ✅ BON |
| **Impédance générateur** | Négligée (Z_source << Z_charge) | À vérifier avec fournisseur | ⚠️ À VALIDER |

### 2.2 Hypothèses thermiques

| Paramètre | Hypothèse | Justification | Niveau confiance |
|-----------|-----------|---------------|------------------|
| **Cp produit** | 4000 J/kg·K | Produits aqueux alimentaires | ✅ BON |
| **ΔT max** | 20°C | Limite qualité produit | ⚠️ À CONFIRMER |
| **Dissipation** | 100% dans le produit | Néglige pertes électrodes | ✅ CONSERVATEUR |

### 2.3 Hypothèses géométriques

| Paramètre | Hypothèse | Justification | Niveau confiance |
|-----------|-----------|---------------|------------------|
| **Champ E** | Uniforme E = V/gap | Géométrie plan-plan | ✅ BON pour carré |
| **Volume = gap × surface** | Négllige volumes morts | Simplifié | ⚠️ À AFFINER |
| **Écoulement continu** | Piston (plug flow) | Pas de recirculation | ⚠️ À VALIDER CFD |

### 2.4 Formules clés du modèle

```
Résistance électrode:    R_elec = ρ × épaisseur / surface
Résistance milieu:       R_med = gap / (σ × surface)
Impédance totale:        Z = R_med + 2 × R_elec
Courant crête:           I = V / Z
Champ électrique:        E = V / gap
Énergie impulsion:       E_pulse = V × I × t_pulse
Puissance moyenne:       P_avg = E_pulse × fréquence
Contrainte thermique:    P_max = débit × Cp × ΔT / 3600
Capacité stockage:       C_stock ≥ I × t_pulse / (0.05 × V)
```

---

## 3. SOLUTIONS PROPOSÉES

### 3.1 Matériau électrodes: TECAPEEK ELS nano black

**Choix retenu:** PEEK chargé nanotubes de carbone (Ensinger)

| Propriété | Valeur | Avantage |
|-----------|--------|----------|
| Résistivité | 10³-10⁵ Ω·cm (100 Ω·m nominal) | Limitation courant intrinsèque |
| T° service | 260°C | Compatible stérilisation |
| Contact alimentaire | Oui (FDA) | Obligatoire CCTP |
| Usinabilité | Bonne | Fabrication locale possible |
| Résistance chimique | Excellente | Nettoyage CIP/SIP |

**Alternative non retenue:** Électrodes inox + résistances externes
- Plus complexe, moins compact, moins fiable

### 3.2 Architecture cellule: Mode résistif avec électrodes épaisses

**Principe:** Les électrodes TECAPEEK agissent comme résistances série pour limiter le courant.

```
     HT (+25 kV)
         │
    ┌────┴────┐
    │ TECAPEEK │  R_elec = ρ × e / S ≈ 90-180 Ω
    │ (15-20mm)│
    ├─────────┤
    │ PRODUIT │  R_med = gap / (σ × S) ≈ 0.06-6 Ω
    │ (gap)   │
    ├─────────┤
    │ TECAPEEK │  R_elec ≈ 90-180 Ω
    │ (15-20mm)│
    └────┬────┘
         │
     HT (-25 kV)
```

**Avantage clé:** Pas besoin de résistance ballast externe, système compact et sûr.

### 3.3 Géométrie: Carrée recommandée pour mode continu

| Critère | Cylindrique | Carrée | Verdict |
|---------|-------------|--------|---------|
| Champ E | Non-uniforme (1/r) | Uniforme | **Carrée** |
| Usinage TECAPEEK | Tournage coûteux | Fraisage simple | **Carrée** |
| Étanchéité | Joints toriques | Joints plats | Équivalent |
| Tenue pression | Meilleure | Suffisante (<5 bar) | Acceptable |

**Recommandation:** Géométrie **CARRÉE** pour toutes les cellules continu.
**Batch:** Au choix (cylindrique ou carrée), différence mineure.

![Comparaison Géométries](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_comparaison_geometries.png)

### 3.4 Dimensionnement optimisé

#### Cellules Batch (mode résistif)

| Volume | Gap | Surface | Côté carré | Épaisseur PEEK | E (kV/cm) | I max (A) | Marge I |
|--------|-----|---------|------------|----------------|-----------|-----------|---------|
| 10 mL | 2.0 mm | 50 cm² | 71 mm | 4.5 mm | 125 | 139 | 13% |
| 50 mL | 3.0 mm | 168 cm² | 130 mm | 14.6 mm | 84 | 144 | 10% |
| 100 mL | 4.9 mm | 203 cm² | 142 mm | 17.7 mm | 51 | 143 | 11% |
| 250 mL | 11.8 mm | 212 cm² | 146 mm | 18.4 mm | 21 | 144 | 10% |
| 500 mL | 22.6 mm | 222 cm² | 149 mm | 19.2 mm | 11 | 144 | 10% |

#### Cellules Continu (mode résistif, géométrie carrée)

| Débit | Gap | Surface | Côté | Épaisseur PEEK | E (kV/cm) | I (A) | P_avg (W) | P_therm (W) |
|-------|-----|---------|------|----------------|-----------|-------|-----------|-------------|
| 10 kg/h | 3.7 mm | 13.5 cm² | 37 mm | 19.2 mm | 67 | 8.8 | 219 | 222 |
| 50 kg/h | 2.2 mm | 64 cm² | 80 mm | 18.1 mm | 116 | 44 | 1111 | 1111 |
| 100 kg/h | 2.2 mm | 128 cm² | 113 mm | 18.1 mm | 116 | 89 | 2221 | 2222 |

![Dimensions Optimisées](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_dimensions_optimisees.png)

![Schéma Cellule Carrée](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_schema_cellule_carree.png)

---

## 4. RÉSULTATS DE SIMULATION

### 4.1 Conformité aux contraintes

| Contrainte | Limite | Pire cas simulé | Marge | Status |
|------------|--------|-----------------|-------|--------|
| Courant crête | 160 A | 144 A (Batch 50mL @ 50mS/cm) | 10% | ✅ |
| Chute tension | 5% | 4.8% | 4% | ✅ |
| Puissance AC/DC | 4 kW | 2.2 kW (Continu 100kg/h) | 45% | ✅ |
| Échauffement | 20°C | 20°C (Continu, limite) | 0% | ⚠️ |

![Conformité CCTP](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_conformite_CCTP.png)

### 4.2 Plage de validité

Le design est valide sur **100% de la plage de conductivité** (1-50 mS/cm) pour toutes les cellules.

![Courant vs Conductivité](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_courant_vs_conductivite.png)

![Validité Designs](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_validite_designs.png)

### 4.3 Champ électrique atteint

| Cellule | E min | E max | Cible électroporation |
|---------|-------|-------|----------------------|
| Batch 10 mL | 125 kV/cm | 125 kV/cm | ✅ Excellent |
| Batch 500 mL | 11 kV/cm | 11 kV/cm | ✅ Suffisant |
| Continu 10 kg/h | 67 kV/cm | 67 kV/cm | ✅ Très bon |
| Continu 100 kg/h | 116 kV/cm | 116 kV/cm | ✅ Excellent |

**Note:** Pour l'électroporation efficace, E > 10 kV/cm est généralement suffisant.

![Champ Électrique](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_champ_electrique_cellules.png)

### 4.4 Capacité de stockage requise

```
C_stock = I_max × t_pulse / (0.05 × V) = 160 × 10µs / (0.05 × 25kV) = 1.28 µF
```

**Avec marge 50%:** C_stock = **1.92 µF** (valeur retenue)

### 4.5 Récapitulatif des spécifications

![Résumé Spécifications](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_resume_specifications.png)

---

## 5. POINTS À TRANCHER

### 🔴 DÉCISION REQUISE #1: Résistivité TECAPEEK

**Problème:** La datasheet donne une plage large (10³-10⁵ Ω·cm = 10-1000 Ω·m).
Nous avons pris ρ = 100 Ω·m (milieu de gamme logarithmique).

| Scénario | ρ (Ω·m) | Impact I_max | Impact design |
|----------|---------|--------------|---------------|
| Pessimiste | 10 | I dépasserait 160 A | ❌ Refonte |
| Nominal | 100 | I ≤ 144 A | ✅ OK |
| Optimiste | 1000 | I très faible | ✅ Sur-dimensionné |

**Actions possibles:**
1. **Demander caractérisation** au fournisseur Ensinger (lot spécifique)
2. **Commander échantillons** et mesurer en interne
3. **Surdimensionner** l'épaisseur PEEK (+50%) par sécurité

**Recommandation:** Option 1 + 3 (caractérisation + marge sécurité)

---

### 🔴 DÉCISION REQUISE #2: Fréquence de répétition

**Problème:** La fréquence f n'est pas spécifiée dans le CCTP.
Nous avons pris f = 100 Hz pour les calculs de P_avg.

| Fréquence | P_avg @ 100kg/h | Contrainte thermique | Nb pulses/s résidence |
|-----------|-----------------|----------------------|----------------------|
| 10 Hz | 222 W | OK | 10 |
| 100 Hz | 2221 W | LIMITE | 100 |
| 1000 Hz | 22 kW | ❌ IMPOSSIBLE | 1000 |

**Impact:** La fréquence max dépend directement de la contrainte thermique.

![Contrainte Thermique](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_contrainte_thermique.png)

**Question au CTO:** Quelle dose de traitement (J/mL) est visée?
- Électroporation réversible: 1-10 J/mL → f faible OK
- Inactivation microbienne: 50-100 J/mL → f élevée nécessaire

---

### 🟡 DÉCISION SUGGÉRÉE #3: Géométrie Batch

**Options:**
- A) Cylindrique (standard industrie, esthétique)
- B) Carrée (même performance, fabrication simplifiée)
- C) Mixte (cylindrique batch, carrée continu)

**Recommandation:** Option C - Permet de capitaliser sur l'expérience cylindrique existante pour le batch, tout en bénéficiant de la géométrie optimale pour le continu.

---

### 🟡 DÉCISION SUGGÉRÉE #4: Redondance cellules

**Question:** Faut-il prévoir des cellules de rechange?

| Option | Coût TECAPEEK | Avantage |
|--------|---------------|----------|
| Juste besoin | ~8 kg | Économique |
| +1 spare par taille | ~12 kg | Sécurité exploitation |
| +100% spare | ~16 kg | R&D, tests destructifs |

**Recommandation:** +1 spare pour les cellules continu (plus critiques).

---

### 🟡 DÉCISION SUGGÉRÉE #5: Mode capacitif

**Le CCTP mentionne un mode capacitif optionnel.**

Notre étude s'est concentrée sur le mode **résistif** (TECAPEEK).
Le mode capacitif (PEEK isolant + effet DBD) permettrait:
- Tension 50 kV (vs 25 kV résistif)
- Champs plus élevés
- Mais complexité accrue (claquage, ionisation)

**Question au CTO:** Faut-il approfondir l'étude du mode capacitif maintenant ou en phase 2?

---

## 6. RISQUES IDENTIFIÉS

### 6.1 Risques techniques

| Risque | Probabilité | Impact | Mitigation |
|--------|-------------|--------|------------|
| ρ TECAPEEK hors spécification | Moyenne | Élevé | Caractérisation préalable |
| Claquage diélectrique PEEK | Faible | Élevé | Marge épaisseur, tests HT |
| Échauffement localisé | Moyenne | Moyen | CFD thermique, capteurs T° |
| Érosion électrodes | Faible | Moyen | Suivi usure, pièces rechange |
| Non-uniformité écoulement | Moyenne | Moyen | CFD hydraulique, design entrée |

### 6.2 Risques projet

| Risque | Probabilité | Impact | Mitigation |
|--------|-------------|--------|------------|
| Délai approvisionnement TECAPEEK | Moyenne | Moyen | Commander tôt, stock tampon |
| Coût usinage sous-estimé | Faible | Faible | Devis préalables |
| Incompatibilité générateur | Faible | Élevé | Interface avec fournisseur |

---

## 7. PROCHAINES ÉTAPES

### Phase immédiate (à valider par CTO)

1. ☐ **Valider les hypothèses** (résistivité, fréquence, ΔT)
2. ☐ **Trancher les points de décision** (#1 à #5)
3. ☐ **Valider le budget matière** (~10 kg TECAPEEK)

### Phase suivante (après validation)

4. ☐ Contacter Ensinger pour caractérisation ρ et devis
5. ☐ Lancer étude CFD thermique/hydraulique
6. ☐ Finaliser plans mécaniques détaillés
7. ☐ Commander matière et lancer usinage prototype

---

## ANNEXES

### A. Images de simulation

#### A.1 Conformité CCTP
![Conformité CCTP](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_conformite_CCTP.png)

#### A.2 Courant vs Conductivité
![Courant vs Conductivité](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_courant_vs_conductivite.png)

#### A.3 Champ Électrique par Cellule
![Champ Électrique](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_champ_electrique_cellules.png)

#### A.4 Dimensions Optimisées
![Dimensions Optimisées](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_dimensions_optimisees.png)

#### A.5 Contrainte Thermique (Mode Continu)
![Contrainte Thermique](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_contrainte_thermique.png)

#### A.6 Comparaison Géométries (Cylindrique vs Carrée)
![Comparaison Géométries](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_comparaison_geometries.png)

#### A.7 Schéma Cellule Carrée
![Schéma Cellule Carrée](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_schema_cellule_carree.png)

#### A.8 Résumé des Spécifications
![Résumé Spécifications](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_resume_specifications.png)

#### A.9 Carte de Validité des Designs
![Validité Designs](https://raw.githubusercontent.com/jpbrasile/images/main/CEP_validite_designs.png)

### B. Fichiers source

| Fichier | Contenu |
|---------|---------|
| electrode_simulation.jl | Code Julia de simulation |
| SPECIFICATIONS_CELLULES_CEP_v2.md | Spécifications détaillées |
| SPECIFICATIONS_CELLULES_CEP.csv | Données tabulaires |
| generate_report_images.jl | Génération des images |

### C. Contact fournisseur

**TECAPEEK ELS nano black**
Ensinger France
Tel: +33 (0)4 74 77 14 00
Web: ensingerplastics.com

---

*Rapport généré le 2026-01-22*
*Simulation: electrode_simulation.jl v1.0*
