---
description: Rapprocher les soldes bancaires, lettrer les comptes tiers et identifier les écarts
argument-hint: "<compte> [période]"
---

# Rapprochement Bancaire et Lettrage

> Pour comprendre les placeholders ou vérifier les outils connectés, voir [CONNECTORS.md](../CONNECTORS.md).

**Important** : Cette commande assiste dans les travaux de rapprochement mais ne constitue pas un conseil comptable. Tous les rapprochements doivent être vérifiés par un professionnel qualifié avant validation.

Rapprocher les soldes du grand livre avec les relevés bancaires, les comptes auxiliaires ou les confirmations de tiers. Identifier et catégoriser les écarts, générer un état de rapprochement.

## Utilisation

```
/rapprochement <compte> <période>
```

### Arguments

- `compte` — Le compte ou la catégorie à rapprocher :
  - `banque` ou `512` — Rapprochement bancaire (GL 512 vs relevé bancaire)
  - `client` ou `411` — Lettrage des comptes clients (auxiliaire vs balance âgée)
  - `fournisseur` ou `401` — Lettrage des comptes fournisseurs
  - `intercompagnie` ou `ic` — Rapprochement des soldes intercompagnies
  - `tva` — Rapprochement des comptes de TVA (445xx vs déclaration CA3)
  - `social` ou `43` — Rapprochement des organismes sociaux (URSSAF, caisses)
  - Un code de compte GL spécifique (ex. `467`, `164`)
- `période` — La date de fin de période (ex. `2025-01`, `2025-01-31`)

## Workflow

### 1. Collecter les deux côtés du rapprochement

Si ~~erp ou ~~banque est connecté :
- Extraire le solde du grand livre pour le(s) compte(s) spécifié(s) à la date de fin de période
- Extraire le relevé bancaire, le solde auxiliaire ou la confirmation du tiers
- Extraire le rapprochement de la période précédente (si disponible) pour le report des éléments en suspens

Si aucune source de données n'est connectée :
> Connectez ~~erp ou ~~banque pour extraire les soldes automatiquement. Pour rapprocher manuellement, fournissez :
> 1. **Côté comptable :** Le solde du grand livre pour le compte à la date de fin de période
> 2. **Côté externe :** Le solde du relevé bancaire, du tiers ou de l'organisme
> 3. **Éléments antérieurs en suspens** (optionnel) : Les écarts du rapprochement précédent

### 2. Comparer les soldes

Calculer la différence entre les deux côtés :

```
Solde comptable (Grand Livre) :      X XXX,XX €
Solde externe (Banque/Tiers) :       X XXX,XX €
                                     ----------
Différence :                         X XXX,XX €
```

### 3. Identifier les éléments de rapprochement

Analyser la différence et catégoriser les écarts :

**Décalages de comptabilisation (éléments en transit) :**
- Chèques émis non encore débités par la banque
- Remises de chèques ou virements en cours d'encaissement
- Prélèvements ou virements comptabilisés par la banque, pas encore en comptabilité
- Factures enregistrées dans un système mais en attente dans l'autre

**Écarts nécessitant correction :**
- Erreurs de saisie (montant, sens, doublon)
- Écritures manquantes (frais bancaires, agios, commissions non comptabilisés)
- Écarts de change sur opérations en devises
- Rejets d'opérations non régularisés

**Éléments reportés de la période précédente :**
- Éléments dénoués depuis → retirer du rapprochement
- Éléments toujours en suspens → reporter avec vieillissement

### 4. Générer l'état de rapprochement

```
ÉTAT DE RAPPROCHEMENT
Compte : [Code] — [Libellé du compte]
Date de rapprochement : [Date]
Préparé par : [Utilisateur]
Date de préparation : [Aujourd'hui]

RAPPROCHEMENT
=============

Solde comptable (Grand Livre) :                  X XXX,XX €

Opérations comptabilisées en banque, pas en comptabilité :
  + [Description]                                X XXX,XX €
  - [Description]                               (X XXX,XX €)
                                                 ----------
  Sous-total :                                   X XXX,XX €

Solde comptable corrigé :                        X XXX,XX €

Solde du relevé bancaire :                       X XXX,XX €

Opérations comptabilisées en comptabilité, pas en banque :
  + [Description]                                X XXX,XX €
  - [Description]                               (X XXX,XX €)
                                                 ----------
  Sous-total :                                   X XXX,XX €

Solde bancaire corrigé :                         X XXX,XX €

ÉCART RÉSIDUEL :                                 0,00 €
```

### 5. Détail des éléments de rapprochement

Présenter chaque élément avec :

| # | Description | Montant | Catégorie | Âge (jours) | Statut | Action requise |
|---|-------------|---------|-----------|-------------|--------|----------------|
| 1 | [Détail]    | X XXX € | Transit   | 5           | Attendu | Surveiller |
| 2 | [Détail]    | X XXX € | Erreur    | N/A         | À corriger | Passer écriture corrective |
| 3 | [Détail]    | X XXX € | Reporté   | 45          | En suspens | Investiguer |

### 6. Lettrage (pour comptes tiers 411/401)

Si le rapprochement porte sur un compte tiers, effectuer le lettrage :

```
LETTRAGE — Compte [411X / 401X] [Nom du tiers]

| Lettre | Date       | Pièce   | Libellé              | Débit      | Crédit     | Solde      |
|--------|------------|---------|----------------------|------------|------------|------------|
| AA     | 05/01/2025 | FA-001  | Facture client X     | 1 200,00   |            |            |
| AA     | 20/01/2025 | RE-015  | Règlement client X   |            | 1 200,00   | 0,00       |
| AB     | 15/01/2025 | FA-012  | Facture client X     | 3 600,00   |            |            |
|        |            |         | **Non lettré**       |            |            | 3 600,00   |
```

### 7. Revue et escalade

Signaler les éléments nécessitant attention :

- **Éléments anciens :** Écarts en suspens depuis plus de 30/60/90 jours
- **Montants importants :** Éléments individuels dépassant le seuil de matérialité
- **Soldes croissants :** Total des écarts en augmentation d'une période à l'autre
- **Éléments non résolus :** Écarts reportés sans résolution depuis plusieurs périodes
- **Écarts inexpliqués :** Montants qui ne peuvent être rattachés à des opérations identifiées

### 8. Sortie

Fournir :
1. L'état de rapprochement formaté
2. La liste des éléments de rapprochement avec catégorisation et vieillissement
3. Les écritures correctives à passer (si des écarts permanents sont identifiés)
4. Le lettrage des comptes tiers (si applicable)
5. Les actions de suivi pour les éléments en suspens
6. La comparaison avec le rapprochement de la période précédente (si disponible)
