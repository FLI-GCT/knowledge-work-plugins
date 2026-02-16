---
name: charges-sociales
description: Comprendre et comptabiliser les charges de personnel françaises, cotisations sociales, DSN et provisions liées au personnel. Utiliser pour préparer les écritures de paie, calculer les cotisations patronales et salariales, comprendre la DSN, provisionner les congés payés ou l'intéressement.
---

# Charges Sociales et Personnel

**Important** : Ce skill assiste dans la compréhension des charges sociales mais ne constitue pas un conseil en droit social ou en paie. Tous les calculs doivent être vérifiés par un gestionnaire de paie qualifié ou un expert-comptable.

Structure du bulletin de paie français, cotisations salariales et patronales, DSN, provisions liées au personnel et comptabilisation.

## Structure du bulletin de paie français

### Du salaire brut au salaire net

```
SALAIRE BRUT                                              X XXX,XX €
  Salaire de base (mensuel)                   X XXX,XX
  Heures supplémentaires                        XXX,XX
  Primes et gratifications                      XXX,XX
  Avantages en nature                           XXX,XX

COTISATIONS SALARIALES                                   (X XXX,XX €)
  Sécurité sociale (maladie, vieillesse)         XXX,XX
  Assurance chômage                              XXX,XX
  Retraite complémentaire (AGIRC-ARRCO)          XXX,XX
  CSG déductible                                 XXX,XX
  CSG/CRDS non déductible                        XXX,XX
  Prévoyance et mutuelle (part salariale)        XXX,XX

NET IMPOSABLE                                             X XXX,XX €

PRÉLÈVEMENT À LA SOURCE (PAS)                           (  XXX,XX €)

NET À PAYER AVANT IMPÔT                                   X XXX,XX €
NET À PAYER                                               X XXX,XX €

COÛT EMPLOYEUR
  Salaire brut                                X XXX,XX
  + Cotisations patronales                    X XXX,XX
  = COÛT TOTAL EMPLOYEUR                      X XXX,XX €
```

## Cotisations sociales — Principaux taux

### Cotisations URSSAF

| Cotisation | Base | Part salariale | Part patronale | Plafond |
|------------|------|:--------------:|:--------------:|---------|
| Maladie | Totalité | 0% | 7,00% (ou 13% si rémunération > 2,5 SMIC) | Pas de plafond |
| Allocations familiales | Totalité | 0% | 3,45% (ou 5,25% si rémunération > 3,5 SMIC) | Pas de plafond |
| Vieillesse plafonnée | Tranche A | 6,90% | 8,55% | 1 PASS |
| Vieillesse déplafonnée | Totalité | 0,40% | 2,02% | Pas de plafond |
| Accidents du travail | Totalité | 0% | Variable selon secteur | Pas de plafond |
| Contribution solidarité autonomie | Totalité | 0% | 0,30% | Pas de plafond |
| FNAL | Totalité (>50 sal.) ou T.A (<50 sal.) | 0% | 0,10% (<50) ou 0,50% (≥50) | 1 PASS ou pas de plafond |

### Assurance chômage

| Cotisation | Base | Part salariale | Part patronale | Plafond |
|------------|------|:--------------:|:--------------:|---------|
| Assurance chômage | Tranches A+B | 0% | 4,05% | 4 PASS |
| AGS (garantie salaires) | Tranches A+B | 0% | 0,20% | 4 PASS |

### Retraite complémentaire (AGIRC-ARRCO)

| Cotisation | Base | Part salariale | Part patronale | Plafond |
|------------|------|:--------------:|:--------------:|---------|
| Tranche 1 | Jusqu'à 1 PASS | 3,15% | 4,72% | 1 PASS |
| Tranche 2 | De 1 à 8 PASS | 8,64% | 12,95% | 8 PASS |
| CEG Tranche 1 | Jusqu'à 1 PASS | 0,86% | 1,29% | 1 PASS |
| CEG Tranche 2 | De 1 à 8 PASS | 1,08% | 1,62% | 8 PASS |
| CET | Totalité | 0,14% | 0,21% | 8 PASS |

*PASS 2025 : 47 100 € annuel / 3 925 € mensuel (vérifier le montant en vigueur)*

### CSG et CRDS

| Cotisation | Base | Taux | Déductibilité fiscale |
|------------|------|------|----------------------|
| CSG déductible | 98,25% du brut (+ 100% des revenus de remplacement) | 6,80% | Déductible du revenu imposable |
| CSG non déductible | 98,25% du brut | 2,40% | Non déductible |
| CRDS | 98,25% du brut | 0,50% | Non déductible |

### Autres cotisations

| Cotisation | Part salariale | Part patronale | Condition |
|------------|:--------------:|:--------------:|-----------|
| Prévoyance cadres (minimum) | — | 1,50% T.A | Cadres uniquement |
| Mutuelle obligatoire | ~50% du coût | ~50% du coût | Toutes entreprises |
| Formation professionnelle | — | 0,55% (<11 sal.) ou 1% (≥11 sal.) | Toutes |
| Taxe d'apprentissage | — | 0,68% | Toutes (sauf Alsace-Moselle) |
| Participation construction | — | 0,45% | ≥ 50 salariés |
| Forfait social | — | 20% (intéressement, participation, abondement PEE) | ≥ 250 salariés |

## Comptabilisation de la paie

### Schéma d'écriture complet

```
Journal : SAL
Date : Dernier jour du mois

Écriture 1 — Salaires et charges salariales :
Débit  641    Rémunérations du personnel           Salaire brut total
Crédit 421    Personnel — rémunérations dues        Salaire net avant PAS
Crédit 431    Sécurité sociale                      Cotisations salariales URSSAF
Crédit 437    Autres organismes sociaux             Cotisations salariales retraite + prévoyance
Crédit 4421   État — Prélèvement à la source        PAS retenu

Écriture 2 — Charges patronales :
Débit  645    Charges de sécurité sociale            Cotisations patronales totales
Crédit 431    Sécurité sociale                       Cotisations patronales URSSAF
Crédit 437    Autres organismes sociaux              Cotisations patronales retraite + prévoyance

Écriture 3 — Paiement des salaires :
Débit  421    Personnel — rémunérations dues         Salaire net
Crédit 512    Banque                                 Salaire net

Écriture 4 — Paiement des charges sociales :
Débit  431    Sécurité sociale                       Total dû URSSAF
Débit  437    Autres organismes sociaux              Total dû autres caisses
Débit  4421   État — Prélèvement à la source         Total PAS
Crédit 512    Banque                                 Total payé
```

## Déclaration Sociale Nominative (DSN)

### Principe

La DSN est la déclaration unique et dématérialisée qui remplace la majorité des déclarations sociales :
- DADS-U
- Attestation employeur Pôle Emploi
- Déclaration de cotisations URSSAF
- Déclarations aux caisses de retraite et prévoyance

### Contenu de la DSN mensuelle

| Bloc | Contenu |
|------|---------|
| Établissement | SIRET, effectifs, convention collective |
| Salarié | NIR, identité, contrat de travail |
| Rémunération | Brut, net imposable, heures, primes |
| Cotisations | Par organisme (URSSAF, retraite, prévoyance) |
| Événements | Embauche, fin de contrat, arrêt maladie |

### Échéances DSN

| Effectif | Date limite |
|----------|-------------|
| ≥ 50 salariés | **5 du mois suivant** (paie du mois M déclarée le 5 M+1) |
| < 50 salariés | **15 du mois suivant** (paie du mois M déclarée le 15 M+1) |

## Provisions liées au personnel

### Provision pour congés payés

Obligatoire si des droits à congés sont acquis mais non pris à la date de clôture.

```
Provision = Σ (Indemnité de congés payés restant à verser par salarié)
           + Charges sociales patronales correspondantes

Débit  641    Rémunérations du personnel            Indemnités CP
Débit  645    Charges sociales                       Charges patronales sur CP
Crédit 4282   Dettes provisionnées — congés payés    Indemnités CP
Crédit 4382   Charges sociales sur congés payés      Charges patronales sur CP
```

**Méthodes de calcul :**
- **Règle du 1/10ème** : 10% de la rémunération brute de la période de référence
- **Règle du maintien de salaire** : Salaire qu'aurait perçu le salarié s'il avait travaillé
- Retenir la méthode la plus favorable au salarié

### Provision pour 13ème mois / primes

Si une prime est acquise progressivement sur l'année, provisionner chaque mois :

```
Provision mensuelle = Prime annuelle totale estimée / 12

Débit  641    Rémunérations du personnel            1/12ème de la prime
Débit  645    Charges sociales                       Charges patronales correspondantes
Crédit 4286   Autres charges de personnel à payer    Prime provisionnée
Crédit 4386   Charges sociales à payer               Charges sociales provisionnées
```

### Provision pour indemnités de départ à la retraite (IDR)

Méthode préférentielle (recommandée par l'ANC) : comptabiliser la provision au bilan.

```
Provision = Σ (Probabilité de présence × Indemnité future estimée × Coefficient d'actualisation)

Débit  6815   Dotation provisions — exploitation     Dotation de l'exercice
Crédit 153    Provisions pour pensions               Provision IDR
```

### Participation des salariés

Obligatoire pour les entreprises ≥ 50 salariés.

**Formule légale :**
```
Réserve Spéciale de Participation (RSP) = 1/2 × (B - 5% × C) × S/VA

B = Bénéfice fiscal (après IS)
C = Capitaux propres
S = Salaires bruts
VA = Valeur ajoutée
```

**Comptabilisation :**
```
Débit  691    Participation des salariés             Montant de la participation
Crédit 4284   Dettes provisionnées — participation   Montant de la participation
```

La participation est comptabilisée en charge de l'exercice au titre duquel elle est calculée, même si elle n'est versée que l'exercice suivant.

### Intéressement

Facultatif, basé sur un accord d'entreprise.

```
Débit  6414   Indemnités et avantages divers         Montant de l'intéressement
Débit  645    Charges sociales (forfait social 20%)  Si ≥ 250 salariés
Crédit 4286   Autres charges de personnel à payer    Montant à verser
```
