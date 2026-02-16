---
description: Préparer des écritures comptables PCG avec comptes, TVA, débits/crédits et pièces justificatives
argument-hint: "<type> [période]"
---

# Écriture Comptable

> Pour comprendre les placeholders ou vérifier les outils connectés, voir [CONNECTORS.md](../CONNECTORS.md).

**Important** : Cette commande assiste dans la préparation d'écritures comptables mais ne constitue pas un conseil comptable ou fiscal. Toutes les écritures doivent être vérifiées par un expert-comptable ou un professionnel qualifié avant comptabilisation.

Préparer des écritures comptables conformes au Plan Comptable Général avec comptes PCG, traitement de la TVA, débits/crédits équilibrés et documentation justificative.

## Utilisation

```
/ecriture <type> <période>
```

### Arguments

- `type` — Le type d'écriture comptable :
  - `achat` — Factures fournisseurs avec TVA déductible (comptes 401, 6xx, 44566)
  - `vente` — Factures clients avec TVA collectée (comptes 411, 7xx, 44571)
  - `salaire` — Écritures de paie : salaires bruts, cotisations, net à payer (comptes 641, 645, 421, 431, 437)
  - `immobilisation` — Dotations aux amortissements et dépréciations (comptes 681, 28x)
  - `od` — Opérations diverses : CCA, PCA, provisions, régularisations (comptes divers)
  - `tva` — Écriture de liquidation de TVA : centralisation et solde (comptes 4455x, 44566, 44571)
- `période` — La période comptable (ex. `2025-01`, `2025-Q1`, `2024`)

## Workflow

### 1. Collecter les données sources

Si ~~erp ou ~~data warehouse est connecté :
- Extraire la balance générale pour la période spécifiée
- Extraire le détail des comptes auxiliaires concernés
- Extraire les écritures du même type de la période précédente pour référence
- Identifier les soldes actuels des comptes affectés

Si aucune source de données n'est connectée :
> Connectez ~~erp ou ~~data warehouse pour extraire les données automatiquement. Vous pouvez aussi coller les données de la balance, charger un fichier Excel/CSV ou fournir les éléments manuellement.

Demander à l'utilisateur de fournir :
- La balance générale ou les soldes des comptes concernés
- Le détail des pièces justificatives (factures, bulletins de paie, tableaux d'amortissement)
- Les écritures de la période précédente pour référence (optionnel)

### 2. Calculer l'écriture

Selon le type d'écriture :

**Achat (facture fournisseur) :**
- Identifier le montant HT et le taux de TVA applicable (20%, 10%, 5,5%, 2,1%)
- Calculer la TVA déductible
- Débit : Compte de charge 6xx (selon nature : 601 achats stockés, 602 achats non stockés, 604 achats d'études, 606 matières et fournitures, 61x services extérieurs, 62x autres services)
- Débit : 44566 TVA déductible sur ABS (ou 44562 sur immobilisations)
- Crédit : 401x Fournisseur (montant TTC)

**Vente (facture client) :**
- Identifier le montant HT et le taux de TVA applicable
- Calculer la TVA collectée
- Débit : 411x Client (montant TTC)
- Crédit : Compte de produit 7xx (701 ventes de produits finis, 706 prestations de services, 707 ventes de marchandises)
- Crédit : 44571 TVA collectée

**Salaire (écriture de paie) :**
- Calculer à partir du bulletin de paie ou du journal de paie
- Débit : 641 Rémunérations du personnel (salaire brut)
- Débit : 645 Charges de sécurité sociale et de prévoyance (cotisations patronales)
- Crédit : 421 Personnel — rémunérations dues (salaire net)
- Crédit : 431 Sécurité sociale (cotisations salariales + patronales URSSAF)
- Crédit : 437 Autres organismes sociaux (retraite complémentaire, prévoyance)
- Crédit : 4421 Prélèvement à la source (PAS)

**Immobilisation (amortissement) :**
- Extraire le tableau d'amortissement du registre des immobilisations
- Calculer la dotation de la période par immobilisation et méthode (linéaire, dégressif fiscal)
- Débit : 6811 Dotations aux amortissements des immobilisations incorporelles et corporelles
- Crédit : 28x Amortissements des immobilisations (280 incorporelles, 281 corporelles)

**OD (opérations diverses) :**
- Charges constatées d'avance (CCA) : Débit 486 / Crédit 6xx
- Produits constatés d'avance (PCA) : Débit 7xx / Crédit 487
- Provisions pour risques et charges : Débit 681x / Crédit 15x
- Factures non parvenues (FNP) : Débit 6xx / Crédit 4081
- Produits à recevoir : Débit 4181 ou 4687 / Crédit 7xx
- Régularisation de charges : Débit/Crédit selon le sens

**TVA (liquidation) :**
- Centraliser la TVA collectée (44571) et la TVA déductible (44566, 44562)
- Calculer TVA collectée - TVA déductible = TVA à payer ou Crédit de TVA
- Si TVA à payer : Débit 44571 / Crédit 44551 TVA à décaisser
- Si crédit de TVA : Débit 44567 Crédit de TVA / Crédit 44566
- Solder les comptes de TVA du mois

### 3. Générer l'écriture comptable

Présenter l'écriture au format standard :

```
ÉCRITURE COMPTABLE : [Type] — [Période]
Préparée par : [Utilisateur]
Date : [Date de fin de période]
Journal : [ACH / VTE / BQ / OD / SAL / AN]
N° pièce : [Référence]

| Ligne | Compte | Libellé du compte        | Libellé écriture      | Débit      | Crédit     |
|-------|--------|--------------------------|----------------------|------------|------------|
| 1     | 6XXX   | [Nom du compte]          | [Détail]             | X XXX,XX   |            |
| 2     | 44566  | TVA déductible sur ABS   | [Détail]             | X XXX,XX   |            |
| 3     | 401X   | Fournisseur [Nom]        | [Détail]             |            | X XXX,XX   |
|       |        |                          | **Total**            | X XXX,XX   | X XXX,XX   |

Pièce justificative :
- [Nature et référence de la pièce (facture n°, bulletin n°, tableau)]
- [Base de calcul et hypothèses]

Extourne : [Oui/Non — si oui, date d'extourne]
```

### 4. Checklist de contrôle

Avant validation, vérifier :

- [ ] Débits = Crédits (écriture équilibrée)
- [ ] Période comptable correcte (pas d'écriture sur période clôturée)
- [ ] Comptes PCG valides et adaptés à la nature de l'opération
- [ ] Taux de TVA correct (20%, 10%, 5,5%, 2,1% ou exonéré)
- [ ] Montants calculés correctement avec pièce justificative
- [ ] Libellé clair et suffisamment détaillé pour le contrôle fiscal (FEC)
- [ ] Journal comptable correct (ACH, VTE, BQ, OD, SAL, AN)
- [ ] Numéro de pièce renseigné et traçable
- [ ] Cohérence avec le traitement de la période précédente
- [ ] Extourne programmée si nécessaire (provisions, CCA, PCA)
- [ ] Pas de doublon avec une écriture existante
- [ ] Conforme aux exigences du FEC (Fichier des Écritures Comptables)

### 5. Sortie

Fournir :
1. L'écriture comptable formatée avec comptes PCG
2. Les calculs justificatifs détaillés
3. La comparaison avec l'écriture du même type de la période précédente (si disponible)
4. Les éléments signalés pour revue ou suivi
5. Les instructions de saisie (saisie manuelle ou format d'import pour le logiciel comptable)
