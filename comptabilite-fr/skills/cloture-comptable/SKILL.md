---
name: cloture-comptable
description: Gérer le processus de clôture comptable mensuelle et annuelle avec checklist, séquencement des tâches, dépendances et calendrier fiscal français. Utiliser pour planifier la clôture, suivre l'avancement, identifier les blocages, ou séquencer les travaux de fin d'exercice.
---

# Clôture Comptable

**Important** : Ce skill assiste dans la gestion de la clôture comptable mais ne constitue pas un conseil comptable ou fiscal. Tous les travaux de clôture doivent être supervisés par un expert-comptable ou un professionnel qualifié.

Checklist de clôture mensuelle et annuelle, séquencement des tâches et dépendances, calendrier fiscal français, et suivi d'avancement.

## Clôture mensuelle — Checklist

### Pré-clôture (J-2 à J-3 avant la fin du mois)

- [ ] Envoyer le calendrier de clôture et les rappels aux contributeurs
- [ ] Confirmer les procédures de cut-off avec la comptabilité fournisseurs et clients
- [ ] Vérifier le bon fonctionnement des interfaces (paie, banque, ERP)
- [ ] Effectuer le rapprochement bancaire préliminaire (hors derniers jours)
- [ ] Recenser les factures non parvenues prévisibles
- [ ] Confirmer le calendrier de traitement de la paie
- [ ] Collecter les informations sur les opérations exceptionnelles connues

### Jour T+1 (1er jour ouvré après la fin du mois)

- [ ] Confirmer le traitement de fin de période dans tous les sous-systèmes (paie, immobilisations, stocks)
- [ ] Comptabiliser les factures fournisseurs en attente
- [ ] Passer les FNP (factures non parvenues) — Comptes 4081
- [ ] Comptabiliser les écritures de paie du mois
- [ ] Enregistrer les derniers encaissements et décaissements
- [ ] Comptabiliser les opérations intercompagnies
- [ ] Compléter le rapprochement bancaire avec le relevé définitif
- [ ] Passer les dotations aux amortissements (si mensualisées)
- [ ] Passer l'amortissement des charges constatées d'avance

### Jour T+2

- [ ] Comptabiliser la reconnaissance de chiffre d'affaires du mois
- [ ] Passer les provisions et écritures d'ajustement restantes
- [ ] Effectuer le lettrage des comptes clients (411)
- [ ] Effectuer le lettrage des comptes fournisseurs (401)
- [ ] Passer les ajustements de stocks (si applicable)
- [ ] Comptabiliser les écarts de change sur opérations en devises
- [ ] Commencer les rapprochements des comptes de bilan

### Jour T+3

- [ ] Compléter tous les rapprochements de comptes de bilan
- [ ] Passer les écritures d'ajustement issues des rapprochements
- [ ] Compléter le rapprochement intercompagnie et les éliminations
- [ ] Éditer la balance provisoire et le compte de résultat
- [ ] Effectuer une première analyse des écarts (flux préliminaire)
- [ ] Investiguer et résoudre les variances significatives

### Jour T+4

- [ ] Préparer et comptabiliser la liquidation de TVA
- [ ] Passer la provision d'IS du mois (si applicable)
- [ ] Finaliser toutes les écritures — soft close
- [ ] Générer les états financiers provisoires (bilan, CR, SIG)
- [ ] Effectuer l'analyse détaillée des écarts et préparer les commentaires
- [ ] Revue de direction des états financiers et métriques clés

### Jour T+5

- [ ] Passer les derniers ajustements suite à la revue direction
- [ ] Clôture définitive — hard close
- [ ] Verrouiller la période dans le logiciel comptable
- [ ] Préparer et distribuer le reporting mensuel
- [ ] Mettre à jour les prévisions sur la base des résultats réels
- [ ] Rétrospective de clôture — identifier les améliorations

## Clôture annuelle — Travaux spécifiques

En plus des travaux de clôture mensuelle, la clôture annuelle requiert :

### Travaux d'inventaire

- [ ] Inventaire physique des stocks et en-cours (obligatoire — C. com. L123-12)
- [ ] Rapprochement inventaire physique / inventaire permanent
- [ ] Comptabilisation des écarts d'inventaire (comptes 603x/713)
- [ ] Dépréciation des stocks obsolètes ou à rotation lente (comptes 39x)

### Immobilisations

- [ ] Revue du registre des immobilisations
- [ ] Calcul des dotations annuelles aux amortissements
- [ ] Identification des immobilisations à déprécier (test de dépréciation)
- [ ] Comptabilisation des cessions d'immobilisations (675/775)
- [ ] Vérification de la cohérence bilan / tableau des immobilisations (feuillet 2054)

### Provisions et dépréciations

- [ ] Revue des litiges en cours → provision pour risques (151)
- [ ] Revue des garanties données → provision pour charges (158)
- [ ] Dépréciation des créances douteuses (416/491)
- [ ] Provision pour congés payés acquis non pris (4282/4382)
- [ ] Provision pour indemnités de départ à la retraite (si méthode préférentielle)
- [ ] Revue et ajustement des provisions antérieures

### Charges et produits constatés d'avance

- [ ] CCA : Identifier les charges payées sur N qui concernent N+1 (486)
- [ ] PCA : Identifier les produits encaissés sur N qui concernent N+1 (487)
- [ ] Vérifier l'extourne des CCA et PCA de l'exercice précédent

### Travaux fiscaux de fin d'exercice

- [ ] Calcul de la provision d'IS (695/444)
- [ ] Rapprochement du résultat comptable et du résultat fiscal (2058-A)
- [ ] Calcul de la participation des salariés (691/4284) si > 50 salariés
- [ ] Provision CET (CFE + CVAE) si applicable
- [ ] Calcul et provision de la C3S si applicable (CA > 19 M€)

### Travaux de consolidation (si groupe)

- [ ] Rapprochement des soldes intercompagnies
- [ ] Éliminations des transactions et marges internes
- [ ] Retraitements d'homogénéité
- [ ] Écritures de consolidation (mise en équivalence, intégration globale/proportionnelle)

## Séquencement des tâches et dépendances

### Carte des dépendances

```
NIVEAU 1 (Pas de dépendance — démarrage immédiat à T+1) :
├── Comptabilisation achats / FNP
├── Écriture de paie
├── Amortissements
├── CCA / PCA
├── Opérations intercompagnies
└── Enregistrement des règlements

NIVEAU 2 (Dépend du Niveau 1) :
├── Rapprochement bancaire (besoin : tous les règlements enregistrés)
├── Lettrage clients (besoin : tous les encaissements comptabilisés)
├── Lettrage fournisseurs (besoin : toutes les factures et règlements)
├── Reconnaissance du CA (besoin : données de facturation finalisées)
└── Écarts de change (besoin : tous les mouvements en devises)

NIVEAU 3 (Dépend du Niveau 2) :
├── Rapprochements de comptes de bilan (besoin : toutes les écritures passées)
├── Rapprochement intercompagnie (besoin : les deux côtés clôturés)
├── Balance provisoire
└── Analyse des écarts préliminaire

NIVEAU 4 (Dépend du Niveau 3) :
├── Liquidation TVA (besoin : CA et TVA finalisés)
├── Provision IS (besoin : résultat avant IS déterminé)
├── États financiers provisoires
└── Analyse des écarts détaillée

NIVEAU 5 (Dépend du Niveau 4) :
├── Revue de direction
├── Ajustements finaux
├── Hard close / verrouillage période
├── Reporting package
└── Mise à jour des prévisions
```

### Chemin critique

```
Règlements/Factures → Rapprochement bancaire → Rapprochements bilan →
  Liquidation TVA / Provision IS → États financiers → Revue direction → Hard close
```

### Stratégies pour raccourcir la clôture

- Automatiser les écritures récurrentes (amortissements, CCA, provisions forfaitaires)
- Pratiquer le rapprochement bancaire en continu (hebdomadaire)
- Lettrer les comptes tiers au fil de l'eau (pas en fin de mois)
- Paralléliser les rapprochements indépendants
- Poser des deadlines fermes aux contributeurs
- Standardiser les templates de rapprochement

## Calendrier fiscal français

### Échéances principales

| Obligation | Fréquence | Date limite |
|------------|-----------|-------------|
| **TVA CA3** | Mensuelle | 15 au 24 du mois suivant (selon département du siège) |
| **TVA CA12** | Annuelle | 2ème jour ouvré suivant le 1er mai (clôture 31/12) |
| **Acompte IS** | Trimestrielle | 15/03, 15/06, 15/09, 15/12 |
| **Solde IS** | Annuelle | 15/05 (clôture 31/12) |
| **Liasse fiscale** | Annuelle | 2ème jour ouvré suivant le 1er mai (clôture 31/12) |
| **DAS2** | Annuelle | 31 janvier N+1 (honoraires versés > 1 200 €/an) |
| **CFE** | Annuelle | 15 décembre |
| **CVAE** | Annuelle | Acomptes 15/06 et 15/09, solde 2ème jour ouvré suivant le 1er mai |
| **C3S** | Annuelle | 15 mai |
| **DSN** | Mensuelle | 5 ou 15 du mois suivant (selon effectif) |
| **AG approbation comptes** | Annuelle | Dans les 6 mois suivant la clôture |

**Note :** Pour les exercices ne clôturant pas au 31/12, les dates sont ajustées (3 mois après la clôture pour la liasse et l'IS).

## Suivi d'avancement

### Dashboard de clôture

| Tâche | Responsable | Deadline | Statut | Bloqueur | Notes |
|-------|-------------|----------|--------|----------|-------|
| [Tâche] | [Personne] | [Jour T+N] | Non démarré / En cours / Terminé / Bloqué | [Si bloqué] | [Notes] |

### Définitions des statuts

- **Non démarré** : La tâche n'a pas encore commencé (peut être en attente de dépendances)
- **En cours** : Travail en cours activement
- **Terminé** : Tâche finalisée et validée
- **Bloqué** : La tâche ne peut pas avancer (dépendance, donnée manquante, problème)
- **En risque** : La tâche est en cours mais risque de ne pas respecter le deadline

### Métriques de clôture

| Métrique | Définition | Cible |
|----------|-----------|-------|
| Durée de clôture | Jours ouvrés de la fin de période au hard close | Réduction progressive |
| Écritures post-soft close | Nombre d'écritures passées après la revue direction | Minimiser |
| Tâches en retard | Tâches terminées après leur deadline | Zéro |
| Exceptions de rapprochement | Éléments de rapprochement nécessitant investigation | Réduction progressive |
| Corrections post-clôture | Erreurs découvertes après le verrouillage de la période | Zéro |

## Amélioration continue

### Questions de rétrospective

Après chaque clôture, se poser :
1. Qu'est-ce qui a bien fonctionné et doit être maintenu ?
2. Qu'est-ce qui a pris plus de temps que prévu et pourquoi ?
3. Quels blocages avons-nous rencontrés et comment les prévenir ?
4. Y a-t-il eu des surprises dans les résultats qu'on aurait pu détecter plus tôt ?
5. Que pouvons-nous automatiser ou simplifier pour le mois prochain ?
