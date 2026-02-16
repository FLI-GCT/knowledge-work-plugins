---
name: analyse-ecarts
description: Décomposer les écarts financiers en facteurs explicatifs avec narratifs, cascades et seuils de matérialité. Utiliser pour analyser les écarts budget vs réel, les variations période sur période, les écarts de chiffre d'affaires ou de charges, ou préparer des commentaires de gestion.
---

# Analyse des Écarts

**Important** : Ce skill assiste dans l'analyse des écarts mais ne constitue pas un conseil financier. Toutes les analyses doivent être vérifiées par un professionnel qualifié.

Techniques de décomposition des écarts, seuils de matérialité, génération de narratifs et méthodologie d'analyse en cascade.

## Techniques de décomposition

### Décomposition Prix / Volume

La décomposition la plus fondamentale, applicable au chiffre d'affaires et aux achats.

**Formules :**
- Effet volume = (Quantité N - Quantité N-1) × Prix N-1
- Effet prix = (Prix N - Prix N-1) × Quantité N
- Écart total = Effet volume + Effet prix

**Exemple :**
```
CA N-1 : 1 000 unités × 50 € = 50 000 €
CA N :   1 100 unités × 52 € = 57 200 €
Écart total : +7 200 €

Effet volume : (1 100 - 1 000) × 50 € = +5 000 € (effet positif du volume)
Effet prix :   (52 € - 50 €) × 1 100  = +2 200 € (effet positif du prix)
Vérification : 5 000 + 2 200 = 7 200 ✓
```

### Décomposition à trois facteurs (Volume / Prix / Mix)

Quand l'entreprise vend plusieurs produits à des prix différents :

- Effet volume = ΔVolume total × Prix moyen pondéré N-1
- Effet mix = Volume N × (Prix moyen N si mix N avec prix N-1 - Prix moyen pondéré N-1)
- Effet prix = Volume N × (Prix moyen N réel - Prix moyen N si mix N avec prix N-1)

### Décomposition Effectif / Coût moyen

Pour la masse salariale :

- Effet effectif = (ETP N - ETP N-1) × Coût moyen N-1
- Effet coût moyen = (Coût moyen N - Coût moyen N-1) × ETP N
- Écart total = Effet effectif + Effet coût moyen

Décomposition plus fine du coût moyen :
- Effet augmentation : augmentations générales et individuelles
- Effet GVT (Glissement Vieillesse Technicité) : ancienneté automatique
- Effet structure : changement de mix entre catégories (cadres/non-cadres)
- Effet noria : remplacement de seniors par des juniors (ou inversement)

### Décomposition par catégorie de charges

Pour les charges d'exploitation, décomposer par nature PCG :

| Catégorie | Comptes PCG | Facteurs d'analyse |
|-----------|-------------|-------------------|
| Achats consommés | 601-607, 6037 | Volume, prix unitaire, mix fournisseurs |
| Services extérieurs | 611-629 | Contrats, volume d'activité, tarifs |
| Impôts et taxes | 631-637 | Assiettes, taux, réglementation |
| Charges de personnel | 641-648 | Effectifs, coût moyen, heures sup |
| Amortissements | 681 | Investissements, durées, méthodes |
| Autres charges | 651-658 | Éléments spécifiques |

## Seuils de matérialité

### Framework de seuils recommandés

| Type de comparaison | Seuil en % | Seuil en montant (PME) | Seuil en montant (ETI/GE) |
|--------------------|--------:|----------------------:|------------------------:|
| Réel vs Budget | 10% | 5 000 € | 50 000 € |
| N vs N-1 | 15% | 10 000 € | 100 000 € |
| Réel vs Forecast | 5% | 2 500 € | 25 000 € |
| Mois vs mois précédent | 20% | 5 000 € | 50 000 € |

**Règle :** Un écart est signalé si le seuil en % **OU** le seuil en montant est dépassé.

### Priorisation des investigations

| Priorité | Critère |
|----------|---------|
| 1 | Plus gros écart en valeur absolue (€) |
| 2 | Plus gros écart en pourcentage |
| 3 | Direction inattendue (favorable quand défavorable attendu, ou inversement) |
| 4 | Écart nouveau (absent la période précédente) |
| 5 | Écart cumulatif croissant sur plusieurs périodes |

## Génération de narratifs

### Structure d'un narratif

Chaque écart significatif doit être expliqué en trois parties :

1. **Constat** : "[Poste] présente un écart [favorable/défavorable] de X XXX € (X,X%) par rapport à [référence]."
2. **Explication** : "Cet écart s'explique principalement par [facteur 1] (+/- X XXX €) et [facteur 2] (+/- X XXX €)."
3. **Perspective** : "Cet écart est [ponctuel/récurrent/tendanciel] et [devrait se reproduire / ne devrait pas se reproduire / nécessite un suivi]."

### Checklist qualité des narratifs

- [ ] Le montant et le pourcentage sont précisés
- [ ] La direction (favorable/défavorable) est indiquée
- [ ] Les causes sont quantifiées (pas juste nommées)
- [ ] Le caractère ponctuel ou récurrent est précisé
- [ ] Les actions éventuelles sont mentionnées

### Anti-patterns à éviter

| Anti-pattern | Exemple | Correction |
|-------------|---------|------------|
| Narratif vague | "Les charges ont augmenté" | "Les charges de sous-traitance ont augmenté de 45K€ (+18%) suite au projet de migration IT lancé en mars" |
| Tautologie | "L'écart est dû à la hausse des charges" | Expliquer POURQUOI les charges ont augmenté |
| Pas de montant | "Le CA a baissé à cause du client X" | "Le CA a baissé de 120K€ dont 95K€ liés à la fin du contrat client X" |
| Attributions non vérifiées | "L'écart est probablement dû à..." | Vérifier avant d'affirmer, ou dire clairement "à confirmer" |

## Méthodologie en cascade (Waterfall)

### Principe

Le waterfall montre la construction de l'écart total, facteur par facteur, depuis la valeur de référence jusqu'à la valeur réelle.

### Format texte

```
[Valeur de référence]                           X XXX €
  |
  |--[+] Facteur 1                            +X XXX €
  |--[+] Facteur 2                            +X XXX €
  |--[-] Facteur 3                            -X XXX €
  |--[+] Facteur 4                            +X XXX €
  |
[Valeur réelle]                                 X XXX €
```

### Bonnes pratiques du waterfall

1. **Ordonnancement** : Du plus grand au plus petit écart (en valeur absolue)
2. **Consolidation** : Regrouper les petits écarts dans un poste "Autres" si > 5 lignes
3. **Vérification** : La somme des facteurs doit être exactement égale à l'écart total
4. **Résiduel** : Si un résidu inexpliqué existe, l'afficher explicitement
5. **Sens** : Distinguer clairement favorable [+] de défavorable [-]

## Comparaisons Budget vs Réel vs Forecast

### Quand utiliser chaque référence

| Comparaison | Objectif | Fréquence |
|-------------|----------|-----------|
| Réel vs Budget | Mesurer la performance vs le plan annuel validé | Mensuelle |
| Réel vs Forecast | Mesurer la performance vs les attentes récentes | Mensuelle |
| Réel vs N-1 | Mesurer la croissance / évolution | Mensuelle et annuelle |
| Budget vs Forecast | Évaluer la pertinence du plan initial | Trimestrielle |
| Réel vs Réel séquentiel | Détecter les tendances de court terme | Mensuelle |

### Analyse de la précision des prévisions

Calculer la précision du forecast avec le MAPE (Mean Absolute Percentage Error) :

```
MAPE = (1/n) × Σ |Réel - Forecast| / |Réel| × 100
```

| MAPE | Qualité de la prévision |
|------|------------------------|
| < 5% | Excellente |
| 5-10% | Bonne |
| 10-20% | Acceptable |
| > 20% | À améliorer |

### Identification des biais systématiques

Analyser si le forecast est systématiquement :
- **Optimiste** : Réel régulièrement en dessous du forecast (surestimation)
- **Pessimiste** : Réel régulièrement au-dessus du forecast (sous-estimation)
- **Précis mais volatile** : MAPE acceptable mais forte dispersion

Un biais systématique suggère un problème de méthodologie de prévision à corriger.
