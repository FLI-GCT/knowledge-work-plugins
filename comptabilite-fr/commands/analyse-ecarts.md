---
description: Décomposer les variances en facteurs explicatifs avec narratifs et analyse en cascade
argument-hint: "<domaine> <période> vs <comparaison>"
---

# Analyse des Écarts

> Pour comprendre les placeholders ou vérifier les outils connectés, voir [CONNECTORS.md](../CONNECTORS.md).

**Important** : Cette commande assiste dans l'analyse des écarts mais ne constitue pas un conseil financier. Toutes les analyses doivent être vérifiées par un professionnel qualifié avant utilisation dans le reporting.

Décomposer les écarts entre deux périodes ou par rapport au budget, fournir des explications narratives pour les variances significatives, et générer une analyse en cascade (waterfall).

## Utilisation

```
/analyse-ecarts <domaine> <comparaison>
```

### Arguments

- `domaine` — Le domaine à analyser :
  - `ca` — Chiffre d'affaires par produit, service, client, zone géographique
  - `charges` — Charges d'exploitation par nature (achats, services extérieurs, etc.)
  - `masse-salariale` — Charges de personnel par catégorie, département
  - `marge` — Marge commerciale et valeur ajoutée avec effets mix et taux
  - `ebe` — Excédent brut d'exploitation et ses composantes
  - `resultat` — Résultat d'exploitation, financier, exceptionnel
  - Un compte ou groupe de comptes PCG spécifique
- `comparaison` — Les périodes à comparer :
  - `2025-01 vs 2024-12` — Mois sur mois
  - `2025-01 vs 2024-01` — Année sur année
  - `2025-Q1 vs 2024-Q4` — Trimestre sur trimestre
  - `2025-01 vs budget` — Réel vs budget
  - `2025-01 vs forecast` — Réel vs prévision
  - `2025-Q1 vs 2024-Q4 vs 2024-Q1` — Comparaison à trois voies

## Workflow

### 1. Collecter les données

Si ~~erp ou ~~data warehouse est connecté :
- Extraire les données réelles des deux périodes au niveau détaillé
- Extraire les données de budget/prévision si comparaison au plan
- Extraire les métriques opérationnelles (effectifs, volumes, tarifs)
- Extraire les analyses de variance antérieures pour le contexte

Si aucune source de données n'est connectée :
> Connectez ~~erp ou ~~data warehouse pour extraire les données automatiquement. Pour analyser manuellement, fournissez :
> 1. Les données réelles des deux périodes comparées (au détail par compte ou par ligne)
> 2. Les données de budget/prévision (si comparaison au plan)
> 3. Les métriques opérationnelles pertinentes (effectifs, volumes, tarifs, etc.)

### 2. Calculer l'écart global

```
SYNTHÈSE DES ÉCARTS : [Domaine] — [Période 1] vs [Période 2]

                              Période 1   Période 2   Écart (€)    Écart (%)
                              ---------   ---------   ---------    ---------
Total [Domaine]               X XXX €     X XXX €     X XXX €      X,X%
```

### 3. Décomposer l'écart par facteur

Ventiler l'écart total en facteurs explicatifs selon le domaine :

**Chiffre d'affaires :**
- **Effet volume :** Variation des quantités/clients/transactions au tarif N-1
- **Effet prix :** Variation des tarifs appliquée au volume courant
- **Effet mix :** Glissement entre produits/segments à marges différentes
- **Nouveau vs existant :** CA de nouveaux clients/produits vs base existante
- **Effet devise :** Impact des taux de change (si applicable)

**Charges d'exploitation :**
- **Effet volume :** Charges variables liées à l'activité (achats, sous-traitance)
- **Effet prix :** Variation des coûts unitaires (matières, énergie, loyers)
- **Charges fixes :** Variation des charges structurelles
- **Éléments non récurrents :** Charges exceptionnelles, contentieux, amendes
- **Effet timing :** Décalages de comptabilisation entre périodes

**Masse salariale :**
- **Effet effectif :** Variation du nombre d'ETP (embauches, départs)
- **Effet coût moyen :** Augmentations, primes, ancienneté
- **Effet structure :** Changement de mix entre catégories
- **Heures supplémentaires / intérim :** Variations de flexibilité
- **Charges sociales :** Variation des taux de cotisation

**Marge commerciale / Valeur ajoutée :**
- **Effet mix produit :** Glissement entre produits à marges différentes
- **Effet taux de marge :** Variation du taux de marge à mix constant
- **Effet volume :** Variation du volume d'activité

### 4. Analyse en cascade (waterfall)

```
CASCADE : [Domaine] — [Période 1] vs [Période 2]

[Base Période 2]                              X XXX €
  |
  |--[+] [Facteur 1 description]            +X XXX €
  |--[+] [Facteur 2 description]            +X XXX €
  |--[-] [Facteur 3 description]            -X XXX €
  |--[+] [Facteur 4 description]            +X XXX €
  |--[-] [Facteur 5 description]            -X XXX €
  |
[Réel Période 1]                              X XXX €

Réconciliation des écarts :
  Facteur 1 :    +X XXX €  (XX% de l'écart total)
  Facteur 2 :    +X XXX €  (XX% de l'écart total)
  Facteur 3 :    -X XXX €  (XX% de l'écart total)
  Facteur 4 :    +X XXX €  (XX% de l'écart total)
  Facteur 5 :    -X XXX €  (XX% de l'écart total)
  Inexpliqué :    X XXX €  (XX% de l'écart total)
                 ---------
  Total :         X XXX €  (100%)
```

### 5. Narratifs explicatifs

Pour chaque facteur significatif, générer un narratif :

> **[Nom du facteur]** — Écart [favorable/défavorable] de X XXX € (X,X%)
>
> [2-3 phrases d'explication de la cause, avec références aux facteurs opérationnels, événements business ou décisions. Inclure la quantification lorsque possible.]
>
> *Perspective :* [Indique si l'écart est attendu à se reproduire, s'inverser ou évoluer dans les périodes futures]

### 6. Identifier les écarts inexpliqués

Si la décomposition n'explique pas totalement l'écart, signaler le résidu :

> **Écart inexpliqué :** X XXX € (X,X% du total)
>
> Causes possibles à investiguer :
> - [Suggestion 1]
> - [Suggestion 2]
> - [Suggestion 3]

Demander à l'utilisateur le contexte manquant sur les écarts inexpliqués.

### 7. Sortie

Fournir :
1. Synthèse de l'écart global
2. Décomposition détaillée par facteur
3. Analyse en cascade (format texte)
4. Narratifs explicatifs pour chaque facteur significatif
5. Écarts inexpliqués avec suggestions d'investigation
6. Contexte de tendance (écart nouveau, croissant, ou récurrent ?)
7. Actions de suivi suggérées
