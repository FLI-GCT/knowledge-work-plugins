---
description: Générer le bilan, le compte de résultat et les SIG au format PCG avec comparatif N-1
argument-hint: "<type> <période>"
---

# États Financiers

> Pour comprendre les placeholders ou vérifier les outils connectés, voir [CONNECTORS.md](../CONNECTORS.md).

**Important** : Cette commande assiste dans la préparation des états financiers mais ne constitue pas un conseil comptable. Tous les états doivent être vérifiés par un expert-comptable ou un commissaire aux comptes avant approbation ou publication.

Générer le bilan, le compte de résultat par nature et les soldes intermédiaires de gestion (SIG) au format Plan Comptable Général, avec comparatif N / N-1 et analyse des variations significatives.

## Utilisation

```
/etats-financiers <type> <période>
```

### Arguments

- `type` — Le type d'état à générer :
  - `bilan` — Bilan comptable format PCG (actif/passif)
  - `compte-resultat` — Compte de résultat par nature
  - `sig` — Soldes intermédiaires de gestion (9 soldes)
  - `complet` — Bilan + Compte de résultat + SIG
- `période` — La période de reporting (ex. `2025-01`, `2025-Q1`, `2024`)

## Workflow

### 1. Collecter les données financières

Si ~~erp ou ~~data warehouse est connecté :
- Extraire la balance générale pour la période spécifiée
- Extraire la balance de la période précédente (N-1) pour comparaison
- Extraire la hiérarchie des comptes et les regroupements pour la présentation

Si aucune source de données n'est connectée :
> Connectez ~~erp ou ~~data warehouse pour extraire les données automatiquement. Vous pouvez aussi coller la balance générale, charger un fichier ou fournir les données par classe de comptes.

Demander à l'utilisateur de fournir :
- La balance générale de la période courante (soldes des comptes par classe)
- La balance de la période précédente pour comparaison
- Les éventuels ajustements ou reclassifications à appliquer

### 2. Générer le bilan (si type = bilan ou complet)

Présenter au format PCG :

```
BILAN AU [Date]
(en euros)

                                            Exercice N      Exercice N-1    Variation
ACTIF                                       Brut    Amort/  Net
                                                    Dépréc.
══════════════════════════════════════════════════════════════════════════════════════

ACTIF IMMOBILISÉ

Immobilisations incorporelles
  Frais d'établissement                     X XXX   X XXX   X XXX   X XXX   X XXX
  Frais de recherche et développement       X XXX   X XXX   X XXX   X XXX   X XXX
  Concessions, brevets, licences            X XXX   X XXX   X XXX   X XXX   X XXX
  Fonds commercial                          X XXX   X XXX   X XXX   X XXX   X XXX
  Autres immobilisations incorporelles      X XXX   X XXX   X XXX   X XXX   X XXX

Immobilisations corporelles
  Terrains                                  X XXX   X XXX   X XXX   X XXX   X XXX
  Constructions                             X XXX   X XXX   X XXX   X XXX   X XXX
  Installations techniques, matériel        X XXX   X XXX   X XXX   X XXX   X XXX
  Autres immobilisations corporelles        X XXX   X XXX   X XXX   X XXX   X XXX
  Immobilisations en cours                  X XXX           X XXX   X XXX   X XXX

Immobilisations financières
  Participations                            X XXX   X XXX   X XXX   X XXX   X XXX
  Créances rattachées à des participations  X XXX           X XXX   X XXX   X XXX
  Autres titres immobilisés                 X XXX   X XXX   X XXX   X XXX   X XXX
  Prêts                                     X XXX           X XXX   X XXX   X XXX

TOTAL ACTIF IMMOBILISÉ                      X XXX   X XXX   X XXX   X XXX   X XXX

ACTIF CIRCULANT

  Stocks et en-cours                        X XXX   X XXX   X XXX   X XXX   X XXX
  Avances et acomptes versés                X XXX           X XXX   X XXX   X XXX
  Créances clients et comptes rattachés     X XXX   X XXX   X XXX   X XXX   X XXX
  Autres créances                           X XXX           X XXX   X XXX   X XXX
  Valeurs mobilières de placement           X XXX   X XXX   X XXX   X XXX   X XXX
  Disponibilités                            X XXX           X XXX   X XXX   X XXX

Charges constatées d'avance                 X XXX           X XXX   X XXX   X XXX

TOTAL ACTIF CIRCULANT                       X XXX   X XXX   X XXX   X XXX   X XXX

TOTAL ACTIF                                 X XXX   X XXX   X XXX   X XXX   X XXX

══════════════════════════════════════════════════════════════════════════════════════

PASSIF                                      Exercice N      Exercice N-1    Variation
══════════════════════════════════════════════════════════════════════════════════════

CAPITAUX PROPRES

  Capital social ou individuel              X XXX           X XXX   X XXX
  Primes d'émission, de fusion              X XXX           X XXX   X XXX
  Écarts de réévaluation                    X XXX           X XXX   X XXX
  Réserve légale                            X XXX           X XXX   X XXX
  Réserves statutaires                      X XXX           X XXX   X XXX
  Autres réserves                           X XXX           X XXX   X XXX
  Report à nouveau                          X XXX           X XXX   X XXX
  Résultat de l'exercice                    X XXX           X XXX   X XXX
  Subventions d'investissement              X XXX           X XXX   X XXX
  Provisions réglementées                   X XXX           X XXX   X XXX

TOTAL CAPITAUX PROPRES                      X XXX           X XXX   X XXX

PROVISIONS POUR RISQUES ET CHARGES

  Provisions pour risques                   X XXX           X XXX   X XXX
  Provisions pour charges                   X XXX           X XXX   X XXX

TOTAL PROVISIONS                            X XXX           X XXX   X XXX

DETTES

  Emprunts et dettes financières            X XXX           X XXX   X XXX
  Avances et acomptes reçus                 X XXX           X XXX   X XXX
  Dettes fournisseurs et comptes rattachés  X XXX           X XXX   X XXX
  Dettes fiscales et sociales               X XXX           X XXX   X XXX
  Autres dettes                             X XXX           X XXX   X XXX

Produits constatés d'avance                 X XXX           X XXX   X XXX

TOTAL DETTES                                X XXX           X XXX   X XXX

TOTAL PASSIF                                X XXX           X XXX   X XXX
```

### 3. Générer le compte de résultat (si type = compte-resultat ou complet)

Présenter par nature (format PCG) :

```
COMPTE DE RÉSULTAT — Exercice clos le [Date]
(en euros)

                                            Exercice N    Exercice N-1    Variation
                                                                          ($)     (%)
══════════════════════════════════════════════════════════════════════════════════════

PRODUITS D'EXPLOITATION
  Ventes de marchandises                    X XXX         X XXX           X XXX   X%
  Production vendue (biens et services)     X XXX         X XXX           X XXX   X%
  Production stockée                        X XXX         X XXX           X XXX   X%
  Production immobilisée                    X XXX         X XXX           X XXX   X%
  Subventions d'exploitation                X XXX         X XXX           X XXX   X%
  Reprises sur provisions et transferts     X XXX         X XXX           X XXX   X%
  Autres produits                           X XXX         X XXX           X XXX   X%
TOTAL PRODUITS D'EXPLOITATION               X XXX         X XXX           X XXX   X%

CHARGES D'EXPLOITATION
  Achats de marchandises                    X XXX         X XXX           X XXX   X%
  Variation de stock de marchandises        X XXX         X XXX           X XXX   X%
  Achats de matières premières              X XXX         X XXX           X XXX   X%
  Variation de stock de matières            X XXX         X XXX           X XXX   X%
  Autres achats et charges externes         X XXX         X XXX           X XXX   X%
  Impôts, taxes et versements assimilés     X XXX         X XXX           X XXX   X%
  Salaires et traitements                   X XXX         X XXX           X XXX   X%
  Charges sociales                          X XXX         X XXX           X XXX   X%
  Dotations aux amortissements et provisions X XXX        X XXX           X XXX   X%
  Autres charges                            X XXX         X XXX           X XXX   X%
TOTAL CHARGES D'EXPLOITATION                X XXX         X XXX           X XXX   X%

RÉSULTAT D'EXPLOITATION                     X XXX         X XXX           X XXX   X%

PRODUITS FINANCIERS                         X XXX         X XXX           X XXX   X%
CHARGES FINANCIÈRES                         X XXX         X XXX           X XXX   X%
RÉSULTAT FINANCIER                          X XXX         X XXX           X XXX   X%

RÉSULTAT COURANT AVANT IMPÔTS              X XXX         X XXX           X XXX   X%

PRODUITS EXCEPTIONNELS                      X XXX         X XXX           X XXX   X%
CHARGES EXCEPTIONNELLES                     X XXX         X XXX           X XXX   X%
RÉSULTAT EXCEPTIONNEL                       X XXX         X XXX           X XXX   X%

Participation des salariés                  X XXX         X XXX           X XXX   X%
Impôt sur les bénéfices                     X XXX         X XXX           X XXX   X%

RÉSULTAT NET                                X XXX         X XXX           X XXX   X%
```

### 4. Générer les SIG (si type = sig ou complet)

```
SOLDES INTERMÉDIAIRES DE GESTION — Exercice [Année]
(en euros)

                                            Exercice N    Exercice N-1    Variation
══════════════════════════════════════════════════════════════════════════════════════

1. Marge commerciale
   Ventes de marchandises                   X XXX         X XXX
   - Coût d'achat des marchandises vendues (X XXX)       (X XXX)
   = MARGE COMMERCIALE                      X XXX         X XXX           X XXX   X%
     Taux de marge :                        XX,X%         XX,X%

2. Production de l'exercice
   Production vendue                        X XXX         X XXX
   + Production stockée                     X XXX         X XXX
   + Production immobilisée                 X XXX         X XXX
   = PRODUCTION DE L'EXERCICE               X XXX         X XXX           X XXX   X%

3. Valeur ajoutée
   Marge commerciale                        X XXX         X XXX
   + Production de l'exercice               X XXX         X XXX
   - Consommations en provenance de tiers  (X XXX)       (X XXX)
   = VALEUR AJOUTÉE                         X XXX         X XXX           X XXX   X%
     VA / CA :                              XX,X%         XX,X%

4. Excédent brut d'exploitation (EBE)
   Valeur ajoutée                           X XXX         X XXX
   + Subventions d'exploitation             X XXX         X XXX
   - Impôts, taxes et versements assimilés (X XXX)       (X XXX)
   - Charges de personnel                  (X XXX)       (X XXX)
   = EBE                                    X XXX         X XXX           X XXX   X%
     EBE / CA :                             XX,X%         XX,X%

5. Résultat d'exploitation
   EBE                                      X XXX         X XXX
   + Reprises sur provisions                X XXX         X XXX
   + Autres produits                        X XXX         X XXX
   - Dotations aux amortissements et prov. (X XXX)       (X XXX)
   - Autres charges                        (X XXX)       (X XXX)
   = RÉSULTAT D'EXPLOITATION                X XXX         X XXX           X XXX   X%

6. Résultat courant avant impôts (RCAI)
   Résultat d'exploitation                  X XXX         X XXX
   + Produits financiers                    X XXX         X XXX
   - Charges financières                   (X XXX)       (X XXX)
   = RCAI                                   X XXX         X XXX           X XXX   X%

7. Résultat exceptionnel
   Produits exceptionnels                   X XXX         X XXX
   - Charges exceptionnelles              (X XXX)       (X XXX)
   = RÉSULTAT EXCEPTIONNEL                  X XXX         X XXX           X XXX   X%

8. Résultat net
   RCAI                                     X XXX         X XXX
   + Résultat exceptionnel                  X XXX         X XXX
   - Participation des salariés            (X XXX)       (X XXX)
   - Impôt sur les bénéfices              (X XXX)       (X XXX)
   = RÉSULTAT NET                           X XXX         X XXX           X XXX   X%
     Résultat net / CA :                    XX,X%         XX,X%

9. Capacité d'autofinancement (CAF)
   Résultat net                             X XXX         X XXX
   + Dotations aux amortissements et prov.  X XXX         X XXX
   - Reprises sur amortissements et prov.  (X XXX)       (X XXX)
   + Valeur nette comptable des éléments cédés X XXX      X XXX
   - Produits de cession d'éléments d'actif(X XXX)       (X XXX)
   - Quote-part subventions virées au résultat(X XXX)    (X XXX)
   = CAF                                    X XXX         X XXX           X XXX   X%
```

### 5. Analyse des variations significatives

Pour chaque poste présentant une variation matérielle, signaler :

| Poste | N | N-1 | Variation (€) | Variation (%) | Direction | Commentaire |
|-------|---|-----|---------------|---------------|-----------|-------------|
| [Poste] | X XXX | X XXX | X XXX | X,X% | Favorable/Défavorable | [Si connu] |

**Seuils de signalement (configurables) :**
- Variation en euros > seuil défini (ex. 10K€, 50K€)
- Variation en % > 10% (ou seuil personnalisé)

### 6. Sortie

Fournir :
1. Les états financiers formatés au format PCG (bilan, CR, SIG selon le type demandé)
2. Le comparatif N / N-1 avec variations
3. Les ratios clés (taux de marge, VA/CA, EBE/CA, résultat net/CA)
4. La liste des variations significatives à investiguer
5. La proposition d'approfondir les écarts avec `/analyse-ecarts`
