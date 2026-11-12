# Architecture — état exploratoire

## Contexte et acteurs

Inclusa vise à aider les bénéficiaires à préparer des dossiers sociaux et les travailleurs sociaux à les examiner. Les interactions avec le MAS, le PNUD et la Banque mondiale sont à préciser (`TBD`). Aucune frontière système n'a encore été acceptée.

## Vue d'ensemble envisagée

Un parcours pourrait relier une personne bénéficiaire, une assistance à la préparation, puis une validation humaine par un travailleur social. Les échanges avec des référentiels officiels ou systèmes MAS supposent des accords, sources et interfaces encore `TBD`. Il ne s'agit pas d'un dessin de déploiement.

## Composants candidats

Assistant conversationnel ; moteur de connaissance et de règles ; intelligence documentaire ; gestion des dossiers ; authentification ; stockage des documents ; interface des travailleurs sociaux. Ces noms sont des **candidats fonctionnels**, pas des services, dépôts, API ou équipes déjà décidés. OCR, RAG et aide à l'éligibilité restent des options à évaluer.

## Contraintes non fonctionnelles

- **IA ciblée :** évaluer des solutions légères, explicables et adaptées à la tâche.
- **Ouverture et hébergement :** privilégier les solutions ouvertes et examiner un hébergement local ou sur site ; choix précis `TBD`.
- **Interopérabilité :** identifier les systèmes existants et les règles d'échange avant toute intégration.
- **Accessibilité :** tester les parcours avec les publics concernés ; niveau et normes applicables `TBD`.
- **Sécurité et confidentialité :** minimisation des données, contrôle d'accès par rôle, traçabilité et séparation des environnements à définir.
- **Décision humaine :** les résultats automatisés ne remplacent pas l'instruction et la validation institutionnelles.

## Vie privée et documents

Les dossiers peuvent contenir des données personnelles et pièces sensibles. Les documents réels et données de bénéficiaires ne doivent pas entrer dans le dépôt ou les tests. Les règles de conservation, chiffrement, journalisation, suppression et accès doivent être établies avec les responsables compétents avant toute implémentation (`TBD`).

## Déploiement et intégration

Le mode d'hébergement, les environnements, l'identité, les systèmes MAS et les contrats d'échange sont `TBD`. Aucun service cloud, schéma de données ou contrat API n'est retenu.

## Questions d'architecture

Voir [questions ouvertes](product/open-questions.md). Tout choix structurant doit être proposé dans un [ADR](decisions/README.md), examiné puis marqué **Accepted** par les responsables avant d'être traité comme décision.
