---
description: Préparer la déclaration de TVA (CA3 mensuelle ou CA12 annuelle) avec rapprochement du chiffre d'affaires
argument-hint: "<type> <période>"
---

# Déclaration de TVA

> Pour comprendre les placeholders ou vérifier les outils connectés, voir [CONNECTORS.md](../CONNECTORS.md).

**Important** : Cette commande assiste dans la préparation des déclarations de TVA mais ne constitue pas un conseil fiscal. Toutes les déclarations doivent être vérifiées par un expert-comptable ou un professionnel qualifié avant télétransmission.

Préparer la déclaration de TVA (CA3 mensuelle ou CA12 annuelle simplifiée), rapprocher le chiffre d'affaires déclaré avec la comptabilité, et générer l'écriture de liquidation.

## Utilisation

```
/declaration-tva <type> <période>
```

### Arguments

- `type` — Le type de déclaration :
  - `ca3` — Déclaration mensuelle (régime réel normal) ou trimestrielle (si option)
  - `ca12` — Déclaration annuelle simplifiée (régime réel simplifié)
  - `mini-reel` — Mini-réel (TVA au réel normal, IS/BIC au simplifié)
- `période` — La période déclarative (ex. `2025-01` pour le CA3 de janvier, `2024` pour le CA12 annuel)

## Workflow

### 1. Collecter les données de TVA

Si ~~erp ou ~~data warehouse est connecté :
- Extraire les soldes des comptes de TVA collectée (44571x) pour la période
- Extraire les soldes des comptes de TVA déductible (44566x, 44562x) pour la période
- Extraire le chiffre d'affaires par taux de TVA (comptes 70x)
- Extraire les acquisitions intracommunautaires (comptes 4452x)
- Extraire le crédit de TVA antérieur (compte 44567) s'il existe

Si aucune source de données n'est connectée :
> Connectez ~~erp ou ~~data warehouse pour extraire les données automatiquement. Pour préparer manuellement, fournissez :
> 1. Le détail de la TVA collectée par taux (20%, 10%, 5,5%, 2,1%)
> 2. Le détail de la TVA déductible (sur biens/services et sur immobilisations)
> 3. Le chiffre d'affaires HT par taux de TVA
> 4. Les opérations intracommunautaires et exportations
> 5. Le crédit de TVA de la période précédente (si applicable)

### 2. Préparer le CA3

Remplir les lignes du formulaire CA3 :

```
DÉCLARATION DE TVA — CA3
Période : [Mois/Année]
Régime : Réel normal

═══════════════════════════════════════════════════════════════
A — MONTANT DES OPÉRATIONS RÉALISÉES
═══════════════════════════════════════════════════════════════

OPÉRATIONS IMPOSABLES (HT)
  Ligne 01 — Ventes, prestations de services              X XXX €
  Ligne 02 — Autres opérations imposables                 X XXX €
  Ligne 03 — Acquisitions intracommunautaires              X XXX €
  Ligne 3A — Achats de prestations de services intra-UE    X XXX €
  Ligne 3B — Importations (hors UE)                        X XXX €

OPÉRATIONS NON IMPOSABLES
  Ligne 04 — Exportations hors UE                          X XXX €
  Ligne 05 — Autres opérations non imposables              X XXX €
  Ligne 06 — Livraisons intracommunautaires                X XXX €
  Ligne 6A — Ventes de prestations intra-UE art. 283-2     X XXX €

═══════════════════════════════════════════════════════════════
B — DÉCOMPTE DE LA TVA À PAYER
═══════════════════════════════════════════════════════════════

TVA BRUTE
  Ligne 08 — Taux normal 20%        Base : X XXX €    TVA : X XXX €
  Ligne 09 — Taux réduit 5,5%       Base : X XXX €    TVA : X XXX €
  Ligne 09B — Taux réduit 10%       Base : X XXX €    TVA : X XXX €
  Ligne 10 — Taux particulier 2,1%  Base : X XXX €    TVA : X XXX €
  Ligne 11 — Acquisitions intra.    Base : X XXX €    TVA : X XXX €
  Ligne 14 — TVA antérieurement déduite à reverser                  X XXX €
  Ligne 15 — TVA due sur régularisations                            X XXX €
  Ligne 16 — TOTAL TVA BRUTE                                        X XXX €

TVA DÉDUCTIBLE
  Ligne 19 — Biens et services                                      X XXX €
  Ligne 20 — Autres biens (immobilisations)                         X XXX €
  Ligne 21 — Autre TVA à déduire                                    X XXX €
  Ligne 22 — Report crédit de TVA antérieur                         X XXX €
  Ligne 23 — TOTAL TVA DÉDUCTIBLE                                   X XXX €

═══════════════════════════════════════════════════════════════
C — RÉSULTAT
═══════════════════════════════════════════════════════════════

  Ligne 25 — CRÉDIT DE TVA (si L23 > L16)                           X XXX €
  Ligne 28 — TVA NETTE DUE (si L16 > L23)                           X XXX €
  Ligne 29 — Taxes assimilées (si applicable)                        X XXX €
  Ligne 32 — TOTAL À PAYER                                          X XXX €
```

### 3. Rapprochement CA déclaré / CA comptable

Vérifier la cohérence entre le CA déclaré et la comptabilité :

```
RAPPROCHEMENT DU CHIFFRE D'AFFAIRES

CA comptable (comptes 70x) :                            X XXX,XX €

Ajustements :
  + Produits imposables hors classe 7                    X XXX,XX €
  - Produits exonérés / hors champ inclus en classe 7   (X XXX,XX €)
  + Acquisitions intracommunautaires (ligne 03)           X XXX,XX €
  +/- Régularisations de CA                              X XXX,XX €
                                                        -----------
CA déclaré reconstitué :                                X XXX,XX €

CA total figurant sur le CA3 (somme L01+L02+L04+L05+L06) : X XXX,XX €

ÉCART :                                                  0,00 €
```

Si un écart existe, investiguer les causes :
- Décalage de comptabilisation (factures à cheval)
- Opérations hors champ non retraitées
- Erreurs de classification de comptes
- Avoirs ou ristournes non pris en compte

### 4. Générer l'écriture de liquidation

```
ÉCRITURE DE LIQUIDATION DE TVA — [Période]
Journal : OD
Date : [Dernier jour de la période]

Si TVA à payer :
| Compte | Libellé                          | Débit      | Crédit     |
|--------|----------------------------------|------------|------------|
| 44571  | TVA collectée                    | X XXX,XX   |            |
| 4452x  | TVA intra / autoliquidation      | X XXX,XX   |            |
| 44566  | TVA déductible sur ABS           |            | X XXX,XX   |
| 44562  | TVA déductible sur immobilisations|            | X XXX,XX   |
| 44551  | TVA à décaisser                  |            | X XXX,XX   |
|        | **Total**                        | X XXX,XX   | X XXX,XX   |

Si crédit de TVA :
| Compte | Libellé                          | Débit      | Crédit     |
|--------|----------------------------------|------------|------------|
| 44571  | TVA collectée                    | X XXX,XX   |            |
| 44567  | Crédit de TVA                    | X XXX,XX   |            |
| 44566  | TVA déductible sur ABS           |            | X XXX,XX   |
|        | **Total**                        | X XXX,XX   | X XXX,XX   |
```

### 5. Points de contrôle

Avant validation de la déclaration :

- [ ] Le CA déclaré est rapproché du CA comptable (écart nul ou expliqué)
- [ ] Les taux de TVA appliqués sont corrects pour chaque catégorie d'opération
- [ ] La TVA intracommunautaire est correctement autoliquidée (collectée ET déduite)
- [ ] Le crédit de TVA antérieur est correctement reporté
- [ ] Les exportations et livraisons intracommunautaires sont bien en opérations non imposables
- [ ] La TVA déductible respecte les règles de déductibilité (prorata si applicable)
- [ ] Les régularisations sont correctement identifiées et documentées
- [ ] La date limite de dépôt est respectée (entre le 15 et le 24 du mois suivant selon le cas)
- [ ] L'écriture de liquidation est équilibrée et soldant les comptes de TVA du mois

### 6. Sortie

Fournir :
1. Le CA3 ou CA12 pré-rempli avec toutes les lignes
2. Le rapprochement CA déclaré / CA comptable avec détail des écarts
3. L'écriture de liquidation de TVA prête à comptabiliser
4. Les points d'attention et anomalies détectées
5. Le rappel de la date limite de dépôt
6. La proposition de comparer avec la déclaration de la période précédente
