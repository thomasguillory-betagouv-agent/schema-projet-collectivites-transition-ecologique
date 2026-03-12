# Schéma commun v0.2.0

> Schéma interopérable pour les projets de transition écologique des collectivités territoriales.
> Format [Frictionless Data Package](https://specs.frictionlessdata.io/data-package/) — compatible [schema.data.gouv.fr](https://schema.data.gouv.fr/).

## Vue d'ensemble

| | Tables | Champs | Référentiels |
|--|--------|--------|-------------|
| v0.1.0 | 1 (`projets-territoire`) | 22 | 2 |
| **v0.2.0** | **4** (plans-transition, fiches-action, projets-operationnels, financements) | **51** | **8** |

### Ce qui change

- **4 tables** au lieu d'1 : Plan de transition, Fiche action (optionnelle), Projet opérationnel + Financements (détail par source)
- **Financements détaillés** : table dédiée (source, montants, statut) au lieu d'un seul `budgetPrevisionnel`
- **IDs plateforme supprimés** du schéma (`mecId`, `tetId`, `recocoId` → gérés par l'API)
- **Table `collectivites` supprimée** : remplacée par `collectiviteResponsableSiren` (qui décide) + `territoireCommunes` (où)
- **Données personnelles supprimées** (RGPD) : `contact*` → mise en relation via API
- **3 axes de classification** : thématiques (138), sites (59), interventions (15)
- **Localisation précise** (optionnelle) : latitude, longitude, adresse, identifiant BAN
- **Leviers SGPE mis à jour** : 50 → 72

### Modèle relationnel

```
plans-transition ◇──────◇ fiches-action ◇──────◇ projets-operationnels ◇────── financements
          planTransitionIds[]    ficheActionIds[]              projetOperationnelId
                (N:N)                  (N:N)                          (1:N)

plans-transition ◇──────◇ projets-operationnels
          planTransitionIds[]
               (N:N, raccourci)
```

## Structure des fichiers

```
datapackage.json
├── plans-transition/
│   ├── schema.json          8 champs
│   └── exemple-valide.csv   2 plans de transition (PCAET, CRTE)
├── fiches-action/
│   ├── schema.json          11 champs
│   └── exemple-valide.csv   3 fiches action
├── projets-operationnels/
│   ├── schema.json          23 champs
│   └── exemple-valide.csv   4 projets opérationnels
├── financements/
│   ├── schema.json          9 champs
│   └── exemple-valide.csv   8 lignes de financement
└── reference-data/
    ├── referentiel-leviers-sgpe.csv               72 leviers (contraignant)
    ├── referentiel-competences-m57.csv             156 compétences (contraignant)
    ├── referentiel-classification-thematiques.csv  138 labels (contraignant)
    ├── referentiel-classification-sites.csv             59 labels (contraignant)
    ├── referentiel-classification-interventions.csv    15 labels (contraignant)
    ├── referentiel-types-plan.csv                 8 types (indicatif)
    ├── referentiel-programmes.csv                 31 programmes (indicatif)
    └── referentiel-sources-financement.csv        58 sources (indicatif)
```

**Contraignant** = les valeurs doivent appartenir au référentiel (FK validée).
**Indicatif** = texte libre, le référentiel sert d'aide à la saisie.

## Tables

### `plans-transition` — 8 champs

Document stratégique de transition écologique sur un horizon temporel (PCAET, CRTE, PAT, PLUi...).

| Champ | Type | Requis | Description |
|-------|------|--------|-------------|
| `id` | uuid | oui | Identifiant unique |
| `nom` | string | oui | Nom du plan de transition |
| `type` | string | | Type (texte libre — voir referentiel-types-plan) |
| `description` | string | | Description |
| `periodeDebut` | date | | Début de la période couverte |
| `periodeFin` | date | | Fin de la période couverte |
| `collectiviteResponsableSiren` | string | | SIREN (9 chiffres) |
| `territoireCommunes` | string[] | | Codes INSEE (5 chiffres) |

### `fiches-action` — 11 champs

Intention politique — ce qu'une collectivité envisage d'entreprendre. Objet **optionnel**.

| Champ | Type | Requis | Description |
|-------|------|--------|-------------|
| `id` | uuid | oui | Identifiant unique |
| `nom` | string | oui | Nom de la fiche action |
| `description` | string | | Description |
| `objectifs` | string | | Objectifs visés |
| `statut` | enum | | À venir, En cours, En retard, En pause, Bloqué, Abandonné, Terminé |
| `competencesM57` | string[] | | Codes M57 |
| `leviersSgpe` | string[] | | Leviers SGPE |
| `planTransitionIds` | uuid[] | | FK → plans-transition |
| `collectiviteResponsableSiren` | string | | SIREN (9 chiffres) |
| `territoireCommunes` | string[] | | Codes INSEE (5 chiffres) |
| `classificationThematiques` | string[] | | FK → referentiel thématiques |

### `projets-operationnels` — 23 champs

Projet concret avec moyens engagés, acteurs identifiés, calendrier et livrables.

| Champ | Type | Requis | Description |
|-------|------|--------|-------------|
| `id` | uuid | oui | Identifiant unique |
| `nom` | string | oui | Nom du projet opérationnel |
| `description` | string | | Description |
| `budgetPrevisionnel` | integer | | Budget total en euros |
| `dateDebut` | date | | Date de début |
| `dateFin` | date | | Date de fin |
| `phase` | enum | | Idée, Étude, Réalisation |
| `phaseStatut` | enum | | À venir, En cours, En retard, En pause, Bloqué, Abandonné, Terminé |
| `collectiviteResponsableSiren` | string | | SIREN (9 chiffres) |
| `porteurOperationnelSiret` | string | | SIRET (14 chiffres) — entité qui exécute |
| `competencesM57` | string[] | | Codes M57 |
| `leviersSgpe` | string[] | | Leviers SGPE |
| `programmesRattachement` | string[] | | Programmes (texte libre — voir referentiel) |
| `planTransitionIds` | uuid[] | | FK → plans-transition |
| `ficheActionIds` | uuid[] | | FK → fiches-action |
| `territoireCommunes` | string[] | | Codes INSEE (5 chiffres) |
| `localisationLatitude` | number | | Latitude WGS 84 (degrés décimaux) |
| `localisationLongitude` | number | | Longitude WGS 84 (degrés décimaux) |
| `localisationAdresse` | string | | Adresse ou description du lieu (texte libre) |
| `localisationBanId` | string | | Clé d'interopérabilité BAN |
| `classificationThematiques` | string[] | | FK → referentiel thématiques |
| `classificationSites` | string[] | | FK → referentiel sites |
| `classificationInterventions` | string[] | | FK → referentiel interventions |

### `financements` — 9 champs

Ligne de financement associée à un projet opérationnel. Un projet opérationnel peut avoir N financements.

| Champ | Type | Requis | Description |
|-------|------|--------|-------------|
| `id` | uuid | oui | Identifiant unique |
| `projetOperationnelId` | uuid | oui | FK → projets-operationnels |
| `source` | string | oui | Source (texte libre — voir referentiel) |
| `referenceExterne` | string | | N° dossier, n° EJ, n° convention... |
| `dateAttribution` | date | | Date de notification |
| `montantDemande` | integer | | Montant demandé (euros) |
| `montantAttribue` | integer | | Montant attribué (euros) |
| `montantPaye` | integer | | Montant payé (euros) |
| `statut` | enum | | Prévisionnel, Demandé, Obtenu, Refusé, Payé |

## Correspondances plateformes

| Table schéma | MEC | TeT | Fonds Vert |
|-------------|-----|-----|-----------|
| **Plans de transition** | `crte` (CRTE uniquement) | `axe` racines + `plan_action_type` | — |
| **Fiches action** | — | Toute `fiche_action` | — |
| **Projets opérationnels** | `projets` | `fiche_action` avec budget/financeur | `dossier` |
| **Financements** | `ProjetsFinancements` | `fiche_action_financeur_tag` | Engagements juridiques |

## Documentation complète

- [Proposition schéma v0.2.0](../matrice/proposition-schema-v0.2.0.md) — spécification exhaustive, décisions de design, guide de nommage
- [Matrice de correspondance](../matrice/matrice-correspondance.md) — mapping attribut par attribut entre plateformes
- [Synthèse visuelle](../matrice/synthese-v0.2.0.html) — vue graphique du schéma (ouvrir dans un navigateur)
- [Glossaire commun](../glossaire/glossaire-commun.md) — définitions Plan de transition, Fiche action, Projet opérationnel

## Licence

[Licence Ouverte / Open Licence 2.0](https://www.etalab.gouv.fr/licence-ouverte-open-licence) — Etalab
