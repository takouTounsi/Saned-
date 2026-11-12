# Planification et GitHub Project

## État

L'existence et la configuration d'un GitHub Project lié à ce dépôt sont **TBD** : l'accès distant n'a pas été confirmé lors de la préparation locale. Aucun Project n'est créé par cette documentation.

## Configuration recommandée si un Project existe

| Champ | Valeurs |
| --- | --- |
| Status | Backlog, Ready, In Progress, Blocked, In Review, Validation, Done |
| Priority | P0, P1, P2, P3 |
| Type | Feature, Bug, Architecture, Research, Documentation, DevOps, Security, Test |
| Effort | XS, S, M, L, XL |

N'ajouter ces champs que si leur usage est utile à l'équipe. Les Issues restent normalement non assignées jusqu'au choix d'un contributeur. Un workflow GitHub Actions avec `GITHUB_TOKEN` ne dispose pas automatiquement des droits d'écriture sur GitHub Projects V2 ; une automatisation éventuelle exige un jeton ou une GitHub App avec permissions adaptées, une approbation et une étude de sécurité. `SECRET_INCLUSA` n'a pas de rôle connu et ne doit pas être utilisé par supposition.

## Mise en place manuelle

1. Vérifier le dépôt et les droits du compte dans GitHub.
2. Ouvrir l'onglet Projects du dépôt ou du propriétaire et identifier un Project existant lié au dépôt.
3. Examiner les champs existants ; ajouter seulement les valeurs manquantes utiles, avec l'accord des responsables.
4. Prévoir un affichage par Status et tester les transitions selon le [plan de test](workflow-test.md).
5. Si aucun Project n'existe, demander l'autorisation avant d'en créer un.

## Labels des formulaires

Les formulaires utilisent `type:feature`, `type:bug`, `type:research`, `type:task` et `workflow-test`. Après avoir obtenu l'accès au dépôt, vérifier ou créer ces labels dans **Settings → Labels** avant de tester la création d'Issues. Ne pas attribuer automatiquement les Issues à un membre. Les droits d'administration du dépôt peuvent être nécessaires pour gérer les labels.
