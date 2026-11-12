# Instructions pour les agents — Inclusa

Lire ce fichier avant toute tâche future. Ce dépôt concerne actuellement la gouvernance et la documentation du projet Inclusa, candidat au Social Tech Challenge 2026 en Tunisie (défi 2). Trois étudiants en génie logiciel y collaborent. Le produit et son architecture restent à valider.

## Sources de vérité

1. Instruction explicite des responsables du projet et Issue autorisée.
2. ADR au statut **Accepted**, dans `docs/decisions/`.
3. Documentation de projet et critères d'acceptation de l'Issue.

Une vision ou un ADR **Proposed** ne vaut ni exigence ni décision approuvée. Marquer les inconnues `TBD`. Ne jamais inventer d'exigences produit ni élargir silencieusement le périmètre.

## Contraintes et responsabilités

- Préserver les principes institutionnels : IA ciblée et légère, logiciels ouverts, hébergement local ou sur site à étudier, interopérabilité, décision humaine, confidentialité, sécurité et accessibilité.
- Traiter tout composant décrit dans `docs/architecture.md` comme candidat. Ne pas inventer d'API, de schéma, de service ou de choix d'infrastructure.
- Maintenir les documents, formulaires, workflows et contrôles cohérents. Ne pas ajouter de dépendance sans justification documentée.
- Ne jamais créer de tickets d'implémentation en production sans autorisation explicite ; ne jamais implémenter de fonctionnalité produit sans Issue autorisée.

## Flux de travail

- Commencer par une Issue ciblée, normalement non assignée ; le contributeur se l'assigne avant de travailler.
- Créer une branche dédiée selon `type/numero-description` ; ne jamais développer de travail substantiel directement sur `main`.
- Relier la Pull Request à l'Issue ; décrire périmètre, validation, documentation et impacts de sécurité/confidentialité.
- Faire examiner par un humain. Ne jamais fusionner une Pull Request de manière autonome.
- Ne pas committer ni pousser sans l'autorisation requise dans la tâche courante.

## Tests et documentation

- Exécuter les contrôles pertinents et fournir des résultats vérifiables. Ne jamais affaiblir des tests pour faire passer la CI.
- Mettre à jour la documentation liée à tout changement approuvé. Rédiger les documents destinés au projet en français ; la configuration technique peut être en anglais.
- Ne jamais déclarer « Done » sur la seule base d'une compilation ou d'une Pull Request sans revue.

## Sécurité et vie privée

- Ne jamais publier de données de bénéficiaires, pièces administratives réelles, identifiants ou secrets. Utiliser uniquement des exemples synthétiques.
- Réduire les données au minimum nécessaire ; prévoir accès par rôle, traçabilité et validation humaine dans les travaux futurs.
- Ne jamais lire, afficher, copier ou exploiter la valeur de `SECRET_INCLUSA`. Ne pas supposer son objet ou ses droits. Préférer `GITHUB_TOKEN` avec permissions minimales lorsque nécessaire.
- Ne pas configurer de production, déploiement ou accès externe sans autorisation.

## Définition de Done

Une tâche est Done seulement si son objectif et ses critères d'acceptation sont atteints, son périmètre respecté, les tests requis réussis, la documentation mise à jour, les impacts sécurité et vie privée traités, les revues requises achevées, la validation effectuée et les changements fusionnés selon le flux approuvé.
