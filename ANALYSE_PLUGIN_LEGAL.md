# Analyse du Plugin Legal

## Vue d'ensemble

Le plugin **legal** est un assistant juridique pour les équipes juridiques internes (in-house). Il accélère la revue de contrats, le triage des NDA, la conformité réglementaire, la préparation de briefings et la gestion des réponses types.

- **Version :** 1.0.0
- **Auteur :** Anthropic
- **Licence :** Apache 2.0
- **Architecture :** 100% markdown déclaratif (aucun code exécutable)
- **Avertissement :** Ce plugin assiste les workflows juridiques mais ne constitue pas un avis juridique. Toute analyse doit être validée par un professionnel du droit qualifié.

---

## Structure des fichiers

```
legal/
├── .claude-plugin/
│   └── plugin.json              # Manifeste du plugin
├── .mcp.json                    # Configuration des connecteurs MCP
├── README.md                    # Documentation utilisateur
├── CONNECTORS.md                # Guide des connecteurs externes
├── LICENSE                      # Licence Apache 2.0
├── commands/                    # 5 commandes slash utilisateur
│   ├── review-contract.md
│   ├── triage-nda.md
│   ├── vendor-check.md
│   ├── brief.md
│   └── respond.md
└── skills/                      # 6 bases de connaissances
    ├── contract-review/SKILL.md
    ├── nda-triage/SKILL.md
    ├── compliance/SKILL.md
    ├── canned-responses/SKILL.md
    ├── legal-risk-assessment/SKILL.md
    └── meeting-briefing/SKILL.md
```

---

## Configuration et connecteurs

### plugin.json

Métadonnées du plugin permettant à Claude de le reconnaître et de le charger.

### .mcp.json — Connecteurs externes

| Catégorie | Service | URL MCP |
|-----------|---------|---------|
| Communication | Slack | `https://mcp.slack.com/mcp` |
| Stockage Cloud | Box | `https://mcp.box.com` |
| Stockage Cloud | Egnyte | `https://mcp-server.egnyte.com/mcp` |
| Gestion de projet | Atlassian (Jira/Confluence) | `https://mcp.atlassian.com/v1/mcp` |
| Office | Microsoft 365 | `https://microsoft365.mcp.claude.com/mcp` |

Références génériques utilisées dans le plugin : `~~chat`, `~~cloud storage`, `~~office suite`, `~~project tracker`, `~~clm`, `~~crm`.

### Configuration locale

Les utilisateurs créent un fichier `legal.local.md` dans le répertoire `.claude/` du projet pour définir :
- Positions contractuelles de l'organisation (standard, acceptable, escalade)
- Paramètres NDA par défaut
- Templates de réponses personnalisés

---

## Les 5 commandes

### 1. `/review-contract`

**But :** Revue de contrat clause par clause avec classification des écarts et suggestions de redline.

**Entrée :** Fichier contrat (PDF, DOCX), URL ou texte collé.

**Flux de traitement :**

```
1. Acceptation du contrat (fichier, URL ou texte)

2. Collecte du contexte
   ├── De quel côté est l'organisation ? (vendeur, client, licencié...)
   ├── Échéance de la négociation ?
   ├── Domaines de focus ?
   └── Contexte du deal ?

3. Chargement du playbook
   └── Depuis legal.local.md (ou standards génériques par défaut)

4. Analyse clause par clause (12 catégories majeures)
   ├── Limitation de responsabilité
   ├── Indemnisation
   ├── Propriété intellectuelle
   ├── Protection des données
   ├── Confidentialité
   ├── Représentations & Garanties
   ├── Durée & Résiliation
   ├── Droit applicable & Résolution des litiges
   ├── Assurance
   ├── Cession
   ├── Force majeure
   └── Conditions de paiement

5. Classification de chaque clause
   ├── VERT (Acceptable) — Conforme à la position standard
   ├── JAUNE (Négocier) — Hors standard mais dans la plage acceptable
   └── ROUGE (Escalader) — Hors plage acceptable, revue senior requise

6. Génération des suggestions de redline
   Pour chaque clause JAUNE/ROUGE :
   ├── Langage actuel
   ├── Modification proposée
   ├── Justification
   ├── Priorité
   └── Position de repli

7. Analyse d'impact business
   ├── Évaluation globale du risque
   ├── Top 3 des problèmes
   ├── Stratégie de négociation
   └── Considérations de calendrier

8. Routage vers le CLM (si connecté via MCP)

9. Sortie : Contract Review Summary structuré
```

**Cadre de priorisation de la négociation :**
- **Tier 1 — Must-Haves** (Deal Breakers) : Non négociables
- **Tier 2 — Should-Haves** (Préférences fortes) : À défendre fermement
- **Tier 3 — Nice-to-Haves** (Concessions possibles) : Peuvent être cédés en échange

---

### 2. `/triage-nda`

**But :** Tri rapide et classification des NDA entrants par niveau de risque.

**Entrée :** Fichier NDA (PDF, DOCX), URL ou texte collé.

**Flux de traitement :**

```
1. Acceptation du NDA

2. Chargement du playbook NDA (depuis legal.local.md)

3. Screening rapide contre 11 critères
   ├── Structure (Mutuel vs Unilatéral)
   ├── Définition des informations confidentielles (portée)
   ├── Durée et survie de l'obligation
   ├── Exclusions standards
   │   ├── Information publique
   │   ├── Possession antérieure
   │   ├── Développement indépendant
   │   ├── Réception d'un tiers
   │   └── Obligation légale
   ├── Divulgations autorisées
   ├── Obligations de restitution/destruction
   ├── Clause de résiduels
   ├── Non-sollicitation
   ├── Non-concurrence
   ├── Injonction
   ├── Droit applicable
   ├── Cession
   └── Dispositions inhabituelles

4. Classification
   ├── VERT (Approbation standard)
   │   → Termes standards, tous critères satisfaits
   │   → Délai cible : approbation le jour même
   ├── JAUNE (Revue par un juriste)
   │   → Déviations mineures, potentiellement acceptables
   │   → Délai cible : 1-2 jours ouvrés
   └── ROUGE (Problèmes significatifs)
       → Déviations matérielles, négociation requise
       → Délai cible : 3-5 jours ouvrés

5. Génération du rapport de triage
   ├── Classification globale
   ├── Tableau des résultats de screening
   ├── Problèmes identifiés avec évaluation du risque et corrections suggérées
   ├── Recommandation
   └── Prochaines étapes

6. Routage selon la classification
```

**Problèmes NDA courants traités :**
- Définition trop large des informations confidentielles
- Absence d'exclusion pour développement indépendant
- Clause de non-sollicitation des employés
- Clause de résiduels trop large
- Obligation de confidentialité perpétuelle

---

### 3. `/vendor-check`

**But :** Vérifier le statut de tous les accords existants avec un fournisseur.

**Entrée :** Nom du fournisseur.

**Flux de traitement :**

```
1. Identification du fournisseur
   (gestion des variantes de nom, abréviations, filiales)

2. Recherche dans les systèmes connectés (par ordre de priorité)
   ├── CLM (Contract Lifecycle Management)
   ├── CRM
   ├── Email (6 derniers mois)
   ├── Stockage documentaire (Box, Egnyte, SharePoint)
   └── Chat (Slack, Teams)

3. Compilation du statut de chaque accord trouvé
   ├── Type d'accord (NDA, MSA, SOW, DPA, SLA, Licence...)
   ├── Statut (Actif, Expiré, En négociation, En attente de signature)
   ├── Date d'effet
   ├── Date d'expiration
   ├── Termes de renouvellement automatique
   ├── Termes clés
   └── Avenants

4. Analyse des lacunes (gap analysis)
   Quels accords existent vs lesquels manquent potentiellement

5. Rapport consolidé
   ├── Détails de la recherche
   ├── Vue d'ensemble de la relation
   ├── Résumé des accords
   ├── Analyse des gaps
   ├── Actions à venir
   └── Notes
```

---

### 4. `/brief`

**But :** Générer des briefings contextuels pour le travail juridique.

**3 modes de fonctionnement :**

#### a) `/brief daily` — Briefing quotidien

```
Scan des sources connectées pour les éléments juridiques pertinents :
├── Email (messages récents)
├── Calendrier (rendez-vous du jour)
├── Chat (conversations récentes)
├── CLM (contrats en mouvement)
└── CRM (opportunités en cours)

Sections de sortie :
├── Éléments urgents
├── Pipeline de contrats
├── Nouvelles demandes
├── Calendrier du jour
├── Activité de l'équipe
├── Échéances de la semaine
└── Sources indisponibles
```

#### b) `/brief topic <sujet>` — Briefing thématique

```
Recherche et synthèse sur une question juridique spécifique :

Sources : Documents, Email, Chat, CLM

Sections de sortie :
├── Résumé
├── Contexte
├── État actuel
├── Considérations clés
├── Précédent interne
├── Lacunes
└── Prochaines étapes recommandées
```

#### c) `/brief incident <sujet>` — Briefing d'incident

```
Briefing rapide pour situations en développement :
(violation de données, menace de litige, enquête réglementaire, litige PI)

Scan de toutes les sources connectées

Sections de sortie :
├── Résumé de la situation
├── Chronologie
├── Considérations juridiques immédiates
├── Accords pertinents
├── Réponse interne
├── Contacts clés
├── Actions immédiates recommandées
├── Lacunes d'information
└── Sources vérifiées
```

---

### 5. `/respond`

**But :** Générer des réponses types pour les demandes juridiques courantes.

**Types de demandes supportés :**

| Code | Type | Description |
|------|------|-------------|
| `dsr` | Data Subject Request | Demandes d'accès/suppression/correction de données |
| `hold` | Discovery Hold | Notifications de conservation de preuves |
| `vendor` | Vendor Question | Questions juridiques de fournisseurs |
| `nda` | NDA Request | Demandes de NDA |
| `privacy` | Privacy Inquiry | Questions liées à la vie privée |
| `subpoena` | Subpoena | Réponses aux assignations |
| `insurance` | Insurance | Notifications de sinistres |
| `custom` | Custom | Template personnalisé |

**Flux de traitement :**

```
1. Identification du type de demande (depuis l'input utilisateur)

2. Chargement du template (depuis legal.local.md ou défaut)

3. Vérification des déclencheurs d'escalade AVANT la génération
   Déclencheurs universels :
   ├── Litige en cours
   ├── Enquête réglementaire
   ├── Engagement contraignant
   ├── Responsabilité pénale
   ├── Exposition médiatique
   ├── Situation sans précédent
   ├── Multi-juridictionnel
   └── Implication de la direction

4. Collecte des détails spécifiques pour personnaliser la réponse

5. Génération de la réponse
   ├── Destinataire
   ├── Objet
   ├── Corps du message (avec variables du template remplies)
   ├── Résultats de la vérification d'escalade
   └── Actions de suivi

6. Présentation du draft pour validation AVANT envoi
```

---

## Les 6 skills (bases de connaissances)

### 1. `contract-review`

Méthodologie détaillée pour la revue de contrats :

**Méthodologie basée sur un playbook :**
- Charge le playbook de l'organisation (positions standard, plages acceptables, déclencheurs d'escalade)
- Identifie le type de contrat (SaaS, services professionnels, licence, partenariat, achat...)
- Détermine le côté de l'organisation
- Lit le contrat entier avant de signaler les problèmes
- Analyse chaque clause matérielle contre le playbook
- Vision holistique du contrat

**Analyse détaillée de 6 catégories majeures :**

| Clause | Points d'analyse |
|--------|-----------------|
| Limitation de responsabilité | Plafond, exclusions, mutuelle vs unilatérale, dommages indirects |
| Indemnisation | Mutuelle vs unilatérale, portée, plafond, procédures |
| Propriété intellectuelle | PI préexistante vs développée, work-for-hire, licences, clauses feedback |
| Protection des données | DPA requis, responsable vs sous-traitant, sous-traitants ultérieurs, notification de violation, transferts transfrontaliers |
| Durée & Résiliation | Durée, renouvellement, résiliation pour convenance, période de transition |
| Droit applicable & Litiges | Juridiction, arbitrage vs contentieux, renonciation au jury, class action waiver |

**Bonnes pratiques de rédaction des redlines :**
- Langage spécifique et exact
- Équilibré et commercialement raisonnable
- Justification explicable pour partage externe
- Positions de repli pour les JAUNES
- Priorisation : must-have vs should-have vs nice-to-have

---

### 2. `nda-triage`

Critères de screening détaillés pour les NDA :

**10 catégories de screening :**

1. **Structure de l'accord** — Mutuel vs Unilatéral, accord autonome
2. **Définition des informations confidentielles** — Portée raisonnable, pas de définition trop large
3. **Obligations de la partie réceptrice** — Standard de soin, restriction d'usage, restriction de divulgation
4. **Exclusions standards** — 5 exclusions vérifiées (publique, possession antérieure, développement indépendant, tiers, obligation légale)
5. **Divulgations autorisées** — Employés, conseillers, affiliés, réglementaire
6. **Durée et survie** — Durée raisonnable, période de survie, pas de perpétuel
7. **Restitution et destruction** — Portée raisonnable, exception de rétention légale
8. **Remèdes** — Reconnaissance de l'injonction, pas de dommages prédéterminés, remèdes mutuels
9. **Dispositions problématiques** — Non-sollicitation, non-concurrence, exclusivité, standstill, résiduels, cession de PI, droits d'audit
10. **Droit applicable et juridiction** — Juridiction raisonnable, cohérence loi/juridiction

**Règles de classification :**
- **VERT** — Tous critères satisfaits, market-standard, pas d'escalade
- **JAUNE** — Une ou plusieurs déviations mineures, potentiellement acceptable
- **ROUGE** — Déviations matérielles ou déclencheurs d'escalade présents

**Positions standard sur les problèmes courants :**
- Définition trop large → Demander un marquage ou une liste
- Développement indépendant manquant → Insérer l'exclusion
- Non-sollicitation → Supprimer ou limiter à 12 mois
- Résiduels trop large → Limiter ou supprimer
- Perpétuel → Limiter à 3-5 ans

---

### 3. `compliance`

Navigation dans les réglementations de protection des données :

**Couverture réglementaire (9 réglementations) :**

| Réglementation | Juridiction | Points clés |
|---------------|-------------|-------------|
| GDPR | UE/EEE | Article 30, notification 72h, DPA obligatoire |
| CCPA/CPRA | Californie | Droits know/delete/opt-out/correct, réponse 45 jours |
| LGPD | Brésil | Similaire GDPR, DPO obligatoire |
| POPIA | Afrique du Sud | Enregistrement obligatoire |
| PIPEDA | Canada | Basé sur le consentement |
| PDPA | Singapour | Notification de violation obligatoire |
| Privacy Act | Australie | Cadre APPs |
| PIPL | Chine | Règles strictes transferts transfrontaliers |
| UK GDPR | Royaume-Uni | Version post-Brexit |

**Checklist de revue des DPA :**
- Éléments requis (Article 28 GDPR)
- Obligations du sous-traitant
- Exigences pour les sous-traitants ultérieurs
- Transferts internationaux
- Considérations pratiques
- Problèmes courants et évaluation des risques

**Gestion des demandes de personnes concernées :**

| Réglementation | Délai de réponse |
|---------------|-----------------|
| GDPR | 30 jours |
| CCPA | 45 jours |
| LGPD | 15 jours |

Processus : Réception → Identification → Vérification des exemptions → Traitement → Réponse

---

### 4. `canned-responses`

Gestion des templates de réponses et identification des situations d'escalade :

**7 catégories de réponses :**

| Catégorie | Sous-catégories |
|-----------|----------------|
| Data Subject Requests | Accusé de réception, Vérification d'identité, Exécution, Refus partiel/total, Extension |
| Discovery Holds | Notice initiale, Rappel, Modification, Levée |
| Privacy Inquiries | Cookies/tracking, Politique de confidentialité, Partage de données, Données enfants, Transferts transfrontaliers |
| Vendor Legal Questions | Statut contrat, Avenant, Certification conformité, Audit, Certificat assurance |
| NDA Requests | Envoi du formulaire standard, Acceptation avec markup, Refus, Renouvellement |
| Subpoena/Legal Process | Accusé de réception, Objection, Extension, Exécution |
| Insurance Notifications | Déclaration initiale, Complémentaire, Réserve de droits |

**Structure d'un template :**
- Catégorie, nom, cas d'usage
- Déclencheurs d'escalade spécifiques
- Variables à remplir
- Corps du message
- Actions de suivi
- Date de revue

**Personnalisation :**
- Obligatoire : noms, dates, faits, juridiction, échéances, signatures
- Ajustement de ton selon l'audience et la relation
- Adaptations juridictionnelles

**Déclencheurs d'escalade universels :**
- Litige, enquête réglementaire, engagement contraignant, responsabilité pénale, médias, situation sans précédent, multi-juridictionnel, implication dirigeants

---

### 5. `legal-risk-assessment`

Cadre d'évaluation et de classification des risques juridiques :

**Matrice Sévérité x Probabilité :**

**Sévérité (1-5) :**
| Niveau | Description | Impact |
|--------|-------------|--------|
| 1 | Négligeable | Inconvénient mineur |
| 2 | Faible | < 1% de la valeur concernée |
| 3 | Modéré | 1-5% de la valeur |
| 4 | Élevé | 5-25% de la valeur |
| 5 | Critique | > 25% de la valeur |

**Probabilité (1-5) :**
| Niveau | Description |
|--------|-------------|
| 1 | Très improbable |
| 2 | Improbable |
| 3 | Possible (avec précédent) |
| 4 | Probable |
| 5 | Quasi certain |

**Score de risque = Sévérité x Probabilité :**

| Score | Niveau | Actions requises |
|-------|--------|-----------------|
| 1-4 | VERT (Faible) | Accepter, documenter, surveiller |
| 5-9 | JAUNE (Moyen) | Atténuer, surveiller activement, assigner un responsable |
| 10-15 | ORANGE (Élevé) | Escalader au senior counsel, plan de mitigation, briefer la direction |
| 16-25 | ROUGE (Critique) | Escalade immédiate, conseil externe, équipe de réponse, assurance, gestion de crise |

**Format du mémo d'évaluation des risques :** 10 sections standardisées.

**Quand engager un conseil externe :**
- **Obligatoire** : situations critiques, litige, enquête
- **Fortement recommandé** : risque élevé, complexité technique
- **À considérer** : risque moyen, questions nouvelles

---

### 6. `meeting-briefing`

Préparation structurée de briefings de réunion et suivi des actions :

**Méthodologie en 5 étapes :**

```
1. Identifier la réunion (titre, type, participants, agenda, rôle, temps disponible)
2. Évaluer les besoins de préparation selon le type
3. Collecter le contexte depuis les sources connectées
4. Synthétiser en briefing structuré
5. Identifier les lacunes de préparation
```

**Types de réunions et besoins de préparation :**

| Type | Éléments de préparation |
|------|------------------------|
| Revue de deal | Statut contrat, points ouverts, historique contrepartie, stratégie |
| Board/Comité | Mises à jour juridiques, risques, réglementaire, approbations |
| Appel fournisseur | Statut accord, problèmes, performance, objectifs de négociation |
| Sync équipe | Charge de travail, priorités, ressources, échéances |
| Client | Termes d'accord, historique support, problèmes, contexte |
| Réglementaire | Contexte de l'affaire, conformité, communications antérieures |
| Litige | Statut du dossier, développements récents, stratégie, paramètres de règlement |
| Cross-fonctionnel | Implications juridiques, évaluation des risques, exigences de conformité |

**Template de briefing (13 sections) :**
- Détails de la réunion, Participants, Agenda, Contexte, Documents clés, Points ouverts, Considérations juridiques, Talking points, Questions à soulever, Décisions nécessaires, Lignes rouges, Suivi de la réunion précédente, Lacunes de préparation

**Suivi des actions :**
- Template avec tableau d'actions (propriétaire, échéance, dépendances)
- Types : équipe juridique, équipe business, externe, réunions de suivi
- Cadence : Haute priorité (quotidien), Moyenne (hebdomadaire), Basse (mensuel)
- Processus : Distribution → Rappels → Mise à jour systèmes → Archivage → Signalement urgent

---

## Flux complet : exemple d'une revue de contrat

```
Utilisateur → /review-contract

1. [Command: review-contract.md]
   Accepte le contrat et collecte le contexte

2. [Config: legal.local.md]
   Charge le playbook de l'organisation

3. [Skill: contract-review]
   ├── Identifie le type de contrat
   ├── Détermine le côté de l'organisation
   ├── Analyse les 12 catégories de clauses
   ├── Classe chaque clause : VERT / JAUNE / ROUGE
   └── Génère les redlines avec positions de repli

4. [Skill: legal-risk-assessment]
   Évalue le risque global du contrat

5. [Output]
   ├── Contract Review Summary
   ├── Analyse clause par clause
   ├── Suggestions de redline priorisées
   ├── Stratégie de négociation
   └── Prochaines étapes

6. [MCP: CLM] (optionnel)
   Route vers le système de gestion des contrats
```

---

## Principes de conception

1. **Agnosticité des outils** — Références par catégorie (`~~chat`, `~~cloud storage`) et non par produit
2. **Dégradation gracieuse** — Fonctionne sans connecteur, signale les sources indisponibles
3. **Basé sur un playbook** — Positions contractuelles personnalisables par organisation
4. **Classification tricolore** — VERT/JAUNE/ROUGE systématique pour la prise de décision rapide
5. **Escalade explicite** — Déclencheurs clairs pour savoir quand impliquer le senior counsel ou le conseil externe
6. **Couverture réglementaire large** — 9 réglementations de protection des données
7. **Prêt pour la production** — Templates, checklists et cadres réutilisables
8. **Privilège préservé** — Briefings et évaluations marquables comme attorney-client privilege / work product
