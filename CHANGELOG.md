# Changelog

## 0.2.0 (2026-03-03)

Refonte majeure du modèle de données. Le schéma passe de 2 tables à 4 objets hiérarchiques.

### Nouveaux objets

- **Plans** : documents stratégiques (PCAET, CRTE, PAT, PLUi…)
- **Actions** : intentions politiques (objet optionnel)
- **Opérations** : projets concrets avec moyens engagés (remplace `projets-territoire`)
- **Financements** : détail par source (montant demandé/attribué/payé, statut)

### Suppressions

- Table `projets-territoire` → remplacée par `operations`
- Table `collectivites` → SIREN porté directement sur chaque objet (`collectiviteResponsableSiren`)
- Champs d'IDs plateforme (`mecId`, `tetId`, `recocoId`) → gérés par l'API, hors schéma
- Champs de contact (`contact`, `email`) → supprimés (RGPD)

### Référentiels

- 8 référentiels (vs 2 en v0.1.0)
- Classification 3 axes : thématiques (138 labels), infrastructures (59), interventions (15)
- Référentiels indicatifs ajoutés : types de plan (8), programmes (31), sources de financement (59)
- Leviers SGPE étendus de 50 à 72
- Compétences M57 conservées (156 entrées, fichier JSON retiré)

### Relations

- Relations N:N via tableaux d'UUIDs (`planIds`, `actionIds`)
- Financement 1:N lié à l'opération (`operationId`)
- Foreign keys Frictionless pour les référentiels contraignants

## 0.1.0 (2025-04-28)

- Version initiale du schéma pour les projets et les collectivités
- Structure organisée avec des dossiers dédiés pour chaque entité (projets et collectivités)
- Fichiers CSV d'exemple pour les deux entités
- Intégration des référentiels comme ressources dans le data package
  - Référentiel des compétences des collectivités M57 (CSV et JSON)
  - Référentiel des leviers SGPE de la transition écologique (CSV)
- Schémas configurés pour utiliser les référentiels avec foreign keys appropriées
- Documentation complète de la structure et de l'utilisation
