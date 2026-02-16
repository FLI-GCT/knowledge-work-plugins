# Analyse Complète du Plugin Comptabilité France

> **Date d'analyse :** 15 février 2026
> **Version du plugin :** 1.0.0
> **Auteur du plugin :** Anthropic
> **Licence :** Apache 2.0
> **Plugin de référence :** Finance (US GAAP/IFRS)

---

## Table des Matières

1. [Vue d'ensemble](#1-vue-densemble)
2. [Architecture et structure des fichiers](#2-architecture-et-structure-des-fichiers)
3. [Métriques détaillées (mots, lignes, couverture)](#3-métriques-détaillées)
4. [Analyse des Commandes (6)](#4-analyse-des-commandes)
5. [Analyse des Skills (7)](#5-analyse-des-skills)
6. [Connecteurs et intégrations MCP](#6-connecteurs-et-intégrations-mcp)
7. [Faire de la comptabilité française sans connecteur ERP](#7-faire-de-la-comptabilité-française-sans-connecteur-erp)
8. [Comment devra être un connecteur ERP français](#8-comment-devra-être-un-connecteur-erp-français)
9. [Traitement des tâches comptables majeures](#9-traitement-des-tâches-comptables-majeures)
10. [Forces et limites du plugin](#10-forces-et-limites-du-plugin)
11. [Synthèse comparative Commandes vs Skills](#11-synthèse-comparative-commandes-vs-skills)
12. [Comparaison avec le plugin Finance (US)](#12-comparaison-avec-le-plugin-finance-us)

---

## 1. Vue d'ensemble

Le plugin Comptabilité France est conçu pour **Cowork** (l'application desktop agentique d'Anthropic) et **Claude Code**. Il couvre les workflows de comptabilité d'entreprise française, entièrement adapté au **Plan Comptable Général (PCG)** et à la réglementation fiscale française :

- Écritures comptables PCG (classes 1-7) avec traitement TVA
- Rapprochement bancaire et lettrage des comptes tiers
- Déclaration de TVA (CA3 mensuelle / CA12 annuelle)
- États financiers format PCG (bilan, compte de résultat par nature, SIG)
- Analyse des écarts et variance analysis
- Liasse fiscale (cerfa 2050-2059) et passage comptable → fiscal
- Clôture mensuelle et annuelle avec calendrier fiscal

**Avertissement intégré :** Chaque fichier du plugin contient un disclaimer en français indiquant qu'il ne fournit pas de conseil comptable, fiscal ou juridique. Tous les résultats doivent être revus par un expert-comptable ou un professionnel qualifié.

### Architecture 100% déclarative

Comme le plugin Finance dont il est dérivé, ce plugin ne contient **aucun code exécutable**. Il est composé exclusivement de fichiers Markdown et JSON servant de prompts structurés pour Claude. Les commandes définissent des workflows étape par étape, et les skills fournissent des bases de connaissances méthodologiques adaptées au référentiel comptable français.

### Genèse et filiation

Ce plugin a été créé comme adaptation française du plugin Finance (US GAAP/IFRS). La structure est volontairement identique (conventions de nommage, patterns de fichiers, architecture tool-agnostic), mais le contenu est intégralement adapté au contexte français :
- Plan Comptable Général au lieu du US Chart of Accounts
- TVA française au lieu de la Sales Tax US
- Liasse fiscale cerfa au lieu de SOX compliance
- SIG (Soldes Intermédiaires de Gestion) au lieu du P&L par fonction
- Charges sociales françaises au lieu du payroll US (FICA/FUTA)

---

## 2. Architecture et structure des fichiers

```
comptabilite-fr/
├── .claude-plugin/
│   └── plugin.json              # Métadonnées du plugin (nom, version, description FR)
├── .mcp.json                    # Configuration des serveurs MCP
├── README.md                    # Documentation principale (en français)
├── CONNECTORS.md                # Référence des connecteurs
├── LICENSE                      # Licence Apache 2.0
│
├── commands/                    # 6 commandes slash invocables par l'utilisateur
│   ├── ecriture-comptable.md    # /ecriture — Écritures PCG avec TVA
│   ├── rapprochement.md         # /rapprochement — Rapprochement bancaire et lettrage
│   ├── declaration-tva.md       # /declaration-tva — CA3/CA12 (NOUVEAU)
│   ├── etats-financiers.md      # /etats-financiers — Bilan + CR + SIG format PCG
│   ├── analyse-ecarts.md        # /analyse-ecarts — Analyse de variance
│   └── liasse-fiscale.md        # /liasse-fiscale — Cerfa 2050-2059
│
└── skills/                      # 7 skills (bases de connaissances méthodologiques)
    ├── ecritures-pcg/
    │   └── SKILL.md             # Plan comptable, schémas d'écritures, FEC
    ├── rapprochement-bancaire/
    │   └── SKILL.md             # Méthodologie rapprochement, lettrage, CFONB/SEPA
    ├── etats-financiers-pcg/
    │   └── SKILL.md             # Bilan, CR par nature, SIG, annexe légale
    ├── analyse-ecarts/
    │   └── SKILL.md             # Techniques décomposition, waterfall, narratifs
    ├── cloture-comptable/
    │   └── SKILL.md             # Checklist clôture, dépendances, calendrier fiscal
    ├── fiscalite-francaise/
    │   └── SKILL.md             # TVA, IS, CET, C3S, liasse fiscale
    └── charges-sociales/
        └── SKILL.md             # Bulletin de paie, cotisations, DSN, provisions
```

**Total : 18 fichiers** (2 config JSON + 3 fichiers racine MD + 1 LICENSE + 6 commandes + 7 skills)

**Comparaison avec le plugin Finance :** +2 fichiers (16 → 18), +1 commande (5 → 6), +1 skill (6 → 7).

---

## 3. Métriques détaillées

### 3.1. Nombre de mots par fichier

| Fichier | Catégorie | Mots | Lignes | Ratio mots/ligne |
|---------|-----------|-----:|-------:|-----------------:|
| `plugin.json` | Config | 44 | 8 | 5,5 |
| `.mcp.json` | Config | 33 | 20 | 1,7 |
| `README.md` | Doc | 805 | 95 | 8,5 |
| `CONNECTORS.md` | Doc | 227 | 22 | 10,3 |
| `LICENSE` | Légal | 1 581 | 202 | 7,8 |
| **Sous-total Config/Doc** | | **2 690** | | |
| | | | | |
| `commands/etats-financiers.md` | Commande | 1 735 | 285 | 6,1 |
| `commands/liasse-fiscale.md` | Commande | 1 410 | 177 | 8,0 |
| `commands/declaration-tva.md` | Commande | 1 161 | 180 | 6,5 |
| `commands/ecriture-comptable.md` | Commande | 1 069 | 146 | 7,3 |
| `commands/rapprochement.md` | Commande | 937 | 156 | 6,0 |
| `commands/analyse-ecarts.md` | Commande | 911 | 152 | 6,0 |
| **Sous-total Commandes** | | **7 223** | **1 096** | **6,6 moy.** |
| | | | | |
| `skills/fiscalite-francaise/SKILL.md` | Skill | 1 758 | 216 | 8,1 |
| `skills/cloture-comptable/SKILL.md` | Skill | 1 657 | 233 | 7,1 |
| `skills/ecritures-pcg/SKILL.md` | Skill | 1 648 | 282 | 5,8 |
| `skills/charges-sociales/SKILL.md` | Skill | 1 371 | 229 | 6,0 |
| `skills/etats-financiers-pcg/SKILL.md` | Skill | 1 352 | 152 | 8,9 |
| `skills/analyse-ecarts/SKILL.md` | Skill | 1 251 | 180 | 7,0 |
| `skills/rapprochement-bancaire/SKILL.md` | Skill | 1 092 | 156 | 7,0 |
| **Sous-total Skills** | | **10 129** | **1 448** | **7,0 moy.** |
| | | | | |
| **GRAND TOTAL** | | **20 042** | **~2 544+** | |

### 3.2. Répartition proportionnelle

```
Skills        ██████████████████████████████████████░░░░░░  50,5%  (10 129 mots)
Commandes     ██████████████████████████░░░░░░░░░░░░░░░░░░  36,0%  (7 223 mots)
Config/Doc    █████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  13,4%  (2 690 mots)
```

**Comparaison avec le plugin Finance :**

| Indicateur | Finance (US) | Comptabilité FR | Évolution |
|------------|-------------:|----------------:|----------:|
| Grand total mots | 15 791 | 20 042 | **+27%** |
| Commandes (mots) | 4 221 | 7 223 | **+71%** |
| Skills (mots) | 9 038 | 10 129 | **+12%** |
| Config/Doc | 2 532 | 2 690 | +6% |
| Nb commandes | 5 | 6 | +1 |
| Nb skills | 6 | 7 | +1 |
| Nb fichiers | 16 | 18 | +2 |

### 3.3. Classement par taille (mots) — Commandes et Skills uniquement

| Rang | Fichier | Type | Mots |
|------|---------|------|-----:|
| 1 | fiscalite-francaise/SKILL.md | Skill | 1 758 |
| 2 | etats-financiers.md | Commande | 1 735 |
| 3 | cloture-comptable/SKILL.md | Skill | 1 657 |
| 4 | ecritures-pcg/SKILL.md | Skill | 1 648 |
| 5 | liasse-fiscale.md | Commande | 1 410 |
| 6 | charges-sociales/SKILL.md | Skill | 1 371 |
| 7 | etats-financiers-pcg/SKILL.md | Skill | 1 352 |
| 8 | analyse-ecarts/SKILL.md | Skill | 1 251 |
| 9 | declaration-tva.md | Commande | 1 161 |
| 10 | rapprochement-bancaire/SKILL.md | Skill | 1 092 |
| 11 | ecriture-comptable.md | Commande | 1 069 |
| 12 | rapprochement.md | Commande | 937 |
| 13 | analyse-ecarts.md | Commande | 911 |

**Observation :** Contrairement au plugin Finance où le fichier le plus volumineux est un skill (audit-support, 2 312 mots), ici les fichiers sont plus équilibrés : le plus gros est un skill (fiscalite-francaise, 1 758 mots) mais la commande `/etats-financiers` (1 735 mots) est quasi-égale. Aucun fichier ne dépasse 1 800 mots, indiquant une distribution plus homogène.

### 3.4. Densité sémantique par commande

| Commande | Mots | Nb étapes workflow | Nb types/arguments | Templates de sortie | Points checklist |
|----------|-----:|------------------:|------------------:|-------------------:|-----------------:|
| `/etats-financiers` | 1 735 | 6 | 4 types × 4 périodes | 3 (bilan, CR, SIG) | Seuils configurables |
| `/liasse-fiscale` | 1 410 | 6 | 10+ feuillets | 3 (2058-A, 2050, contrôles) | 11 points |
| `/declaration-tva` | 1 161 | 6 | 3 types | 3 (CA3, rapproch. CA, liquidation) | 9 points |
| `/ecriture` | 1 069 | 5 | 6 types | 1 (JE formatée PCG) | 12 points |
| `/rapprochement` | 937 | 8 | 6+ types comptes | 2 (état rappr., lettrage) | 5 critères escalade |
| `/analyse-ecarts` | 911 | 7 | 6 domaines × 6 comparaisons | 2 (waterfall, narratif) | — |

---

## 4. Analyse des Commandes

Les commandes sont des **prompts slash** que l'utilisateur invoque directement. Elles définissent un workflow étape par étape avec des arguments, des templates de sortie et des instructions pour Claude. Toutes les commandes sont en français et utilisent les comptes PCG.

### 4.1. `/ecriture` — 1 069 mots, 146 lignes

**Fonction :** Préparer des écritures comptables conformes au PCG avec comptes, TVA, débits/crédits et pièces justificatives.

| Attribut | Détail |
|----------|--------|
| **Syntaxe** | `/ecriture <type> <période>` |
| **Types d'écriture** | `achat`, `vente`, `salaire`, `immobilisation`, `od`, `tva` |
| **Périodes** | `2025-01`, `2025-Q1`, `2024` |
| **Étapes du workflow** | 5 (collecte données → calcul → génération JE → checklist → sortie) |
| **Template de sortie** | Tableau formaté (ligne, compte PCG, libellé, débit, crédit) |
| **Checklist de revue** | 12 points de vérification |

**Détails par type d'écriture :**

| Type | Débit | Crédit | Spécificités françaises |
|------|-------|--------|-------------------------|
| **Achat** | 6xx (charge par nature) + 44566 (TVA déductible) | 401x (fournisseur TTC) | 4 taux TVA (20%, 10%, 5,5%, 2,1%), distinction 44566/44562 |
| **Vente** | 411x (client TTC) | 7xx (produit) + 44571 (TVA collectée) | Comptes 701/706/707 selon nature |
| **Salaire** | 641 (brut) + 645 (patronales) | 421 (net), 431 (SS), 437 (autres org.), 4421 (PAS) | Prélèvement à la source, cotisations françaises |
| **Immobilisation** | 6811 (dotation) | 28x (amortissements cumulés) | Linéaire et dégressif fiscal (coefficients 1,25/1,75/2,25) |
| **OD** | Variable | Variable | CCA (486), PCA (487), FNP (4081), provisions (15x) |
| **TVA** | 44571 (collectée) | 44551 (TVA à décaisser) ou 44567 (crédit TVA) | Liquidation mensuelle, centralisation des comptes 445xx |

**Checklist de revue complète (12 points) :**
1. Débits = Crédits (écriture équilibrée)
2. Période comptable correcte (pas de période clôturée)
3. Comptes PCG valides et adaptés à la nature
4. Taux de TVA correct (20%, 10%, 5,5%, 2,1% ou exonéré)
5. Montants correctement calculés avec pièce justificative
6. Libellé clair et suffisant pour le contrôle fiscal (FEC)
7. Journal comptable correct (ACH, VTE, BQ, OD, SAL, AN)
8. Numéro de pièce renseigné et traçable
9. Cohérence avec le traitement de la période précédente
10. Extourne programmée si nécessaire (provisions, CCA, PCA)
11. Pas de doublon avec une écriture existante
12. Conforme aux exigences du FEC

**Comparaison avec `/je` (Finance US) :** +338 mots (+46%), +1 type d'écriture (`tva`), +1 point de checklist. Intègre la gestion multi-taux TVA et la conformité FEC, absentes du plugin US.

---

### 4.2. `/rapprochement` — 937 mots, 156 lignes

**Fonction :** Rapprocher les soldes bancaires, lettrer les comptes tiers et identifier les écarts.

| Attribut | Détail |
|----------|--------|
| **Syntaxe** | `/rapprochement <compte> <période>` |
| **Types de comptes** | `banque`/`512`, `client`/`411`, `fournisseur`/`401`, `intercompagnie`/`ic`, `tva`, `social`/`43`, ou code GL |
| **Étapes du workflow** | 8 (collecte → comparaison → identification → état rappr. → détail → lettrage → revue → sortie) |
| **Templates de sortie** | 2 (état de rapprochement + tableau de lettrage) |

**Types de rapprochement spécifiquement français :**

| Type | Spécificité française |
|------|----------------------|
| `banque` / `512` | Rapprochement classique GL vs relevé, format CFONB/camt.053 |
| `client` / `411` | **Lettrage** avec attribution de lettres (AA, AB, etc.) — technique française |
| `fournisseur` / `401` | Lettrage inversé (débit = règlements, crédit = factures) |
| `tva` | **Nouveau** — Rapprochement comptes 445xx vs déclaration CA3 |
| `social` / `43` | **Nouveau** — Rapprochement URSSAF, caisses de retraite vs comptes 431/437 |
| `intercompagnie` / `ic` | Rapprochement des comptes 451 entre entités du groupe |

**Template de lettrage (spécifique français) :**
```
| Lettre | Date       | Pièce   | Libellé            | Débit     | Crédit    | Solde     |
|--------|------------|---------|--------------------|-----------|-----------|-----------|
| AA     | 05/01/2025 | FA-001  | Facture client X   | 1 200,00  |           |           |
| AA     | 20/01/2025 | RE-015  | Règlement client X |           | 1 200,00  | 0,00      |
| AB     | 15/01/2025 | FA-012  | Facture client X   | 3 600,00  |           |           |
|        |            |         | **Non lettré**     |           |           | 3 600,00  |
```

**Seuils d'escalade :**
- Éléments anciens > 30/60/90 jours
- Montants individuels > seuil de matérialité
- Soldes croissants d'une période à l'autre
- Éléments non résolus depuis plusieurs périodes
- Écarts inexpliqués

**Comparaison avec `/recon` (Finance US) :** +236 mots (+34%), +2 types de comptes (`tva`, `social`). Intègre le lettrage français (absent du plugin US) et le rapprochement des organismes sociaux.

---

### 4.3. `/declaration-tva` — 1 161 mots, 180 lignes — COMMANDE SANS ÉQUIVALENT DANS LE PLUGIN FINANCE

**Fonction :** Préparer la déclaration de TVA (CA3 mensuelle ou CA12 annuelle), rapprocher le CA déclaré avec la comptabilité et générer l'écriture de liquidation.

| Attribut | Détail |
|----------|--------|
| **Syntaxe** | `/declaration-tva <type> <période>` |
| **Types** | `ca3` (mensuelle), `ca12` (annuelle simplifiée), `mini-reel` |
| **Étapes du workflow** | 6 (collecte TVA → préparation CA3 → rapprochement CA → écriture liquidation → contrôles → sortie) |
| **Templates de sortie** | 3 (CA3 complet, rapprochement CA, écriture de liquidation) |
| **Checklist** | 9 points de vérification |

**Template du CA3 — Structure complète :**

| Section | Lignes | Contenu |
|---------|--------|---------|
| **A — Opérations réalisées** | L01-L06 | CA imposable, exportations, livraisons/acquisitions intracommunautaires |
| **B — TVA brute** | L08-L16 | TVA collectée par taux (20%, 5,5%, 10%, 2,1%), acquisitions intra, régularisations |
| **B — TVA déductible** | L19-L23 | TVA sur biens/services (44566), immobilisations (44562), report crédit |
| **C — Résultat** | L25-L32 | Crédit de TVA ou TVA nette due, taxes assimilées, total à payer |

**Rapprochement CA déclaré / CA comptable :**

Le plugin inclut un template de rapprochement spécifique :
```
CA comptable (comptes 70x)
+ Produits imposables hors classe 7
- Produits exonérés / hors champ inclus en classe 7
+ Acquisitions intracommunautaires
+/- Régularisations
= CA déclaré reconstitué    →    Doit correspondre au CA total du CA3
```

**Écriture de liquidation (2 variantes) :**
- TVA à payer : Débit 44571 / Crédit 44551
- Crédit de TVA : Débit 44567 / Crédit 44566

**Checklist (9 points) :**
1. CA déclaré rapproché du CA comptable (écart nul ou expliqué)
2. Taux de TVA corrects pour chaque catégorie d'opération
3. TVA intracommunautaire correctement autoliquidée
4. Crédit de TVA antérieur correctement reporté
5. Exportations et livraisons intra-UE en opérations non imposables
6. Règles de déductibilité respectées (prorata si applicable)
7. Régularisations identifiées et documentées
8. Date limite de dépôt respectée (15 au 24 du mois suivant)
9. Écriture de liquidation équilibrée et soldant les comptes TVA

**Importance :** Cette commande comble la lacune la plus critique identifiée dans l'analyse du plugin Finance US, qui ne mentionne jamais explicitement la TVA.

---

### 4.4. `/etats-financiers` — 1 735 mots, 285 lignes (la commande la plus volumineuse)

**Fonction :** Générer le bilan, le compte de résultat par nature et les SIG au format PCG avec comparatif N / N-1.

| Attribut | Détail |
|----------|--------|
| **Syntaxe** | `/etats-financiers <type> <période>` |
| **Types** | `bilan`, `compte-resultat`, `sig`, `complet` |
| **Étapes du workflow** | 6 (collecte → bilan → CR → SIG → analyse variations → sortie) |
| **Templates de sortie** | 3 (bilan PCG, CR par nature, 9 SIG) |
| **Colonnes** | N (Brut, Amort/Dépréc, Net), N-1, Variation (€ et %) |

**Structure du bilan PCG (vs US GAAP) :**

| Section PCG | Postes détaillés | Différence vs Finance US |
|-------------|------------------|--------------------------|
| Actif immobilisé | Incorporelles (20x), Corporelles (21x), Financières (26-27x) | Détail par nature vs "PP&E net" unique |
| Actif circulant | Stocks (3x), Créances (41x, 44x), VMP (50x), Disponibilités (51-53x) | Ordre de liquidité croissante |
| CCA | Charges constatées d'avance (486) | Poste séparé (vs inclus dans Prepaid) |
| Capitaux propres | Capital, Primes, Réserves (légale, statutaires, autres), Report à nouveau, Résultat | Détail réglementaire français |
| Provisions | Provisions pour risques (151), Provisions pour charges (15x) | Poste intermédiaire distinct |
| Dettes | Emprunts (16x), Fournisseurs (401), Fiscales/sociales (42-44x) | Classement par exigibilité |

**Les 9 Soldes Intermédiaires de Gestion (SIG) — Spécificité française majeure :**

| # | Solde | Formule | Ratio clé |
|---|-------|---------|-----------|
| 1 | **Marge commerciale** | Ventes marchandises - Coût d'achat | Taux de marge |
| 2 | **Production de l'exercice** | Produite vendue + stockée + immobilisée | — |
| 3 | **Valeur ajoutée** | Marge + Production - Consommations de tiers | VA / CA |
| 4 | **EBE** | VA + Subventions - Impôts/taxes - Personnel | EBE / CA |
| 5 | **Résultat d'exploitation** | EBE + Reprises + Autres - Dotations - Autres charges | — |
| 6 | **RCAI** | Résultat exploitation + Résultat financier | — |
| 7 | **Résultat exceptionnel** | Produits exceptionnels - Charges exceptionnelles | — |
| 8 | **Résultat net** | RCAI + Résultat exceptionnel - Participation - IS | RN / CA |
| 9 | **CAF** | RN + Dotations - Reprises + VNC cédés - Produits cessions | CAF / CA |

**Seuils de signalement des variations (configurables) :**
- Variation en euros > seuil défini (ex. 10K€, 50K€)
- Variation en % > 10% (ou seuil personnalisé)

**Comparaison avec `/income-statement` (Finance US) :** +1 035 mots (+148%). Le plugin FR couvre bilan + CR + SIG dans une seule commande, contre seulement le P&L dans le plugin US. Le format PCG par nature est fondamentalement différent du format US par fonction.

---

### 4.5. `/analyse-ecarts` — 911 mots, 152 lignes

**Fonction :** Décomposer les écarts entre deux périodes ou par rapport au budget, fournir des narratifs explicatifs et générer une analyse en cascade.

| Attribut | Détail |
|----------|--------|
| **Syntaxe** | `/analyse-ecarts <domaine> <comparaison>` |
| **Domaines** | `ca`, `charges`, `masse-salariale`, `marge`, `ebe`, `resultat`, ou compte/groupe PCG |
| **Comparaisons** | MoM, YoY, QoQ, réel vs budget, réel vs forecast, three-way |
| **Étapes du workflow** | 7 (collecte → écart global → décomposition → waterfall → narratifs → inexpliqués → sortie) |

**Décomposition par domaine (adapté au vocabulaire PCG) :**

| Domaine | Facteurs de décomposition |
|---------|--------------------------|
| **Chiffre d'affaires** | Volume, Prix, Mix, Nouveau vs existant, Effet devise |
| **Charges d'exploitation** | Volume, Prix unitaires, Charges fixes, Non récurrents, Timing |
| **Masse salariale** | Effectif (ETP), Coût moyen, Structure, Heures sup/intérim, Charges sociales |
| **Marge** | Mix produit, Taux de marge, Volume |
| **EBE** | Combinaison marge + charges de personnel + impôts/taxes |

**Template waterfall (texte) :**
```
[Base Période 2]                              X XXX €
  |--[+] Facteur 1 description              +X XXX €
  |--[-] Facteur 2 description              -X XXX €
  |
[Réel Période 1]                              X XXX €

Réconciliation : Facteur 1 (XX%) + Facteur 2 (XX%) + Inexpliqué (XX%) = 100%
```

**Structure du narratif :**
- Nom du facteur + Favorable/Défavorable + montant + %
- 2-3 phrases d'explication causale
- Perspective : ponctuel, récurrent, tendanciel

**Comparaison avec `/flux` (Finance US) :** +26 mots (+3%). Structure quasi-identique, adapté au vocabulaire PCG (EBE, masse salariale, charges sociales au lieu de OpEx, headcount, COGS).

---

### 4.6. `/liasse-fiscale` — 1 410 mots, 177 lignes — REMPLACE `/sox-testing`

**Fonction :** Préparer les feuillets de la liasse fiscale (cerfa 2050-2059) avec passage du résultat comptable au résultat fiscal.

| Attribut | Détail |
|----------|--------|
| **Syntaxe** | `/liasse-fiscale <feuillets> <exercice>` |
| **Feuillets** | `2050` à `2059-A`, `2050-2059` (complet), `bilan-complet`, `resultat-fiscal` |
| **Étapes du workflow** | 6 (collecte → 2058-A → feuillets bilan → contrôles → checklist → sortie) |
| **Templates de sortie** | 3 (2058-A détaillé, 2050 bilan actif, contrôles inter-feuillets) |
| **Checklist** | 11 points de vérification |

**Template du feuillet 2058-A — Détermination du résultat fiscal :**

C'est le feuillet le plus critique : passage du résultat comptable au résultat fiscal.

```
RÉSULTAT COMPTABLE
  Bénéfice comptable (ligne WA)           ou Déficit comptable (ligne WB)

RÉINTÉGRATIONS (charges non déductibles)
  IS (WC), Amendes (WD), TVS (WF), Amortissements excédentaires (WG),
  Provisions non déductibles (WI), Charges somptuaires (WJ),
  QPFC sur dividendes, Autres (WQ)
  → TOTAL RÉINTÉGRATIONS (WR)

DÉDUCTIONS
  Produits non imposables, Dividendes mère-fille, PVLT à taux réduit,
  CIR, Provisions devenues déductibles, Autres (XI)
  → TOTAL DÉDUCTIONS (XJ)

RÉSULTAT FISCAL = WA + WR - XJ
  → Bénéfice fiscal (XN) ou Déficit fiscal (XO)

IS = 42 500 × 15% (PME) + (Résultat - 42 500) × 25%
```

**Contrôles de cohérence inter-feuillets (7 contrôles) :**

| Contrôle | Source | Cible | Règle |
|----------|--------|-------|-------|
| Total actif = Total passif | 2050 case CO | 2051 case EE | Égaux |
| Résultat CR = Résultat bilan | 2052 case HN | 2051 case DI | Égaux |
| Résultat comptable 2058-A | 2058-A case WA | 2052 case HN | Égaux |
| Immobilisations brutes | 2050 cases | 2054 totaux | Correspondent |
| Amortissements | 2050 colonnes amort. | 2055 totaux | Correspondent |
| Provisions | 2051 cases | 2056 totaux | Correspondent |
| Capital | 2051 case DA | Statuts | Correspondent |

**Template du feuillet 2050 — Bilan Actif :**

Mapping complet des cases cerfa avec comptes PCG :

| Case | Libellé | Colonnes |
|------|---------|----------|
| AA | Capital souscrit non appelé | Brut |
| AB | Frais d'établissement | Brut, Amort/Dépréc, Net N, Net N-1 |
| CX | Frais de R&D | idem |
| AF | Concessions, brevets, licences | idem |
| AH | Fonds commercial | idem |
| AL-AV | Immobilisations corporelles | idem |
| CS-BH | Immobilisations financières | idem |
| BJ | **TOTAL ACTIF IMMOBILISÉ** | idem |
| CO | **TOTAL GÉNÉRAL** | idem |

**Calcul de l'IS :**
- Taux réduit PME : 15% sur les premiers 42 500 € (si CA < 10 M€, capital détenu ≥ 75% par des personnes physiques)
- Taux normal : 25% au-delà
- Contribution sociale : 3,3% si CA > 7,63 M€

**Checklist (11 points) :**
1. Balance définitive (toutes écritures d'inventaire passées)
2. Résultat CR = Résultat bilan
3. Total actif = Total passif
4. Passage comptable → fiscal complet et documenté
5. Réintégrations et déductions conformes au CGI
6. Immobilisations brutes et amortissements cohérents (2054/2055)
7. Provisions (2056) correspondent aux comptes 15x
8. Échéancier créances/dettes (2057) ventilé correctement
9. Données N-1 correctement reportées
10. Taux d'IS correct
11. Date limite de dépôt respectée

**Comparaison avec `/sox-testing` (Finance US) :** +206 mots (+17%). Remplace complètement l'approche SOX par la conformité fiscale française. Le plugin US teste les contrôles internes ; le plugin FR prépare les formulaires déclaratifs.

---

## 5. Analyse des Skills

Les skills sont des **bases de connaissances méthodologiques** que Claude consulte en contexte. Contrairement aux commandes, elles ne sont pas invoquées directement par l'utilisateur mais enrichissent les réponses de Claude avec des bonnes pratiques et des méthodologies adaptées au contexte français.

### 5.1. `ecritures-pcg` — 1 648 mots, 282 lignes

**Rôle :** Plan comptable général, schémas d'écritures courantes, traitement de la TVA et conformité FEC.

**Sections couvertes :**

| Section | Contenu |
|---------|---------|
| **Classes PCG (1-8)** | Table des 8 classes avec intitulé, nature et position bilan/résultat |
| **Comptes les plus utilisés** | ~60 comptes détaillés : classe 4 (tiers, TVA), classe 5 (banque, caisse), classe 6 (charges par nature), classe 7 (produits par nature) |
| **Schémas d'écritures** | 8 schémas complets avec variantes : achat avec TVA, vente avec TVA, règlement fournisseur, encaissement client, paie, amortissement, opérations d'inventaire (CCA, PCA, FNP, provisions) |
| **Traitement TVA** | 4 taux avec applications, TVA intracommunautaire (autoliquidation via 4452), TVA non déductible (véhicules de tourisme, cadeaux > 73 € TTC) |
| **FEC** | 18 champs du Fichier des Écritures Comptables, obligation légale (art. L.47 A du LPF), règles de conformité |
| **Journaux comptables** | 8 codes (ACH, VTE, BQ, CA, OD, SAL, AN, EXT) |
| **Erreurs courantes** | 10 erreurs à vérifier (déséquilibre, mauvais compte, TVA incorrecte, absence pièce, doublon, extourne oubliée, non-conformité FEC) |

**Comparaison avec `journal-entry-prep` (Finance US) :** +545 mots (+49%). Le skill FR est plus volumineux car il intègre le référentiel PCG complet (classes, comptes), le traitement multi-taux TVA et la conformité FEC — éléments absents du contexte US.

---

### 5.2. `rapprochement-bancaire` — 1 092 mots, 156 lignes

**Rôle :** Méthodologie de rapprochement bancaire, techniques de lettrage, formats bancaires français.

**Sections couvertes :**

| Section | Contenu |
|---------|---------|
| **Rapprochement bancaire** | Processus en 5 étapes (512 vs relevé), format standard d'état de rapprochement |
| **Lettrage clients (411)** | 4 techniques : simple, partiel, groupé, avec écart (658/758) |
| **Lettrage fournisseurs (401)** | Même logique inversée |
| **Rapprochement intercompagnie** | Comptes 451, processus bilatéral |
| **Causes d'écarts** | 9 causes catégorisées (transit, comptable, erreur, permanent) avec résolutions |
| **Formats bancaires français** | CFONB (120/160/240), SEPA (pain.001/008, camt.053/054), MT940 (SWIFT) |
| **Analyse aging** | 2 grilles : bancaire (0-5j / 6-15j / 16-30j / >30j) et clients (non échu / 0-30 / 31-60 / 61-90 / >90j) |
| **Seuils d'escalade** | 5 critères déclencheurs |
| **Bonnes pratiques** | 8 pratiques (fréquence, exhaustivité, documentation, séparation des tâches, conservation 10 ans) |

**Spécificités françaises absentes du plugin US :**
- CFONB 120/160/240 (formats bancaires historiques français)
- SEPA pain.001/008 et camt.053/054
- Conservation 10 ans (Code de commerce)
- Techniques de lettrage avec attribution de lettres

**Comparaison avec `reconciliation` (Finance US) :** -56 mots (-5%). Taille similaire mais contenu adapté : formats bancaires français, lettrage au lieu du GL-to-subledger.

---

### 5.3. `etats-financiers-pcg` — 1 352 mots, 152 lignes

**Rôle :** Formats des états financiers PCG, SIG, annexe légale et références normatives.

**Sections couvertes :**

| Section | Contenu |
|---------|---------|
| **Bilan — Actif** | Structure détaillée : immobilisations incorporelles/corporelles/financières (comptes 20x, 21x, 26-27x), actif circulant (3x, 41x, 50x, 51x), CCA (486). Règles : Brut/Amort/Net obligatoire, liquidité croissante |
| **Bilan — Passif** | Capitaux propres (101-14x), provisions pour risques et charges (15x), dettes (16x-46x), PCA (487). Règle : exigibilité croissante |
| **Compte de résultat** | Par nature (pas par fonction) : résultat d'exploitation (classes 6/7 exploitation), résultat financier (66x/76x), résultat exceptionnel (67x/77x), participation (691), IS (695) |
| **SIG** | 9 soldes avec formules et signification, 6 ratios clés |
| **Annexe légale** | 10 informations obligatoires (règles/méthodes, tableaux immobilisations/amortissements/provisions, créances/dettes par échéance, engagements hors bilan, effectifs, rémunérations dirigeants, entreprises liées) |
| **Références normatives** | PCG art. 810-1 à 810-3, Code de commerce L123-12 à L123-28, Règlement ANC 2014-03, Directive 2013/34/UE |
| **Ajustements fin d'exercice** | 8 types : CCA, PCA, FNP, produits à recevoir, amortissements, provisions, variation de stocks, provision congés payés |

**Comparaison avec `financial-statements` (Finance US) :** +11 mots (+1%). Taille quasi-identique mais contenu fondamentalement différent : PCG par nature vs US GAAP par fonction, SIG français vs marge brute/opérationnelle US, annexe légale française vs disclosures SEC, ASC/IAS vs ANC/C. com.

---

### 5.4. `analyse-ecarts` — 1 251 mots, 180 lignes

**Rôle :** Techniques de décomposition des écarts, seuils de matérialité, narratifs et waterfall.

**Sections couvertes :**

| Section | Contenu |
|---------|---------|
| **Décomposition Prix/Volume** | Formules avec exemple chiffré (vérification incluse) |
| **Trois facteurs (Volume/Prix/Mix)** | Pour entreprises multi-produits |
| **Effectif/Coût moyen** | Pour la masse salariale, avec GVT et effet noria (termes français) |
| **Par catégorie de charges** | Table mapping comptes PCG → facteurs d'analyse |
| **Seuils de matérialité** | Framework avec seuils PME et ETI/GE séparés |
| **Priorisation** | 5 critères de priorisation des investigations |
| **Narratifs** | Structure en 3 parties (constat/explication/perspective), checklist qualité, 4 anti-patterns |
| **Waterfall** | Format texte, 5 bonnes pratiques |
| **Budget vs Réel vs Forecast** | 5 types de comparaison avec objectif et fréquence |
| **MAPE** | Formule et grille d'interprétation (4 niveaux) |
| **Biais systématiques** | Optimiste, pessimiste, précis mais volatile |

**Spécificités françaises :**
- Décomposition GVT (Glissement Vieillesse Technicité) et effet noria — terminologie française
- Mapping des catégories de charges sur les comptes PCG (601-607, 611-629, 631-637, 641-648, 681)
- Seuils différenciés PME / ETI-GE

**Comparaison avec `variance-analysis` (Finance US) :** -377 mots (-23%). Plus concis car certaines techniques sont identiques (pas de duplication inutile).

---

### 5.5. `cloture-comptable` — 1 657 mots, 233 lignes

**Rôle :** Gestion du processus de clôture mensuelle et annuelle avec calendrier fiscal français.

**Sections couvertes :**

| Section | Contenu |
|---------|---------|
| **Checklist mensuelle** | 6 phases : Pré-clôture (7 tâches), T+1 (9 tâches), T+2 (7 tâches), T+3 (6 tâches), T+4 (6 tâches), T+5 (6 tâches) — **41 tâches au total** |
| **Clôture annuelle** | Travaux spécifiques : inventaire physique (obligatoire C. com. L123-12), immobilisations (5 tâches), provisions/dépréciations (6 tâches), CCA/PCA (3 tâches), travaux fiscaux (5 tâches : IS, 2058-A, participation, CET, C3S), consolidation (4 tâches) |
| **Dépendances** | 5 niveaux de séquencement avec carte de dépendances détaillée |
| **Chemin critique** | Règlements/Factures → Rapprochement bancaire → Rapprochements bilan → Liquidation TVA / Provision IS → États financiers → Revue direction → Hard close |
| **Stratégies d'accélération** | 6 leviers |
| **Calendrier fiscal** | 11 obligations avec fréquences et dates limites |
| **Dashboard de suivi** | Format : Tâche / Responsable / Deadline / Statut / Bloqueur / Notes |
| **5 statuts** | Non démarré, En cours, Terminé, Bloqué, En risque |
| **5 métriques** | Durée clôture, écritures post-soft close, tâches en retard, exceptions rapprochement, corrections post-clôture |
| **Rétrospective** | 5 questions d'amélioration continue |

**Calendrier fiscal français (11 obligations) :**

| Obligation | Fréquence | Date limite |
|------------|-----------|-------------|
| TVA CA3 | Mensuelle | 15-24 du mois suivant |
| TVA CA12 | Annuelle | 2ème jour ouvré après le 1er mai |
| Acomptes IS | Trimestrielle | 15/03, 15/06, 15/09, 15/12 |
| Solde IS | Annuelle | 15/05 |
| Liasse fiscale | Annuelle | 2ème jour ouvré après le 1er mai |
| DAS2 | Annuelle | 31/01 N+1 |
| CFE | Annuelle | 15/12 |
| CVAE | Annuelle | Acomptes 15/06 et 15/09, solde 2ème jour ouvré après le 1er mai |
| C3S | Annuelle | 15/05 |
| DSN | Mensuelle | 5 ou 15 du mois suivant |
| AG approbation comptes | Annuelle | 6 mois après clôture |

**Comparaison avec `close-management` (Finance US) :** +151 mots (+10%). Le skill FR ajoute la clôture annuelle spécifique (inventaire physique, travaux fiscaux, participation) et le calendrier fiscal français complet — absents du plugin US.

---

### 5.6. `fiscalite-francaise` — 1 758 mots, 216 lignes (le plus gros fichier du plugin) — REMPLACE `audit-support`

**Rôle :** Fiscalité d'entreprise française complète : TVA, IS, CET, C3S, liasse fiscale et calendrier.

**Sections couvertes :**

| Section | Contenu |
|---------|---------|
| **TVA — Régimes** | 3 régimes (franchise en base, réel simplifié, réel normal) avec seuils et type de déclaration |
| **TVA — Taux** | 4 taux (20%, 10%, 5,5%, 2,1%) avec applications détaillées |
| **TVA — Territorialité** | 7 types d'opérations (vente France, prestation France, livraison/acquisition intra, export/import, prestations intra-UE) avec règle TVA |
| **TVA — Déductibilité** | 4 conditions, 6 exclusions (véhicules tourisme, gazole 80%, essences, hébergement, cadeaux > 73 €) |
| **TVA — Prorata** | Formule coefficient de déduction (assujettissement × taxation × admission) |
| **TVA — CA3** | Synthèse des sections A/B/C |
| **IS — Taux** | 15% PME (≤ 42 500 €) et 25% normal |
| **IS — Calcul** | Résultat comptable + réintégrations - déductions = résultat fiscal |
| **IS — Réintégrations** | 8 réintégrations avec articles CGI (213, 39-2, 39-4, 216, 210 sexies) |
| **IS — Déductions** | 5 déductions avec articles CGI (145, 216, 219, 244 quater B) |
| **IS — Acomptes** | 4 acomptes trimestriels (25% IS N-1) + solde |
| **CET — CFE** | Base (valeur locative), taux (communal), exonérations, cotisation minimale, paiement 15/12 |
| **CET — CVAE** | Seuil (CA > 500K€), base (VA fiscale), taux progressif, plafonnement CET (1,625% VA) |
| **C3S** | Seuil (CA > 19 M€), taux (0,16%), déductibilité |
| **Liasse fiscale** | 13 feuillets (2050 à 2059-E) avec contenu et source comptable |
| **Contrôles de cohérence** | 7 contrôles inter-feuillets |
| **Calendrier récapitulatif** | Mois par mois (janvier à décembre) : obligation + formulaire |

**Comparaison avec `audit-support` (Finance US) :** -554 mots (-24%). Le skill US est le plus gros du plugin Finance (2 312 mots) car il couvre un framework SOX très détaillé (CEAVOP, échantillonnage, 20 templates de contrôle). Le skill FR est plus synthétique mais couvre 5 impôts différents (TVA, IS, CET, C3S, liasse) au lieu d'un seul framework.

---

### 5.7. `charges-sociales` — 1 371 mots, 229 lignes — SKILL SANS ÉQUIVALENT DANS LE PLUGIN FINANCE

**Rôle :** Charges de personnel françaises, cotisations sociales, DSN et provisions liées au personnel.

**Sections couvertes :**

| Section | Contenu |
|---------|---------|
| **Bulletin de paie** | Structure complète du brut au net : salaire de base, heures sup, primes, avantages en nature → cotisations salariales → net imposable → PAS → net à payer + coût employeur |
| **Cotisations URSSAF** | 7 cotisations : maladie (7% ou 13%), allocations familiales (3,45% ou 5,25%), vieillesse plafonnée (6,90% sal. + 8,55% pat.), vieillesse déplafonnée, AT, CSA, FNAL |
| **Assurance chômage** | 2 cotisations : chômage (4,05% patronal), AGS (0,20%) |
| **Retraite AGIRC-ARRCO** | 5 cotisations sur 2 tranches : T1 (≤1 PASS), T2 (1-8 PASS), CEG T1/T2, CET |
| **CSG/CRDS** | 3 composantes : CSG déductible (6,80%), CSG non déductible (2,40%), CRDS (0,50%) sur base 98,25% du brut |
| **Autres cotisations** | 6 cotisations : prévoyance cadres (1,50% T.A), mutuelle, formation (0,55%/1%), taxe apprentissage (0,68%), participation construction (0,45%), forfait social (20%) |
| **Comptabilisation paie** | 4 écritures complètes : salaires/charges salariales, charges patronales, paiement salaires, paiement charges |
| **DSN** | Principe (remplacement DADS-U), 5 blocs de contenu, échéances (5 ou 15 du mois selon effectif) |
| **Provision congés payés** | Obligation, formule (avec charges patronales), écriture (641+645/4282+4382), 2 méthodes (1/10ème vs maintien salaire) |
| **Provision 13ème mois** | Provisionnement mensuel (1/12ème), écriture (641+645/4286+4386) |
| **Provision IDR** | Méthode préférentielle ANC, formule actuarielle, écriture (6815/153) |
| **Participation** | Formule légale RSP = 1/2 × (B - 5%C) × S/VA, écriture (691/4284), seuil ≥ 50 salariés |
| **Intéressement** | Accord d'entreprise, écriture (6414+645/4286), forfait social 20% si ≥ 250 salariés |

**Importance :** Ce skill comble une lacune majeure du plugin Finance US qui ne couvre les charges de paie qu'au niveau macro (4 composantes : salaires, avantages, taxes FICA/FUTA, primes) sans détail des cotisations. Le système social français est considérablement plus complexe que le système US.

---

## 6. Connecteurs et intégrations MCP

### 6.1. Fonctionnement des connecteurs

Le plugin utilise le même système de **placeholders catégoriels** (`~~catégorie`) que le plugin Finance, pour référencer les outils de manière agnostique. Par exemple, `~~erp` peut signifier Sage, Cegid, Pennylane ou tout autre logiciel comptable avec un serveur MCP.

Les fichiers de commandes contiennent des **instructions conditionnelles** en français :
```
Si ~~erp ou ~~data warehouse est connecté :
  → Extraire les données automatiquement

Si aucune source de données n'est connectée :
  → Demander à l'utilisateur de fournir les données manuellement
```

### 6.2. État actuel des connecteurs

| Catégorie | Placeholder | Serveurs dans `.mcp.json` | URL configurée ? | Alternatives mentionnées |
|-----------|-------------|--------------------------|:-----------------:|-------------------------|
| Data warehouse | `~~data warehouse` | Snowflake | Non (vide) | Databricks, PostgreSQL |
| Data warehouse | `~~data warehouse` | **BigQuery** | **Oui** | — |
| Email | `~~email` | **Microsoft 365** | **Oui** | Gmail |
| Suite bureautique | `~~suite bureautique` | **Microsoft 365** | **Oui** | Google Workspace |
| Chat | `~~chat` | **Slack** | **Oui** | Microsoft Teams |
| ERP / Comptabilité | `~~erp` | **Aucun** | — | Sage, Cegid, Pennylane, QuickBooks, EBP, Divalto |
| Paie | `~~paie` | **Aucun** | — | Silae, PayFit, Sage Paie, ADP |
| Banque | `~~banque` | **Aucun** | — | Open Banking (DSP2), Qonto, Shine |
| Analytics / BI | `~~analytics` | **Aucun** | — | Power BI, Tableau, Looker |

**Bilan : 3 serveurs fonctionnels** (BigQuery, Slack, MS 365), **1 placeholder** (Snowflake), **4 catégories vides** (ERP, Paie, Banque, Analytics).

### 6.3. Catégories spécifiques au contexte français

Par rapport au plugin Finance US, le plugin comptabilite-fr ajoute **3 catégories de connecteurs** :

| Catégorie | Rôle | Commandes consommatrices |
|-----------|------|--------------------------|
| `~~paie` (Silae, PayFit) | Données salariales, cotisations, bulletins | `/ecriture salaire`, charges-sociales skill |
| `~~banque` (Open Banking DSP2) | Relevés bancaires automatiques | `/rapprochement banque` |
| `~~erp` (Sage, Cegid, Pennylane) | Spécifiquement des ERP français | Toutes les commandes |

### 6.4. Catégories recommandées par le plugin

Le README liste 7 catégories d'intégration recommandées :

| # | Catégorie | Rôle dans le plugin |
|---|-----------|-------------------|
| 1 | `erp-comptabilite` | GL, auxiliaires, écritures — **le plus critique** |
| 2 | `data-warehouse` | Requêtes financières et historiques |
| 3 | `paie` | Données salariales, cotisations, DSN |
| 4 | `banque` | Relevés bancaires pour rapprochement |
| 5 | `suite-bureautique` | Génération de documents |
| 6 | `email` | Envoi de rapports et demandes de validation |
| 7 | `chat` | Communication d'équipe pour le suivi de clôture |

---

## 7. Faire de la comptabilité française sans connecteur ERP

### 7.1. Ce que prévoit le plugin

Le plugin est **explicitement conçu pour fonctionner sans ERP**. Chaque commande contient un fallback clair en français :

> *"Connectez ~~erp ou ~~data warehouse pour extraire les données automatiquement. Vous pouvez aussi coller les données de la balance, charger un fichier Excel/CSV ou fournir les éléments manuellement."*

**4 modes de fonctionnement sans ERP :**

| Mode | Description | Quand l'utiliser |
|------|-------------|-----------------|
| **Copier-coller** | L'utilisateur colle les données dans le chat | Petits volumes, analyses ponctuelles |
| **Upload de fichier** | L'utilisateur charge un fichier Excel/CSV | Exports ERP, balances, grands livres |
| **Saisie guidée** | Claude demande les informations nécessaires | Première utilisation, données partielles |
| **Data warehouse** | Requêtes SQL via BigQuery/Snowflake | Données répliquées dans un DWH |

### 7.2. Limites concrètes sans ERP

| Capacité | Avec ERP | Sans ERP |
|----------|----------|----------|
| Pull automatique de la balance | Automatique via MCP | Manuel (copier-coller/upload) |
| Accès au subledger détaillé | Requête directe | L'utilisateur doit exporter |
| Historique N-1 | Requête comparative | L'utilisateur doit fournir |
| Posting d'écritures dans le GL | Potentiellement automatisable | Saisie manuelle dans le logiciel |
| Rapprochement bancaire temps réel | Possible (données live via DSP2) | Basé sur exports ponctuels |
| Extraction des comptes TVA | Automatique | L'utilisateur doit extraire les 445xx |
| Données de paie et cotisations | Via ~~paie | L'utilisateur fournit les bulletins |
| Verrouillage de période | Commande via API | Action manuelle dans le logiciel |
| Génération FEC | Possible via API ERP | L'utilisateur exporte depuis son logiciel |

### 7.3. Stratégie alternative via Data Warehouse

```
┌─────────────┐     ┌──────────────┐     ┌───────────────┐     ┌──────────────┐
│ ERP FR      │ ──→ │ ETL /        │ ──→ │ Data Warehouse│ ──→ │ Plugin       │
│ (Sage,      │     │ Réplication  │     │ (BigQuery,    │     │ Comptabilité │
│  Cegid,     │     │              │     │  Snowflake)   │     │ FR via MCP   │
│  Pennylane) │     │              │     │               │     │              │
└─────────────┘     └──────────────┘     └───────────────┘     └──────────────┘
```

### 7.4. Synthèse : ce qui est faisable vs infaisable

**Faisable sans ERP (le plugin le supporte déjà) :**
- Préparation d'écritures comptables PCG (template + calculs)
- Rapprochement bancaire et lettrage (avec données fournies)
- Préparation du CA3/CA12 (avec soldes TVA fournis)
- Génération d'états financiers PCG (avec balance fournie)
- Analyse de variance (avec données des 2 périodes)
- Préparation de la liasse fiscale (avec balance définitive)
- Calcul de l'IS et du passage comptable → fiscal

**Infaisable sans ERP ni data warehouse :**
- Pull automatique de données en temps réel
- Posting automatique d'écritures dans le GL
- Verrouillage de période comptable
- Génération automatique du FEC
- Extraction automatique des données de paie
- Rapprochement bancaire continu / rolling
- Suivi automatique des statuts de clôture dans le système

---

## 8. Comment devra être un connecteur ERP français

### 8.1. Exigences fonctionnelles — Outils MCP à exposer

Un connecteur ERP français pour ce plugin devrait exposer les outils MCP suivants :

#### A. Lecture des données (Read Operations) — Essentielles

| Outil MCP | Description | Commandes consommatrices |
|-----------|-------------|--------------------------|
| `get_balance_generale(period, entity?)` | Balance comptable PCG | Toutes |
| `get_grand_livre(account, period)` | Grand livre d'un compte avec mouvements | `/rapprochement`, `/ecriture` |
| `get_ecritures(period, journal?, type?)` | Écritures comptables | `/ecriture`, `/liasse-fiscale` |
| `get_soldes_comptes(accounts[], period)` | Soldes de comptes spécifiques | `/rapprochement`, `/declaration-tva` |
| `get_plan_comptable()` | Plan comptable avec hiérarchie | Toutes |
| `get_releve_bancaire(compte_512, period)` | Relevé bancaire (CFONB/camt.053) | `/rapprochement banque` |
| `get_registre_immobilisations(period?)` | Registre des immobilisations | `/ecriture immobilisation`, `/liasse-fiscale` |
| `get_tableau_amortissements(period)` | Tableau des amortissements | `/ecriture immobilisation`, `/liasse-fiscale 2055` |
| `get_tableau_provisions(period)` | Mouvements des provisions | `/liasse-fiscale 2056` |
| `get_balance_agee_clients(period)` | Balance âgée clients | `/rapprochement client` |
| `get_balance_agee_fournisseurs(period)` | Balance âgée fournisseurs | `/rapprochement fournisseur` |
| `get_soldes_tva(period)` | Soldes des comptes 445xx | `/declaration-tva` |
| `get_donnees_budget(period, version?)` | Données budgétaires | `/analyse-ecarts` |
| `get_echeancier_creances_dettes(period)` | Ventilation < 1 an / 1-5 ans / > 5 ans | `/liasse-fiscale 2057` |
| `get_fec(period)` | Fichier des Écritures Comptables | Conformité FEC |
| `get_rapprochement_anterieur(account, period)` | Rapprochement précédent | `/rapprochement` |

#### B. Données de paie (via connecteur ~~paie)

| Outil MCP | Description | Commande |
|-----------|-------------|----------|
| `get_journal_paie(period)` | Journal de paie mensuel | `/ecriture salaire` |
| `get_cotisations(period)` | Détail des cotisations par organisme | `/rapprochement social` |
| `get_dsn(period)` | DSN mensuelle | charges-sociales skill |
| `get_effectifs(period)` | Effectifs par catégorie | `/analyse-ecarts masse-salariale` |

#### C. Écriture des données (Write Operations) — Optionnelles

| Outil MCP | Description | Commande |
|-----------|-------------|----------|
| `post_ecriture(entry_data)` | Comptabiliser une écriture | `/ecriture` |
| `extourner_ecriture(entry_id)` | Extourner une écriture | `/ecriture od` |
| `verrouiller_periode(period)` | Verrouiller une période comptable | cloture-comptable |
| `deverrouiller_periode(period)` | Déverrouiller (avec autorisation) | cloture-comptable |
| `generer_fec(period)` | Générer le FEC réglementaire | Conformité fiscale |

#### D. Métadonnées et configuration

| Outil MCP | Description |
|-----------|-------------|
| `get_entites()` | Entités juridiques (SIRET) |
| `get_exercices(status?)` | Exercices comptables (ouverts/clôturés) |
| `get_journaux()` | Liste des journaux comptables |
| `get_taux_tva()` | Taux de TVA configurés |
| `get_conventions_collectives()` | Conventions collectives applicables |

### 8.2. Format de données attendu

Les données doivent être en **JSON structuré** avec les conventions françaises :

```json
{
  "balance_generale": {
    "period": "2024-12",
    "entity_siret": "12345678900012",
    "currency": "EUR",
    "comptes": [
      {
        "numero_compte": "44571",
        "libelle_compte": "TVA collectée",
        "classe": "4",
        "solde_debiteur": 0,
        "solde_crediteur": 45000.00,
        "solde_debiteur_n1": 0,
        "solde_crediteur_n1": 42500.00
      }
    ]
  }
}
```

**Conventions :**
- Montants en euros avec 2 décimales (séparateur décimal : point dans le JSON)
- Dates au format ISO 8601 (`2024-12-31`)
- Numéros de comptes en string (pour préserver les zéros)
- Identifiants entreprise : SIRET (14 chiffres), SIREN (9 chiffres)
- N° TVA intracommunautaire : format `FR` + 2 chiffres + SIREN

### 8.3. Connecteurs ERP potentiels — Évaluation de faisabilité

| ERP | Part de marché FR | Maturité API | Faisabilité MCP |
|-----|:-----------------:|-------------|:--------------:|
| **Sage 100 / X3** | Très forte | REST API (X3), SDK .NET (100) | **Moyenne** (X3 bon, 100 limité) |
| **Cegid** | Forte (expert-comptables) | API REST en développement | **Moyenne** |
| **Pennylane** | Croissante (startups/PME) | API REST moderne | **Haute** |
| **QuickBooks France** | Moyenne | REST API V3 mature | **Haute** |
| **EBP** | Moyenne (TPE/PME) | Très limitée (SDK local) | **Très faible** |
| **Divalto** | PME industrielles | API REST disponible | **Moyenne** |
| **Silae** (paie) | Forte (paie externalisée) | API REST | **Moyenne-Haute** |
| **PayFit** (paie) | Croissante | API REST moderne | **Haute** |
| **Qonto** (banque) | Forte (néobanque pro) | API REST, Open Banking | **Haute** |

---

## 9. Traitement des tâches comptables majeures

### 9.1. Déclaration de TVA

**Couverture : EXCELLENTE** (vs "FAIBLE" dans le plugin Finance US)

Le plugin couvre la déclaration de TVA de bout en bout :

| Étape | Commande/Skill | Couverture |
|-------|---------------|------------|
| Régimes de TVA (franchise, simplifié, normal) | `fiscalite-francaise` skill | Complète |
| 4 taux de TVA (20%, 10%, 5,5%, 2,1%) | `ecritures-pcg` skill + `/ecriture` | Complète |
| Territorialité et autoliquidation | `fiscalite-francaise` skill | Complète |
| Préparation CA3 (toutes les lignes) | `/declaration-tva ca3` | Complète |
| Préparation CA12 | `/declaration-tva ca12` | Supportée |
| Rapprochement CA déclaré / comptable | `/declaration-tva` (section 3) | Complète |
| Écriture de liquidation | `/declaration-tva` (section 4) | Complète |
| Prorata de déduction | `fiscalite-francaise` skill | Formule fournie |
| Exclusions de déductibilité | `fiscalite-francaise` skill | 6 exclusions détaillées |
| Date limite de dépôt | `cloture-comptable` + `fiscalite-francaise` | Calendrier complet |

---

### 9.2. Bilan et états financiers

**Couverture : EXCELLENTE**

| Composant | Commande/Skill | Couverture |
|-----------|---------------|------------|
| Bilan actif (format PCG) | `/etats-financiers bilan` | Template complet avec brut/amort/net |
| Bilan passif (format PCG) | `/etats-financiers bilan` | Template complet |
| Compte de résultat par nature | `/etats-financiers compte-resultat` | Template complet (exploitation/financier/exceptionnel) |
| SIG (9 soldes) | `/etats-financiers sig` | Formules + ratios + template |
| Comparatif N/N-1 | `/etats-financiers` | Variation en € et % |
| Analyse des variations | `/etats-financiers` (section 5) | Seuils configurables |
| Annexe légale | `etats-financiers-pcg` skill | 10 informations obligatoires |
| Références normatives | `etats-financiers-pcg` skill | PCG, C. com., ANC, Directive UE |
| Ajustements de clôture | `etats-financiers-pcg` skill | 8 types d'ajustements |
| Mapping cerfa (2050/2051/2052) | `/liasse-fiscale` | Cases cerfa avec comptes PCG |

---

### 9.3. Clôture mensuelle

**Couverture : EXCELLENTE**

Le workflow de clôture est le point fort majeur du plugin, calqué sur le close-management du plugin Finance US et enrichi des spécificités françaises.

#### Workflow de clôture complet

```
PHASE PRÉ-CLÔTURE (J-2 à J-3) — 7 tâches
│  Calendrier, cut-off, vérification interfaces, rapprochement préliminaire,
│  FNP prévisibles, paie, opérations exceptionnelles
│
├── JOUR T+1 : Écritures de base — 9 tâches
│   ├── /ecriture achat 2025-01      → Factures fournisseurs + FNP
│   ├── /ecriture salaire 2025-01    → Écritures de paie + cotisations
│   ├── /ecriture immobilisation     → Amortissements
│   ├── Amortissement des CCA
│   ├── Derniers encaissements/décaissements
│   ├── Opérations intercompagnies
│   └── Rapprochement bancaire (relevé définitif)
│
├── JOUR T+2 : Reconnaissance CA et lettrage — 7 tâches
│   ├── Reconnaissance du CA
│   ├── Provisions et ajustements restants
│   ├── /rapprochement client 2025-01   → Lettrage 411
│   ├── /rapprochement fournisseur      → Lettrage 401
│   ├── Ajustements de stocks
│   ├── Écarts de change
│   └── Début rapprochements bilan
│
├── JOUR T+3 : Rapprochements bilan — 6 tâches
│   ├── /rapprochement banque 2025-01   → État de rapprochement
│   ├── /rapprochement ic 2025-01       → Intercompagnie
│   ├── Écritures issues des rapprochements
│   ├── Balance provisoire
│   ├── /analyse-ecarts (préliminaire)
│   └── Investigation variances significatives
│
├── JOUR T+4 : TVA, IS et états financiers — 6 tâches
│   ├── /declaration-tva ca3 2025-01    → Liquidation TVA
│   ├── Provision IS mensuelle
│   ├── Soft close — finalisation écritures
│   ├── /etats-financiers complet 2025-01 → Bilan + CR + SIG
│   ├── /analyse-ecarts détaillée
│   └── Revue de direction
│
└── JOUR T+5 : Finalisation — 6 tâches
    ├── Ajustements suite revue
    ├── Hard close / verrouillage
    ├── Reporting mensuel
    ├── Mise à jour prévisions
    └── Rétrospective
```

**Total : 41 tâches réparties sur 6 phases avec 5 niveaux de dépendances.**

---

### 9.4. Liasse fiscale et conformité

**Couverture : EXCELLENTE** (vs "ABSENTE" dans le plugin Finance US)

| Composant | Commande/Skill | Couverture |
|-----------|---------------|------------|
| Feuillets cerfa 2050-2059 | `/liasse-fiscale` | 13 feuillets détaillés |
| Passage comptable → fiscal (2058-A) | `/liasse-fiscale` | Template complet (réintégrations/déductions) |
| Calcul IS (taux normal + réduit PME) | `/liasse-fiscale` + `fiscalite-francaise` | Formules détaillées |
| Réintégrations extra-comptables | `fiscalite-francaise` skill | 8 réintégrations avec articles CGI |
| Déductions extra-comptables | `fiscalite-francaise` skill | 5 déductions avec articles CGI |
| Contrôles inter-feuillets | `/liasse-fiscale` | 7 contrôles croisés |
| Acomptes IS | `fiscalite-francaise` skill | 4 acomptes + solde |
| CET (CFE + CVAE) | `fiscalite-francaise` skill | Bases, taux, plafonnement |
| C3S | `fiscalite-francaise` skill | Seuil, taux, déductibilité |
| Calendrier fiscal | `cloture-comptable` + `fiscalite-francaise` | Mois par mois, exercice 31/12 |
| FEC | `ecritures-pcg` skill | 18 champs, obligation légale |

---

### 9.5. Charges sociales et paie

**Couverture : BONNE** (vs "PARTIELLE" dans le plugin Finance US)

| Composant | Commande/Skill | Couverture |
|-----------|---------------|------------|
| Structure bulletin de paie | `charges-sociales` skill | Du brut au net complet |
| Cotisations URSSAF | `charges-sociales` skill | 7 cotisations avec taux |
| Cotisations retraite AGIRC-ARRCO | `charges-sociales` skill | 5 cotisations sur 2 tranches |
| CSG/CRDS | `charges-sociales` skill | 3 composantes, base 98,25% |
| Écriture de paie | `/ecriture salaire` + `charges-sociales` | 4 écritures complètes |
| DSN | `charges-sociales` skill | Principe, 5 blocs, échéances |
| Provision congés payés | `charges-sociales` skill | 2 méthodes, écriture, charges patronales |
| Provision 13ème mois | `charges-sociales` skill | Provisionnement mensuel |
| Provision IDR | `charges-sociales` skill | Méthode préférentielle ANC |
| Participation | `charges-sociales` skill | Formule RSP, seuil, écriture |
| Intéressement | `charges-sociales` skill | Forfait social, écriture |

---

### 9.6. Tableau de synthèse des tâches comptables

| Tâche comptable | Couverture | Comment c'est traité | Ce qui manque |
|----------------|:----------:|---------------------|---------------|
| **Déclaration de TVA** | Excellente | `/declaration-tva` + `fiscalite-francaise` : CA3/CA12 complet, rapprochement CA, liquidation | — |
| **Clôture mensuelle** | Excellente | Workflow T+1 à T+5, 41 tâches, dépendances, métriques | — |
| **Bilan PCG** | Excellente | `/etats-financiers bilan` : format brut/amort/net, comparatif N-1 | — |
| **Compte de résultat** | Excellente | `/etats-financiers compte-resultat` : par nature, 3 niveaux | — |
| **SIG** | Excellente | `/etats-financiers sig` : 9 soldes avec formules et ratios | — |
| **Liasse fiscale** | Excellente | `/liasse-fiscale` : 13 feuillets cerfa, 7 contrôles | — |
| **Passage comptable → fiscal** | Excellente | `/liasse-fiscale` 2058-A : réintégrations + déductions + IS | — |
| **Rapprochement bancaire** | Excellente | `/rapprochement banque` : état de rapprochement + aging | — |
| **Lettrage tiers** | Excellente | `/rapprochement client/fournisseur` : template avec lettres | — |
| **Analyse de variance** | Excellente | `/analyse-ecarts` : 5 domaines, waterfall, narratifs | — |
| **Écritures de paie** | Bonne | `/ecriture salaire` + `charges-sociales` : 4 écritures | Pas de calcul automatique du bulletin |
| **Amortissements** | Bonne | `/ecriture immobilisation` : linéaire + dégressif fiscal | Pas de registre d'immobilisations intégré |
| **Clôture annuelle** | Bonne | `cloture-comptable` skill : inventaire, provisions, travaux fiscaux | Pas de commande dédiée `/cloture` |
| **Consolidation** | Partielle | Mentionnée dans cloture-comptable | Pas de commande `/consolidation` |
| **Budget** | Partielle | Comparaison réel vs budget dans `/analyse-ecarts` | Pas de préparation budgétaire |
| **Bilan social** | Absent | Non mentionné | Tout à créer |
| **Tableau flux trésorerie** | Absent | Non mentionné | À ajouter dans `/etats-financiers` |

---

## 10. Forces et limites du plugin

### 10.1. Forces

| Force | Détail |
|-------|--------|
| **Couverture fiscale complète** | TVA (4 taux, territorialité, prorata), IS (réintégrations/déductions), CET, C3S — toute la fiscalité courante |
| **Conformité FEC** | 18 champs du FEC documentés, règles de conformité, obligation légale référencée |
| **SIG (9 soldes)** | Spécificité française majeure, absente du plugin Finance US, avec formules et ratios |
| **Déclaration TVA de bout en bout** | CA3 complet, rapprochement CA, écriture de liquidation — comble la lacune #1 du plugin US |
| **Liasse fiscale exhaustive** | 13 feuillets cerfa, passage comptable → fiscal, 7 contrôles inter-feuillets |
| **Charges sociales détaillées** | Cotisations URSSAF, AGIRC-ARRCO, CSG/CRDS avec taux, provisions CP/IDR/participation |
| **Calendrier fiscal** | Mois par mois, 11 obligations avec dates limites |
| **Architecture agnostique** | Même système `~~catégorie` que le plugin Finance, dégradation gracieuse |
| **Lettrage français** | Technique de lettrage avec attribution de lettres, 4 méthodes |
| **Références normatives** | PCG, Code de commerce, CGI, Règlement ANC, Directive UE |
| **Disclaimers systématiques** | Chaque fichier rappelle la nécessité de validation par un expert-comptable |
| **Volume de contenu** | 20 042 mots — +27% par rapport au plugin Finance US |

### 10.2. Limites

| Limite | Impact | Criticité |
|--------|--------|:---------:|
| **Pas de connecteur ERP français** | Le connecteur le plus critique (Sage, Cegid, Pennylane) est absent | Haute |
| **Pas de connecteur paie** | Silae, PayFit non connectés | Haute |
| **Pas de connecteur bancaire** | Open Banking / DSP2 non connecté | Moyenne |
| **Pas de tableau des flux de trésorerie** | Absent des états financiers (couvert dans le plugin US) | Moyenne |
| **Pas de commande `/cloture`** | La clôture est un skill mais pas une commande invocable | Moyenne |
| **Pas de consolidation** | Pas de `/consolidation` malgré les mentions d'éliminations interco | Moyenne |
| **Pas de budget** | Pas de préparation budgétaire | Moyenne |
| **Taux de cotisations susceptibles d'évoluer** | Les taux 2025 sont indicatifs et doivent être vérifiés | Moyenne |
| **Pas de bilan social** | Non mentionné | Faible |
| **Format de sortie texte** | Templates en markdown/ASCII — pas d'export Excel/PDF natif | Faible |
| **Pas de multi-devises avancé** | Écarts de change mentionnés mais pas de gestion IFRS 21 | Faible |

---

## 11. Synthèse comparative Commandes vs Skills

### 11.1. Matrice de correspondance

| Domaine | Commande | Mots cmd | Skill | Mots skill | Ratio Skill/Cmd |
|---------|----------|--------:|-------|----------:|----------------:|
| Écritures comptables | `/ecriture` | 1 069 | `ecritures-pcg` | 1 648 | 1,54x |
| Rapprochement | `/rapprochement` | 937 | `rapprochement-bancaire` | 1 092 | 1,17x |
| Déclaration TVA | `/declaration-tva` | 1 161 | `fiscalite-francaise` | 1 758 | 1,51x |
| États financiers | `/etats-financiers` | 1 735 | `etats-financiers-pcg` | 1 352 | **0,78x** |
| Analyse des écarts | `/analyse-ecarts` | 911 | `analyse-ecarts` | 1 251 | 1,37x |
| Liasse fiscale | `/liasse-fiscale` | 1 410 | `fiscalite-francaise` | 1 758 | 1,25x |
| Clôture comptable | *aucune* | — | `cloture-comptable` | 1 657 | ∞ |
| Charges sociales | *aucune* | — | `charges-sociales` | 1 371 | ∞ |
| **Total** | **6 commandes** | **7 223** | **7 skills** | **10 129** | **1,40x** |

**Observations :**
- Le ratio moyen skill/commande est de **1,33x** (hors skills sans commande correspondante), inférieur au plugin Finance US (1,77x). Cela reflète des commandes plus volumineuses dans le plugin FR (+71% de mots dans les commandes).
- Exception notable : `/etats-financiers` (1 735 mots) est **plus volumineuse** que son skill correspondant (1 352 mots), ratio inversé de 0,78x. La commande intègre directement les templates bilan, CR et SIG.
- 2 skills n'ont pas de commande correspondante : `cloture-comptable` et `charges-sociales`.

### 11.2. Différence de rôle

| Aspect | Commandes (6) | Skills (7) |
|--------|---------------|------------|
| **Invocation** | Explicite par l'utilisateur (`/ecriture`, `/rapprochement`, etc.) | Implicite — Claude les consulte en contexte |
| **Langue** | Français intégral | Français intégral |
| **Structure** | Workflow séquentiel (étapes 1→N) | Base de connaissances thématique |
| **Arguments** | Oui (type, période, compte) | Non |
| **Templates de sortie** | Oui (formats pré-définis avec placeholders) | Oui (formats de référence) |
| **Conditionnalité** | Oui (`si ~~erp connecté` / `si aucune source`) | Non |
| **Disclaimer** | Oui (dans chaque fichier) | Oui (dans chaque fichier) |
| **Référence CONNECTORS.md** | Oui (lien en haut de chaque commande) | Non |
| **Total mots** | 7 223 (36,0% du total) | 10 129 (50,5% du total) |

### 11.3. Architecture d'ensemble

```
                          ┌──────────────────────────────┐
                          │     UTILISATEUR               │
                          │  (coller / upload / chat)     │
                          └──────────────┬───────────────┘
                                         │
                          ┌──────────────▼───────────────┐
                          │     6 COMMANDES SLASH         │
                          │  /ecriture  /rapprochement    │
                          │  /declaration-tva             │
                          │  /etats-financiers            │
                          │  /analyse-ecarts              │
                          │  /liasse-fiscale              │
                          │     7 223 mots (36%)          │
                          └──────────────┬───────────────┘
                                         │ consulte
                          ┌──────────────▼───────────────┐
                          │     7 SKILLS (Knowledge)      │
                          │  ecritures-pcg                │
                          │  rapprochement-bancaire       │
                          │  etats-financiers-pcg         │
                          │  analyse-ecarts               │
                          │  cloture-comptable            │
                          │  fiscalite-francaise          │
                          │  charges-sociales             │
                          │     10 129 mots (51%)         │
                          └──────────────┬───────────────┘
                                         │ accède via
                          ┌──────────────▼───────────────┐
                          │     CONNECTEURS MCP           │
                          │  ~~erp (aucun)                │
                          │  ~~paie (aucun)               │
                          │  ~~banque (aucun)             │
                          │  ~~data warehouse (BigQuery)  │
                          │  ~~chat (Slack)               │
                          │  ~~email/office (MS 365)      │
                          │     2 690 mots (13%)          │
                          └──────────────────────────────┘
```

---

## 12. Comparaison avec le plugin Finance (US)

### 12.1. Vue quantitative

| Indicateur | Finance (US) | Comptabilité FR | Différence |
|------------|-------------:|----------------:|-----------:|
| Fichiers totaux | 16 | 18 | +2 (+13%) |
| Commandes | 5 | 6 | +1 |
| Skills | 6 | 7 | +1 |
| Mots totaux | 15 791 | 20 042 | +4 251 (+27%) |
| Mots commandes | 4 221 | 7 223 | +3 002 (+71%) |
| Mots skills | 9 038 | 10 129 | +1 091 (+12%) |
| Mots moy. par commande | 844 | 1 204 | +360 (+43%) |
| Mots moy. par skill | 1 506 | 1 447 | -59 (-4%) |
| Fichier le plus gros | audit-support (2 312) | fiscalite-francaise (1 758) | -554 |
| Points checklist (max) | 11 (/je) | 12 (/ecriture) | +1 |
| Catégories connecteurs | 7 | 9 | +2 (paie, banque) |

### 12.2. Mapping fonctionnel

| Domaine | Finance US | Comptabilité FR | Adaptation |
|---------|-----------|-----------------|------------|
| Écritures | `/je` (731 mots) | `/ecriture` (1 069 mots) | +46% — TVA multi-taux, FEC, PCG |
| Rapprochement | `/recon` (701 mots) | `/rapprochement` (937 mots) | +34% — Lettrage FR, CFONB/SEPA |
| États financiers | `/income-statement` (700 mots) | `/etats-financiers` (1 735 mots) | +148% — Bilan + CR + SIG (vs P&L seul) |
| Analyse variance | `/flux` (885 mots) | `/analyse-ecarts` (911 mots) | +3% — Adapté vocabulaire PCG |
| Conformité | `/sox` (1 204 mots) | `/liasse-fiscale` (1 410 mots) | +17% — Cerfa au lieu de SOX |
| **Nouveau** | *aucun* | `/declaration-tva` (1 161 mots) | **Entièrement nouveau** |

### 12.3. Couverture comparative des tâches comptables

| Tâche | Finance US | Comptabilité FR |
|-------|:----------:|:---------------:|
| Clôture mensuelle | Excellente | Excellente |
| Rapprochement bancaire | Excellente | Excellente |
| Écritures comptables | Excellente | Excellente |
| Analyse de variance | Excellente | Excellente |
| Conformité SOX / Audit | Excellente | N/A (SOX ≠ droit français) |
| Bilan | Bonne (US GAAP) | Excellente (PCG) |
| Compte de résultat | Excellente (P&L par fonction) | Excellente (CR par nature) |
| SIG | Absents | Excellente |
| Déclaration de TVA | **Faible** | **Excellente** |
| Liasse fiscale | **Absente** | **Excellente** |
| IS et fiscalité | Faible (provision IS seulement) | **Excellente** (IS, CET, C3S, liasse) |
| Charges sociales | Partielle (4 composantes) | **Bonne** (cotisations détaillées, DSN) |
| FEC | Absent | Intégré (18 champs) |
| Tableau flux trésorerie | Bonne | **Absent** |
| Consolidation | Partielle | Partielle |
| Budget | Partielle | Partielle |

---

> **Conclusion :** Le plugin Comptabilité France constitue une adaptation complète et approfondie du plugin Finance US au contexte français. Avec **20 042 mots** répartis sur 18 fichiers, il dépasse le plugin original de 27% en volume de contenu. Son architecture identique (tool-agnostic, dégradation gracieuse, YAML frontmatter) garantit la compatibilité avec l'écosystème Cowork/Claude Code. Les deux ajouts majeurs — la commande `/declaration-tva` et le skill `charges-sociales` — comblent les lacunes les plus critiques identifiées dans l'analyse du plugin Finance pour le marché français. La couverture fiscale (TVA, IS, CET, C3S, liasse fiscale cerfa 2050-2059) et la conformité FEC en font un outil opérationnel pour les cabinets d'expertise comptable et les directions financières françaises, sous réserve de validation par un professionnel qualifié. Le potentiel maximum du plugin sera atteint lorsque des connecteurs MCP pour les ERP français (Sage, Cegid, Pennylane) et les logiciels de paie (Silae, PayFit) seront disponibles.
