---
name: rapprochement-bancaire
description: Rapprocher les comptes bancaires et lettrer les comptes de tiers selon les méthodes françaises. Utiliser pour effectuer un rapprochement bancaire, lettrer les comptes clients ou fournisseurs, analyser le vieillissement des écarts, ou préparer les justificatifs de comptes.
---

# Rapprochement Bancaire et Lettrage

**Important** : Ce skill assiste dans les travaux de rapprochement mais ne constitue pas un conseil comptable. Tous les rapprochements doivent être vérifiés par un professionnel qualifié.

Méthodologie de rapprochement bancaire, techniques de lettrage des comptes de tiers, formats bancaires français, bonnes pratiques et seuils d'escalade.

## Types de rapprochement

### Rapprochement bancaire (Compte 512 vs relevé)

Comparer le solde du compte 512 en comptabilité avec le solde du relevé bancaire à la même date.

**Processus :**
1. Relever le solde comptable du compte 512 à la date de rapprochement
2. Relever le solde du relevé bancaire à la même date
3. Pointer chaque opération présente des deux côtés (montant, date, référence)
4. Identifier les opérations non pointées de chaque côté
5. Vérifier que le solde comptable +/- les non pointés bancaires = solde bancaire +/- les non pointés comptables

**Format standard :**
```
ÉTAT DE RAPPROCHEMENT BANCAIRE
Compte 512 — [Nom de la banque]
Au [Date]

Solde comptable :                           X XXX,XX €
+ Opérations au crédit du relevé,
  non encore en comptabilité                X XXX,XX €
- Opérations au débit du relevé,
  non encore en comptabilité               (X XXX,XX €)
= Solde comptable ajusté :                  X XXX,XX €

Solde du relevé bancaire :                  X XXX,XX €
+ Opérations au débit de la comptabilité,
  non encore sur le relevé                  X XXX,XX €
- Opérations au crédit de la comptabilité,
  non encore sur le relevé                 (X XXX,XX €)
= Solde bancaire ajusté :                   X XXX,XX €

ÉCART :                                     0,00 €
```

### Lettrage des comptes clients (411)

Rapprocher les factures émises avec les règlements reçus.

**Processus :**
1. Lister toutes les écritures au débit (factures) et au crédit (règlements, avoirs) du compte
2. Rapprocher chaque règlement avec la facture correspondante (même montant ou solde partiel)
3. Attribuer une lettre commune aux écritures rapprochées
4. Le solde non lettré représente les créances clients ouvertes

**Techniques de lettrage :**
- **Lettrage simple** : Montant exact facture = règlement
- **Lettrage partiel** : Règlement couvrant partiellement une facture → lettre provisoire
- **Lettrage groupé** : Un règlement couvrant plusieurs factures → même lettre pour toutes
- **Lettrage avec écart** : Petit écart d'arrondi → écriture d'écart de règlement (compte 658/758)

### Lettrage des comptes fournisseurs (401)

Même logique que le lettrage clients mais inversé :
- Débit 401 = règlements envoyés
- Crédit 401 = factures reçues
- Le solde non lettré = dettes fournisseurs ouvertes

### Rapprochement intercompagnie

Pour les groupes : rapprocher les soldes des comptes 451 (comptes de groupe) entre les entités.

**Processus :**
1. Extraire les soldes des comptes 451 dans chaque entité
2. Vérifier que le solde de A envers B = le solde de B envers A (en sens inverse)
3. Identifier les écarts (transactions en transit, écarts de change, erreurs)
4. Préparer les écritures d'ajustement et les éliminations de consolidation

## Causes courantes d'écarts

| Cause | Catégorie | Résolution |
|-------|-----------|------------|
| Chèque émis non débité | Transit | Attendre le débit bancaire |
| Remise de chèques en cours d'encaissement | Transit | Attendre le crédit bancaire |
| Virement reçu non comptabilisé | Comptable | Passer l'écriture de recette |
| Frais bancaires / agios non comptabilisés | Comptable | Passer l'écriture 627 / 6611 |
| Prélèvement automatique non comptabilisé | Comptable | Passer l'écriture |
| Rejet de chèque ou prélèvement | Comptable | Contre-passer l'écriture initiale |
| Erreur de montant | Erreur | Corriger l'écriture |
| Doublon de saisie | Erreur | Supprimer ou contre-passer |
| Écart de change (opérations en devises) | Permanent | Écriture 666/766 |

## Formats bancaires français

### CFONB (Comité Français d'Organisation et de Normalisation Bancaires)

Format historique français pour les échanges bancaires :
- **CFONB 120** : Relevé de compte (120 caractères par ligne)
- **CFONB 160** : Fichier de virements (160 caractères par ligne)
- **CFONB 240** : Fichier de prélèvements

### SEPA (Single Euro Payments Area)

Formats européens modernes (XML) :
- **pain.001** : Virements SEPA (SCT - SEPA Credit Transfer)
- **pain.008** : Prélèvements SEPA (SDD - SEPA Direct Debit)
- **camt.053** : Relevé de compte (remplace CFONB 120)
- **camt.054** : Avis de crédit/débit

### MT940 (SWIFT)

Format international pour les relevés bancaires, utilisé par certaines banques françaises pour les comptes en devises ou les opérations internationales.

## Analyse de vieillissement (aging)

### Pour le rapprochement bancaire

| Tranche d'âge | Interprétation | Action |
|---------------|---------------|--------|
| 0-5 jours | Normal — opération récente | Surveiller |
| 6-15 jours | Acceptable — délai de traitement | Vérifier |
| 16-30 jours | Attention — délai anormal | Investiguer |
| > 30 jours | Alerte — problème probable | Escalader |

### Pour le lettrage clients (balance âgée)

| Tranche | Qualification | Action |
|---------|--------------|--------|
| Non échu | Créance courante | Aucune |
| 0-30 jours | Retard modéré | Relance amiable |
| 31-60 jours | Retard significatif | Relance ferme |
| 61-90 jours | Retard important | Mise en demeure |
| > 90 jours | Retard critique | Provision pour dépréciation à envisager |

## Seuils d'escalade

| Critère | Seuil | Action |
|---------|-------|--------|
| Montant individuel | > seuil de matérialité | Signaler au responsable comptable |
| Total des écarts | > X% du solde du compte | Revoir avec le chef comptable |
| Âge d'un élément | > 30 jours (bancaire), > 90 jours (tiers) | Escalade hiérarchique |
| Écart résiduel inexpliqué | Tout montant | Investigation obligatoire |
| Tendance croissante | Total en hausse sur 3 mois consécutifs | Revue de process |

## Bonnes pratiques

1. **Fréquence** : Rapprochement bancaire mensuel minimum, hebdomadaire recommandé pour les volumes importants
2. **Exhaustivité** : Pointer 100% des opérations, pas d'écart non investigué
3. **Documentation** : Chaque rapprochement daté, signé par le préparateur et le validateur
4. **Séparation des tâches** : Le préparateur ne doit pas être le validateur
5. **Suivi systématique** : Les éléments en suspens doivent être suivis jusqu'à résolution
6. **Analyse des causes** : Identifier les causes récurrentes d'écarts pour améliorer les process
7. **Standardisation** : Utiliser un modèle uniforme pour tous les rapprochements
8. **Conservation** : Archiver les rapprochements selon les obligations légales (10 ans — Code de commerce)
