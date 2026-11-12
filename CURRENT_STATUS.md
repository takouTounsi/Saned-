# État actuel d'Inclusa

**Mis à jour le 12 novembre 2026.** Ce document décrit l'état constaté de la copie de travail et des vérifications GitHub ; il ne remplace pas les décisions ni les Issues.

## Projet

Inclusa est en phase de préparation de la collaboration. La [vision produit](docs/product/vision.md) et les [composants candidats](docs/architecture.md) restent à valider. Exigences produit validées : **aucune**. Décisions d'architecture acceptées : **aucune**. Fonctionnalités implémentées : **aucune**.

## Dépôt local

- Le dépôt était vide au début du bootstrap : aucun commit ni fichier de projet préexistant.
- Les documents de gouvernance, formulaires GitHub, workflow CI et contrôles locaux sont préparés dans la copie de travail.
- Le contrôle `python scripts/validate_repo.py` réussit localement.
- Le bootstrap est enregistré dans un commit local initial sur `main` ; aucun push n'a été effectué.

## GitHub distant

- Remote configuré : `https://github.com/Medmas07/Inclusa`.
- Le connecteur GitHub lié à `Wiso-1` a reçu une réponse 404 pour ce dépôt ; son existence et les permissions de ce compte sont **TBD**.
- Issues, labels, Project, protection de branche et GitHub Actions : **non vérifiés à distance**.
- Aucun ticket temporaire `[TEST]`, branche distante ni Pull Request n'a été créé. Le détail des tests est dans le [journal du flux](docs/planning/workflow-test.md).

## Prochaines étapes

1. Confirmer le dépôt cible et donner accès au compte connecté si nécessaire.
2. Vérifier les labels et l'éventuel GitHub Project ; suivre la [configuration proposée](docs/planning/README.md).
3. Avec autorisation, pousser le commit local, puis observer l'exécution réelle de la CI.
4. Effectuer les tests du flux autorisés lorsque le dépôt est accessible ; demander une nouvelle autorisation avant toute branche distante ou Pull Request de test.

Aucun secret ni document de bénéficiaire ne doit être ajouté au dépôt.
