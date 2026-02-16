---
name: fiscalite-francaise
description: Maîtriser la fiscalité d'entreprise française incluant TVA, impôt sur les sociétés, CET, C3S et liasse fiscale. Utiliser pour préparer une déclaration de TVA, calculer l'IS, déterminer le résultat fiscal, préparer la liasse fiscale, ou comprendre le calendrier des obligations déclaratives.
---

# Fiscalité Française d'Entreprise

**Important** : Ce skill assiste dans les travaux de fiscalité mais ne constitue pas un conseil fiscal. Toutes les déclarations et calculs doivent être vérifiés par un expert-comptable ou un avocat fiscaliste avant soumission à l'administration fiscale.

TVA, impôt sur les sociétés, contribution économique territoriale, C3S, liasse fiscale et calendrier des obligations déclaratives.

## Taxe sur la Valeur Ajoutée (TVA)

### Régimes de TVA

| Régime | Critères (services) | Critères (ventes) | Déclaration |
|--------|--------------------|--------------------|-------------|
| **Franchise en base** | CA < 36 800 € | CA < 91 900 € | Pas de TVA |
| **Réel simplifié** | CA < 254 000 € | CA < 840 000 € | CA12 annuelle + acomptes |
| **Réel normal** | CA > seuils simplifiés | CA > seuils simplifiés | CA3 mensuelle |

*Seuils 2025 — vérifier les montants en vigueur.*

### Taux de TVA

| Taux | Application |
|------|-------------|
| **20%** | Taux normal — majorité des biens et services |
| **10%** | Taux intermédiaire — restauration, transport de voyageurs, travaux d'amélioration habitation, hébergement, médicaments non remboursés |
| **5,5%** | Taux réduit — produits alimentaires de première nécessité, livres, abonnements énergie (gaz, électricité), travaux de rénovation énergétique, équipements handicapés |
| **2,1%** | Taux super-réduit — presse inscrite CPPAP, médicaments remboursables, certains spectacles vivants, redevance TV (supprimée) |

### Territorialité

| Opération | Règle de TVA |
|-----------|-------------|
| Vente de biens en France | TVA française collectée |
| Prestation de services en France | TVA française collectée |
| Livraison intracommunautaire (B2B) | Exonérée (avec N° TVA intra du client) |
| Acquisition intracommunautaire (B2B) | Autoliquidation (TVA collectée + déduite) |
| Exportation hors UE | Exonérée |
| Importation hors UE | TVA due à l'importation (autoliquidation depuis 01/2022) |
| Prestation de services intra-UE (B2B) | Autoliquidation par le preneur (art. 283-2 CGI) |

### Déductibilité de la TVA

**Conditions de déductibilité :**
1. La TVA doit figurer sur une facture conforme
2. Le bien ou service doit être utilisé pour les besoins de l'activité imposable
3. La TVA doit être exigible chez le fournisseur
4. Pas d'exclusion légale de déductibilité

**Exclusions de déductibilité :**
- Véhicules de tourisme (sauf loueurs, taxis, auto-écoles)
- Gazole véhicules de tourisme : déductible à 80% seulement
- Essences : déductible à 80% (véhicules de tourisme) ou 100% (utilitaires)
- Hébergement des dirigeants et salariés (sauf déplacements professionnels dans certaines conditions)
- Cadeaux d'affaires > 73 € TTC par an et par bénéficiaire
- Services relatifs à des biens exclus du droit à déduction

### Prorata de déduction

Si l'entreprise réalise à la fois des opérations ouvrant droit à déduction et des opérations n'y ouvrant pas droit :

```
Coefficient de déduction = Coefficient d'assujettissement × Coefficient de taxation × Coefficient d'admission

Coefficient de taxation = CA ouvrant droit à déduction / CA total
```

Si le coefficient < 100%, la TVA n'est déductible qu'à proportion du coefficient.

### Le CA3 — Lignes principales

| Section | Lignes | Contenu |
|---------|--------|---------|
| **A — Opérations réalisées** | L01-L06 | CA imposable par taux, opérations intracommunautaires, exportations |
| **B — TVA brute** | L08-L16 | TVA collectée par taux, acquisitions intra, régularisations |
| **B — TVA déductible** | L19-L23 | TVA sur biens/services, immobilisations, report crédit |
| **C — Résultat** | L25-L32 | Crédit de TVA ou TVA nette due, taxes assimilées, total à payer |

## Impôt sur les Sociétés (IS)

### Taux d'IS

| Tranche | Taux | Condition |
|---------|------|-----------|
| 0 à 42 500 € | **15%** (taux réduit PME) | CA < 10 M€, capital détenu à 75%+ par des personnes physiques |
| Au-delà de 42 500 € | **25%** (taux normal) | Toutes les sociétés |

### Calcul de l'IS

```
Résultat comptable (bénéfice ou perte)
+ Réintégrations extra-comptables
- Déductions extra-comptables
= Résultat fiscal

Si résultat fiscal > 0 :
  IS = (42 500 × 15%) + ((Résultat fiscal - 42 500) × 25%)    [si éligible taux PME]
  IS = Résultat fiscal × 25%                                    [si non éligible]

Si résultat fiscal < 0 :
  Déficit reportable en avant (illimité dans le temps, plafonné à 1 M€ + 50% du bénéfice excédant 1 M€)
```

### Principales réintégrations

| Réintégration | Article CGI | Montant |
|---------------|-------------|---------|
| IS de l'exercice | 213 | Totalité |
| Amendes et pénalités | 39-2 | Totalité |
| Taxe sur véhicules de société (TVS) | 39-4 | Totalité |
| Amortissements excédentaires (véhicules de tourisme) | 39-4 | Au-delà du plafond (18 300 € ou 9 900 € selon émissions CO2) |
| Provisions non déductibles | 39-1-5° | Provisions non justifiées ou à caractère de réserve |
| Charges somptuaires | 39-4 | Chasse, pêche, résidences de plaisance, bateaux |
| Quote-part frais et charges (QPFC) sur dividendes | 216 | 5% des dividendes reçus (régime mère-fille) |
| Jetons de présence excédentaires | 210 sexies | Au-delà de 5% × (rémunération moyenne × nb administrateurs) |

### Principales déductions

| Déduction | Article CGI | Montant |
|-----------|-------------|---------|
| Dividendes régime mère-fille | 145, 216 | 95% des dividendes reçus de filiales (participation ≥ 5%) |
| Plus-values long terme sur titres de participation | 219-I-a quinquies | Exonération (sauf QPFC de 12%) |
| Crédit d'impôt recherche (CIR) | 244 quater B | 30% des dépenses de R&D (< 100 M€) |
| Crédit d'impôt innovation (CII) | 244 quater B | 20% des dépenses d'innovation (PME, plafonné 400K€) |
| Provisions antérieurement réintégrées | — | Quand la provision est utilisée ou reprise |

### Acomptes d'IS

| Acompte | Date limite | Base de calcul |
|---------|-------------|---------------|
| 1er acompte | 15 mars | 25% de l'IS N-1 |
| 2ème acompte | 15 juin | 25% de l'IS N-1 |
| 3ème acompte | 15 septembre | 25% de l'IS N-1 |
| 4ème acompte | 15 décembre | 25% de l'IS N-1 (ajusté si besoin) |
| Solde | 15 mai N+1 | IS dû - acomptes versés |

Si IS N-1 < 3 000 € : pas d'acomptes, paiement en une fois au solde.

## Contribution Économique Territoriale (CET)

### CFE (Cotisation Foncière des Entreprises)

- **Base** : Valeur locative des biens passibles de taxe foncière utilisés pour l'activité
- **Taux** : Fixé par la commune
- **Exonérations** : Première année de création, certaines zones (ZFU, ZRR)
- **Cotisation minimale** : Fixée par la commune (entre 237 € et 7 046 € selon le CA)
- **Paiement** : 15 décembre (acompte 15 juin si CFE N-1 > 3 000 €)

### CVAE (Cotisation sur la Valeur Ajoutée des Entreprises)

- **Assujettissement** : CA HT > 500 000 €
- **Base** : Valeur ajoutée fiscale (produits d'exploitation - consommations de tiers, plafonnée à 80% du CA)
- **Taux** : Progressif de 0% à 0,75% selon le CA
- **Plafonnement CET** : La CET (CFE + CVAE) est plafonnée à 1,625% de la valeur ajoutée
- **Acomptes** : 15 juin et 15 septembre (50% chacun de la CVAE estimée)
- **Solde** : 2ème jour ouvré suivant le 1er mai

## C3S (Contribution Sociale de Solidarité des Sociétés)

- **Assujettissement** : CA HT > 19 M€
- **Taux** : 0,16% du CA HT excédant 19 M€
- **Déclaration et paiement** : 15 mai
- **Déductibilité** : Déductible du résultat fiscal

## Liasse fiscale — Feuillets 2050 à 2059

### Contenu des feuillets

| Feuillet | Contenu | Source comptable |
|----------|---------|-----------------|
| **2050** | Bilan — Actif | Balance classes 1-5 (comptes à solde débiteur) |
| **2051** | Bilan — Passif | Balance classes 1-5 (comptes à solde créditeur) |
| **2052** | Compte de résultat (1ère partie) | Balance classes 6-7 |
| **2053** | Compte de résultat (suite) + Affectation du résultat | Balance classes 6-7 + PV AG |
| **2054** | Immobilisations | Tableau des mouvements des immobilisations |
| **2055** | Amortissements | Tableau des mouvements des amortissements |
| **2056** | Provisions | Tableau des mouvements des provisions |
| **2057** | Créances et dettes — État des échéances | Ventilation < 1 an / 1-5 ans / > 5 ans |
| **2058-A** | Détermination du résultat fiscal | Réintégrations et déductions |
| **2058-B** | Déficits, indemnités pour congés à payer, provisions non déductibles | Suivi pluriannuel |
| **2058-C** | Tableau d'affectation du résultat et renseignements divers | Dividendes, réserves |
| **2059-A** | Plus et moins-values | Cessions d'immobilisations |
| **2059-B** | Affectation des plus-values à court terme | Étalement optionnel |
| **2059-E** | Effectifs et valeur ajoutée (pour la CVAE) | Données sociales et financières |

### Contrôles de cohérence

Les contrôles suivants sont vérifiés par l'administration fiscale :

1. Total actif (2050) = Total passif (2051)
2. Résultat net (2052/2053) = Résultat au bilan (2051 case DI)
3. Résultat comptable (2058-A case WA ou WB) = Résultat net (2052/2053)
4. Immobilisations brutes (2050) = Totaux (2054)
5. Amortissements (2050 colonnes amort.) = Totaux (2055)
6. Provisions (2051) = Totaux (2056)
7. Capital (2051 case DA) = Capital social statutaire

## Calendrier récapitulatif des obligations fiscales

### Pour un exercice clos au 31/12

| Mois | Obligation | Formulaire |
|------|-----------|------------|
| **Janvier** | DAS2 (honoraires > 1 200 €/an) | DAS2 |
| **Février** | TVA CA3 janvier | CA3 |
| **Mars** | TVA CA3 février + 1er acompte IS | CA3 + Relevé 2571 |
| **Avril** | TVA CA3 mars | CA3 |
| **Mai** | TVA CA3 avril + Liasse fiscale + Solde IS + CVAE solde + C3S | CA3 + 2065 + 2050-2059 + 2571 + 1er/2ème jour ouvré suivant le 1er mai |
| **Juin** | TVA CA3 mai + 2ème acompte IS + 1er acompte CVAE | CA3 + 2571 + 1329-AC |
| **Juillet-Août** | TVA CA3 | CA3 |
| **Septembre** | TVA CA3 août + 3ème acompte IS + 2ème acompte CVAE | CA3 + 2571 + 1329-AC |
| **Octobre-Novembre** | TVA CA3 | CA3 |
| **Décembre** | TVA CA3 novembre + 4ème acompte IS + CFE | CA3 + 2571 |
