# Analyse Complète du Plugin Finance

> **Date d'analyse :** 15 février 2026
> **Version du plugin :** 1.0.0
> **Auteur du plugin :** Anthropic
> **Licence :** Apache 2.0

---

## Table des Matières

1. [Vue d'ensemble](#1-vue-densemble)
2. [Architecture et structure des fichiers](#2-architecture-et-structure-des-fichiers)
3. [Métriques détaillées (mots, lignes, couverture)](#3-métriques-détaillées)
4. [Analyse des Commandes (5)](#4-analyse-des-commandes)
5. [Analyse des Skills (6)](#5-analyse-des-skills)
6. [Connecteurs et intégrations MCP](#6-connecteurs-et-intégrations-mcp)
7. [Faire de la finance sans connecteur ERP](#7-faire-de-la-finance-sans-connecteur-erp)
8. [Comment devra être un connecteur ERP](#8-comment-devra-être-un-connecteur-erp)
9. [Traitement des tâches comptables majeures](#9-traitement-des-tâches-comptables-majeures)
10. [Forces et limites du plugin](#10-forces-et-limites-du-plugin)
11. [Synthèse comparative Commandes vs Skills](#11-synthèse-comparative-commandes-vs-skills)

---

## 1. Vue d'ensemble

Le plugin Finance est conçu pour **Cowork** (l'application desktop agentique d'Anthropic) et **Claude Code**. Il couvre les workflows de finance et comptabilité d'entreprise :

- Clôture mensuelle (month-end close)
- Préparation d'écritures comptables (journal entries)
- Rapprochement de comptes (reconciliation)
- Génération d'états financiers (financial statements)
- Analyse de variance / flux (variance analysis)
- Support d'audit SOX 404

**Avertissement intégré :** Chaque fichier du plugin contient un disclaimer indiquant qu'il ne fournit pas de conseil financier, fiscal ou d'audit. Tous les résultats doivent être revus par des professionnels qualifiés.

### Architecture 100% déclarative

Le plugin ne contient **aucun code exécutable**. Il est composé exclusivement de fichiers Markdown et JSON qui servent de prompts structurés pour Claude. Les commandes définissent des workflows étape par étape, et les skills fournissent des bases de connaissances méthodologiques que Claude consulte en contexte.

---

## 2. Architecture et structure des fichiers

```
finance/
├── .claude-plugin/
│   └── plugin.json              # Métadonnées du plugin (nom, version, description)
├── .mcp.json                    # Configuration des serveurs MCP
├── README.md                    # Documentation principale
├── CONNECTORS.md                # Référence des connecteurs
├── LICENSE                      # Licence Apache 2.0
│
├── commands/                    # 5 commandes slash invocables par l'utilisateur
│   ├── income-statement.md      # /income-statement — Génération du compte de résultat
│   ├── journal-entry.md         # /je — Préparation d'écritures comptables
│   ├── reconciliation.md        # /recon — Rapprochement de comptes
│   ├── sox-testing.md           # /sox — Tests de conformité SOX
│   └── variance-analysis.md     # /flux — Analyse de variance
│
└── skills/                      # 6 skills (base de connaissances méthodologique)
    ├── audit-support/
    │   └── SKILL.md             # Méthodologie d'audit SOX 404
    ├── close-management/
    │   └── SKILL.md             # Gestion de la clôture mensuelle
    ├── financial-statements/
    │   └── SKILL.md             # Formats GAAP des états financiers
    ├── journal-entry-prep/
    │   └── SKILL.md             # Préparation des écritures
    ├── reconciliation/
    │   └── SKILL.md             # Méthodologie de rapprochement
    └── variance-analysis/
        └── SKILL.md             # Techniques d'analyse de variance
```

**Total : 16 fichiers** (2 config JSON + 3 fichiers racine MD + 5 commandes + 6 skills)

---

## 3. Métriques détaillées

### 3.1. Nombre de mots par fichier

| Fichier | Catégorie | Mots | Lignes | Ratio mots/ligne |
|---------|-----------|-----:|-------:|------------------:|
| `plugin.json` | Config | 39 | ~8 | 4.9 |
| `.mcp.json` | Config | 40 | ~18 | 2.2 |
| `README.md` | Doc | 689 | ~90 | 7.7 |
| `CONNECTORS.md` | Doc | 183 | ~25 | 7.3 |
| `LICENSE` | Légal | 1 581 | ~200 | 7.9 |
| **Sous-total Config/Doc** | | **2 532** | | |
| | | | | |
| `commands/income-statement.md` | Commande | 700 | 138 | 5.1 |
| `commands/journal-entry.md` | Commande | 731 | 130 | 5.6 |
| `commands/reconciliation.md` | Commande | 701 | 147 | 4.8 |
| `commands/sox-testing.md` | Commande | 1 204 | 217 | 5.5 |
| `commands/variance-analysis.md` | Commande | 885 | 158 | 5.6 |
| **Sous-total Commandes** | | **4 221** | **790** | **5.3 moy.** |
| | | | | |
| `skills/audit-support/SKILL.md` | Skill | 2 312 | 373 | 6.2 |
| `skills/close-management/SKILL.md` | Skill | 1 506 | 220 | 6.8 |
| `skills/financial-statements/SKILL.md` | Skill | 1 341 | 261 | 5.1 |
| `skills/journal-entry-prep/SKILL.md` | Skill | 1 103 | 185 | 6.0 |
| `skills/reconciliation/SKILL.md` | Skill | 1 148 | 174 | 6.6 |
| `skills/variance-analysis/SKILL.md` | Skill | 1 628 | 265 | 6.1 |
| **Sous-total Skills** | | **9 038** | **1 478** | **6.1 moy.** |
| | | | | |
| **GRAND TOTAL** | | **15 791** | **~2 268+** | |

### 3.2. Répartition proportionnelle

```
Skills        ████████████████████████████████████████░░  57.2%  (9 038 mots)
Commandes     ██████████████████████░░░░░░░░░░░░░░░░░░░░  26.7%  (4 221 mots)
Config/Doc    ██████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  16.0%  (2 532 mots)
```

### 3.3. Classement par taille (mots) — Commandes et Skills uniquement

| Rang | Fichier | Type | Mots |
|------|---------|------|-----:|
| 1 | audit-support/SKILL.md | Skill | 2 312 |
| 2 | variance-analysis/SKILL.md | Skill | 1 628 |
| 3 | close-management/SKILL.md | Skill | 1 506 |
| 4 | financial-statements/SKILL.md | Skill | 1 341 |
| 5 | sox-testing.md | Commande | 1 204 |
| 6 | reconciliation/SKILL.md | Skill | 1 148 |
| 7 | journal-entry-prep/SKILL.md | Skill | 1 103 |
| 8 | variance-analysis.md | Commande | 885 |
| 9 | journal-entry.md | Commande | 731 |
| 10 | reconciliation.md | Commande | 701 |
| 11 | income-statement.md | Commande | 700 |

### 3.4. Densité sémantique par commande

| Commande | Mots | Nb étapes workflow | Nb types/arguments | Templates de sortie | Points checklist |
|----------|-----:|------------------:|------------------:|-------------------:|-----------------:|
| `/sox` | 1 204 | 7 | 10 domaines | 3 (matrice, sample, workpaper) | 5 niveaux conclusion |
| `/flux` | 885 | 7 | 6 domaines x 6 comparaisons | 2 (waterfall, narratif) | — |
| `/je` | 731 | 5 | 5 types | 1 (JE formatée) | 11 points |
| `/recon` | 701 | 7 | 7+ types comptes | 2 (workpaper, détail) | 5 critères escalade |
| `/income-statement` | 700 | 6 | 4 types période | 3 (P&L, métriques, variances) | Seuils configurables |

---

## 4. Analyse des Commandes

Les commandes sont des **prompts slash** que l'utilisateur invoque directement. Elles définissent un workflow étape par étape avec des arguments, des templates de sortie, et des instructions pour Claude.

### 4.1. `/journal-entry` (alias `/je`) — 731 mots, 130 lignes

**Fonction :** Préparer des écritures comptables avec débits, crédits et pièces justificatives.

| Attribut | Détail |
|----------|--------|
| **Syntaxe** | `/je <type> <period>` |
| **Types d'écriture** | `ap-accrual`, `fixed-assets`, `prepaid`, `payroll`, `revenue` |
| **Périodes** | `2024-12`, `2024-Q4`, `2024` |
| **Étapes du workflow** | 5 (collecte données → calcul → génération JE → checklist → sortie) |
| **Template de sortie** | Tableau formaté (ligne, code compte, nom, débit, crédit, département, mémo) |
| **Checklist de revue** | 11 points de vérification |

**Détails par type d'écriture :**
- **AP Accrual :** Biens/services reçus non facturés → Débit charges, Crédit dettes fournisseurs. Sources : bons de commande, contrats, arrangements récurrents, notes de frais non traitées.
- **Fixed Assets :** Calcul amortissement par classe d'actif. 3 méthodes supportées : linéaire (Cost - Salvage / Useful life), dégressif (taux fixe sur valeur nette), unités de production (usage réel vs total attendu).
- **Prepaid :** Étalement des charges constatées d'avance (assurance, logiciels, loyers, contrats de maintenance, acomptes événements).
- **Payroll :** 4 composantes : provision salaires (jours travaillés non payés), provision avantages (santé, retraite, congés), taxes employeur (FICA, FUTA), primes (selon termes du plan).
- **Revenue :** Reconnaissance selon obligations de performance (ASC 606). 3 schémas : débit produits constatés d'avance / crédit revenu, débit créances / crédit revenu, débit cash / crédit produits constatés d'avance.

**Checklist de revue complète (11 points) :**
1. Débits = Crédits
2. Période comptable correcte
3. Codes comptes valides
4. Montants correctement calculés avec justificatifs
5. Mémo/description clair et suffisant pour l'audit
6. Codification département/centre de coût correcte
7. Cohérence avec le traitement de la période précédente
8. Flag d'extourne défini (les provisions doivent s'auto-extourner)
9. Documentation justificative référencée ou jointe
10. Écriture dans les limites d'autorité du préparateur
11. Pas de montants inhabituels ou hors tendance nécessitant investigation

---

### 4.2. `/reconciliation` (alias `/recon`) — 701 mots, 147 lignes

**Fonction :** Rapprochement du solde GL avec le sous-ledger, relevé bancaire ou tiers.

| Attribut | Détail |
|----------|--------|
| **Syntaxe** | `/recon <account> <period>` |
| **Types de comptes** | `cash`/`bank`, `ar`/`accounts-receivable`, `ap`/`accounts-payable`, `fixed-assets`/`fa`, `intercompany`/`ic`, `prepaid`, `accrued-liabilities`, ou code GL spécifique |
| **Étapes du workflow** | 7 (collecte des 2 côtés → comparaison → identification écarts → workpaper → détail → revue → sortie) |
| **Template de sortie** | Workpaper de rapprochement complet avec format standard |

**Catégorisation des écarts :**

| Catégorie | Exemples | Traitement |
|-----------|----------|------------|
| **Différences de timing** | Chèques en circulation, dépôts en transit, factures en transit | Attendre le dénouement |
| **Différences permanentes** | Erreurs, écritures manquantes, frais bancaires, écarts de change | Écriture corrective |
| **Éléments antérieurs** | Items de la période précédente encore ouverts | Report avec vieillissement |

**Template du workpaper :**
```
Solde GL                          $XX,XXX.XX
+ Éléments augmentant le GL       $X,XXX.XX
- Éléments diminuant le GL       ($X,XXX.XX)
= Solde GL ajusté                 $XX,XXX.XX

Solde Banque/Auxiliaire           $XX,XXX.XX
+/- Éléments de rapprochement
= Solde ajusté                    $XX,XXX.XX

DIFFÉRENCE :                      $0.00
```

**Seuils d'escalade :**
- Éléments âgés > 30 / 60 / 90 jours
- Éléments individuels dépassant les seuils de matérialité
- Soldes d'écarts croissants d'une période à l'autre
- Éléments antérieurs non résolus
- Différences inexpliquées

---

### 4.3. `/income-statement` — 700 mots, 138 lignes

**Fonction :** Génération du compte de résultat avec comparaison période sur période et analyse de variance.

| Attribut | Détail |
|----------|--------|
| **Syntaxe** | `/income-statement <period-type> <period>` |
| **Types de période** | `monthly`, `quarterly`, `annual`, `ytd` |
| **Étapes du workflow** | 6 (collecte → génération P&L → analyse variance → métriques clés → résumé variances → sortie) |
| **Colonnes du template** | Période courante, Période précédente, Variance ($), Variance (%), Budget, Variance Budget |

**Structure du P&L généré :**
```
REVENUE (Product, Service, Other)
COST OF REVENUE
GROSS PROFIT + Gross Margin %
OPERATING EXPENSES (R&D, S&M, G&A)
OPERATING INCOME + Operating Margin %
OTHER INCOME (EXPENSE) (Interest, Other net)
INCOME BEFORE TAXES
NET INCOME + Net Margin %
```

**Métriques clés calculées :**
- Croissance du revenu (%)
- Marge brute (%), Marge opérationnelle (%), Marge nette (%)
- OpEx en % du revenu
- Taux effectif d'imposition (%)
- Variations exprimées en points de pourcentage (pp)

**Seuils de matérialité (configurables par l'utilisateur) :**
- Variance en dollars > seuil défini (ex. 50K$, 100K$)
- Variance en % > 10% (ou seuil personnalisé)
- Si l'un OU l'autre est dépassé → flag

---

### 4.4. `/variance-analysis` (alias `/flux`) — 885 mots, 158 lignes

**Fonction :** Décomposition des variances en facteurs explicatifs avec narratifs et analyse en cascade (waterfall).

| Attribut | Détail |
|----------|--------|
| **Syntaxe** | `/flux <area> <period-comparison>` |
| **Domaines** | `revenue`, `opex`, `capex`, `headcount`, `cogs`/`cost-of-revenue`, `gross-margin`, ou code GL |
| **Comparaisons** | MoM, YoY, QoQ, actual vs budget, actual vs forecast, three-way |
| **Étapes du workflow** | 7 (collecte → variance top-level → décomposition → waterfall → narratifs → variances inexpliquées → sortie) |

**Décomposition par domaine :**

| Domaine | Facteurs de décomposition |
|---------|--------------------------|
| **Revenue** | Volume, Prix/ASP, Mix produit, New vs existing, Effet devises |
| **OpEx** | Effectifs, Rémunération, Volume-driven, Nouveaux programmes, One-time, Timing |
| **CapEx** | Par projet, Timing, Scope changes, Cost overruns |
| **Headcount** | Rythme de recrutement, Attrition, Mix rémunération, Contractors/intérim |

**Template waterfall (texte) :**
```
[Base Période 2]                          $XX,XXX
  |--[+] Driver 1 description           +$X,XXX
  |--[+] Driver 2 description           +$X,XXX
  |--[-] Driver 3 description           -$X,XXX
  |
[Réel Période 1]                          $XX,XXX

Réconciliation : Driver 1 (XX%) + Driver 2 (XX%) - Driver 3 (XX%) + Inexpliqué (XX%) = 100%
```

**Structure du narratif pour chaque driver significatif :**
- Nom du driver + Favorable/Défavorable + montant + %
- 2-3 phrases d'explication causale avec quantification
- Outlook : tendance attendue (continuation, renversement, changement)

---

### 4.5. `/sox-testing` (alias `/sox`) — 1 204 mots, 217 lignes

**Fonction :** Tests de conformité SOX 404 — sélection d'échantillons, papiers de travail, évaluation des contrôles.

C'est la **commande la plus volumineuse** du plugin (1 204 mots).

| Attribut | Détail |
|----------|--------|
| **Syntaxe** | `/sox <control-area> <period>` |
| **Domaines de contrôle** | 10 domaines + ID spécifique |
| **Étapes du workflow** | 7 (identification → taille échantillon → sélection → workpaper → templates → évaluation → sortie) |

**Les 10 domaines de contrôle :**

| # | Domaine | Description |
|---|---------|-------------|
| 1 | `revenue-recognition` | Cycle order-to-cash |
| 2 | `procure-to-pay` / `p2p` | Cycle purchase-to-pay |
| 3 | `payroll` | Traitement paie et rémunération |
| 4 | `financial-close` | Contrôles de clôture |
| 5 | `treasury` | Gestion de trésorerie |
| 6 | `fixed-assets` | Cycle de vie des immobilisations |
| 7 | `inventory` | Valorisation et gestion des stocks |
| 8 | `itgc` | Contrôles généraux IT |
| 9 | `entity-level` | Contrôles au niveau entité |
| 10 | `journal-entries` | Contrôles sur les écritures |

**Framework d'assertions (CEAVOP) :**
- **C**omplétude — Toutes les transactions sont enregistrées
- **E**xistence/Occurrence — Les transactions ont réellement eu lieu
- **A**ccuracy — Les montants sont correctement enregistrés
- **V**aluation — Les actifs/passifs sont correctement valorisés
- **O**bligations/Rights — L'entité a les droits sur les actifs
- **P**résentation/Disclosure — Correctement classifié et divulgué

**Guide de taille d'échantillon :**

| Fréquence du contrôle | Population | Échantillon recommandé |
|----------------------|-----------|----------------------|
| Annuel | 1 | 1 (tester l'instance) |
| Trimestriel | 4 | 2 |
| Mensuel | 12 | 2-4 (selon risque) |
| Hebdomadaire | 52 | 5-15 (selon risque) |
| Quotidien | ~250 | 20-40 (selon risque) |
| Par transaction | Variable | 25-60 (selon risque et volume) |

**3 méthodes de sélection :**
1. **Aléatoire** (défaut pour contrôles transactionnels) — couverture sur toute la période
2. **Systématique** — intervalles fixes avec point de départ aléatoire
3. **Ciblée** — items à caractéristiques de risque spécifiques (montant élevé, inhabituel, fin de période)

**5 niveaux de conclusion :**
1. Effectif — aucune exception
2. Effectif avec exceptions — exceptions isolées
3. Déficience — contrôle non effectif
4. Déficience significative — plus qu'inconséquentiel
5. Faiblesse matérielle — possibilité raisonnable d'anomalie significative

**Templates de contrôle pré-construits (20 contrôles) :**
- Revenue Recognition : 5 contrôles (approbation commande, preuve de livraison, timing revenu, prix, avoirs)
- Procure to Pay : 5 contrôles (approbation PO, three-way match, données fournisseurs, paiement, doublons)
- Financial Close : 5 contrôles (exhaustivité rapprochements, approbation JE, revue direction, consolidation, checklist disclosure)
- ITGC : 5 contrôles (provisioning accès, revue accès privilégiés, change management, jobs batch, backup/recovery)

---

## 5. Analyse des Skills

Les skills sont des **bases de connaissances méthodologiques** que Claude consulte en contexte. Contrairement aux commandes, elles ne sont pas invoquées directement par l'utilisateur mais enrichissent les réponses de Claude avec des bonnes pratiques et des méthodologies.

### 5.1. `audit-support` — 2 312 mots, 373 lignes (le plus gros fichier du plugin)

**Rôle :** Méthodologie complète de test SOX 404.

**Sections couvertes :**

| Section | Contenu |
|---------|---------|
| **Scoping** | Comptes significatifs avec facteurs quantitatifs (% des actifs/revenus/charges) et qualitatifs (complexité, historique, risque fraude) |
| **Assertions CEAVOP** | Table des assertions pertinentes par type de compte (trésorerie, revenus, stocks, immobilisations, etc.) |
| **Design vs Operating** | Différence entre efficacité de conception et efficacité opérationnelle |
| **Échantillonnage** | 4 approches : aléatoire, ciblée (judgmental), haphazard, systématique |
| **Taille d'échantillon** | Table par fréquence de contrôle ET niveau de risque (3 niveaux) |
| **Documentation** | Standards des papiers de travail, exigences de preuve suffisante vs insuffisante |
| **Organisation** | Structure des papiers de travail par domaine de contrôle |
| **Déficiences** | 3 niveaux (déficience, significative, material weakness) + analyse d'agrégation |
| **Remédiation** | Planification avec analyse des causes racines |
| **Types de contrôles** | ITGCs (accès, change management, opérations IT), manuels, automatisés, IT-dependent manual, entity-level — chacun avec exemples et approche de test |

---

### 5.2. `close-management` — 1 506 mots, 220 lignes

**Rôle :** Gestion du processus de clôture mensuelle.

**Sections couvertes :**

| Section | Contenu |
|---------|---------|
| **Checklist de clôture** | 6 phases : Pre-close (7 tâches), T+1 (8 tâches), T+2 (7 tâches), T+3 (6 tâches), T+4 (6 tâches), T+5 (6 tâches) — **40 tâches au total** |
| **Dépendances** | 5 niveaux de séquencement avec carte de dépendances détaillée |
| **Chemin critique** | Séquence minimum : Cash/AP/AR → Subledger recs → BS recs → Tax → Draft → Revue → Hard close |
| **Stratégies d'accélération** | 5 leviers : automatisation Level 1, rapprochement continu, parallélisation, deadlines strictes, templates standardisés |
| **Dashboard de suivi** | Format : Task / Owner / Deadline / Status / Blocker / Notes |
| **5 statuts** | Not Started, In Progress, Complete, Blocked, At Risk |
| **Standup quotidien** | Format 15 min : revue statuts → identification blockers → réassignation → mise à jour timeline |
| **5 métriques** | Durée clôture, ajustements post-soft close, tâches en retard, exceptions rapprochement, retraitements |
| **Calendrier 5 jours** | Activités détaillées par jour avec responsables assignés |
| **Clôture accélérée 3 jours** | Prérequis : JE automatisées, rapprochement continu, éliminations interco auto, pre-close, équipe autonome, intégration temps réel |
| **Goulots d'étranglement** | 6 problèmes courants avec causes racines et solutions |
| **Rétrospective** | 5 questions de retour d'expérience |

---

### 5.3. `financial-statements` — 1 341 mots, 261 lignes

**Rôle :** Formats d'états financiers et exigences de présentation GAAP.

**Les 3 états financiers couverts :**

| État | Format | Normes référencées |
|------|--------|-------------------|
| **Compte de résultat** | Classification par fonction (COGS, R&D, S&M, G&A) | ASC 220 / IAS 1 |
| **Bilan** | Classifié courant/non-courant, ordre de liquidité | ASC 210 / IAS 1, ASC 326, ASC 350, ASC 842 |
| **Tableau des flux de trésorerie** | Méthode indirecte (la plus courante) | ASC 230 / IAS 7 |

**Considérations de présentation détaillées :**
- Revenue disaggregation (ASC 606)
- Stock-based compensation (répartition par fonction)
- Restructuring charges (présentation séparée si matériel)
- Non-GAAP adjustments (réconciliation obligatoire)
- Goodwill non amorti (test de dépréciation annuel, ASC 350)
- Leases (actifs/passifs de droit d'utilisation, ASC 842)
- Créances nettes d'allowance (ASC 326)

**8 types d'ajustements de fin de période :**
1. Accruals (charges constatées d'avance)
2. Deferrals (produits/charges différés)
3. Depreciation/Amortization
4. Bad debt provision (provision créances douteuses)
5. Inventory adjustments (dépréciation stocks)
6. FX revaluation (réévaluation devises)
7. Tax provision (impôts courants et différés)
8. Fair value adjustments (mark-to-market)

**6 types de reclassifications :**
1. Current/non-current (dette LT arrivant à maturité)
2. Contra account netting (allowances, amortissement cumulé)
3. Intercompany elimination
4. Discontinued operations
5. Equity method adjustments
6. Segment reclassifications

**Méthodologie de flux analysis intégrée :**
- Calcul de variance ($, %, basis points)
- Seuils de matérialité par taille de poste (table 3 niveaux)
- 7 types de décomposition (volume, prix, mix, nouveau, one-time, timing, devises)
- Process d'investigation et narratif en 6 étapes

---

### 5.4. `journal-entry-prep` — 1 103 mots, 185 lignes

**Rôle :** Bonnes pratiques de préparation des écritures comptables.

**5 types d'écritures standards avec guidance détaillée :**

| Type | Débit | Crédit | Sources | Considérations clés |
|------|-------|--------|---------|-------------------|
| **AP Accruals** | Charges (ou actif) | Dettes fournisseurs | PO ouverts, contrats, récurrents, notes de frais | Auto-reversal, estimation cohérente, tracking réel vs provision |
| **Fixed Asset Depreciation** | Dotation amortissement | Amortissement cumulé | Registre immobilisations | Vérifier nouvelles additions, cessions, dépréciations |
| **Prepaid Amortization** | Charges (assurance, logiciels, loyer) | Charges constatées d'avance | Tableau d'amortissement | Dates début/fin, items immaterial, contrats résiliés |
| **Payroll Accruals** | Salaires, Avantages, Taxes | Provisions correspondantes | Registre paie | Jours travaillés, termes du plan bonus, taxes employeur, congés |
| **Revenue Recognition** | Produits constatés d'avance / Créances | Revenus | Contrats clients | Framework ASC 606 en 5 étapes |

**7 éléments de documentation obligatoires :** Description, Calculs, Pièces justificatives, Période, Préparateur, Approbation, Indicateur d'extourne.

**Matrice d'approbation :**

| Type d'écriture | Seuil | Approbateur |
|----------------|-------|------------|
| Récurrent standard | Tout montant | Accounting Manager |
| Non-récurrent / manuel | < 50K$ | Accounting Manager |
| Non-récurrent / manuel | 50K$-250K$ | Controller |
| Non-récurrent / manuel | > 250K$ | CFO / VP Finance |
| Top-side / consolidation | Tout montant | Controller+ |
| Ajustement hors période | Tout montant | Controller+ |

**12 erreurs courantes à détecter :**
Écritures non équilibrées, Mauvaise période, Mauvais signe, Doublons, Mauvais compte, Reversal manquant, Accruals stales, Montants ronds suspects, Taux FX incorrect, Intercompagnie sans élimination, Erreurs de capitalisation, Erreurs de cut-off.

---

### 5.5. `reconciliation` — 1 148 mots, 174 lignes

**Rôle :** Méthodologie de rapprochement de comptes.

**3 types de rapprochement avec processus détaillé :**

| Type | Côté 1 | Côté 2 | Processus spécifique |
|------|--------|--------|---------------------|
| **GL-to-Subledger** | Solde GL | Total auxiliaire | Comparer totaux, analyser les écarts d'interface |
| **Bancaire** | Solde GL trésorerie | Relevé bancaire | Pointer chèques/dépôts, identifier items non pointés |
| **Intercompagnie** | Solde entité A | Solde entité B | Confirmer avec contrepartie, identifier écarts bilatéraux |

**3 catégories d'éléments rapprochants :**

| Catégorie | Résolution attendue | Exemples |
|-----------|-------------------|----------|
| **Timing** | Auto-résolution | Posting timing, corrections en cours |
| **Ajustement requis** | Écriture corrective | Erreurs, écritures manquantes |
| **Investigation** | Analyse approfondie | Écarts inexpliqués, items récurrents |

**Analyse de vieillissement (aging) :**
- Buckets : 0-30 jours, 31-60 jours, 61-90 jours, >90 jours
- Tracking par catégorie d'âge avec statut et responsable
- Tendances des éléments rapprochants dans le temps

**Seuils d'escalade :**

| Critère | Déclencheur |
|---------|------------|
| Montant individuel | Dépasse le seuil de matérialité |
| Total des éléments | Somme des écarts excessive |
| Âge de l'élément | > 30/60/90 jours selon la politique |
| Différence non rapprochée | Écart résiduel inexpliqué |
| Tendance croissante | Total des écarts en hausse |

**8 bonnes pratiques :** Rapidité, Complétude, Documentation, Séparation des tâches, Suivi systématique, Analyse des causes racines, Standardisation, Conservation des documents.

---

### 5.6. `variance-analysis` — 1 628 mots, 265 lignes

**Rôle :** Techniques avancées d'analyse de variance.

**5 techniques de décomposition avec formules :**

| Technique | Formule | Application |
|-----------|---------|-------------|
| **Prix/Volume** | Volume = ΔQ × P_prior ; Prix = ΔP × Q_current | Revenue, COGS |
| **Three-way (avec Mix)** | Volume + Prix + Mix (résiduel) | Revenue multi-produit |
| **Rate/Mix** | Rate = ΔRate × Volume ; Mix = ΔMix × Volume total | Marge par segment |
| **Headcount/Compensation** | HC = ΔHC × Comp/HC_prior ; Comp = ΔComp/HC × HC_current | Charges de personnel |
| **Par catégorie** | ΔCatégorie pour chaque ligne de dépense | OpEx, CapEx |

**Seuils de matérialité recommandés :**

| Type de comparaison | Seuil % | Seuil $ (ex.) |
|--------------------|--------:|---------------:|
| Actual vs Budget | 10% | Selon taille |
| YoY | 15% | Selon taille |
| Actual vs Forecast | 5% | Selon taille |
| Séquentiel (MoM/QoQ) | 20% | Selon taille |

**Guide de narratif :**
- Structure : Quoi (montant/%) → Pourquoi (cause business) → Et ensuite (outlook)
- Checklist qualité : Spécifique, Quantifié, Actionnable, Contextuel
- Anti-patterns à éviter : "Divers", "Comme prévu", narratifs vagues sans montants, attribution causale sans preuve

**Méthodologie waterfall :**
- Format textuel avec base → drivers → résultat
- Table de bridge reconciliation
- Bonnes pratiques : ordonner du plus grand au plus petit, consolider les petits items dans "Autres", vérifier que la somme = total, distinction favorable/défavorable

**Framework Budget vs Actual vs Forecast :**

| Comparaison | Quand l'utiliser | Ce qu'elle montre |
|-------------|-----------------|-------------------|
| Actual vs Budget | Planning annuel, accountability | Performance vs objectifs approuvés |
| Actual vs Forecast | Gestion courante, rolling forecast | Performance vs attentes récentes |
| Budget vs Forecast | Planification, reforecasting | Évolution des attentes vs plan initial |

- Analyse de précision des prévisions avec calcul MAPE (Mean Absolute Percentage Error)
- Trending de variance pour identifier les biais systématiques (surestimation/sous-estimation récurrente)

---

## 6. Connecteurs et intégrations MCP

### 6.1. Fonctionnement des connecteurs

Le plugin utilise un système de **placeholders catégoriels** (`~~category`) pour référencer les outils de manière agnostique. Par exemple, `~~data warehouse` peut signifier Snowflake, BigQuery, ou tout autre entrepôt de données avec un serveur MCP.

Les fichiers de commandes contiennent des **instructions conditionnelles** :
```
If ~~erp or ~~data warehouse is connected:
  → Pull des données automatiquement

If no data source is connected:
  → Demander à l'utilisateur de fournir les données manuellement
```

Cette architecture "tool-agnostic" signifie que les workflows décrivent des catégories d'outils plutôt que des produits spécifiques. Le `.mcp.json` pré-configure certains serveurs, mais n'importe quel serveur MCP de la même catégorie fonctionnera.

### 6.2. État actuel des connecteurs

| Catégorie | Placeholder | Serveurs dans `.mcp.json` | URL configurée ? | Alternatives mentionnées |
|-----------|-------------|--------------------------|:-----------------:|-------------------------|
| Data warehouse | `~~data warehouse` | Snowflake | Non (vide) | Redshift, PostgreSQL |
| Data warehouse | `~~data warehouse` | Databricks | Non (vide) | — |
| Data warehouse | `~~data warehouse` | **BigQuery** | **Oui** | — |
| Email | `~~email` | **Microsoft 365** | **Oui** | — |
| Office suite | `~~office suite` | **Microsoft 365** | **Oui** | — |
| Chat | `~~chat` | **Slack** | **Oui** | Microsoft Teams |
| ERP / Comptabilité | `~~erp` | **Aucun** | — | NetSuite, SAP, QuickBooks, Xero |
| Analytics / BI | `~~analytics` | **Aucun** | — | Tableau, Looker, Power BI |

**Bilan : 3 serveurs fonctionnels** (BigQuery, Slack, MS 365), **2 placeholders** (Snowflake, Databricks), **2 catégories vides** (ERP, Analytics).

### 6.3. Catégories recommandées par le plugin

Le README liste 7 catégories d'intégration recommandées :

| # | Catégorie | Rôle dans le plugin |
|---|-----------|-------------------|
| 1 | `erp-accounting` | GL, subledger, données d'écritures — **le plus critique** |
| 2 | `data-warehouse` | Requêtes financières et données historiques |
| 3 | `spreadsheets` | Génération de workpapers et templates |
| 4 | `analytics-bi` | Dashboards et KPIs |
| 5 | `documents` | Politiques, mémos, documentation support |
| 6 | `email` | Envoi de rapports et demandes d'approbation |
| 7 | `chat` | Communication d'équipe pour le suivi de clôture |

---

## 7. Faire de la finance sans connecteur ERP

### 7.1. Ce que prévoit le plugin

Le plugin est **explicitement conçu pour fonctionner sans ERP**. Chaque commande contient un fallback clair :

> *"Connect ~~erp or ~~data warehouse to pull financial data automatically. You can also paste trial balance data, upload a spreadsheet, or provide income statement data for analysis."*

**4 modes de fonctionnement sans ERP :**

| Mode | Description | Quand l'utiliser |
|------|-------------|-----------------|
| **Copier-coller** | L'utilisateur colle les données directement dans le chat | Petits volumes, analyses ponctuelles |
| **Upload de fichier** | L'utilisateur charge un fichier Excel/CSV | Exports ERP, balances, grands livres |
| **Saisie guidée** | Claude demande les informations nécessaires étape par étape | Première utilisation, données partielles |
| **Data warehouse** | Requêtes SQL via BigQuery/Snowflake | Alternative si données répliquées |

### 7.2. Limites concrètes sans ERP

| Capacité | Avec ERP | Sans ERP |
|----------|----------|----------|
| Pull automatique de la balance | Automatique via MCP | Manuel (copier-coller/upload) |
| Accès au subledger détaillé | Requête directe | L'utilisateur doit exporter et fournir |
| Historique des écritures (N-1) | Requête comparative | L'utilisateur doit fournir les données |
| Posting d'écritures dans le GL | Potentiellement automatisable | Manuel (l'utilisateur saisit dans l'ERP) |
| Rapprochement temps réel | Possible (données live) | Basé sur des exports ponctuels (snapshot) |
| Volume de données | Illimité via API MCP | Limité par la taille du contexte Claude |
| Verrouillage de période | Commande via API | Impossible — action manuelle dans l'ERP |
| Suivi de statut de clôture | Mise à jour automatique | Tracking manuel (checklist) |

### 7.3. Stratégie alternative via Data Warehouse

Si l'entreprise réplique ses données financières dans un data warehouse (BigQuery, Snowflake), le plugin peut fonctionner de manière quasi-automatique **sans connecteur ERP direct** :

```
┌─────────────┐     ┌──────────────┐     ┌───────────────┐     ┌──────────────┐
│ ERP         │ ──→ │ ETL /        │ ──→ │ Data Warehouse│ ──→ │ Plugin       │
│ (NetSuite,  │     │ Réplication  │     │ (BigQuery,    │     │ Finance      │
│  SAP, etc.) │     │              │     │  Snowflake)   │     │ via MCP      │
└─────────────┘     └──────────────┘     └───────────────┘     └──────────────┘
```

C'est une architecture réaliste pour les entreprises qui ont déjà un pipeline de données analytiques. Le data warehouse devient le point d'accès unifié.

**Avantages de cette approche :**
- Pas besoin d'attendre un connecteur MCP ERP (qui n'existe pas encore)
- Données structurées et requêtables en SQL
- Possibilité de joindre données financières et opérationnelles
- BigQuery est déjà configuré avec une URL MCP fonctionnelle

**Limites :**
- Pas de write-back vers l'ERP (lecture seule)
- Latence de réplication (données pas en temps réel)
- Nécessite un pipeline ETL existant

### 7.4. Synthèse : ce qui est faisable vs infaisable

**Faisable sans ERP (le plugin le supporte déjà) :**
- Préparation d'écritures comptables (template + calculs)
- Rapprochement bancaire (avec données fournies manuellement)
- Génération d'états financiers (avec balance comptable fournie)
- Analyse de variance (avec données des 2 périodes fournies)
- Tests SOX (sélection d'échantillons, workpapers)
- Gestion de clôture (checklist, suivi, dépendances)

**Infaisable sans ERP ni data warehouse :**
- Pull automatique de données en temps réel
- Posting automatique d'écritures dans le GL
- Verrouillage de période comptable
- Rapprochement continu / rolling
- Suivi automatique des statuts de clôture dans le système

---

## 8. Comment devra être un connecteur ERP

### 8.1. Exigences fonctionnelles — Outils MCP à exposer

Un connecteur ERP pour ce plugin devrait exposer les **outils MCP** suivants, dérivés de l'analyse des besoins en données de chaque commande :

#### A. Lecture des données (Read Operations) — Essentielles

| Outil MCP | Description | Commandes consommatrices |
|-----------|-------------|--------------------------|
| `get_trial_balance(period, entity?)` | Balance comptable pour une période | `/je`, `/income-statement`, `/flux` |
| `get_subledger_detail(account, period)` | Détail du grand auxiliaire | `/recon`, `/je` |
| `get_journal_entries(period, type?, status?)` | Liste des écritures | `/je`, `/sox` |
| `get_account_balances(accounts[], period)` | Soldes de comptes spécifiques | `/recon`, `/flux` |
| `get_chart_of_accounts()` | Plan comptable avec hiérarchie | Toutes |
| `get_bank_statement(account, period)` | Relevé bancaire intégré | `/recon cash` |
| `get_fixed_asset_register(period?)` | Registre des immobilisations | `/je fixed-assets` |
| `get_depreciation_schedule(period)` | Tableau d'amortissement | `/je fixed-assets` |
| `get_prepaid_schedule(period)` | Tableau des charges constatées d'avance | `/je prepaid` |
| `get_ar_aging(period)` | Balance âgée clients | `/recon ar` |
| `get_ap_aging(period)` | Balance âgée fournisseurs | `/recon ap` |
| `get_intercompany_balances(period)` | Soldes intercompagnies | `/recon ic` |
| `get_budget_data(period, version?)` | Données budgétaires | `/flux`, `/income-statement` |
| `get_forecast_data(period, version?)` | Données prévisionnelles | `/flux` |
| `get_prior_reconciliation(account, period)` | Rapprochement antérieur | `/recon` |

#### B. Écriture des données (Write Operations) — Optionnelles

| Outil MCP | Description | Commande |
|-----------|-------------|----------|
| `post_journal_entry(entry_data)` | Comptabiliser une écriture | `/je` |
| `reverse_journal_entry(entry_id)` | Extourner une écriture | `/je` |
| `lock_period(period)` | Verrouiller une période comptable | `close-management` |
| `unlock_period(period)` | Déverrouiller (avec autorisation) | `close-management` |
| `update_reconciliation_status(account, period, status)` | Mise à jour statut rapprochement | `/recon` |

#### C. Métadonnées et configuration

| Outil MCP | Description |
|-----------|-------------|
| `get_entities()` | Liste des entités juridiques |
| `get_departments()` | Départements / centres de coûts |
| `get_periods(status?)` | Périodes comptables (ouvertes/fermées) |
| `get_currencies()` | Devises et taux de change |
| `get_approval_workflows()` | Workflows d'approbation configurés |

### 8.2. Format de données attendu

Les données retournées devraient être en **JSON structuré** avec des conventions standard :

```json
{
  "trial_balance": {
    "period": "2024-12",
    "entity": "CORP",
    "currency": "USD",
    "accounts": [
      {
        "account_code": "4010",
        "account_name": "Product Revenue",
        "account_type": "revenue",
        "debit_balance": 0,
        "credit_balance": 1250000.00,
        "department": "Sales",
        "prior_period_balance": 1180000.00,
        "budget_balance": 1300000.00
      }
    ]
  }
}
```

**Conventions :**
- Montants en unités monétaires (pas en centimes) avec 2 décimales
- Dates au format ISO 8601 (`2024-12-31`)
- Périodes au format `YYYY-MM` ou `YYYY-QN`
- Codes comptes en string (pour préserver les zéros en tête)
- Pagination pour les résultats volumineux (curseur ou offset/limit)

### 8.3. Exigences non fonctionnelles

| Exigence | Détail | Justification |
|----------|--------|--------------|
| **Authentification** | OAuth 2.0 ou API key avec scopes granulaires | Sécurité des données financières |
| **Autorisations** | Lecture seule par défaut ; écriture en option avec approbation | Principe du moindre privilège |
| **Performance** | Réponse < 5s pour une balance comptable | Expérience utilisateur fluide |
| **Pagination** | Support curseur ou offset/limit | Subledgers volumineux (>10K lignes) |
| **Filtrage** | Par période, entité, département, compte, type | Réduction du volume de données |
| **Audit trail** | Log de tous les accès aux données financières | Conformité SOX / traçabilité |
| **Idempotence** | Les opérations d'écriture doivent être idempotentes | Prévention des doublons |
| **Erreurs** | Messages clairs (période fermée, compte invalide, etc.) | Diagnostic utilisateur |
| **Rate limiting** | Limites documentées avec retry-after | Stabilité du système source |
| **Sandboxing** | Environnement de test disponible | Développement et validation |

### 8.4. Connecteurs ERP potentiels — Évaluation de faisabilité

| ERP | Maturité API | Faisabilité MCP | Notes |
|-----|-------------|:--------------:|-------|
| **NetSuite** | REST API SuiteQL, mature | **Haute** | API RESTful bien documentée, SuiteQL pour requêtes flexibles |
| **QuickBooks Online** | REST API V3 | **Haute** | API simple, bien adaptée PME, OAuth 2.0 natif |
| **Xero** | REST API OAuth 2.0 | **Haute** | API moderne, webhooks, bonne documentation |
| **Sage Intacct** | REST API / SDK | **Moyenne-Haute** | API puissante, multi-entité natif |
| **SAP S/4HANA** | OData / BAPI / RFC | **Moyenne** | Complexité d'intégration SAP, mais APIs OData modernes |
| **Microsoft Dynamics 365** | OData | **Moyenne** | Écosystème Microsoft, APIs OData standardisées |
| **Sage 50 / Ciel** | Limité (SDK local) | **Faible** | Pas d'API cloud native, accès base locale |
| **EBP** | Très limité | **Très faible** | Pas d'API REST, solutions françaises legacy |

---

## 9. Traitement des tâches comptables majeures

### 9.1. Déclaration de TVA

**Couverture actuelle du plugin : FAIBLE**

Le plugin **ne mentionne jamais explicitement la TVA** (VAT/Sales Tax). Les seules références fiscales sont :
- `Income tax expense` dans les états financiers
- `Tax provision entries` dans la clôture (T+4) — mais il s'agit d'impôt sur les bénéfices
- `sales tax, property tax` mentionnés brièvement dans le close-management

**Comment le plugin pourrait traiter une déclaration de TVA avec les outils existants :**

| Étape de la déclaration | Outil plugin disponible | Ce qui manque |
|------------------------|------------------------|---------------|
| 1. Extraire les comptes de TVA collectée (44571x) | `/recon` pourrait rapprocher les comptes TVA | Pas de commande dédiée TVA |
| 2. Extraire les comptes de TVA déductible (44566x) | Data warehouse (si répliqué) | Pas de template de calcul TVA |
| 3. Calculer TVA collectée - TVA déductible = TVA à payer | Claude peut faire le calcul | Pas de gestion des régimes de TVA |
| 4. Rapprocher avec le CA3 précédent | `/recon` avec données manuelles | Pas de format CA3/CA12 |
| 5. Générer le formulaire fiscal | **Absent** | Pas de template cerfa |
| 6. Écriture de liquidation TVA | `/je` pourrait préparer l'OD | Pas de type `vat` prévu |
| 7. Paiement / Crédit de TVA | Hors périmètre | Pas d'intégration paiement |

**Ce qu'il faudrait ajouter pour une couverture TVA complète :**
- Commande `/vat <period>` ou `/tva <period>` avec types : `ca3` (mensuelle), `ca12` (annuelle simplifiée), `mini-réel`
- Skill `tax-compliance` couvrant TVA, IS, CET, C3S
- Templates de formulaires fiscaux par juridiction (France, EU, UK, US)
- Distinction TVA collectée / TVA déductible / TVA intracommunautaire / TVA sur immobilisations
- Gestion du prorata de déduction et des régularisations
- Gestion des TVA à taux réduits (5.5%, 10%, 20% en France)

---

### 9.2. Bilan comptable

**Couverture actuelle du plugin : BONNE (format US GAAP/IFRS)**

Le skill `financial-statements` fournit un **template de bilan classifié complet** :

| Section du bilan (plugin) | Postes détaillés | Norme |
|--------------------------|------------------|-------|
| **Current Assets** | Cash, Short-term investments, AR net, Inventory, Prepaid | ASC 210 |
| **Non-Current Assets** | PP&E net, ROU assets, Goodwill, Intangibles net, LT investments | ASC 350, 842 |
| **Current Liabilities** | AP, Accrued liabilities, Deferred revenue CT, LTD CT, Lease CT | ASC 210 |
| **Non-Current Liabilities** | LT debt, Deferred revenue LT, Lease LT, Other | — |
| **Stockholders' Equity** | Common stock, APIC, Retained earnings, AOCI, Treasury stock | — |

**Normes GAAP effectivement couvertes :**
- ASC 210 / IAS 1 (présentation du bilan)
- ASC 326 (pertes de crédit — créances nettes d'allowance)
- ASC 350 (goodwill — test de dépréciation annuel, pas d'amortissement)
- ASC 842 (leases — actifs et passifs de droit d'utilisation)

**Limites pour un bilan français (PCG) :**

| Aspect | Plugin actuel (US GAAP) | Bilan français (PCG) |
|--------|------------------------|---------------------|
| Format | Classifié courant/non-courant | Actif immobilisé / Actif circulant |
| Immobilisations | PP&E net (une ligne) | Incorporelles / Corporelles / Financières (détaillé) |
| Goodwill | Non amorti (dépréciation) | Amorti sur durée d'utilité (PCG) |
| Leases | ROU assets (ASC 842) | Crédit-bail en hors-bilan (PCG) ou IFRS 16 |
| Provisions | Accrued liabilities | Provisions pour risques et charges (détaillées) |
| Capitaux propres | Stockholders' equity | Capital, Primes, Réserves, Report à nouveau, Résultat |
| Formulaires | Pas de cerfa | Cerfa 2050, 2051, 2052, 2053 |
| Comptes de régularisation | Inclus dans Prepaid/Accrued | Charges et produits constatés d'avance (postes séparés) |

---

### 9.3. Clôture mensuelle

**Couverture actuelle du plugin : EXCELLENTE**

C'est le **point fort majeur** du plugin. Le skill `close-management` + les commandes forment un workflow complet et opérationnel.

#### Workflow de clôture supporté par le plugin

```
PHASE PRE-CLOSE (J-2 à J-3) — 7 tâches
│  Rappels calendrier, confirmation cut-off, vérification sous-systèmes,
│  rapprochement bancaire préliminaire, revue PO ouverts, confirmation paie,
│  collecte info transactions inhabituelles
│
├── JOUR T+1 : Écritures de base — 8 tâches
│   ├── /je ap-accrual 2024-12     → Provisions fournisseurs
│   ├── /je payroll 2024-12        → Provisions paie
│   ├── /je fixed-assets 2024-12   → Amortissements
│   ├── /je prepaid 2024-12        → Étalement charges
│   ├── Confirmation des sous-systèmes
│   ├── Enregistrement encaissements/décaissements
│   ├── Écritures intercompagnies
│   └── Rapprochement bancaire final
│
├── JOUR T+2 : Reconnaissance et rapprochements — 7 tâches
│   ├── /je revenue 2024-12        → Reconnaissance de revenu
│   ├── Autres provisions restantes
│   ├── /recon ar 2024-12          → Rapprochement clients
│   ├── /recon ap 2024-12          → Rapprochement fournisseurs
│   ├── Ajustements de stocks
│   ├── Réévaluation devises (FX)
│   └── Début rapprochements bilan
│
├── JOUR T+3 : Rapprochements bilan — 6 tâches
│   ├── /recon cash 2024-12        → Rapprochement bancaire complet
│   ├── /recon intercompany 2024-12 → Rapprochement interco
│   ├── Écritures d'ajustement issues des rapprochements
│   ├── Éliminations intercompagnies
│   ├── Balance provisoire
│   └── /flux (analyse préliminaire)
│
├── JOUR T+4 : Provisions fiscales et états financiers — 6 tâches
│   ├── Provision fiscale (IS, taxes)
│   ├── Roll-forward capitaux propres (SBC, actions propres)
│   ├── Soft close — finalisation des écritures
│   ├── /income-statement monthly 2024-12  → P&L draft
│   ├── /flux revenue 2024-12 vs 2024-11   → Analyse de variance détaillée
│   └── Revue direction
│
└── JOUR T+5 : Finalisation — 6 tâches
    ├── Ajustements finaux (suite revue direction)
    ├── Hard close — verrouillage de la période
    ├── Distribution du reporting package
    ├── Mise à jour des forecasts
    └── Rétrospective de clôture
```

**Total : 40 tâches réparties sur 6 phases avec 5 niveaux de dépendances.**

#### Métriques de clôture suivies

| Métrique | Définition | Cible |
|----------|-----------|-------|
| Durée de clôture | Jours ouvrés de la fin de période au hard close | Réduction progressive |
| Écritures post-soft close | Écritures postées après la revue direction | Minimiser |
| Tâches en retard | Tâches dépassant leur deadline | Zéro |
| Exceptions de rapprochement | Éléments nécessitant investigation | Réduction progressive |
| Retraitements | Erreurs découvertes après clôture | Zéro |

#### Option clôture accélérée (3 jours)

Le plugin détaille aussi un modèle de **clôture en 3 jours** avec des prérequis stricts :
- Écritures récurrentes automatisées (amortissements, provisions standard)
- Rapprochement continu pendant le mois (pas tout en fin de mois)
- Éliminations intercompagnies automatisées
- Activités pre-close terminées avant la fin du mois
- Équipe autonome avec ownership clair et minimum de handoffs
- Intégration temps réel ou quasi-temps réel des sous-systèmes

---

### 9.4. Tableau de synthèse des tâches comptables

| Tâche comptable | Couverture | Comment c'est traité | Ce qui manque |
|----------------|:----------:|---------------------|---------------|
| **Clôture mensuelle** | Excellente | Workflow complet T+1 à T+5, 40 tâches, dépendances, métriques | — |
| **Rapprochement bancaire** | Excellente | `/recon cash` avec workpaper, catégorisation, aging | — |
| **Compte de résultat** | Excellente | `/income-statement` multi-colonnes avec variance analysis | Format PCG français |
| **Audit SOX** | Excellente | `/sox` + `audit-support` (2 312 mots) | — |
| **Analyse de variance** | Excellente | `/flux` avec 5 techniques de décomposition, waterfall, narratifs | — |
| **Écritures de paie** | Bonne | `/je payroll` (4 composantes) | Cotisations sociales françaises |
| **Amortissements** | Bonne | `/je fixed-assets` (3 méthodes) | Modes fiscaux dérogatoires |
| **Revenue Recognition** | Bonne | `/je revenue` + ASC 606 | IFRS 15 (couvert via IAS réf.) |
| **Bilan** | Bonne | Template US GAAP/IFRS complet | Format PCG, cerfa |
| **Tableau flux trésorerie** | Bonne | Template méthode indirecte | — |
| **Consolidation** | Partielle | Éliminations interco mentionnées | Pas de commande `/consolidation` |
| **Budget** | Partielle | Comparaison actual vs budget | Pas de préparation budgétaire |
| **Déclaration IS** | Faible | Provision fiscale en T+4 | Pas de commande/template IS |
| **Déclaration de TVA** | Faible | Aucune commande dédiée | Tout à créer |
| **Liasse fiscale** | Absente | Non mentionnée | Tout à créer |
| **Déclaration CET/C3S** | Absente | Non mentionnée | Tout à créer |
| **Bilan social** | Absent | Non mentionné | Tout à créer |

---

## 10. Forces et limites du plugin

### 10.1. Forces

| Force | Détail |
|-------|--------|
| **Workflow de clôture le plus complet** | 40 tâches sur 6 phases avec dépendances à 5 niveaux, chemin critique, clôture accélérée |
| **Architecture agnostique** | Fonctionne avec ou sans ERP grâce au système `~~category` — dégradation gracieuse |
| **Profondeur méthodologique** | 9 038 mots de connaissances dans les skills — un vrai manuel de référence comptable |
| **Templates professionnels** | Formats de sortie standardisés (workpapers, JE, rapprochements) prêts pour l'audit |
| **Framework SOX exhaustif** | 10 domaines de contrôle, guide d'échantillonnage, 20 templates de contrôle pré-construits |
| **Analyse de variance avancée** | 5 techniques de décomposition avec formules, waterfall, narratifs, anti-patterns |
| **Ratio skill/commande optimal** | Chaque commande a un skill complémentaire (ratio moyen 1.77x en mots) |
| **Scalabilité** | De la PME (copier-coller) à l'entreprise (data warehouse + ERP) |
| **Disclaimers systématiques** | Chaque fichier rappelle que les résultats doivent être revus par des professionnels |
| **Références normatives** | ASC 606, 210, 220, 230, 326, 350, 842 / IAS 1, 7 explicitement cités |

### 10.2. Limites

| Limite | Impact | Criticité |
|--------|--------|:---------:|
| **Pas de connecteur ERP** | Le connecteur le plus critique est marqué "Aucun serveur supporté" | Haute |
| **Orientation US GAAP** | Normes françaises (PCG), fiscalité locale (TVA, IS, CET) non couvertes | Haute (marché FR) |
| **Pas de commande fiscale** | Aucune commande dédiée pour les déclarations fiscales | Haute |
| **Pas de consolidation** | Pas de `/consolidation` malgré les mentions d'éliminations interco | Moyenne |
| **Pas de budget** | Pas de commande de préparation budgétaire | Moyenne |
| **Format de sortie texte** | Templates en markdown/ASCII — pas d'export Excel/PDF natif | Moyenne |
| **Pas de liasse fiscale** | Aucun format cerfa ou équivalent fiscal | Haute (marché FR) |
| **Anglais uniquement** | Toutes les commandes, skills et templates sont en anglais | Moyenne (marché FR) |
| **Pas de multi-devises avancé** | Réévaluation FX mentionnée mais pas de gestion IFRS 21/IAS 21 détaillée | Faible |
| **Pas de gestion des immobilisations** | Pas de skill dédié au cycle de vie des actifs (acquisition → cession) | Faible |

---

## 11. Synthèse comparative Commandes vs Skills

### 11.1. Matrice de correspondance

| Domaine | Commande | Mots cmd | Skill | Mots skill | Ratio Skill/Cmd |
|---------|----------|--------:|-------|----------:|----------------:|
| Journal Entries | `/je` | 731 | `journal-entry-prep` | 1 103 | 1.51x |
| Reconciliation | `/recon` | 701 | `reconciliation` | 1 148 | 1.64x |
| Variance Analysis | `/flux` | 885 | `variance-analysis` | 1 628 | 1.84x |
| SOX Testing | `/sox` | 1 204 | `audit-support` | 2 312 | 1.92x |
| Income Statement | `/income-statement` | 700 | `financial-statements` | 1 341 | 1.92x |
| Close Management | *aucune* | — | `close-management` | 1 506 | ∞ |
| **Total** | **5 commandes** | **4 221** | **6 skills** | **9 038** | **2.14x** |

**Observation :** Les skills sont en moyenne **1.77x plus détaillées** que les commandes correspondantes (hors `close-management`). Le ratio total skills/commandes est de **2.14x**, confirmant que la majeure partie de l'intelligence du plugin réside dans les bases de connaissances.

### 11.2. Différence de rôle

| Aspect | Commandes (5) | Skills (6) |
|--------|---------------|------------|
| **Invocation** | Explicite par l'utilisateur (`/je`, `/recon`, etc.) | Implicite — Claude les consulte en contexte |
| **Structure** | Workflow séquentiel (étapes 1→N) | Base de connaissances thématique |
| **Arguments** | Oui (type, période, compte) | Non |
| **Templates de sortie** | Oui (formats pré-définis avec placeholders) | Oui (formats de référence) |
| **Conditionnalité** | Oui (`if ~~erp connected` / `if no data source`) | Non |
| **Disclaimer** | Oui (dans chaque fichier) | Oui (dans chaque fichier) |
| **Référence CONNECTORS.md** | Oui (lien en haut de chaque commande) | Non |
| **Total mots** | 4 221 (26.7% du total) | 9 038 (57.2% du total) |

### 11.3. Architecture d'ensemble

```
                          ┌──────────────────────────────┐
                          │     UTILISATEUR               │
                          │  (paste data / upload / chat) │
                          └──────────────┬───────────────┘
                                         │
                          ┌──────────────▼───────────────┐
                          │     5 COMMANDES SLASH         │
                          │  /je  /recon  /income-stmt    │
                          │  /flux  /sox                   │
                          │     4 221 mots (27%)          │
                          └──────────────┬───────────────┘
                                         │ consulte
                          ┌──────────────▼───────────────┐
                          │     6 SKILLS (Knowledge)      │
                          │  journal-entry-prep            │
                          │  reconciliation                │
                          │  financial-statements          │
                          │  variance-analysis             │
                          │  close-management              │
                          │  audit-support                 │
                          │     9 038 mots (57%)          │
                          └──────────────┬───────────────┘
                                         │ accède via
                          ┌──────────────▼───────────────┐
                          │     CONNECTEURS MCP           │
                          │  ~~erp (aucun)                │
                          │  ~~data warehouse (BigQuery)  │
                          │  ~~chat (Slack)               │
                          │  ~~email/office (MS 365)      │
                          │     2 532 mots (16%)          │
                          └──────────────────────────────┘
```

---

> **Conclusion :** Le plugin Finance est un outil de productivité comptable mature pour le contexte US GAAP/IFRS, avec une excellente couverture des workflows de clôture (40 tâches séquencées), rapprochement (7 types de comptes) et audit SOX (10 domaines de contrôle). Ses **15 791 mots** répartis sur 16 fichiers constituent un véritable manuel de référence comptable intégré. Son architecture agnostique (fonctionnement avec ou sans ERP via le système `~~category`) le rend utilisable immédiatement, mais son potentiel maximum nécessite un connecteur ERP MCP qui n'existe pas encore. Pour une utilisation en contexte français (PCG, TVA, IS, liasse fiscale, cotisations sociales), des extensions significatives seraient nécessaires, tant au niveau des commandes que des skills.
