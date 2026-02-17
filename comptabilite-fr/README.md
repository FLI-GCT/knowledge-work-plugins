# Plugin Comptabilité France

Un plugin de comptabilité française conçu pour [Cowork](https://claude.com/product/cowork), l'application desktop agentique d'Anthropic — compatible également avec Claude Code. Couvre les écritures comptables PCG, le rapprochement bancaire, la déclaration de TVA, les états financiers, la liasse fiscale, la clôture comptable et le référentiel PCG complet.

**Auteur :** Guillaume CLEMENT — [Flow Line Intégration](https://flowline.fr)

**Conformité :** PCG 2025 — Règlement ANC 2014-03 modifié par ANC 2022-06 (applicable au 1er janvier 2025)

> **Avertissement — Décharge de responsabilité** : Ce plugin assiste dans les workflows de comptabilité française mais **ne fournit pas de conseil comptable, fiscal ou juridique**. Les résultats produits par l'intelligence artificielle peuvent contenir des erreurs. Tous les résultats doivent être **systématiquement vérifiés et validés par un expert-comptable ou un professionnel qualifié** avant toute utilisation dans les déclarations fiscales, les états financiers ou les documents réglementaires. L'auteur et les contributeurs déclinent toute responsabilité en cas d'erreur, d'omission ou de préjudice résultant de l'utilisation de ce plugin.

## Origine et licence

Ce plugin est une **adaptation française** du [plugin Finance](https://github.com/anthropics/knowledge-work-plugins/tree/main/finance) officiel d'Anthropic (US GAAP/IFRS), réalisée par de **multiples agents IA** (Claude Opus, Claude Sonnet) sous la direction de Guillaume CLEMENT.

L'ensemble du travail est publié sous **licence Apache 2.0** — voir le fichier [LICENSE](LICENSE) pour les termes complets.

> Le plugin Finance original est la propriété d'Anthropic. Cette adaptation française ne constitue pas un produit officiel Anthropic. Les noms Anthropic, Claude, Cowork sont des marques d'Anthropic, PBC.

## Installation

```bash
claude plugins add knowledge-work-plugins/comptabilite-fr
```

## Commandes

| Commande | Description |
|----------|-------------|
| `/ecriture` | Écritures comptables — préparer des écritures PCG avec comptes, TVA, débits/crédits et pièces justificatives |
| `/rapprochement` | Rapprochement bancaire et lettrage — pointer le relevé bancaire, lettrer les comptes tiers, identifier les écarts |
| `/declaration-tva` | Déclaration de TVA — préparer le CA3 (mensuel) ou CA12 (annuel simplifié) avec rapprochement du CA |
| `/etats-financiers` | États financiers — générer le bilan, le compte de résultat et les SIG au format PCG avec comparatif N-1 |
| `/analyse-ecarts` | Analyse des écarts — décomposer les variances en facteurs explicatifs avec narratifs et analyse en cascade |
| `/liasse-fiscale` | Liasse fiscale — préparer les feuillets cerfa 2050-2059 avec passage du résultat comptable au résultat fiscal |

## Skills

| Skill | Description |
|-------|-------------|
| `plan-comptable-general` | Référentiel complet du PCG 2025 (ANC 2014-03 modifié par ANC 2022-06) — 8 classes, ~500 comptes officiels, principes de numérotation, table des erreurs fréquentes |
| `ecritures-pcg` | Schémas d'écritures PCG, classes 1-7, traitement TVA (collectée, déductible, intracommunautaire), écritures d'inventaire et obligations FEC |
| `rapprochement-bancaire` | Méthodologie de rapprochement bancaire, lettrage des comptes tiers, formats CFONB/SEPA, bonnes pratiques et seuils d'escalade |
| `etats-financiers-pcg` | Bilan et compte de résultat format PCG, soldes intermédiaires de gestion (SIG), contenu de l'annexe légale et références normatives |
| `analyse-ecarts` | Techniques de décomposition des écarts (prix/volume/mix), seuils de matérialité, génération de narratifs et analyse en cascade |
| `cloture-comptable` | Checklist de clôture mensuelle et annuelle, calendrier fiscal français, séquencement des tâches et dépendances |
| `fiscalite-francaise` | TVA (régimes, taux, territorialité), IS (calcul, acomptes), CET, C3S, liasse fiscale et calendrier des obligations |
| `charges-sociales` | Structure du bulletin de paie, cotisations URSSAF, DSN, provisions congés payés et participation/intéressement |

## Exemples de workflows

### Clôture mensuelle

1. Lancer `/ecriture achat 2025-01` pour comptabiliser les factures fournisseurs
2. Lancer `/ecriture salaire 2025-01` pour enregistrer les écritures de paie
3. Lancer `/ecriture immobilisation 2025-01` pour passer les amortissements
4. Lancer `/rapprochement banque 2025-01` pour pointer le relevé bancaire
5. Lancer `/rapprochement client 2025-01` pour lettrer les comptes clients
6. Lancer `/declaration-tva ca3 2025-01` pour préparer la déclaration de TVA
7. Lancer `/etats-financiers complet 2025-01` pour générer bilan, CR et SIG

### Clôture annuelle et liasse fiscale

1. Lancer `/ecriture od 2024-12` pour passer les écritures d'inventaire (CCA, PCA, provisions)
2. Lancer `/etats-financiers complet 2024` pour générer les états annuels
3. Lancer `/liasse-fiscale 2050-2059 2024` pour préparer la liasse complète
4. Vérifier les contrôles de cohérence inter-feuillets

### Analyse de performance

1. Lancer `/analyse-ecarts ca 2025-01 vs 2024-01` pour analyser l'évolution du chiffre d'affaires
2. Lancer `/analyse-ecarts charges 2025-01 vs budget` pour investiguer les écarts budgétaires
3. Examiner le waterfall et fournir le contexte sur les variances inexpliquées

## Intégration MCP

> Pour comprendre les placeholders ou vérifier les outils connectés, voir [CONNECTORS.md](CONNECTORS.md).

Ce plugin fonctionne au mieux lorsqu'il est connecté à vos sources de données via des serveurs MCP. Ajoutez les serveurs pertinents à votre `.mcp.json` :

### ERP / Logiciel comptable

Connectez votre ERP (ex. Sage, Cegid, Pennylane) pour extraire automatiquement la balance générale, le grand livre, les écritures et les données auxiliaires.

### Data Warehouse

Connectez votre data warehouse (ex. BigQuery, Snowflake) pour requêter les données financières, lancer des analyses de variance et récupérer les historiques.

### Logiciel de paie

Connectez votre logiciel de paie (ex. Silae, PayFit) pour récupérer les données salariales, les cotisations et alimenter les écritures de paie.

### Banque

Connectez vos comptes bancaires (via Open Banking / DSP2) pour récupérer automatiquement les relevés et accélérer le rapprochement.

> **Note :** Connectez votre ERP et data warehouse pour extraire les données automatiquement. Sans ces connecteurs, vous pouvez coller des données ou charger des fichiers pour analyse.

## Configuration

Ajoutez vos serveurs MCP dans la section `mcpServers` du fichier `.mcp.json` de ce plugin. Les catégories d'intégration recommandées :

- `erp-comptabilite` — ERP ou logiciel comptable pour le GL, les auxiliaires et les écritures
- `data-warehouse` — Entrepôt de données pour les requêtes financières et historiques
- `paie` — Logiciel de paie pour les données salariales et cotisations
- `banque` — Connexion bancaire pour les relevés et le rapprochement
- `suite-bureautique` — Outils bureautiques pour la génération de documents
- `email` — Email pour l'envoi de rapports et demandes de validation
- `chat` — Communication d'équipe pour le suivi de clôture

## Métriques

| Indicateur | Valeur |
|---|---|
| Mots de savoir total | 23 947 |
| Commandes | 6 |
| Skills | 8 |
| Fichiers | 20 |
| Conformité | PCG 2025 (ANC 2022-06) |
