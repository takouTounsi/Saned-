# Contribuer à Inclusa

## Cycle d'une Issue

`Backlog → Ready → auto-attribution → In Progress → branche dédiée → travail → Pull Request → In Review → Validation → Done`

`Blocked` signale un obstacle explicite ; son motif et la condition de déblocage sont documentés dans l'Issue. Les Issues commencent normalement sans responsable. Chacun choisit son sujet et se l'assigne avant de commencer. Aucune attribution permanente automatique par membre. Une Issue correspond à une branche focalisée et une Pull Request présente un lien clair avec cette Issue. Les statuts du Project ne sont utilisés que si le Project est accessible et configuré.

## Avant de commencer

Vérifier le contexte, les critères d'acceptation, les dépendances et la validation prévue. Les idées produit ne deviennent pas des tickets d'implémentation sans autorisation explicite. Déplacer l'Issue vers `In Progress` si le Project le permet.

## Branches et commits

Créer une branche depuis `main` au format `type/numero-description`, par exemple `docs/125-guide-contribution`, `research/126-regles-officielles`, `architecture/127-options-hebergement`, `test/128-flux-issues`, `devops/129-controle-ci`, `feat/123-description` ou `fix/124-description` lorsque le développement produit sera autorisé. Ne pas travailler directement sur `main` pour un changement substantiel.

Employer Conventional Commits : `docs(repo): initialize documentation`, `chore(github): configure issue templates`, `ci(workflow): add documentation validation`, `test(workflow): verify issue lifecycle`.

## Pull Requests

Utiliser la trame fournie. Indiquer l'Issue liée (`Closes #123` seulement si la fusion doit réellement la clôturer), le périmètre, les exclusions, la preuve de validation, les changements documentaires et l'impact sécurité/vie privée. Demander une revue humaine. Une Pull Request non revue ou non fusionnée n'est pas Done. Aucune fusion automatique.

## Qualité et sécurité

Exécuter `python scripts/validate_repo.py` après les changements de gouvernance ; la CI exécute le même contrôle. Ajouter les vérifications pertinentes pour tout futur code autorisé, sans affaiblir les contrôles existants. N'utiliser que des données synthétiques. Ne jamais committer de secrets ou de documents administratifs réels. Justifier toute nouvelle dépendance et consigner les choix d'architecture dans un ADR proposé avant décision.

## Définition de Done

Objectif atteint ; critères d'acceptation satisfaits ; périmètre respecté ; tests requis réussis ; documentation mise à jour ; confidentialité et sécurité traitées ; revues requises terminées ; validation effectuée ; fusion selon le processus approuvé.
