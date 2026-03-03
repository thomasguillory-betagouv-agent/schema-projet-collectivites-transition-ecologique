# Schéma des projets de transition écologique des collectivités

Ce dépôt contient les schémas de données standardisés pour décrire les projets de transition écologique menés par les collectivités territoriales françaises.

## Vue d'ensemble

Ce schéma permet de structurer les données relatives aux projets de collectivités en lien avec la transition écologique. Il facilite le partage, l'analyse et la valorisation de ces données entre différents services numériques de l'État français et les collectivités territoriales.

Le modèle est organisé en 4 objets hiérarchiques : **Plan → Action → Opération → Financement**.

## Structure du schéma

### Tables principales

| Table | Description | Champs |
|-------|-------------|--------|
| **[Plans](plans/)** | Documents stratégiques définissant des orientations sur un horizon temporel (PCAET, CRTE, PAT, PLUi…) | 8 |
| **[Actions](actions/)** | Intentions politiques décrivant ce qu'une collectivité envisage d'entreprendre (objet optionnel) | 11 |
| **[Opérations](operations/)** | Projets concrets avec moyens engagés, acteurs identifiés, calendrier et livrables | 19 |
| **[Financements](financements/)** | Lignes de financement associées aux opérations (N financements par opération) | 9 |

### Référentiels

Les référentiels sont disponibles dans [`reference-data/`](reference-data/) :

**Contraignants** (foreign key validée) :

| Référentiel | Entrées | Description |
|-------------|---------|-------------|
| [Leviers SGPE](reference-data/referentiel-leviers-sgpe.csv) | 72 | Leviers d'action de la planification écologique |
| [Compétences M57](reference-data/referentiel-competences-m57.csv) | 156 | Nomenclature fonctionnelle M57 des collectivités |
| [Classification — Thématiques](reference-data/referentiel-classification-thematiques.csv) | 138 | Domaines thématiques (énergie, biodiversité, mobilité…) |
| [Classification — Infrastructures](reference-data/referentiel-classification-infrastructures.csv) | 59 | Types d'infrastructure ou de site |
| [Classification — Interventions](reference-data/referentiel-classification-interventions.csv) | 15 | Types d'intervention (rénovation, construction…) |

**Indicatifs** (texte libre, pas de foreign key) :

| Référentiel | Entrées | Description |
|-------------|---------|-------------|
| [Types de plan](reference-data/referentiel-types-plan.csv) | 8 | Types de plans stratégiques connus |
| [Programmes](reference-data/referentiel-programmes.csv) | 31 | Programmes institutionnels de rattachement |
| [Sources de financement](reference-data/referentiel-sources-financement.csv) | 59 | Sources de financement connues |

## Relations

```
Plan ──< Action ──< Opération ──< Financement
  │         │           │
  └─────────┴───────────┘  (N:N via tableaux d'IDs)
```

- **Plan → Action** : une action peut être rattachée à N plans (`planIds`)
- **Action → Opération** : une opération peut concrétiser N actions (`actionIds`)
- **Plan → Opération** : une opération peut être rattachée directement à N plans (`planIds`)
- **Opération → Financement** : un financement est lié à 1 opération (`operationId`)
- Les champs de classification (`competencesM57`, `leviersSgpe`, `classificationThematiques`, `classificationInfrastructures`, `classificationInterventions`) font référence aux référentiels contraignants via foreign keys

## Utilisation

Ces schémas sont conçus pour être utilisés par tout service numérique qui accompagne les collectivités dans leurs projets de transition écologique, comme par exemple :

- [Mon Espace Collectivité (MEC)](https://mon-espace-collectivite.incubateur.anct.gouv.fr/)
- [Territoires en Transitions (TeT)](https://territoiresentransitions.fr/)
- [API Collectivités](https://api.communs-transition-ecologique.beta.gouv.fr/)

## Format

Les schémas sont au format [Table Schema](https://specs.frictionlessdata.io/table-schema/) et [Data Package](https://specs.frictionlessdata.io/data-package/), conforme aux spécifications Frictionless Data, pour faciliter la validation et l'interopérabilité des données.

## Contenu du dépôt

```
├── datapackage.json                  # Descripteur du package de données
├── plans/
│   ├── schema.json                   # Schéma des plans
│   └── exemple-valide.csv            # Exemple de données valides
├── actions/
│   ├── schema.json                   # Schéma des actions
│   └── exemple-valide.csv
├── operations/
│   ├── schema.json                   # Schéma des opérations
│   └── exemple-valide.csv
├── financements/
│   ├── schema.json                   # Schéma des financements
│   └── exemple-valide.csv
└── reference-data/
    ├── referentiel-leviers-sgpe.csv
    ├── referentiel-competences-m57.csv
    ├── referentiel-classification-thematiques.csv
    ├── referentiel-classification-infrastructures.csv
    ├── referentiel-classification-interventions.csv
    ├── referentiel-types-plan.csv
    ├── referentiel-programmes.csv
    └── referentiel-sources-financement.csv
```

## Auteurs

- **Thomas Guillory** — [API Collectivités / beta.gouv.fr](https://beta.gouv.fr/)

## Licence

Les schémas et la documentation sont sous [Licence Ouverte Etalab 2.0](https://www.etalab.gouv.fr/licence-ouverte-open-licence/).
