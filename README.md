# Inclusa

Inclusa est un projet étudiant pour le **Social Tech Challenge 2026 — Tunisie**, défi 2 : « Digitalisation du dépôt des dossiers sociaux ». Il vise à faciliter la compréhension des démarches sociales, la préparation des dossiers et les échanges avec les travailleurs sociaux. Le projet est porté par trois étudiants en génie logiciel, en dialogue avec les parties prenantes institutionnelles (MAS, PNUD et Banque mondiale).

## Problème et vision

Les démarches administratives peuvent être difficiles à comprendre et les dossiers incomplets retardent leur traitement. La vision exploratoire comprend une assistance en français, arabe et dialecte tunisien, l'accès à des règles officielles, une aide à la préparation des formulaires et documents, et une validation humaine par les travailleurs sociaux. Ces idées **ne sont pas des exigences validées**.

## État du projet

Ce dépôt est au stade de **préparation de la collaboration**. Aucun produit, composant applicatif ou choix d'architecture n'est considéré comme implémenté ou approuvé. Les exigences validées, les décisions acceptées et les fonctionnalités livrées sont actuellement : **aucune**. Les points inconnus sont notés `TBD`.

## Collaboration

L'équipe travaille à partir d'Issues ciblées, choisies et assignées par les contributeurs eux-mêmes. Chaque sujet suit une branche dédiée, une Pull Request liée, une revue humaine et une validation avant clôture. Le travail substantiel ne se fait pas directement sur `main`. Voir [CONTRIBUTING.md](CONTRIBUTING.md) et [AGENTS.md](AGENTS.md).

## Documentation

| Document | Rôle |
| --- | --- |
| [Guide documentaire](docs/README.md) | Carte et statut des documents |
| [État actuel](CURRENT_STATUS.md) | Avancement local et vérifications distantes |
| [Journal des changements](CHANGELOG.md) | Changements préparés et publiés |
| [Vision](docs/product/vision.md) | Hypothèses produit à valider |
| [Questions ouvertes](docs/product/open-questions.md) | Questions produit et institutionnelles |
| [Architecture](docs/architecture.md) | Contraintes et composants candidats |
| [Décisions](docs/decisions/README.md) | Processus ADR et décisions acceptées |
| [Planification](docs/planning/README.md) | Configuration GitHub Project et tests du flux |

Une proposition d'architecture devient une décision seulement après examen humain et ADR au statut **Accepted**. Une idée de fonctionnalité devient une exigence seulement après validation explicite par les responsables du projet. La documentation distingue systématiquement ces statuts.

## Flux GitHub

Les [formulaires d'Issue](.github/ISSUE_TEMPLATE/) cadrent la demande. La [trame de Pull Request](.github/PULL_REQUEST_TEMPLATE.md) demande le lien vers l'Issue, le périmètre et les preuves de validation. La CI vérifie les fichiers de gouvernance, la documentation et la configuration YAML ; elle ne construit aucune application. Le [plan de test du flux](docs/planning/workflow-test.md) sépare les contrôles locaux des opérations GitHub restant à effectuer.

## Licence

`TBD` — aucune licence logicielle n'a été choisie ou accordée à ce stade.
