---
description: Préparer les feuillets de la liasse fiscale (cerfa 2050-2059) avec passage du résultat comptable au résultat fiscal
argument-hint: "<feuillets> <exercice>"
---

# Liasse Fiscale

> Pour comprendre les placeholders ou vérifier les outils connectés, voir [CONNECTORS.md](../CONNECTORS.md).

**Important** : Cette commande assiste dans la préparation de la liasse fiscale mais ne constitue pas un conseil fiscal. Tous les feuillets doivent être vérifiés par un expert-comptable ou un professionnel qualifié avant télétransmission à l'administration fiscale.

Préparer les feuillets de la liasse fiscale (cerfa 2050 à 2059) pour le régime réel normal, effectuer le passage du résultat comptable au résultat fiscal, et vérifier la cohérence inter-feuillets.

## Utilisation

```
/liasse-fiscale <feuillets> <exercice>
```

### Arguments

- `feuillets` — Les feuillets à préparer :
  - `2050` — Bilan — Actif
  - `2051` — Bilan — Passif
  - `2052` — Compte de résultat de l'exercice (en liste)
  - `2053` — Compte de résultat de l'exercice (suite) et affectation du résultat
  - `2054` — Immobilisations (état des immobilisations)
  - `2055` — Amortissements (état des amortissements)
  - `2056` — Provisions inscrites au bilan
  - `2057` — État des échéances des créances et des dettes
  - `2058-A` — Détermination du résultat fiscal (passage comptable → fiscal)
  - `2059-A` — Détermination des plus et moins-values
  - `2050-2059` — Liasse complète (tous les feuillets)
  - `bilan-complet` — Feuillets 2050 + 2051 uniquement
  - `resultat-fiscal` — Feuillets 2058-A uniquement
- `exercice` — L'exercice fiscal (ex. `2024`, `2024-2025` si exercice décalé)

## Workflow

### 1. Collecter les données

Si ~~erp ou ~~data warehouse est connecté :
- Extraire la balance définitive après écritures d'inventaire
- Extraire le tableau des immobilisations et amortissements
- Extraire le tableau des provisions
- Extraire l'échéancier des créances et dettes
- Extraire les données de l'exercice précédent (N-1)

Si aucune source de données n'est connectée :
> Connectez ~~erp ou ~~data warehouse pour extraire les données automatiquement. Pour préparer manuellement, fournissez :
> 1. La balance générale définitive (après écritures d'inventaire)
> 2. Le tableau des immobilisations (mouvements de l'exercice)
> 3. Le tableau des amortissements
> 4. Le tableau des provisions
> 5. Le détail des créances et dettes par échéance

### 2. Préparer le feuillet 2058-A — Détermination du résultat fiscal

C'est le feuillet le plus critique : passage du résultat comptable au résultat fiscal.

```
FEUILLET 2058-A — DÉTERMINATION DU RÉSULTAT FISCAL
Exercice clos le : [Date]

RÉSULTAT COMPTABLE
  Bénéfice comptable (ligne WA)                          X XXX €
  ou Déficit comptable (ligne WB)                       (X XXX €)

RÉINTÉGRATIONS (charges non déductibles)
  Impôt sur les sociétés (ligne WC)                      X XXX €
  Amendes et pénalités (ligne WD)                        X XXX €
  Taxe sur les véhicules de société (ligne WF)           X XXX €
  Amortissements excédentaires (ligne WG)                X XXX €
  Provisions non déductibles (ligne WI)                  X XXX €
  Charges somptuaires (ligne WJ)                         X XXX €
  Charges à payer non déductibles                        X XXX €
  Écarts de change latents (pertes) non déduites         X XXX €
  Quote-part de frais et charges sur dividendes          X XXX €
  Autres réintégrations (ligne WQ)                       X XXX €
                                                         ----------
  TOTAL RÉINTÉGRATIONS (ligne WR)                        X XXX €

DÉDUCTIONS
  Produits non imposables                                X XXX €
  Dividendes éligibles au régime mère-fille              X XXX €
  Plus-values à long terme taxées à taux réduit          X XXX €
  Provisions antérieurement non déduites devenues déduc. X XXX €
  Amortissements différés réintégrés                     X XXX €
  Écarts de change latents (gains) non imposés           X XXX €
  Crédit d'impôt recherche (CIR)                         X XXX €
  Autres déductions (ligne XI)                           X XXX €
                                                         ----------
  TOTAL DÉDUCTIONS (ligne XJ)                            X XXX €

RÉSULTAT FISCAL
  Bénéfice fiscal (ligne XN) = WA + WR - XJ             X XXX €
  ou Déficit fiscal (ligne XO)                          (X XXX €)

IMPÔT SUR LES SOCIÉTÉS
  Bénéfice imposable au taux normal (25%)                X XXX €
  Bénéfice imposable au taux réduit PME (15%)            X XXX €
  IS au taux normal (25%)                                X XXX €
  IS au taux réduit (15% sur les premiers 42 500 €)      X XXX €
  Contribution sociale (3,3% si CA > 7,63 M€)            X XXX €
  TOTAL IS                                               X XXX €
```

### 3. Préparer les feuillets de bilan (2050/2051)

Mapper les comptes PCG vers les cases cerfa :

```
FEUILLET 2050 — BILAN ACTIF

| Case | Libellé                                    | Brut        | Amort/Dépréc | Net N       | Net N-1     |
|------|--------------------------------------------|-------------|-------------|-------------|-------------|
| AA   | Capital souscrit non appelé                |             |             |             |             |
| AB   | Frais d'établissement                      | X XXX       | X XXX       | X XXX       | X XXX       |
| CX   | Frais de R&D                               | X XXX       | X XXX       | X XXX       | X XXX       |
| AF   | Concessions, brevets, licences             | X XXX       | X XXX       | X XXX       | X XXX       |
| AH   | Fonds commercial                           | X XXX       | X XXX       | X XXX       | X XXX       |
| AJ   | Autres immobilisations incorporelles       | X XXX       | X XXX       | X XXX       | X XXX       |
| AL   | Terrains                                   | X XXX       | X XXX       | X XXX       | X XXX       |
| AN   | Constructions                              | X XXX       | X XXX       | X XXX       | X XXX       |
| AP   | Installations techniques, matériel         | X XXX       | X XXX       | X XXX       | X XXX       |
| AR   | Autres immobilisations corporelles         | X XXX       | X XXX       | X XXX       | X XXX       |
| AT   | Immobilisations en cours                   | X XXX       |             | X XXX       | X XXX       |
| AV   | Avances et acomptes                        | X XXX       |             | X XXX       | X XXX       |
| CS   | Participations                             | X XXX       | X XXX       | X XXX       | X XXX       |
| BB   | Créances rattachées à participations       | X XXX       |             | X XXX       | X XXX       |
| BD   | Autres titres immobilisés                  | X XXX       | X XXX       | X XXX       | X XXX       |
| BF   | Prêts                                      | X XXX       |             | X XXX       | X XXX       |
| BH   | Autres immobilisations financières         | X XXX       |             | X XXX       | X XXX       |
| BJ   | TOTAL ACTIF IMMOBILISÉ                     | X XXX       | X XXX       | X XXX       | X XXX       |
| ...  | [Actif circulant - stocks, créances, etc.] | ...         | ...         | ...         | ...         |
| CO   | TOTAL GÉNÉRAL                              | X XXX       | X XXX       | X XXX       | X XXX       |
```

### 4. Contrôles de cohérence inter-feuillets

Vérifier les contrôles croisés entre feuillets :

| Contrôle | Feuillet source | Feuillet cible | Règle |
|----------|----------------|----------------|-------|
| Total actif = Total passif | 2050 case CO | 2051 case EE | Doivent être égaux |
| Résultat CR = Résultat bilan | 2052 case HN | 2051 case DI | Doivent être égaux |
| Résultat comptable 2058-A | 2058-A case WA | 2052 case HN | Doivent être égaux |
| Immobilisations brutes bilan | 2050 cases | 2054 totaux | Doivent correspondre |
| Amortissements bilan | 2050 colonnes amort. | 2055 totaux | Doivent correspondre |
| Provisions bilan | 2051 cases | 2056 totaux | Doivent correspondre |
| Capital bilan | 2051 case DA | Statuts | Doivent correspondre |

### 5. Points de contrôle

Avant finalisation :

- [ ] La balance est définitive (toutes les écritures d'inventaire sont passées)
- [ ] Le résultat du CR correspond au résultat du bilan
- [ ] Total actif = Total passif
- [ ] Le passage comptable → fiscal (2058-A) est complet et documenté
- [ ] Les réintégrations et déductions sont justifiées et conformes au CGI
- [ ] Les immobilisations brutes et amortissements (2054/2055) sont cohérents avec le bilan
- [ ] L'état des provisions (2056) correspond aux comptes 15x du bilan
- [ ] L'échéancier créances/dettes (2057) est ventilé correctement
- [ ] Les données N-1 sont correctement reportées
- [ ] Le taux d'IS appliqué est correct (25% normal, 15% PME sur 42 500 €)
- [ ] La date limite de dépôt est respectée (2ème jour ouvré suivant le 1er mai, ou 3 mois après la clôture)

### 6. Sortie

Fournir :
1. Les feuillets cerfa préparés avec les cases remplies
2. Le passage du résultat comptable au résultat fiscal (2058-A) détaillé
3. Les contrôles de cohérence inter-feuillets avec résultat (OK / Écart)
4. Le calcul de l'IS avec distinction taux normal / taux réduit PME
5. Les points d'attention et anomalies détectées
6. Le rappel de la date limite de télétransmission
