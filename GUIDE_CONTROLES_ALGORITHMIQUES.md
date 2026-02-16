# Contrôles Algorithmiques pour la Fiabilité Comptable

> **Objectif :** Lister les outils mathématiques, algorithmiques et statistiques à implémenter pour **garantir** la fiabilité des résultats produits par un plugin comptable. Un LLM ne peut pas faire d'arithmétique fiable seul — ces contrôles sont le filet de sécurité obligatoire.
>
> **Principe fondamental :** Tout résultat chiffré produit par le plugin doit être **vérifiable algorithmiquement** avant d'être présenté à l'utilisateur. Le LLM rédige, l'algorithme vérifie.

---

## Table des Matières

1. [Pourquoi un LLM seul ne suffit pas](#1-pourquoi-un-llm-seul-ne-suffit-pas)
2. [Architecture : LLM + Moteur de vérification](#2-architecture--llm--moteur-de-vérification)
3. [Niveau 1 — Invariants fondamentaux (bloquants)](#3-niveau-1--invariants-fondamentaux-bloquants)
4. [Niveau 2 — Contrôles de cohérence croisée](#4-niveau-2--contrôles-de-cohérence-croisée)
5. [Niveau 3 — Détection statistique d'anomalies](#5-niveau-3--détection-statistique-danomalies)
6. [Niveau 4 — Algorithmes de rapprochement automatique](#6-niveau-4--algorithmes-de-rapprochement-automatique)
7. [Niveau 5 — Intégrité et traçabilité cryptographique](#7-niveau-5--intégrité-et-traçabilité-cryptographique)
8. [Niveau 6 — Simulation et estimation robuste](#8-niveau-6--simulation-et-estimation-robuste)
9. [Minimum vital pour passer un cap](#9-minimum-vital-pour-passer-un-cap)
10. [Implémentation technique recommandée](#10-implémentation-technique-recommandée)

---

## 1. Pourquoi un LLM seul ne suffit pas

### Les 5 failles structurelles d'un LLM en comptabilité

| Faille | Explication | Risque comptable |
|--------|-------------|-----------------|
| **Hallucination arithmétique** | Un LLM peut affirmer que 1 247,50 + 892,30 = 2 140,80 (alors que c'est 2 139,80) | Écriture déséquilibrée, TVA erronée, IS faux |
| **Dérive d'arrondi** | Accumulation d'erreurs sur des séries de calculs en virgule flottante | Écarts résiduels non expliqués, centimes perdus |
| **Inversion de signe** | Confusion débit/crédit, favorable/défavorable | Résultat fiscal inversé, TVA à payer au lieu de crédit |
| **Oubli de contrainte** | Ignorer une réintégration fiscale, un taux de TVA spécial, un plafond | Liasse fiscale incomplète, déclaration erronée |
| **Confiance excessive** | Le LLM présente un résultat faux avec la même assurance qu'un résultat juste | L'utilisateur ne remet pas en question |

### Conséquence

> **Chaque nombre produit par le plugin doit passer par un moteur de vérification déterministe (code) avant d'être affiché.** Le LLM orchestre le workflow et rédige les narratifs ; l'algorithme fait les maths.

---

## 2. Architecture : LLM + Moteur de vérification

```
┌─────────────────────────────────────────────────────────────────┐
│                        UTILISATEUR                               │
│  /ecriture achat 2025-01    /declaration-tva ca3 2025-01         │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────────────────┐
│                    CLAUDE (LLM)                                  │
│  • Comprend l'intention                                          │
│  • Collecte les données (copier-coller / MCP)                    │
│  • Orchestre le workflow                                         │
│  • Rédige les narratifs et commentaires                          │
│  • Appelle le moteur de vérification via tool_use                │
└─────────────────────────┬───────────────────────────────────────┘
                          │ tool_use
┌─────────────────────────▼───────────────────────────────────────┐
│              MOTEUR DE VÉRIFICATION (CODE)                        │
│  • Calculs arithmétiques exacts (Decimal, pas Float)             │
│  • Vérification des invariants (D=C, Actif=Passif)              │
│  • Contrôles de cohérence croisée                                │
│  • Détection d'anomalies statistiques                            │
│  • Hash de traçabilité                                           │
│  • Retourne : résultats + statut (PASS/FAIL/WARNING)             │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────────────────┐
│                  RÉSULTAT VÉRIFIÉ                                 │
│  ✅ Écriture équilibrée (vérifié algorithmiquement)              │
│  ✅ TVA correcte (vérifié : 10 000 × 20% = 2 000,00)            │
│  ✅ Total actif = Total passif (vérifié : 0,00 € d'écart)       │
│  ⚠️ Montant inhabituel détecté (> 3σ de la moyenne)              │
│  ❌ BLOQUANT : Débits ≠ Crédits (écart 0,01 €)                  │
└─────────────────────────────────────────────────────────────────┘
```

### Modes de fonctionnement

| Mode | Quand | Comportement |
|------|-------|-------------|
| **Bloquant** (❌) | Invariant fondamental violé | Le résultat n'est PAS affiché. Message d'erreur explicite. |
| **Avertissement** (⚠️) | Anomalie détectée mais pas de violation d'invariant | Le résultat est affiché avec un warning visible. |
| **Validé** (✅) | Tous les contrôles passent | Le résultat est affiché avec la mention "vérifié algorithmiquement". |

---

## 3. Niveau 1 — Invariants fondamentaux (bloquants)

Ce sont les **équations qui doivent TOUJOURS être vraies**. Si un seul invariant est violé, le résultat est rejeté. Aucune exception.

### 3.1. Équilibre débit/crédit (Invariant #1)

**Règle :** Pour toute écriture comptable, la somme des débits doit être exactement égale à la somme des crédits.

```
INVARIANT : Σ(débits) = Σ(crédits)

Vérification :
  écart = abs(Σ(débits) - Σ(crédits))
  SI écart > 0    →  ❌ BLOQUANT : "Écriture déséquilibrée de {écart} €"
  SI écart == 0   →  ✅ PASS
```

**Implémentation critique :** Utiliser l'arithmétique en **virgule fixe décimale** (Python `Decimal`, Java `BigDecimal`, JS `decimal.js`), **jamais** le type `float`/`double`.

```python
from decimal import Decimal, ROUND_HALF_UP

def verifier_equilibre(lignes):
    total_debit = sum(Decimal(str(l.debit)) for l in lignes)
    total_credit = sum(Decimal(str(l.credit)) for l in lignes)
    ecart = total_debit - total_credit
    if ecart != Decimal('0'):
        return BLOQUANT(f"Déséquilibre : {ecart} €")
    return PASS()
```

**Pourquoi c'est crucial :** C'est le contrôle le plus ancien de la comptabilité (Luca Pacioli, 1494). Si cette règle est violée, RIEN d'autre n'est fiable.

---

### 3.2. Équation du bilan (Invariant #2)

**Règle :** Total Actif Net = Total Passif. Toujours. Sans exception.

```
INVARIANT : Total_Actif_Brut - Total_Amortissements = Total_Passif

Composantes :
  Actif_Net = Σ(Immobilisations brutes) - Σ(Amortissements/Dépréciations) + Σ(Actif circulant) + CCA
  Passif    = Σ(Capitaux propres) + Σ(Provisions) + Σ(Dettes) + PCA

  écart = abs(Actif_Net - Passif)
  SI écart > 0   →  ❌ BLOQUANT
  SI écart == 0  →  ✅ PASS
```

---

### 3.3. Cohérence résultat CR / résultat bilan (Invariant #3)

**Règle :** Le résultat net du compte de résultat doit être identique au résultat figurant au passif du bilan.

```
INVARIANT : Résultat_CR = Résultat_Bilan

  Résultat_CR    = Σ(Produits classes 7) - Σ(Charges classes 6) - Participation - IS
  Résultat_Bilan = Poste "Résultat de l'exercice" au passif (compte 120 ou 129)

  SI Résultat_CR ≠ Résultat_Bilan  →  ❌ BLOQUANT
```

---

### 3.4. Calcul TVA déterministe (Invariant #4)

**Règle :** Le montant de TVA doit être exactement égal à la base HT multipliée par le taux applicable.

```
INVARIANT : TVA = Base_HT × Taux

  Pour chaque ligne avec TVA :
    tva_calculee = Base_HT × Taux
    tva_declaree = montant saisi/calculé par le LLM

    SI abs(tva_calculee - tva_declaree) > 0.01  →  ❌ BLOQUANT
    SI abs(tva_calculee - tva_declaree) == 0.01  →  ⚠️ WARNING (arrondi)
    SI abs(tva_calculee - tva_declaree) == 0     →  ✅ PASS

  Règle d'arrondi :
    TVA arrondie au centime inférieur (Convention fiscale française)
    tva = floor(Base_HT × Taux × 100) / 100

  Vérification TTC :
    INVARIANT : TTC = HT + TVA  (exactement)
```

**Les 4 taux à vérifier :**

```python
TAUX_TVA = {
    'normal':        Decimal('0.20'),
    'intermediaire': Decimal('0.10'),
    'reduit':        Decimal('0.055'),
    'super_reduit':  Decimal('0.021'),
}

def verifier_tva(base_ht, tva_saisie, taux_code):
    taux = TAUX_TVA[taux_code]
    tva_attendue = (Decimal(str(base_ht)) * taux).quantize(
        Decimal('0.01'), rounding=ROUND_DOWN
    )
    if tva_saisie != tva_attendue:
        return BLOQUANT(f"TVA attendue : {tva_attendue}, saisie : {tva_saisie}")
    return PASS()
```

---

### 3.5. Résultat fiscal déterministe (Invariant #5)

**Règle :** Résultat fiscal = Résultat comptable + Réintégrations - Déductions. L'IS en découle mécaniquement.

```
INVARIANT :
  Résultat_Fiscal = Résultat_Comptable + Σ(Réintégrations) - Σ(Déductions)

  SI Résultat_Fiscal > 0 :
    SI éligible_taux_PME :
      IS = min(Résultat_Fiscal, 42500) × 0.15
         + max(Résultat_Fiscal - 42500, 0) × 0.25
    SINON :
      IS = Résultat_Fiscal × 0.25

  Vérification :
    SI IS_calculé ≠ IS_présenté  →  ❌ BLOQUANT
```

---

### 3.6. Liquidation TVA déterministe (Invariant #6)

```
INVARIANT :
  TVA_Collectée     = Σ(soldes comptes 44571x)
  TVA_Déductible    = Σ(soldes comptes 44566x + 44562x)
  Crédit_Antérieur  = solde compte 44567

  TVA_Nette = TVA_Collectée - TVA_Déductible - Crédit_Antérieur

  SI TVA_Nette > 0  →  TVA à décaisser (44551) = TVA_Nette     ✅
  SI TVA_Nette < 0  →  Crédit de TVA (44567) = abs(TVA_Nette)  ✅
  SI TVA_Nette == 0 →  Pas de TVA due                           ✅

  Vérification écriture :
    Σ(débits liquidation) = Σ(crédits liquidation)  [Invariant #1]
    ET tous les comptes 44571/44566/44562 sont soldés après l'écriture
```

---

### 3.7. Tableau de synthèse des invariants

| # | Invariant | Formule | Tolérance | Niveau |
|---|-----------|---------|-----------|--------|
| 1 | Équilibre D/C | Σ(D) = Σ(C) | 0,00 € | ❌ Bloquant |
| 2 | Équation du bilan | Actif Net = Passif | 0,00 € | ❌ Bloquant |
| 3 | Résultat CR = Bilan | RN(CR) = RN(Bilan) | 0,00 € | ❌ Bloquant |
| 4 | TVA = HT × Taux | TVA calculée vs saisie | 0,01 € | ❌ Bloquant |
| 5 | Résultat fiscal | RC + Réintég - Déduc = RF | 0,00 € | ❌ Bloquant |
| 6 | Liquidation TVA | Collectée - Déductible = Nette | 0,00 € | ❌ Bloquant |
| 7 | FEC séquentiel | N° écriture N > N° écriture N-1 | 0 | ❌ Bloquant |

---

## 4. Niveau 2 — Contrôles de cohérence croisée

Ces contrôles vérifient la **cohérence entre documents et feuillets**. Pas d'invariant mathématique pur, mais des relations qui doivent tenir entre données de sources différentes.

### 4.1. Matrice de cohérence inter-feuillets (Liasse fiscale)

```
CONTRÔLES AUTOMATISÉS LIASSE FISCALE :

┌──────────────┐     doit être     ┌──────────────┐
│ 2050 case CO │ ════════════════ │ 2051 case EE │
│ Total Actif  │       égal       │ Total Passif │
└──────────────┘                  └──────────────┘

┌──────────────┐     doit être     ┌──────────────┐
│ 2052 case HN │ ════════════════ │ 2051 case DI │
│ Résultat CR  │       égal       │ Résultat Bil.│
└──────────────┘                  └──────────────┘

┌──────────────┐     doit être     ┌──────────────┐
│ 2058-A WA/WB │ ════════════════ │ 2052 case HN │
│ Rés. compta. │       égal       │ Résultat CR  │
└──────────────┘                  └──────────────┘

┌──────────────┐     doit être     ┌──────────────┐
│ 2050 Immob.  │ ════════════════ │ 2054 Totaux  │
│ Brut Bilan   │       égal       │ Brut Tableau │
└──────────────┘                  └──────────────┘

┌──────────────┐     doit être     ┌──────────────┐
│ 2050 Amort.  │ ════════════════ │ 2055 Totaux  │
│ Amort. Bilan │       égal       │ Amort. Table │
└──────────────┘                  └──────────────┘

┌──────────────┐     doit être     ┌──────────────┐
│ 2051 Prov.   │ ════════════════ │ 2056 Totaux  │
│ Prov. Bilan  │       égal       │ Prov. Table  │
└──────────────┘                  └──────────────┘
```

**Implémentation :**

```python
def verifier_coherence_liasse(feuillets):
    controles = []

    # Contrôle 1 : Actif = Passif
    controles.append(check_egal(
        feuillets['2050'].case('CO'),
        feuillets['2051'].case('EE'),
        "Total Actif (2050 CO) = Total Passif (2051 EE)"
    ))

    # Contrôle 2 : Résultat CR = Résultat Bilan
    controles.append(check_egal(
        feuillets['2052'].case('HN'),
        feuillets['2051'].case('DI'),
        "Résultat CR (2052 HN) = Résultat Bilan (2051 DI)"
    ))

    # Contrôle 3 : Résultat comptable 2058-A = CR
    controles.append(check_egal(
        feuillets['2058A'].case('WA') or -feuillets['2058A'].case('WB'),
        feuillets['2052'].case('HN'),
        "Résultat comptable (2058-A) = Résultat CR (2052)"
    ))

    # ... etc pour chaque contrôle

    return all(c.status == PASS for c in controles), controles
```

---

### 4.2. Rapprochement CA comptable / CA déclaré TVA

```
CONTRÔLE :
  CA_Comptable = Σ(soldes comptes 701 à 708)
  CA_Declaré   = Σ(lignes CA3 : L01 + L02 + L04 + L05 + L06)

  Ajustements connus :
    + Produits imposables hors classe 7
    - Produits exonérés inclus en classe 7
    + Acquisitions intracommunautaires (L03)
    +/- Régularisations

  CA_Reconstitué = CA_Comptable + Ajustements
  Écart = CA_Reconstitué - CA_Declaré

  SI abs(Écart) > seuil  →  ⚠️ WARNING : "Écart CA {Écart} € à justifier"
  SI abs(Écart) == 0     →  ✅ PASS
```

---

### 4.3. Continuité des soldes (contrôle inter-périodes)

```
CONTRÔLE :
  Pour chaque compte du bilan :
    Solde_ouverture_N = Solde_cloture_N-1

  SI Solde_ouverture_N ≠ Solde_cloture_N-1
    →  ❌ BLOQUANT : "Rupture de continuité sur le compte {num}"
```

---

### 4.4. Cohérence des tableaux de mouvements

```
CONTRÔLE (applicable aux immobilisations, amortissements, provisions) :

  Solde_fin = Solde_début + Augmentations - Diminutions

  Pour les immobilisations (2054) :
    Brut_fin = Brut_début + Acquisitions + Virements_entrants
               - Cessions - Virements_sortants

  Pour les amortissements (2055) :
    Amort_fin = Amort_début + Dotations - Reprises (sur cessions)

  Pour les provisions (2056) :
    Prov_fin = Prov_début + Dotations - Reprises - Utilisations

  SI équation non vérifiée  →  ❌ BLOQUANT
```

---

### 4.5. Vérification des SIG (chaîne de calcul)

Les 9 SIG forment une **chaîne de calcul** : chaque solde dépend du précédent. Vérifier toute la chaîne.

```
CONTRÔLE CHAÎNE SIG :

  MC  = Ventes_Marchandises - Coût_Achat_Marchandises_Vendues
  PE  = Production_Vendue + Production_Stockée + Production_Immobilisée
  VA  = MC + PE - Consommations_Tiers
  EBE = VA + Subventions_Exploitation - Impôts_Taxes - Charges_Personnel
  RE  = EBE + Reprises + Autres_Produits - Dotations - Autres_Charges
  RCAI = RE + Résultat_Financier
  RExc = Produits_Exceptionnels - Charges_Exceptionnelles
  RN  = RCAI + RExc - Participation - IS
  CAF = RN + Dotations_Total - Reprises_Total + VNC_Cédés - Produits_Cessions - QP_Subventions

  Pour chaque SIG :
    SI SIG_calculé ≠ SIG_présenté  →  ❌ BLOQUANT

  Vérification finale :
    SI RN ≠ Résultat_CR  →  ❌ BLOQUANT : "Le SIG #8 ne correspond pas au résultat du CR"
```

---

### 4.6. Contrôle de la balance générale

```
CONTRÔLE :
  Σ(soldes_débiteurs) = Σ(soldes_créditeurs) sur l'ensemble de la balance

  Par classe :
    Classe 1 : Solde créditeur (sauf résultat déficitaire 129)
    Classe 2 : Solde débiteur (brut)
    Classe 3 : Solde débiteur
    Classe 4 : Variable (chaque compte doit être dans le bon sens)
    Classe 5 : Solde débiteur (sauf découvert bancaire)
    Classe 6 : Solde débiteur
    Classe 7 : Solde créditeur

  SI un compte de charge (6xx) a un solde créditeur net  →  ⚠️ WARNING
  SI un compte de produit (7xx) a un solde débiteur net  →  ⚠️ WARNING
  (sauf extournes, ristournes, avoirs)
```

---

## 5. Niveau 3 — Détection statistique d'anomalies

Ces outils ne vérifient pas des invariants mathématiques mais détectent des **patterns suspects** qui nécessitent investigation.

### 5.1. Loi de Benford (détection de fraude / fabrication de chiffres)

La loi de Benford prédit la distribution des premiers chiffres significatifs dans les données financières naturelles.

```
Distribution attendue du 1er chiffre significatif :
  P(d) = log₁₀(1 + 1/d)    pour d = 1, 2, ..., 9

  d=1: 30,1%    d=4: 9,7%    d=7: 5,8%
  d=2: 17,6%    d=5: 7,9%    d=8: 5,1%
  d=3: 12,5%    d=6: 6,7%    d=9: 4,6%

APPLICATION :
  1. Extraire les montants de toutes les écritures de la période
  2. Calculer la fréquence observée du 1er chiffre significatif
  3. Comparer avec la distribution théorique (test du chi² ou MAD)

  MAD (Mean Absolute Deviation) :
    MAD = (1/9) × Σ|Observé(d) - Attendu(d)|

    SI MAD < 0.006   →  ✅ Conformité forte
    SI MAD < 0.012   →  ✅ Conformité acceptable
    SI MAD < 0.015   →  ⚠️ Conformité marginale
    SI MAD ≥ 0.015   →  ⚠️ Non-conformité : investigation recommandée
```

**Implémentation :**

```python
import math
from collections import Counter

def benford_test(montants):
    # Extraire le premier chiffre significatif
    premiers_chiffres = []
    for m in montants:
        m_abs = abs(m)
        if m_abs >= 1:
            s = str(m_abs).lstrip('0').lstrip('.')
            if s and s[0].isdigit() and s[0] != '0':
                premiers_chiffres.append(int(s[0]))

    # Distribution observée
    total = len(premiers_chiffres)
    if total < 100:
        return SKIP("Échantillon insuffisant (< 100 écritures)")

    observee = Counter(premiers_chiffres)

    # Test MAD
    mad = 0
    for d in range(1, 10):
        attendu = math.log10(1 + 1/d)
        observe = observee.get(d, 0) / total
        mad += abs(observe - attendu)
    mad /= 9

    if mad >= 0.015:
        return WARNING(f"Distribution Benford non conforme (MAD={mad:.4f})")
    return PASS(f"Benford conforme (MAD={mad:.4f})")
```

**Quand l'appliquer :**
- Sur toutes les écritures d'un journal donné
- Sur les écritures manuelles (OD) — les plus susceptibles de manipulation
- Sur les montants de factures fournisseurs
- Exclure : écritures générées automatiquement, salaires (montants fixes)

---

### 5.2. Détection de doublons (exact et fuzzy)

```
ALGORITHME DÉTECTION DOUBLONS :

  Niveau 1 — Doublons exacts :
    Clé = (date, compte, montant, sens)
    SI deux écritures ont la même clé  →  ⚠️ WARNING : "Doublon potentiel"

  Niveau 2 — Doublons flous (fuzzy) :
    Pour chaque paire d'écritures sur le même compte, même sens :
      SI même montant ET |date1 - date2| ≤ 7 jours
        →  ⚠️ WARNING : "Doublon possible à {date1} et {date2}"

  Niveau 3 — Doublons inversés :
    SI même montant, même date, même compte, sens inversé
      ET PAS d'écriture de lettrage associée
        →  ⚠️ WARNING : "Contre-passation sans lettrage"
```

**Implémentation :**

```python
def detecter_doublons(ecritures):
    # Doublons exacts
    vues = {}
    doublons = []
    for e in ecritures:
        cle = (e.date, e.compte, e.montant, e.sens)
        if cle in vues:
            doublons.append((vues[cle], e))
        vues[cle] = e

    # Doublons flous (fenêtre glissante par compte)
    par_compte = defaultdict(list)
    for e in ecritures:
        par_compte[e.compte].append(e)

    for compte, liste in par_compte.items():
        liste.sort(key=lambda e: e.date)
        for i, e1 in enumerate(liste):
            for e2 in liste[i+1:]:
                if abs((e2.date - e1.date).days) > 7:
                    break
                if e1.montant == e2.montant and e1.sens == e2.sens:
                    doublons.append((e1, e2))

    return doublons
```

---

### 5.3. Détection de montants aberrants (Z-Score et IQR)

```
ALGORITHME DÉTECTION OUTLIERS :

  Pour chaque compte, calculer sur la période courante et les 12 derniers mois :

  Méthode Z-Score :
    μ = moyenne des montants
    σ = écart-type des montants
    z = (montant - μ) / σ

    SI |z| > 3.0   →  ⚠️ WARNING : "Montant aberrant (z={z:.1f})"
    SI |z| > 4.5   →  ⚠️ WARNING FORT : "Montant très aberrant"

  Méthode IQR (plus robuste aux outliers) :
    Q1 = percentile 25%
    Q3 = percentile 75%
    IQR = Q3 - Q1
    borne_basse = Q1 - 1.5 × IQR
    borne_haute = Q3 + 1.5 × IQR

    SI montant < borne_basse OU montant > borne_haute
      →  ⚠️ WARNING : "Montant hors intervalle [{borne_basse}, {borne_haute}]"
```

---

### 5.4. Détection d'écritures de fin de période suspectes

```
ALGORITHME :

  Définir la "zone suspecte" : 3 derniers jours ouvrés de chaque mois

  Pour chaque écriture dans la zone suspecte :
    - SI écriture manuelle (journal OD)
    - ET montant > seuil de matérialité
    - ET pas de pièce justificative référencée
      →  ⚠️ WARNING : "Écriture manuelle significative en fin de période"

  Statistique sur 12 mois :
    ratio = nb_ecritures_OD_fin_mois / nb_ecritures_OD_total
    SI ratio > 40%  →  ⚠️ WARNING : "Concentration anormale d'OD en fin de mois"
```

---

### 5.5. Analyse de tendance et rupture de série

```
ALGORITHME :

  Pour chaque compte significatif (solde > seuil de matérialité) :
    Collecter les soldes mensuels sur 12-24 mois

    1. Régression linéaire : y = ax + b
       Calculer R² et résidu de la dernière période

       SI R² > 0.7 ET résidu > 2σ
         →  ⚠️ WARNING : "Rupture de tendance sur le compte {num}"

    2. Test de ratio :
       ratio = solde_N / solde_N-1
       SI ratio > 2.0 OU ratio < 0.5
         →  ⚠️ WARNING : "Variation > 100% sur le compte {num}"
         (sauf comptes de résultat exceptionnel)
```

---

## 6. Niveau 4 — Algorithmes de rapprochement automatique

### 6.1. Algorithme de matching 1-à-1 (rapprochement bancaire)

```
ALGORITHME RAPPROCHEMENT BANCAIRE AUTOMATIQUE :

  Entrées :
    mouvements_GL = liste d'écritures du compte 512
    mouvements_BQ = liste d'opérations du relevé bancaire

  Phase 1 — Matching exact (montant + date) :
    Pour chaque paire (gl, bq) :
      SI gl.montant == bq.montant ET gl.date == bq.date
        →  RAPPROCHÉ (confiance : 100%)
    Retirer les paires rapprochées

  Phase 2 — Matching montant exact, date proche (±5 jours) :
    Pour chaque paire restante :
      SI gl.montant == bq.montant ET |gl.date - bq.date| ≤ 5
        →  RAPPROCHÉ (confiance : 90%)

  Phase 3 — Matching n-à-1 (plusieurs écritures GL = 1 mouvement BQ) :
    Algorithme de somme de sous-ensemble (subset sum) :
    Pour chaque bq non rapproché :
      Chercher une combinaison de gl non rapprochés telle que :
        Σ(gl_subset.montant) == bq.montant
      Contraintes : max 5 éléments, dates dans les 15 jours
      SI trouvé  →  RAPPROCHÉ (confiance : 75%)

  Phase 4 — Éléments non rapprochés :
    →  Liste des éléments en suspens avec vieillissement
```

**Complexité :** Phase 3 est NP-complète (subset sum), mais avec la contrainte de max 5 éléments et des montants bornés, une approche par programmation dynamique est faisable en O(n × S) où S = somme maximale.

```python
def subset_sum_match(montants_gl, cible, max_elements=5):
    """Trouver un sous-ensemble de montants_gl dont la somme = cible."""
    # Convertir en centimes pour éviter les problèmes de virgule flottante
    cible_cents = int(round(cible * 100))
    montants_cents = [int(round(m * 100)) for m in montants_gl]

    # Programmation dynamique avec traceback
    n = len(montants_cents)
    # dp[i] = (possible, nb_elements, dernier_index)
    dp = {0: (True, 0, [])}

    for i, m in enumerate(montants_cents):
        new_dp = {}
        for s, (_, count, indices) in dp.items():
            if count < max_elements:
                new_s = s + m
                if new_s == cible_cents:
                    return indices + [i]  # Solution trouvée
                if new_s < cible_cents and new_s not in new_dp:
                    new_dp[new_s] = (True, count + 1, indices + [i])
        dp.update(new_dp)

    return None  # Pas de solution
```

---

### 6.2. Algorithme de lettrage automatique (comptes tiers)

```
ALGORITHME LETTRAGE AUTOMATIQUE :

  Entrées :
    ecritures = mouvements d'un compte tiers (411x ou 401x)

  Phase 1 — Lettrage 1-à-1 (facture = règlement) :
    débits = [e pour e dans ecritures si e.sens == 'D' et e.non_lettré]
    crédits = [e pour e dans ecritures si e.sens == 'C' et e.non_lettré]

    Pour chaque paire (d, c) :
      SI d.montant == c.montant
        →  LETTRER(d, c, lettre_suivante())

  Phase 2 — Lettrage n-à-1 (1 règlement = n factures) :
    Pour chaque crédit non lettré (compte 411) ou débit non lettré (compte 401) :
      Chercher un sous-ensemble de factures dont la somme = règlement
      (algorithme subset_sum_match ci-dessus)
      SI trouvé  →  LETTRER_GROUPE(factures, règlement)

  Phase 3 — Lettrage avec écart (tolérance d'arrondi) :
    SI |d.montant - c.montant| ≤ seuil_arrondi (ex. 0.05 €)
      →  LETTRER(d, c) + ÉCRITURE_ÉCART(658 ou 758)

  Phase 4 — Non lettrés :
    →  Liste des écritures non lettrées = balance âgée
```

---

### 6.3. Algorithme de matching optimal (Hongrois)

Pour les cas complexes (rapprochement intercompagnie, matching multi-critères), utiliser l'algorithme hongrois (Kuhn-Munkres) pour le matching optimal.

```
ALGORITHME HONGROIS ADAPTÉ :

  Problème :
    Deux listes d'écritures (A et B) à rapprocher
    Coût de matching = f(écart_montant, écart_date, similarité_libellé)

  Matrice de coûts :
    cost[i][j] = w1 × |A[i].montant - B[j].montant| / max(|A[i].montant|, 1)
               + w2 × |A[i].date - B[j].date| / 30
               + w3 × (1 - similarité_cosinus(A[i].libellé, B[j].libellé))

    Poids suggérés : w1 = 0.6, w2 = 0.2, w3 = 0.2

  Contrainte : ne matcher que si cost[i][j] < seuil (ex. 0.3)

  Résolution : algorithme de Kuhn-Munkres → O(n³)

  Résultat : matching optimal minimisant le coût total
```

---

## 7. Niveau 5 — Intégrité et traçabilité cryptographique

### 7.1. Hash chain pour le FEC (Chaîne de hachage)

Le FEC (Fichier des Écritures Comptables) doit être **infalsifiable**. Une chaîne de hachage garantit qu'aucune écriture ne peut être insérée, supprimée ou modifiée sans détection.

```
ALGORITHME HASH CHAIN FEC :

  Pour chaque écriture e[i] dans l'ordre chronologique :
    données = concat(
      e[i].JournalCode,
      e[i].EcritureNum,
      e[i].EcritureDate,
      e[i].CompteNum,
      e[i].Debit,
      e[i].Credit,
      e[i].PieceRef
    )

    SI i == 0 :
      hash[0] = SHA-256(données)
    SINON :
      hash[i] = SHA-256(hash[i-1] + données)

  Vérification :
    Recalculer la chaîne complète
    SI hash_final_recalculé ≠ hash_final_stocké
      →  ❌ BLOQUANT : "Intégrité du FEC compromise"

    Pour localiser la rupture :
      Parcourir la chaîne et identifier le premier hash divergent
```

```python
import hashlib

def construire_hash_chain(ecritures):
    hash_precedent = b''
    for e in ecritures:
        donnees = f"{e.journal}|{e.num}|{e.date}|{e.compte}|{e.debit}|{e.credit}|{e.piece}"
        contenu = hash_precedent + donnees.encode('utf-8')
        hash_courant = hashlib.sha256(contenu).digest()
        e.hash = hash_courant.hex()
        hash_precedent = hash_courant
    return ecritures[-1].hash  # Hash final = empreinte du FEC complet

def verifier_hash_chain(ecritures, hash_attendu):
    hash_calcule = construire_hash_chain(ecritures)
    return hash_calcule == hash_attendu
```

---

### 7.2. Empreinte de vérification des résultats

Chaque sortie du plugin doit inclure une empreinte permettant de vérifier que les données n'ont pas été altérées après calcul.

```
EMPREINTE DE VÉRIFICATION :

  Pour chaque document produit (CA3, bilan, liasse) :

    données_clés = concat(
      date_calcul,
      période,
      total_actif, total_passif,
      résultat_net,
      tva_collectée, tva_déductible, tva_nette,
      is_calculé
    )

    empreinte = SHA-256(données_clés)

    Inclure en pied de document :
    "Empreinte de vérification : {empreinte[:16]}
     Calculé le {date_calcul} — Vérifiable algorithmiquement"
```

---

## 8. Niveau 6 — Simulation et estimation robuste

### 8.1. Monte Carlo pour les provisions

Les provisions (risques, garanties, IDR) impliquent des estimations. La simulation Monte Carlo quantifie l'incertitude.

```
ALGORITHME MONTE CARLO POUR PROVISION IDR :

  Paramètres par salarié :
    - Ancienneté : A (années)
    - Salaire mensuel : S
    - Taux de turnover annuel : t (probabilité de départ avant retraite)
    - Âge actuel : a
    - Âge de départ à la retraite : R
    - Taux d'actualisation : r
    - Convention collective : indemnité = f(A) (ex. 1/4 mois par année < 10 ans, 1/3 au-delà)

  Simulation (N = 10 000 itérations) :
    Pour chaque itération :
      Pour chaque salarié :
        - Simuler la présence à la retraite : Bernoulli(p = (1-t)^(R-a))
        - Si présent : calculer l'indemnité avec ancienneté projetée
        - Actualiser : IDR / (1+r)^(R-a)
      - Total_itération = Σ(IDR individuels)

    Résultat :
      Provision_centrale = médiane(totaux)
      IC_95 = [percentile_2.5%(totaux), percentile_97.5%(totaux)]

  Présentation :
    "Provision IDR : {central} € [IC 95% : {bas} € — {haut} €]"
```

---

### 8.2. Analyse de sensibilité pour l'IS

```
ALGORITHME SENSIBILITÉ IS :

  Variables incertaines :
    - Réintégrations : [min, base, max]
    - Déductions : [min, base, max]
    - Résultat comptable : fixe (déterminé)

  Scénarios :
    IS_optimiste = calcul_IS(RC + reintegrations_min - deductions_max)
    IS_central   = calcul_IS(RC + reintegrations_base - deductions_base)
    IS_pessimiste = calcul_IS(RC + reintegrations_max - deductions_min)

  Présentation :
    "IS estimé : {central} € (fourchette : {optimiste} € à {pessimiste} €)"
    "Sensibilité : ±{max(pessimiste-central, central-optimiste)} €"
```

---

## 9. Minimum vital pour passer un cap

### Priorisation : les 10 outils indispensables

Pour transformer le plugin d'un "assistant" en un outil **fiable**, voici le **strict minimum** à implémenter, classé par priorité d'impact :

| Priorité | Outil | Niveau | Effort | Impact fiabilité |
|:--------:|-------|--------|:------:|:----------------:|
| **P0** | Arithmétique en Decimal (pas de float) | Fondation | Faible | Critique |
| **P0** | Vérification D=C sur chaque écriture | Invariant #1 | Faible | Critique |
| **P0** | Calcul TVA déterministe (HT × Taux) | Invariant #4 | Faible | Critique |
| **P0** | Calcul IS déterministe | Invariant #5 | Faible | Critique |
| **P1** | Vérification Actif = Passif | Invariant #2 | Moyen | Haute |
| **P1** | Chaîne SIG vérifiée | Contrôle §4.5 | Moyen | Haute |
| **P1** | Cohérence inter-feuillets liasse | Contrôle §4.1 | Moyen | Haute |
| **P2** | Détection de doublons | Anomalie §5.2 | Moyen | Moyenne |
| **P2** | Rapprochement bancaire automatique | Matching §6.1 | Élevé | Haute |
| **P2** | Lettrage automatique | Matching §6.2 | Élevé | Haute |

### Le MVP (Minimum Viable Protection)

```
┌─────────────────────────────────────────────────────────────────┐
│  MVP : 4 outils P0 — OBLIGATOIRES avant toute mise en production │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. TYPE DECIMAL PARTOUT                                         │
│     → Jamais de float pour un montant                            │
│     → Arrondi explicite (ROUND_HALF_UP pour les montants,        │
│        ROUND_DOWN pour la TVA)                                   │
│                                                                  │
│  2. VÉRIFICATION D=C                                             │
│     → Sur chaque écriture avant affichage                        │
│     → Tolérance : 0,00 € (zéro absolu)                          │
│     → Si échec : ne PAS afficher l'écriture                      │
│                                                                  │
│  3. CALCUL TVA DÉTERMINISTE                                      │
│     → Entrée : HT + code taux → Sortie : TVA + TTC              │
│     → Le LLM ne calcule JAMAIS la TVA lui-même                   │
│     → L'outil calcule ET vérifie                                 │
│                                                                  │
│  4. CALCUL IS DÉTERMINISTE                                       │
│     → Entrée : résultat fiscal + éligibilité PME                 │
│     → Sortie : IS ventilé (15% / 25%) + total                   │
│     → Le LLM ne calcule JAMAIS l'IS lui-même                     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Roadmap d'implémentation

```
PHASE 1 — Semaine 1-2 : Fondations (P0)
├── Bibliothèque Decimal pour tous les calculs monétaires
├── Fonction verify_balance(ecritures) → PASS/FAIL
├── Fonction calcul_tva(ht, taux) → {tva, ttc}
├── Fonction calcul_is(resultat_fiscal, eligible_pme) → {is_15, is_25, total}
└── Intégration : chaque sortie chiffrée passe par la vérification

PHASE 2 — Semaine 3-4 : Cohérence (P1)
├── Fonction verify_bilan(actif, passif) → PASS/FAIL
├── Fonction verify_sig_chain(sig_data) → PASS/FAIL pour les 9 soldes
├── Fonction verify_liasse_coherence(feuillets) → liste de contrôles
└── Fonction verify_resultat_cr_bilan(cr, bilan) → PASS/FAIL

PHASE 3 — Semaine 5-8 : Détection et automatisation (P2)
├── Fonction detecter_doublons(ecritures) → liste de suspects
├── Fonction rapprochement_bancaire(gl, releve) → matching + suspens
├── Fonction lettrage_auto(ecritures_tiers) → lettrage + solde
└── Fonction benford_test(montants) → conformité + MAD

PHASE 4 — Mois 3+ : Avancé (P3)
├── Hash chain FEC
├── Monte Carlo provisions
├── Algorithme hongrois pour matching complexe
├── Détection outliers (Z-score + IQR)
└── Analyse de tendance et rupture de série
```

---

## 10. Implémentation technique recommandée

### 10.1. Serveur MCP dédié : `mcp-comptabilite-verifier`

La méthode la plus propre est de créer un **serveur MCP dédié** qui expose les outils de vérification comme des tools MCP que Claude peut appeler via `tool_use`.

```json
{
  "mcpServers": {
    "comptabilite-verifier": {
      "type": "http",
      "url": "http://localhost:3847/mcp"
    }
  }
}
```

**Outils MCP exposés :**

| Outil | Entrée | Sortie |
|-------|--------|--------|
| `verify_balance` | `{lignes: [{compte, debit, credit}]}` | `{status: "PASS"/"FAIL", ecart, details}` |
| `calcul_tva` | `{base_ht, taux_code}` | `{tva, ttc, taux_applique}` |
| `calcul_is` | `{resultat_fiscal, eligible_pme}` | `{is_taux_reduit, is_taux_normal, total_is}` |
| `verify_bilan` | `{actif_net, passif}` | `{status, ecart}` |
| `verify_sig_chain` | `{sig_data: {mc, pe, va, ...}}` | `{status, erreurs[]}` |
| `verify_liasse` | `{feuillets: {f2050, f2051, ...}}` | `{controles: [{regle, status, ecart}]}` |
| `rapprocher_banque` | `{gl: [...], releve: [...]}` | `{rapproches, suspens_gl, suspens_bq}` |
| `lettrer_compte` | `{ecritures: [...]}` | `{lettrees, non_lettrees, ecarts}` |
| `benford_test` | `{montants: [...]}` | `{conforme, mad, distribution}` |
| `detecter_doublons` | `{ecritures: [...]}` | `{doublons: [{e1, e2, confiance}]}` |
| `hash_fec` | `{ecritures: [...]}` | `{hash_final, chaine_valide}` |
| `calcul_liquidation_tva` | `{collectee, deductible, credit_ant}` | `{tva_nette, sens, ecriture}` |

### 10.2. Stack technique recommandée

```
Langage : Python 3.12+ (ou TypeScript avec decimal.js)

Bibliothèques essentielles :
  ├── decimal         → Arithmétique exacte (standard library Python)
  ├── hashlib         → SHA-256 pour hash chain FEC
  ├── scipy.stats     → Tests statistiques (chi², Benford)
  ├── numpy           → Calculs statistiques (Z-score, IQR, régression)
  ├── scipy.optimize  → Algorithme hongrois (linear_sum_assignment)
  └── fastmcp         → Framework serveur MCP Python

Format d'échange : JSON avec montants en string
  "montant": "1247.50"    ← String, pas float
  Parsing : Decimal(montant_string)
```

### 10.3. Convention de réponse

Chaque outil retourne un objet structuré :

```json
{
  "status": "PASS | FAIL | WARNING | SKIP",
  "controles": [
    {
      "nom": "Équilibre débit/crédit",
      "status": "PASS",
      "attendu": "0.00",
      "obtenu": "0.00",
      "ecart": "0.00",
      "bloquant": true
    },
    {
      "nom": "Loi de Benford",
      "status": "WARNING",
      "attendu": "MAD < 0.015",
      "obtenu": "MAD = 0.018",
      "ecart": "0.003",
      "bloquant": false,
      "message": "Distribution des premiers chiffres non conforme — investigation recommandée"
    }
  ],
  "resume": "11 contrôles passés, 0 bloquants, 1 avertissement",
  "empreinte": "a7f3c2d1e8b9..."
}
```

### 10.4. Intégration dans le workflow du plugin

```
AVANT (plugin actuel — LLM seul) :
  Utilisateur → /ecriture achat → LLM calcule → Affiche le résultat
  ⚠️ Risque : erreur arithmétique non détectée

APRÈS (plugin avec moteur de vérification) :
  Utilisateur → /ecriture achat → LLM prépare les données
    → tool_use: calcul_tva(base_ht=10000, taux_code="normal")
    → tool_use: verify_balance(lignes=[...])
    → SI tout PASS : LLM affiche le résultat avec ✅
    → SI FAIL : LLM affiche l'erreur et tente de corriger
    → SI WARNING : LLM affiche le résultat avec ⚠️ et explication
```

---

> **Conclusion :** Un plugin comptable basé sur un LLM sans contrôles algorithmiques est un outil de rédaction, pas un outil de comptabilité. Les 4 outils P0 (Decimal, D=C, TVA déterministe, IS déterministe) constituent le **seuil de crédibilité minimum**. Les niveaux 2-6 transforment progressivement le plugin en un outil de niveau professionnel. L'architecture recommandée — un serveur MCP `comptabilite-verifier` exposant des tools déterministes — est la solution la plus propre : le LLM orchestre, le code vérifie, l'utilisateur est protégé.
