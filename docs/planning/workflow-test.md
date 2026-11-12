# Plan de test du flux GitHub

Ce document distingue les contrôles locaux des opérations réellement exécutées sur GitHub. Ne pas marquer un test distant réussi sur la seule base d'un fichier YAML local.

## Contrôle local

Exécuter `python scripts/validate_repo.py`. Vérifier que les fichiers obligatoires, liens locaux, formulaires d'Issue et workflows YAML sont cohérents. La CI doit exécuter la même commande après publication et être observée sur GitHub Actions.

## Tests distants autorisés, si le dépôt est vérifié et accessible

Créer au plus trois Issues temporaires avec le préfixe `[TEST]` et le label `workflow-test`, sans données réelles ni ticket produit. Consigner l'URL, les étapes et les résultats. Fermer après validation réussie lorsque cela convient.

| ID | Vérifications | État initial |
| --- | --- | --- |
| TEST-001 | Création d'Issue, label, auto-attribution manuelle, transition de statut si Project disponible | Non exécuté |
| TEST-002 | Convention de branche, lien Issue/branche, trame PR, liaison, revue et validation ; ne pas créer de branche distante ni PR sans nouvelle autorisation | Non exécuté |
| TEST-003 | Transitions Ready → In Progress → In Review → Validation → Done ; mutations Project seulement si autorisées | Non exécuté |

## Journal

| Date | Vérification | Résultat | Preuve ou blocage |
| --- | --- | --- | --- |
| 2026-11-12 | Accès au dépôt `Medmas07/Inclusa` par le connecteur GitHub | Bloqué | Réponse 404 ; identité connectée `Wiso-1`, accès au dépôt non confirmé |
| 2026-11-12 | Premier contrôle local | Échec corrigé | Titre H1 manquant dans la trame de Pull Request |
| 2026-11-12 | Contrôle local final | Réussi | `python scripts/validate_repo.py` : fichiers, liens, YAML et formulaires valides |
| TBD | CI sur GitHub Actions | Non exécuté | Publication du dépôt requise |
| TBD | TEST-001 à TEST-003 | Non exécuté | Accès au dépôt requis |
