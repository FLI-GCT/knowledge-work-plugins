---
name: ecritures-pcg
description: Préparer des écritures comptables conformes au Plan Comptable Général avec schémas de comptes, traitement de la TVA et documentation. Utiliser pour comptabiliser des achats, ventes, salaires, amortissements, opérations diverses, ou toute écriture manuelle au journal.
---

# Écritures Comptables PCG

**Important** : Ce skill assiste dans la préparation d'écritures comptables mais ne constitue pas un conseil comptable ou fiscal. Toutes les écritures doivent être vérifiées par un expert-comptable avant comptabilisation.

Schémas d'écritures conformes au Plan Comptable Général, traitement de la TVA, documentation justificative et contrôles de qualité.

## Le Plan Comptable Général — Structure des classes

### Vue d'ensemble des 8 classes

| Classe | Intitulé | Nature | Bilan/Résultat |
|--------|----------|--------|----------------|
| 1 | Comptes de capitaux | Capitaux propres, emprunts, provisions | Bilan — Passif |
| 2 | Comptes d'immobilisations | Actifs non courants et amortissements | Bilan — Actif |
| 3 | Comptes de stocks et en-cours | Matières, marchandises, produits | Bilan — Actif |
| 4 | Comptes de tiers | Fournisseurs, clients, État, personnel | Bilan — Actif/Passif |
| 5 | Comptes financiers | Banque, caisse, VMP | Bilan — Actif |
| 6 | Comptes de charges | Charges d'exploitation, financières, exceptionnelles | Résultat — Charges |
| 7 | Comptes de produits | Produits d'exploitation, financiers, exceptionnels | Résultat — Produits |
| 8 | Comptes spéciaux | Engagements hors bilan | Hors bilan |

### Comptes les plus utilisés

**Classe 4 — Tiers :**
- 401 Fournisseurs
- 4081 Fournisseurs — factures non parvenues
- 411 Clients
- 4181 Clients — produits non encore facturés
- 421 Personnel — rémunérations dues
- 431 Sécurité sociale
- 437 Autres organismes sociaux
- 4421 Prélèvement à la source
- 44566 TVA déductible sur autres biens et services
- 44562 TVA déductible sur immobilisations
- 44571 TVA collectée
- 44551 TVA à décaisser
- 44567 Crédit de TVA à reporter
- 4452 TVA due intracommunautaire
- 445 État — Taxes sur le chiffre d'affaires

**Classe 5 — Financiers :**
- 512 Banques
- 514 Chèques postaux
- 53 Caisse
- 580 Virements internes

**Classe 6 — Charges :**
- 601 Achats stockés — matières premières
- 602 Achats stockés — autres approvisionnements
- 604 Achats d'études et de prestations
- 606 Achats non stockés de matières et fournitures
- 607 Achats de marchandises
- 611 Sous-traitance générale
- 613 Locations
- 616 Primes d'assurance
- 622 Rémunérations d'intermédiaires et honoraires
- 625 Déplacements, missions et réceptions
- 626 Frais postaux et de télécommunications
- 627 Services bancaires
- 631 Impôts, taxes et versements assimilés sur rémunérations
- 635 Autres impôts, taxes et versements assimilés
- 641 Rémunérations du personnel
- 645 Charges de sécurité sociale et de prévoyance
- 646 Cotisations sociales personnelles de l'exploitant
- 6611 Intérêts des emprunts et dettes
- 657 Valeurs comptables des immobilisations incorporelles et corporelles cédées
- 658 Pénalités et autres charges (6581 pénalités sur marchés, 6582 amendes)
- 681 Dotations aux amortissements, dépréciations et provisions — Exploitation
- 686 Dotations aux amortissements, dépréciations et provisions — Financier
- 687 Dotations aux amortissements, dépréciations et provisions — Exceptionnel

**Classe 7 — Produits :**
- 701 Ventes de produits finis
- 706 Prestations de services
- 707 Ventes de marchandises
- 708 Produits des activités annexes
- 713 Variation des stocks
- 721 Production immobilisée — immobilisations incorporelles
- 722 Production immobilisée — immobilisations corporelles
- 741 Subventions d'exploitation
- 747 Quote-part des subventions d'investissement virée au résultat
- 757 Produits des cessions d'immobilisations incorporelles et corporelles
- 781 Reprises sur amortissements, dépréciations et provisions — Exploitation
- 786 Reprises — Financier
- 787 Reprises — Exceptionnel

## Schémas d'écritures courantes

### Facture d'achat avec TVA

```
Débit  6xx    Charge selon nature          Montant HT
Débit  44566  TVA déductible sur ABS       Montant TVA
Crédit 401    Fournisseur                  Montant TTC
```

Variantes :
- Si immobilisation : Débit 2xx + 44562 / Crédit 404
- Si facture non parvenue : Crédit 4081 au lieu de 401
- Si intracommunautaire : Débit 44566 + Crédit 4452 (autoliquidation)

### Facture de vente avec TVA

```
Débit  411    Client                       Montant TTC
Crédit 7xx    Produit selon nature         Montant HT
Crédit 44571  TVA collectée                Montant TVA
```

### Règlement fournisseur

```
Débit  401    Fournisseur                  Montant
Crédit 512    Banque                       Montant
```

### Encaissement client

```
Débit  512    Banque                       Montant
Crédit 411    Client                       Montant
```

### Écriture de paie (simplifiée)

```
Débit  641    Rémunérations du personnel   Salaire brut
Débit  645    Charges de sécurité sociale  Cotisations patronales
Crédit 421    Personnel — rémunérations dues   Salaire net
Crédit 431    Sécurité sociale             Cotisations salariales + patronales URSSAF
Crédit 437    Autres organismes sociaux    Retraite complémentaire + prévoyance
Crédit 4421   Prélèvement à la source     PAS
```

### Dotation aux amortissements

```
Débit  6811   Dotation amortissements exploitation    Montant
Crédit 28x    Amortissements des immobilisations      Montant
```

Méthodes d'amortissement :
- **Linéaire** : Dotation = (Valeur d'origine - Valeur résiduelle) / Durée d'utilité
- **Dégressif fiscal** : Coefficient fiscal × taux linéaire (1,25 pour 3-4 ans, 1,75 pour 5-6 ans, 2,25 pour >6 ans)
- **Par composants** : Amortissement séparé de chaque composant identifié (obligatoire PCG)

### Opérations d'inventaire

**Charges constatées d'avance (CCA) :**
```
Débit  486    Charges constatées d'avance   Montant
Crédit 6xx    Charge                        Montant
```
Extourne en début de période suivante.

**Produits constatés d'avance (PCA) :**
```
Débit  7xx    Produit                       Montant
Crédit 487    Produits constatés d'avance   Montant
```

**Factures non parvenues (FNP) :**
```
Débit  6xx    Charge                        Montant HT
Débit  44586  TVA — Factures non parvenues  Montant TVA
Crédit 4081   Fournisseurs — FNP            Montant TTC
```

**Provision pour risques et charges :**
```
Débit  6815   Dotation provisions exploitation   Montant
Crédit 15x    Provisions pour risques/charges    Montant
```

## Traitement de la TVA

### Taux de TVA applicables en France métropolitaine

| Taux | Compte collectée | Compte déductible | Application |
|------|-----------------|-------------------|-------------|
| 20% (normal) | 44571 | 44566 | Majorité des biens et services |
| 10% (intermédiaire) | 44571 | 44566 | Restauration, transport, travaux habitation, hébergement |
| 5,5% (réduit) | 44571 | 44566 | Alimentation, livres, énergie, travaux rénovation énergétique |
| 2,1% (super-réduit) | 44571 | 44566 | Presse, médicaments remboursés, spectacles vivants (certains) |

### TVA intracommunautaire (autoliquidation)

```
Débit  6xx    Charge                          Montant HT
Débit  4452   TVA due intracommunautaire      Montant TVA (calculé)
Crédit 401    Fournisseur intracommunautaire  Montant HT (facture sans TVA)
Crédit 4452   TVA due intracommunautaire      Montant TVA (neutralisation)
```

L'autoliquidation rend l'opération neutre en TVA pour l'acheteur (la TVA est à la fois collectée et déduite).

### TVA non déductible

Certaines dépenses ont une TVA non déductible ou partiellement déductible :
- Véhicules de tourisme : TVA non récupérable
- Gazole véhicules de tourisme : déductible à 80%
- Cadeaux d'affaires > 73 € TTC : TVA non récupérable
- Services liés à des biens exclus : TVA non récupérable

Dans ces cas, la TVA non déductible est intégrée au coût de la charge.

## Fichier des Écritures Comptables (FEC)

### Obligation légale

Toute entreprise soumise à un régime réel d'imposition doit pouvoir fournir un FEC en cas de contrôle fiscal (article L.47 A du LPF).

### Format obligatoire du FEC

| Champ | Description | Obligatoire |
|-------|-------------|:-----------:|
| JournalCode | Code du journal | Oui |
| JournalLib | Libellé du journal | Oui |
| EcritureNum | Numéro de l'écriture | Oui |
| EcritureDate | Date de l'écriture (YYYYMMDD) | Oui |
| CompteNum | Numéro de compte | Oui |
| CompteLib | Libellé du compte | Oui |
| CompAuxNum | Numéro de compte auxiliaire | Si applicable |
| CompAuxLib | Libellé du compte auxiliaire | Si applicable |
| PieceRef | Référence de la pièce | Oui |
| PieceDate | Date de la pièce | Oui |
| EcritureLib | Libellé de l'écriture | Oui |
| Debit | Montant au débit | Oui |
| Credit | Montant au crédit | Oui |
| EcrtureLet | Lettre de lettrage | Si applicable |
| DateLet | Date de lettrage | Si applicable |
| ValidDate | Date de validation | Oui |
| Montantdevise | Montant en devise | Si applicable |
| Idevise | Identifiant de la devise | Si applicable |

### Règles de conformité FEC

- Numérotation continue et chronologique des écritures
- Pas d'écriture antidatée (sauf extournes justifiées)
- Libellés suffisamment détaillés pour identifier l'opération
- Référence de pièce justificative obligatoire
- Date de validation renseignée (irréversibilité)

## Documentation et pièces justificatives

Chaque écriture doit comporter :

1. **Libellé explicite** : Nature de l'opération identifiable
2. **Référence pièce** : N° de facture, bulletin, contrat, délibération
3. **Date pièce** : Date du fait générateur
4. **Calcul justificatif** : Base de calcul pour les estimations (amortissements, provisions)
5. **Journal** : Code journal adapté (ACH, VTE, BQ, OD, SAL, AN, etc.)

### Journaux comptables courants

| Code | Libellé | Utilisation |
|------|---------|-------------|
| ACH | Achats | Factures fournisseurs |
| VTE | Ventes | Factures clients |
| BQ / BQ1 | Banque | Mouvements bancaires |
| CA | Caisse | Mouvements d'espèces |
| OD | Opérations diverses | Provisions, régularisations, OD |
| SAL | Salaires | Écritures de paie |
| AN | À nouveau | Reprise des à-nouveaux |
| EXT | Extournes | Contre-passation d'écritures |

## Erreurs courantes à vérifier

1. **Écriture déséquilibrée** : Total débits ≠ Total crédits
2. **Mauvais compte PCG** : Compte inexistant ou inadapté à la nature
3. **TVA incorrecte** : Mauvais taux, TVA sur une opération exonérée, TVA non déductible comptabilisée
4. **Absence de pièce** : Écriture sans référence de pièce justificative
5. **Mauvaise période** : Fait générateur d'une autre période (cut-off)
6. **Doublon** : Même facture comptabilisée deux fois
7. **Sens inversé** : Débit au lieu de crédit ou inversement
8. **Extourne oubliée** : CCA/PCA/FNP de la période précédente non extournées
9. **Lettrage incorrect** : Mauvais rapprochement entre facture et règlement
10. **Non-conformité FEC** : Libellé vide, numérotation cassée, date de validation absente
