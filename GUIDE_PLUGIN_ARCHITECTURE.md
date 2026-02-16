# Guide Complet : Architecture et Conception de Plugins Claude

> Analyse transversale des 11 plugins du repository `knowledge-work-plugins`. Ce document explique la structure, la logique, les patterns récurrents et les bonnes pratiques pour concevoir un plugin performant.

---

## Table des matières

1. [Vue d'ensemble](#1-vue-densemble)
2. [Architecture d'un plugin](#2-architecture-dun-plugin)
3. [Le manifeste plugin.json](#3-le-manifeste-pluginjson)
4. [Les Skills : expertise métier automatique](#4-les-skills--expertise-métier-automatique)
5. [Les Commands : actions explicites](#5-les-commands--actions-explicites)
6. [Les Connectors : intégration d'outils externes](#6-les-connectors--intégration-doutils-externes)
7. [Composants avancés (Agents, Hooks)](#7-composants-avancés-agents-hooks)
8. [Le pattern Standalone / Supercharged](#8-le-pattern-standalone--supercharged)
9. [Patterns d'écriture performants](#9-patterns-décriture-performants)
10. [Anatomie comparée des 11 plugins](#10-anatomie-comparée-des-11-plugins)
11. [Checklist de création d'un plugin](#11-checklist-de-création-dun-plugin)
12. [Anti-patterns à éviter](#12-anti-patterns-à-éviter)

---

## 1. Vue d'ensemble

### Qu'est-ce qu'un plugin ?

Un plugin est un **ensemble de fichiers Markdown et JSON** — sans code, sans infrastructure, sans build — qui transforme Claude en spécialiste d'un métier. Chaque plugin encode :

- **L'expertise métier** (skills) que Claude mobilise automatiquement
- **Les actions explicites** (commands) déclenchées par l'utilisateur via `/`
- **Les connexions aux outils** (connectors) via le protocole MCP

### Philosophie fondamentale

Tous les plugins du repository partagent trois principes :

| Principe | Description |
|----------|-------------|
| **File-based** | Tout est Markdown + JSON. Pas de code, pas de serveur, pas de build. Personnalisable par des non-développeurs. |
| **Tool-agnostic** | Les workflows sont décrits par catégories (`~~CRM`, `~~chat`) et non par produit spécifique. |
| **Graceful degradation** | Chaque plugin fonctionne sans aucun outil connecté (standalone) et s'améliore progressivement avec chaque outil ajouté (supercharged). |

### Les 11 plugins existants

| Plugin | Domaine | Commands | Skills | Connectors |
|--------|---------|----------|--------|------------|
| **productivity** | Gestion de tâches, mémoire, journée | 2 | 2 | 8 |
| **sales** | Prospection, pipeline, outreach | 3 | 6 | 9 |
| **customer-support** | Tickets, escalade, base de connaissances | 5 | 5 | 7 |
| **product-management** | Specs, roadmap, recherche utilisateur | 6 | 6 | 12 |
| **marketing** | Contenu, campagnes, brand voice | 7 | 5 | 9 |
| **legal** | Contrats, NDA, conformité, risque | 5 | 6 | 5 |
| **finance** | Écritures, rapprochements, clôture | 5 | 6 | 5 |
| **data** | SQL, visualisation, dashboards | 6 | 7 | 6 |
| **enterprise-search** | Recherche cross-plateforme | 2 | 3 | 6 |
| **bio-research** | Recherche préclinique, génomique | 1 | 5 | 10 |
| **cowork-plugin-management** | Création et gestion de plugins | 0 | 2 | 0 |

---

## 2. Architecture d'un plugin

### Structure de répertoire standard

Chaque plugin suit **exactement** cette arborescence :

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json              # Manifeste (requis)
├── .mcp.json                    # Configuration MCP des connecteurs
├── commands/                    # Slash commands (fichiers .md)
│   ├── command-one.md
│   └── command-two.md
├── skills/                      # Expertise métier (sous-répertoires)
│   ├── skill-one/
│   │   ├── SKILL.md             # Définition du skill (requis)
│   │   ├── references/          # Documentation détaillée (optionnel)
│   │   │   └── detailed-guide.md
│   │   ├── scripts/             # Scripts utilitaires (optionnel)
│   │   └── examples/            # Exemples concrets (optionnel)
│   └── skill-two/
│       └── SKILL.md
├── agents/                      # Sous-agents autonomes (optionnel, rare)
│   └── agent-name.md
├── hooks/                       # Hooks événementiels (optionnel, rare)
│   └── hooks.json
├── README.md                    # Documentation du plugin
├── CONNECTORS.md                # Guide des intégrations (si tool-agnostic)
└── LICENSE
```

### Conventions de nommage

| Élément | Convention | Exemple |
|---------|------------|---------|
| Répertoire du plugin | `kebab-case` | `product-management` |
| Répertoire de skill | `kebab-case` | `account-research` |
| Fichier de command | `kebab-case.md` | `call-summary.md` |
| Fichiers structurels | `MAJUSCULES.md` | `SKILL.md`, `README.md`, `CONNECTORS.md` |
| plugin.json | Champ `name` en `kebab-case` | `"name": "finance"` |

### Hiérarchie des fichiers obligatoires vs optionnels

```
OBLIGATOIRE :
  .claude-plugin/plugin.json     → Identité du plugin
  README.md                      → Documentation pour l'utilisateur

QUASI-OBLIGATOIRE (présent dans 100% des plugins métier) :
  .mcp.json                      → Connexions aux outils
  skills/*/SKILL.md              → Au moins un skill
  commands/*.md                  → Au moins une command (sauf cowork-plugin-management)
  CONNECTORS.md                  → Guide des connecteurs (si plugin distribué)

OPTIONNEL :
  skills/*/references/*.md       → Documentation approfondie
  skills/*/scripts/              → Scripts d'automatisation
  agents/*.md                    → Sous-agents (rare en Cowork)
  hooks/hooks.json               → Hooks événementiels (rare en Cowork)
```

---

## 3. Le manifeste plugin.json

Le fichier `.claude-plugin/plugin.json` est le **seul fichier strictement requis**. Il identifie le plugin auprès du système.

### Format

```json
{
  "name": "plugin-name",
  "version": "1.0.0",
  "description": "Description concise de ce que fait le plugin (1-2 phrases).",
  "author": {
    "name": "Nom de l'auteur ou de l'organisation"
  }
}
```

### Règles

| Champ | Règle |
|-------|-------|
| `name` | kebab-case, minuscules avec tirets, pas d'espaces ni caractères spéciaux |
| `version` | Format semver : `MAJOR.MINOR.PATCH`. Commencer à `0.1.0` pour les nouveaux plugins |
| `description` | Orientée action, expliquant la valeur ajoutée. Max 1-2 phrases |
| `author.name` | Nom de la personne ou organisation |

### Champs optionnels

```json
{
  "homepage": "https://...",
  "repository": "https://github.com/...",
  "license": "Apache-2.0",
  "keywords": ["finance", "accounting", "close"],
  "commands": "./custom-commands",
  "agents": ["./agents", "./specialized-agents"],
  "hooks": "./config/hooks.json",
  "mcpServers": "./.mcp.json"
}
```

### Exemples réels

**Finance** :
```json
{
  "name": "finance",
  "version": "1.0.0",
  "description": "Streamline finance and accounting workflows, from journal entries and reconciliation to financial statements and variance analysis.",
  "author": { "name": "Anthropic" }
}
```

**Sales** :
```json
{
  "name": "sales",
  "version": "1.0.0",
  "description": "Prospect, craft outreach, and build deal strategy faster.",
  "author": { "name": "Anthropic" }
}
```

---

## 4. Les Skills : expertise métier automatique

### Concept

Un skill est un **corpus de connaissances métier** que Claude charge automatiquement quand le contexte de la conversation le justifie. L'utilisateur ne déclenche pas un skill — Claude le mobilise de lui-même.

### Structure d'un skill

```
skill-name/
├── SKILL.md              # Corps principal (requis)
├── references/           # Documentation détaillée (optionnel)
│   ├── methodology.md
│   └── templates.md
├── examples/             # Exemples concrets (optionnel)
│   └── sample-output.md
└── scripts/              # Scripts d'automatisation (optionnel)
    └── generate.py
```

### Format du SKILL.md

#### Frontmatter YAML (requis)

```yaml
---
name: nom-du-skill
description: >
  Description à la troisième personne avec des phrases de déclenchement spécifiques.
  Exemple : "This skill should be used when the user asks to 'review a contract',
  'analyze an agreement', or 'check clause terms'."
---
```

| Champ | Obligatoire | Règle |
|-------|-------------|-------|
| `name` | Oui | kebab-case, identifiant unique du skill |
| `description` | Oui | Troisième personne, avec des **trigger phrases** entre guillemets |
| `version` | Non | Semver |

**Pourquoi la troisième personne ?** La description est lue par le système pour décider quand activer le skill. Elle doit décrire les conditions d'activation, pas s'adresser à l'utilisateur.

#### Corps du SKILL.md

Le corps suit un schéma récurrent observé dans les 53 SKILL.md du repository :

```markdown
# Nom du Skill

[Disclaimer si domaine sensible : finance, legal, médical]

[Description du rôle que Claude joue]

## Méthodologie / Framework

[Framework structuré : étapes, règles, principes]

## Catégories / Types

[Taxonomie du domaine : types de contrats, types d'écritures, etc.]

## Workflow d'Exécution

### Étape 1 : [Analyse / Collecte]
[Instructions précises]

### Étape 2 : [Traitement / Calcul]
[Instructions précises]

### Étape 3 : [Génération / Output]
[Instructions précises]

## Format de Sortie

[Template exact de l'output attendu]

## Erreurs Courantes / Pièges

[Liste des erreurs à éviter]

## Ressources Additionnelles

[Références vers les fichiers dans references/]
```

### Principe de divulgation progressive (Progressive Disclosure)

Le système de skills fonctionne en **trois niveaux de profondeur** :

| Niveau | Quand chargé | Taille recommandée | Contenu |
|--------|-------------|---------------------|---------|
| **Métadonnées** | Toujours en contexte | ~100 mots | `name` + `description` du frontmatter |
| **Corps SKILL.md** | Quand le skill est activé | < 3 000 mots (idéal : 1 500-2 000) | Connaissance cœur, frameworks, workflows |
| **Références** | À la demande | Illimité | Documentation détaillée, templates, scripts |

**Règle d'or** : Le SKILL.md doit être **autonome et actionnable** sans les références. Les références approfondissent mais ne sont pas indispensables.

### Exemples de skills performants

#### Skill structuré par taxonomie (finance/journal-entry-prep)

```yaml
---
name: journal-entry-prep
description: Prepare journal entries with proper debits, credits, and supporting
  documentation for month-end close. Use when booking accruals, prepaid amortization,
  fixed asset depreciation, payroll entries, revenue recognition, or any manual
  journal entry.
---
```

Le corps décompose ensuite les 5 types d'écritures (AP accruals, Fixed Assets, Prepaids, Payroll, Revenue) avec pour chacun : l'écriture type, les sources de calcul, et les points de vigilance.

#### Skill structuré par workflow (sales/account-research)

```yaml
---
name: account-research
description: Research a company or person and get actionable sales intel. Works
  standalone with web search, supercharged when you connect enrichment tools or
  your CRM. Trigger with "research [company]", "look up [person]", "intel on
  [prospect]", "who is [name] at [company]".
---
```

Le corps suit un flow en 5 étapes (Parse → Web Search → Enrichment → CRM Check → Synthesize) avec un template de sortie détaillé.

#### Skill structuré par framework décisionnel (legal/contract-review)

```yaml
---
name: contract-review
description: Review contracts against your organization's negotiation playbook,
  flagging deviations and generating redline suggestions. Use when reviewing vendor
  contracts, customer agreements, or any commercial agreement.
---
```

Le corps encode une méthodologie complète : chargement du playbook, analyse clause par clause, classification GREEN/YELLOW/RED, génération de redlines, et framework de priorité de négociation en 3 tiers.

---

## 5. Les Commands : actions explicites

### Concept

Une command est une **action que l'utilisateur déclenche explicitement** via une slash command (ex: `/finance:journal-entry`). C'est une instruction **pour Claude**, pas une documentation pour l'utilisateur.

### Format d'une command

#### Frontmatter YAML

```yaml
---
description: "Description courte (< 60 caractères), affichée dans /help"
argument-hint: "<type> [period]"
allowed-tools: Read, Write, Bash(git:*)
model: sonnet
---
```

| Champ | Obligatoire | Description |
|-------|-------------|-------------|
| `description` | Non | Affiché dans `/help`. Max 60 caractères |
| `argument-hint` | Non | Documente les arguments attendus pour l'autocomplétion |
| `allowed-tools` | Non | Restreint les outils utilisables |
| `model` | Non | Override du modèle (`sonnet`, `opus`, `haiku`) |

#### Corps de la command

Le corps est une **instruction directive pour Claude**. Il suit un pattern récurrent :

```markdown
# Nom de la Command

> Si vous voyez des placeholders inconnus, voir [CONNECTORS.md](../CONNECTORS.md).

[Disclaimer si domaine sensible]

[Description de ce que fait la command]

## Usage

```
/command-name <arg1> [arg2]
```

### Arguments
- `arg1` — Description
- `arg2` — Description (optionnel)

## Workflow

### 1. Collecter les données
[Instructions : si ~~outil connecté → action automatique, sinon → demander à l'utilisateur]

### 2. Traiter / Calculer
[Instructions de traitement par type/cas]

### 3. Générer la sortie
[Template exact de l'output]

### 4. Vérifier / Review
[Checklist de contrôle]

### 5. Output final
[Ce qui est présenté à l'utilisateur + propositions de suite]
```

### Syntaxe spéciale dans les commands

| Syntaxe | Fonction | Exemple |
|---------|----------|---------|
| `$ARGUMENTS` | Capture tous les arguments | `Analyze $ARGUMENTS` |
| `$1`, `$2`, `$3` | Arguments positionnels | `Review @$1 for security issues` |
| `@path` | Inclut le contenu d'un fichier | `Read the transcript at @$1` |
| `` !`cmd` `` | Exécute un bash inline | `` !`git diff --name-only` `` |
| `${CLAUDE_PLUGIN_ROOT}` | Chemin racine du plugin | Portabilité des références internes |

### Différence fondamentale : Skill vs Command

| Aspect | Skill | Command |
|--------|-------|---------|
| **Déclenchement** | Automatique par Claude | Explicite par l'utilisateur (`/`) |
| **Rôle** | Connaissance métier de fond | Action spécifique avec workflow |
| **Ton** | Référence, encyclopédique | Directif, impératif |
| **Persistence** | Chargé quand pertinent | Exécuté une fois |
| **Output** | Influence les réponses de Claude | Produit un livrable structuré |

### Comment ils collaborent

Un **command appelle implicitement les skills** pertinents. Exemple dans le plugin `finance` :

- La command `/journal-entry ap-accrual 2024-12` déclenche le workflow de la command
- Le skill `journal-entry-prep` fournit la connaissance métier (types d'écritures, bonnes pratiques, checklist de review)
- Le skill `close-management` peut aussi être mobilisé si le contexte est une clôture mensuelle

Claude **orchestre automatiquement** cette collaboration.

---

## 6. Les Connectors : intégration d'outils externes

### Le protocole MCP

Les plugins se connectent aux outils externes via **MCP (Model Context Protocol)**. La configuration est définie dans `.mcp.json` à la racine du plugin.

### Format de .mcp.json

```json
{
  "mcpServers": {
    "nom-du-serveur": {
      "type": "http",
      "url": "https://service.example.com/mcp"
    }
  }
}
```

### Types de serveurs MCP

| Type | Usage | Authentification |
|------|-------|-----------------|
| **http** | APIs REST, services cloud | Headers / Token |
| **sse** | Services hébergés avec OAuth | OAuth automatique |
| **stdio** | Outils locaux, serveurs custom | Variables d'environnement |

### Exemples réels de configuration

**Finance** (data warehouses + communication) :
```json
{
  "mcpServers": {
    "snowflake": { "type": "http", "url": "" },
    "databricks": { "type": "http", "url": "" },
    "bigquery": { "type": "http", "url": "https://bigquery.googleapis.com/mcp" },
    "slack": { "type": "http", "url": "https://mcp.slack.com/mcp" },
    "ms365": { "type": "http", "url": "https://microsoft365.mcp.claude.com/mcp" }
  }
}
```

**Product Management** (12 connecteurs) :
```json
{
  "mcpServers": {
    "slack": { "type": "http", "url": "https://mcp.slack.com/mcp" },
    "linear": { "type": "sse", "url": "https://mcp.linear.app/sse" },
    "notion": { "type": "http", "url": "https://mcp.notion.com/mcp" },
    "figma": { "type": "http", "url": "https://mcp.figma.com/mcp" },
    "amplitude": { "type": "http", "url": "https://mcp.amplitude.com/mcp" }
  }
}
```

### Le système de placeholders `~~catégorie`

Les plugins sont **tool-agnostic** : ils décrivent les workflows en termes de **catégories** et non de produits spécifiques.

| Placeholder | Signification | Exemples d'outils |
|-------------|--------------|-------------------|
| `~~chat` | Messagerie d'équipe | Slack, Microsoft Teams, Discord |
| `~~CRM` | Gestion de la relation client | HubSpot, Salesforce, Close |
| `~~project tracker` | Gestion de projet | Jira, Linear, Asana, Monday, ClickUp |
| `~~knowledge base` | Base de connaissances | Notion, Guru, Confluence |
| `~~data warehouse` | Entrepôt de données | Snowflake, BigQuery, Databricks |
| `~~erp` | Système ERP/comptable | NetSuite, SAP, QuickBooks |
| `~~email` | Email | Microsoft 365, Gmail |
| `~~office suite` | Suite bureautique | Microsoft 365, Google Workspace |
| `~~analytics` | Analytics/BI | Amplitude, Pendo, Tableau |
| `~~enrichment` | Enrichissement de données | Clay, ZoomInfo, Apollo |
| `~~cloud storage` | Stockage de documents | Box, Egnyte, Google Drive |
| `~~product analytics` | Analytics produit | Amplitude, Pendo, Mixpanel |

### Utilisation dans les fichiers

Dans les commands et skills, les outils sont référencés de façon générique :

```markdown
If ~~CRM is connected:
- Pull the account history and recent opportunities
- Check for existing contacts

If no CRM is connected:
> Connect ~~CRM to pull account data automatically.
Prompt the user to provide account context manually.
```

### Le fichier CONNECTORS.md

Chaque plugin distribué inclut un `CONNECTORS.md` qui documente les catégories de connecteurs :

```markdown
# Connectors

## How tool references work

Plugin files use `~~category` as a placeholder for whatever tool the user
connects in that category. Plugins are **tool-agnostic**.

## Connectors for this plugin

| Category | Placeholder | Included servers | Other options |
|----------|-------------|-----------------|---------------|
| Data warehouse | `~~data warehouse` | Snowflake*, BigQuery | Redshift, PostgreSQL |
| Chat | `~~chat` | Slack | Microsoft Teams |
| ERP | `~~erp` | — (pas de serveur MCP) | NetSuite, SAP |
```

---

## 7. Composants avancés (Agents, Hooks)

### Agents (sous-agents autonomes)

Les agents sont des **processus autonomes** qui exécutent des tâches multi-étapes. Ils sont **rarement utilisés** dans les plugins Cowork (aucun des 11 plugins n'en utilise directement), mais le système les supporte.

#### Format

```markdown
---
name: agent-name
description: >
  Use this agent when the user needs to [trigger condition].

  <example>
  Context: [situation]
  user: "[user message]"
  assistant: "[how Claude would invoke the agent]"
  <commentary>
  [Why this agent is appropriate here]
  </commentary>
  </example>

model: inherit
color: cyan
tools: ["Read", "Grep", "Glob"]
---

[System prompt : instructions pour l'agent]
```

#### Quand utiliser un agent

- Tâches autonomes multi-étapes (analyse, validation, génération)
- Tâches qui bénéficient d'un contexte isolé
- Tâches qui nécessitent un modèle ou des outils spécifiques

### Hooks (événements automatiques)

Les hooks exécutent des actions automatiquement en réponse à des événements système.

#### Événements disponibles

| Événement | Quand il se déclenche |
|-----------|----------------------|
| `PreToolUse` | Avant l'exécution d'un outil |
| `PostToolUse` | Après l'exécution d'un outil |
| `Stop` | Quand Claude termine une réponse |
| `SessionStart` | Au démarrage de session |
| `UserPromptSubmit` | Quand l'utilisateur envoie un message |

#### Types de hooks

**Prompt-based** (logique complexe, évaluation par LLM) :
```json
{
  "type": "prompt",
  "prompt": "Evaluate whether this file write follows project conventions: $TOOL_INPUT",
  "timeout": 30
}
```

**Command-based** (vérifications déterministes) :
```json
{
  "type": "command",
  "command": "bash ${CLAUDE_PLUGIN_ROOT}/hooks/scripts/validate.sh",
  "timeout": 60
}
```

---

## 8. Le pattern Standalone / Supercharged

C'est le **pattern architectural le plus important** des plugins. Chaque skill et command est conçu pour fonctionner à deux niveaux :

### Diagramme ASCII (pattern universel)

```
┌─────────────────────────────────────────────────────────────────┐
│                     NOM DE LA FONCTIONNALITÉ                     │
├─────────────────────────────────────────────────────────────────┤
│  STANDALONE (fonctionne toujours)                                │
│  ✓ Capacité de base via web search et input utilisateur         │
│  ✓ Capacité de base via raisonnement de Claude                  │
│  ✓ Capacité de base via données fournies manuellement           │
├─────────────────────────────────────────────────────────────────┤
│  SUPERCHARGED (avec connecteurs)                                 │
│  + Données automatiques depuis ~~outil                          │
│  + Enrichissement depuis ~~autre-outil                          │
│  + Actions dans ~~outil-de-sortie                               │
└─────────────────────────────────────────────────────────────────┘
```

### Implémentation dans les workflows

Le pattern se traduit systématiquement par cette structure dans les commands :

```markdown
### Étape N : Collecter les données

If ~~data source is connected:
- Pull [data] automatically from ~~data source
- Query [specific information]

If no data source is connected:
> Connect ~~data source to pull data automatically. You can also
> paste data or upload a file.

Prompt the user to provide:
- [Data item 1]
- [Data item 2]
- [Data item 3] (optional)
```

### Pourquoi ce pattern est essentiel

1. **Adoption immédiate** : L'utilisateur peut commencer à utiliser le plugin sans aucune configuration
2. **Valeur incrémentale** : Chaque outil connecté ajoute de la valeur sans casser le fonctionnement existant
3. **Résilience** : Si un outil tombe ou est rate-limité, le plugin continue avec les autres sources

---

## 9. Patterns d'écriture performants

### 9.1 Écriture directive (pour les commands)

Les commands sont des **instructions pour Claude**, pas de la documentation. Utilisez l'impératif :

```markdown
# BON : Directif
Pull the trial balance for the specified period.
Calculate accrual amounts from PO receipts, contracts, or estimates.
Present the entry in standard format.

# MAUVAIS : Descriptif
This command allows the user to create journal entries.
The system will calculate the amounts.
```

### 9.2 Trigger phrases précises (pour les skills)

La description du skill détermine quand il est activé. Soyez spécifique :

```yaml
# BON : Trigger phrases explicites
description: >
  Research a company or person and get actionable sales intel.
  Trigger with "research [company]", "look up [person]",
  "intel on [prospect]", "who is [name] at [company]".

# MAUVAIS : Trop vague
description: >
  This skill helps with sales-related research tasks.
```

### 9.3 Templates de sortie structurés

Chaque command et skill performant inclut un **template exact** de l'output :

```markdown
## Output Format

```
Journal Entry: [Type] — [Period]
Prepared by: [User]
Date: [Period end date]

| Line | Account Code | Account Name | Debit | Credit | Department | Memo |
|------|-------------|--------------|-------|--------|------------|------|
| 1    | XXXX        | [Name]       | X,XXX |        | [Dept]     | [Detail] |

Supporting Detail:
- [Calculation basis and assumptions]
```
```

Cela garantit :
- **Cohérence** des outputs d'une exécution à l'autre
- **Professionnalisme** du rendu
- **Actionnabilité** immédiate (copier-coller dans un outil)

### 9.4 Checklists de validation

Les plugins performants terminent toujours par une **checklist de contrôle** :

```markdown
### Review Checklist

Before finalizing, verify:

- [ ] Debits equal credits
- [ ] Correct accounting period
- [ ] Account codes are valid
- [ ] Amounts are calculated correctly with supporting detail
- [ ] Memo/description is clear and specific enough for audit
- [ ] Entry is consistent with prior period treatment
```

### 9.5 Propositions de suite

Terminez chaque output par des **propositions d'actions suivantes** :

```markdown
"Would you like me to:
- Dive deeper into any section?
- Draft specific content pieces?
- Adjust the plan for a different budget?
- Run a competitive analysis?"
```

Cela transforme une interaction ponctuelle en **workflow continu**.

### 9.6 Classification par sévérité

Pour les skills d'analyse ou de review, utilisez une classification visuelle :

```markdown
### GREEN — Acceptable
[Pas d'action nécessaire]

### YELLOW — Negotiate
[Action recommandée, avec alternatives]

### RED — Escalate
[Action requise, avec justification et chemin d'escalade]
```

Ce pattern est utilisé dans `legal/contract-review` et est applicable à tout domaine nécessitant une priorisation.

### 9.7 Tableaux de pondération et scoring

Pour les skills de décision ou de classement :

```markdown
| Factor | Weight (Decision) | Weight (Status) | Weight (Document) |
|--------|-------------------|------------------|--------------------|
| Keyword match | 0.3 | 0.2 | 0.4 |
| Freshness | 0.3 | 0.4 | 0.2 |
| Authority | 0.2 | 0.1 | 0.3 |
| Completeness | 0.2 | 0.3 | 0.1 |
```

Cela rend les décisions **transparentes et reproductibles**.

### 9.8 Exécution parallèle

Pour les workflows multi-sources, spécifiez l'exécution parallèle :

```markdown
## Parallel Execution

Always execute searches across sources in parallel, never sequentially.

[User query]
     ↓ decompose
[~~chat query] [~~email query] [~~wiki query] [~~tracker query]
     ↓            ↓            ↓            ↓
  (parallel execution)
     ↓
[Merge + Rank + Deduplicate]
     ↓
[Synthesized answer]
```

---

## 10. Anatomie comparée des 11 plugins

### Par complexité

| Niveau | Plugins | Caractéristiques |
|--------|---------|-----------------|
| **Simple** | productivity, enterprise-search | 2-3 skills, 2 commands, workflows linéaires |
| **Intermédiaire** | customer-support, sales, marketing | 5-6 skills, 3-7 commands, workflows conditionnels |
| **Avancé** | finance, legal, product-management | 6 skills, 5-6 commands, frameworks décisionnels, taxonomies complexes |
| **Spécialisé** | data, bio-research | Skills techniques profonds, scripts, transformations de données |
| **Méta** | cowork-plugin-management | Plugin qui crée d'autres plugins |

### Par type de connaissance encodée

| Type de connaissance | Plugins qui l'utilisent | Exemple |
|---------------------|------------------------|---------|
| **Taxonomie métier** | finance, legal, customer-support | Types d'écritures, types de clauses, catégories de tickets |
| **Framework décisionnel** | legal, product-management, marketing | GREEN/YELLOW/RED, RICE, SMART |
| **Workflow opérationnel** | finance, sales, data | Clôture mensuelle, pipeline review, exploration de données |
| **Template de sortie** | Tous | Formats structurés pour chaque output |
| **Matrice de décision** | legal, enterprise-search | Priorité de négociation, priorité de sources |
| **Connaissance technique** | bio-research, data | Protocoles scRNA-seq, dialectes SQL |

### Connecteurs les plus utilisés

| Outil | Plugins (sur 11) | Catégorie |
|-------|-------------------|-----------|
| Slack | 10/11 | Communication |
| Microsoft 365 | 8/11 | Email, bureau |
| Notion | 7/11 | Knowledge base |
| Jira/Atlassian | 7/11 | Project tracking |
| HubSpot | 4/11 | CRM |
| Asana | 4/11 | Project management |
| BigQuery | 3/11 | Data warehouse |
| Amplitude | 3/11 | Analytics |

---

## 11. Checklist de création d'un plugin

### Phase 1 : Conception

- [ ] Identifier le **métier cible** et les workflows clés
- [ ] Lister les **5-7 situations** où l'utilisateur a besoin d'aide
- [ ] Pour chaque situation, décider : skill (automatique) ou command (explicite) ?
- [ ] Identifier les **outils externes** nécessaires et les catégoriser (`~~`)
- [ ] Vérifier que le plugin fonctionne **sans aucun outil connecté**

### Phase 2 : Structure

- [ ] Créer l'arborescence de répertoires standard
- [ ] Écrire le `plugin.json` avec un `name` kebab-case et une `description` orientée action
- [ ] Configurer le `.mcp.json` avec les serveurs MCP pertinents
- [ ] Créer le `CONNECTORS.md` si le plugin utilise des `~~placeholders`

### Phase 3 : Skills

- [ ] Pour chaque skill, écrire un frontmatter avec des **trigger phrases spécifiques**
- [ ] Structurer le corps avec : méthodologie → workflow → format de sortie → erreurs courantes
- [ ] Garder le SKILL.md sous **3 000 mots** (idéal : 1 500-2 000)
- [ ] Déplacer le contenu détaillé dans `references/`
- [ ] Écrire en **style impératif** (verbes d'action)

### Phase 4 : Commands

- [ ] Pour chaque command, écrire un frontmatter avec `description` (< 60 chars) et `argument-hint`
- [ ] Structurer le workflow : Collecter → Traiter → Générer → Vérifier → Output
- [ ] Implémenter le pattern **Standalone / Supercharged** à chaque étape de collecte
- [ ] Inclure un **template de sortie** exact
- [ ] Terminer par des **propositions de suite**
- [ ] Ajouter une **checklist de validation** si pertinent

### Phase 5 : Documentation

- [ ] Écrire le `README.md` avec : description, installation, tableau des commands, tableau des skills, exemples de workflows, configuration MCP
- [ ] Inclure un **disclaimer** si le domaine est sensible (finance, legal, médical)
- [ ] Documenter les variables d'environnement requises

### Phase 6 : Validation

- [ ] Tester chaque command en mode standalone (sans connecteurs)
- [ ] Tester chaque command en mode supercharged (avec connecteurs)
- [ ] Vérifier que les skills se déclenchent sur les bonnes phrases
- [ ] Vérifier la cohérence des `~~placeholders` entre CONNECTORS.md et les fichiers
- [ ] Valider avec `claude plugin validate <path>`

---

## 12. Anti-patterns à éviter

### Structurels

| Anti-pattern | Pourquoi c'est problématique | Bonne pratique |
|-------------|------------------------------|----------------|
| SKILL.md trop long (> 5 000 mots) | Surcharge le contexte, dilue l'information | Garder < 3 000 mots, déporter dans `references/` |
| Pas de template de sortie | Outputs incohérents et non professionnels | Toujours inclure un format exact |
| Command sans workflow clair | Claude improvise, résultats imprévisibles | Structurer en étapes numérotées |
| Skill sans trigger phrases | Le skill ne se déclenche jamais au bon moment | Inclure 3-5 phrases de déclenchement spécifiques |
| Hardcoder des chemins absolus | Plugin non portable | Utiliser `${CLAUDE_PLUGIN_ROOT}` |

### Rédactionnels

| Anti-pattern | Bonne pratique |
|-------------|----------------|
| Écrire des skills comme de la doc utilisateur | Écrire comme des **instructions pour Claude** |
| Description à la première personne | Utiliser la **troisième personne** dans les frontmatter |
| Vocabulaire vague ("gérer les données") | Vocabulaire **précis et actionnable** ("query the trial balance, calculate variances") |
| Pas de fallback si outil absent | Toujours implémenter le pattern **Standalone / Supercharged** |
| Ignorer les cas d'erreur | Lister les **erreurs courantes** et comment les gérer |

### Architecturaux

| Anti-pattern | Bonne pratique |
|-------------|----------------|
| Un seul mega-skill qui fait tout | Découper en **skills spécialisés** (1 skill = 1 domaine de connaissance) |
| Commands sans lien avec les skills | Les commands **s'appuient** sur les skills pour la connaissance métier |
| Trop de commands (> 10) | Se limiter à **5-7 commands** qui couvrent les workflows critiques |
| Connecteurs obligatoires | Les connecteurs sont toujours **optionnels** — le plugin doit fonctionner sans |
| Pas de CONNECTORS.md pour un plugin distribué | Toujours documenter les catégories de connecteurs |

---

## Annexe : Résumé des fichiers clés

| Fichier | Emplacement | Rôle | Format |
|---------|-------------|------|--------|
| `plugin.json` | `.claude-plugin/` | Identité du plugin | JSON |
| `.mcp.json` | Racine | Configuration MCP | JSON |
| `SKILL.md` | `skills/*/` | Expertise métier | YAML frontmatter + Markdown |
| `*.md` (commands) | `commands/` | Actions explicites | YAML frontmatter + Markdown |
| `README.md` | Racine | Documentation utilisateur | Markdown |
| `CONNECTORS.md` | Racine | Guide des connecteurs | Markdown |
| `references/*.md` | `skills/*/references/` | Documentation approfondie | Markdown |
| `hooks.json` | `hooks/` | Événements automatiques | JSON |
| `*.md` (agents) | `agents/` | Sous-agents autonomes | YAML frontmatter + Markdown |

---

> **Ce guide est dérivé de l'analyse des 11 plugins du repository `knowledge-work-plugins` d'Anthropic (53 skills, 41 commands, 30+ connecteurs). Les patterns décrits sont ceux effectivement observés dans les fichiers sources, pas des recommandations théoriques.**
