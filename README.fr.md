# Inclusa

[English](README.md) · **Français**

> **Code source disponible sur demande.** Dans ce dépôt, les fichiers source (`.py`, `.ts`, `.tsx`, `.js`, `.html`, `.css`) gardent leur nom et tout leur historique, mais leur contenu est remplacé par une ligne d'avertissement. La documentation, la configuration et les ressources graphiques sont complètes.

Inclusa est un projet professionnel pour le **Social Tech Challenge 2026 — Tunisie**, défi 2 : « Digitaliser le dépôt des demandes d'aide sociale ». Il vise à rendre les exigences administratives plus faciles à comprendre, à aider les personnes à préparer un dossier complet et à faciliter les échanges avec les travailleurs sociaux. Le projet est mené dans le cadre d'échanges avec le ministère des Affaires sociales (MAS), le PNUD et la Banque mondiale.

## Problème et vision

Les démarches d'aide sociale peuvent être difficiles à suivre, et un dossier incomplet retarde son traitement. La vision exploratoire comprend une assistance en français, en arabe et en dialecte tunisien, l'accès aux règles officielles, une aide à la préparation des formulaires et des pièces, et une validation humaine par les travailleurs sociaux. **Ce sont des capacités proposées, pas des exigences validées.**

## Étape actuelle

L'application locale active est l'interface bénéficiaire Sned dans `frontend/`. C'est une reconstruction en React du design Sned de l'équipe, dont l'original est conservé tel quel dans `sned/`. Elle est servie par le backend applicatif, avec le scanner OCR et le worker GPU Falcon pour l'OCR réel. Les exigences produit et les choix d'architecture doivent encore être validés : aucune décision d'architecture n'a été acceptée. Les inconnues sont notées `TBD`. Voir [l'état actuel](CURRENT_STATUS.md), en anglais.

## Démarrer en local

Depuis la racine du dépôt, dans PowerShell :

```powershell
python scripts/start_local.py              # LOCAL : OCR réel
python scripts/start_local.py --mode mock  # DÉMO : sans modèle OCR, sans GPU, sans service Internet
```

Le lanceur crée les environnements Python, installe ce qui manque et compile le frontend (Node.js 20.19+ requis). Il démarre Ollama s'il est installé, puis le backend. Si un GPU NVIDIA est détecté, il démarre aussi le worker Falcon sur le port 8002. Ouvrez ensuite :
- `http://127.0.0.1:8001/` pour l'application Sned ;
- `http://127.0.0.1:8001/demo` ou `/demo60` pour les démonstrations scénarisées ;
- `http://127.0.0.1:8001/docs` pour l'API.

En mode mock, seul le backend démarre, et l'OCR renvoie le texte de la carte d'identité de démonstration. Ctrl+C arrête les services lancés.

## L'architecture expliquée simplement

Imaginez Inclusa, dont l'interface s'appelle **Sned / سند** (« soutien »), comme un guichetier patient, ouvert toute la journée sur votre téléphone :

1. **Vous expliquez votre besoin**, en touchant de grandes images ou en parlant, par exemple « Je voudrais une allocation mensuelle pour ma famille ».
2. **Sned pose une seule question simple à la fois** : âge, foyer, revenus, scolarité… Avec votre accord, il peut aussi récupérer la composition de votre famille auprès de l'état civil ; c'est simulé dans cette démo.
3. **Sned indique quels programmes peuvent vous concerner, et pourquoi.** C'est une orientation seulement : la décision revient toujours aux services sociaux compétents.
4. **Sned liste les documents à préparer.** Il explique où et comment obtenir chacun, et vous permet de signaler ce qui manque. Vos réponses sont gardées, vous pouvez revenir plus tard.
5. **Vous vous connectez avec votre identité numérique et photographiez vos documents.** Le téléphone vérifie que la photo est nette, redresse la carte et lit les champs de la carte d'identité. Vous vérifiez et corrigez avant tout enregistrement.
6. **Vous prenez rendez-vous** avec le travailleur social et recevez un SMS avec votre ticket. Vous suivez l'avancement de votre dossier sans vous déplacer. Le rendez-vous et le SMS sont simulés dans cette démo.

Un guide vocal lit chaque étape à voix haute, en dialecte tunisien, en français ou en anglais. L'interface est pensée pour les personnes peu habituées au numérique. **Les règles décident, l'IA aide à lire et à expliquer, un humain valide.**

## Architecture technique

```
 Navigateur (téléphone ou PC)                Serveur local (boucle locale)                IA locale optionnelle
 ┌────────────────────────────┐   HTTP/WS   ┌───────────────────────────────────────┐    ┌────────────────────────┐
 │ React 19 + TypeScript +    │ ──────────► │ Backend FastAPI (Python)              │    │ Worker GPU Falcon-OCR  │
 │ Vite, 7 pages Sned (RTL,   │             │  • moteur de règles : questions,      │ ─► │ (transformers, CUDA,   │
 │ ar/fr/en, guide vocal)     │ ◄────────── │    orientation, liste des documents   │    │ /v1/chat/completions)  │
 │ Web Speech API (lecture)   │             │  • connexion démo, dossier chiffré    │    ├────────────────────────┤
 │ caméra + test de netteté   │             │  • RDV / suivi (simulés)              │    │ EasyOCR (CPU)          │
 └────────────────────────────┘             │  • sessions de scan + WebSocket       │    ├────────────────────────┤
                                            │  • traitement d'image (OpenCV) + OCR  │    │ Petit LLM via Ollama   │
                                            └───────────────────────────────────────┘    │ (prototype d'intention)│
                                                                                         └────────────────────────┘
```

| Couche | Technologies | Rôle |
|---|---|---|
| Frontend | React 19, TypeScript, Vite (une page par écran Sned), CSS Sned d'origine | Parcours bénéficiaire ; arabe (RTL), français et anglais ; guide lu à voix haute par la synthèse vocale du navigateur. La *saisie* vocale est une démonstration scénarisée |
| Moteur de règles | Python (`backend/sned/knowledge.py`), écrit à partir d'un document fonctionnel sur les programmes tunisiens de protection sociale | Choisit la question suivante, donne une *orientation* (probablement éligible / à vérifier / non éligible, avec les raisons) et construit la liste des pièces. Déterministe et vérifiable, sans IA |
| Session et identité | FastAPI, cookie HttpOnly signé (HMAC), PIN haché (PBKDF2), dossiers et scans chiffrés par utilisateur (Fernet : AES + HMAC) | Connexion e-Houwiya de démonstration ; les données restent sur la machine locale |
| Traitement des documents | OpenCV (résolution, netteté par Laplacien, luminosité, reflets ; détection du contour de la carte et correction de perspective), rapidfuzz (mots-clés du titre), expressions régulières et heuristiques de mise en page pour la CIN tunisienne | Contrôle qualité, redressement, vérification du type de document, extraction des champs. L'utilisateur confirme toujours les champs |
| Modèles OCR | **Falcon-OCR** (modèle vision-langage de TII, exécuté localement sur GPU NVIDIA via un petit worker FastAPI) ou **EasyOCR** (réseau CRAFT de détection et de reconnaissance de texte, sur CPU). Un mode **mock** renvoie le texte de la CIN de démo | Lire le texte des photos. Choisi au démarrage (`--mode local` ou `--mode mock`), jamais changé en silence |
| Modèle de langage | Petit LLM local optionnel via **Ollama** (`qwen3:4b-instruct` testé). Il extrait seulement `{intention, écriture, faits}`, validés par une liste blanche stricte | Prototype expérimental (POC-17) dans l'ancien `demo/`. **Non branché** sur l'interface Sned, qui garde son dialogue déterministe |
| Recherche documentaire (RAG) | **Non implémentée** | La consultation des règles officielles fait partie de la vision ; l'intérêt d'un RAG ou de petits modèles reste une question ouverte (`TBD`) |

Tout tourne en local, avec des composants open source et uniquement des données fictives. C'est conforme aux principes du projet : une IA petite et ciblée, un hébergement local ou sur site, une décision humaine, la confidentialité.

## Ce qui fonctionne aujourd'hui et ce qui est prévu

**Fonctionne aujourd'hui (PoC local, données fictives) :**

- Le parcours Sned complet dans le navigateur : accueil, démonstration vocale, types de demande (première demande, renouvellement, recours), une question par écran, relecture et correction, orientation, liste des pièces avec « où l'obtenir », pièces manquantes, passage au scan.
- La connexion d'identité numérique de démonstration, avec un dossier local chiffré.
- Le scanner de documents sur téléphone (`/m`) et sur ordinateur (`/scanner`) : contrôle qualité, redressement, extraction des champs, correction manuelle et validation. Ce parcours fonctionne de bout en bout en mode **mock**.
- Falcon-OCR sur un GPU RTX local et EasyOCR sur CPU ont chacun lu correctement une image de test synthétique.
- La prise de rendez-vous, l'écran SMS et le suivi du dossier, tous simulés.
- Les démonstrations `/demo` et `/demo60`, entièrement hors ligne.
- Un lanceur en une commande, avec les modes `local` et `mock`, et des tests automatisés pour le backend, le lanceur, les services OCR et le frontend.

**Pas encore vérifié ou limité :**

- L'OCR réel (Falcon ou EasyOCR) à travers la nouvelle interface React n'a pas encore été vérifié de bout en bout : la machine de test a manqué de disque et de mémoire.
- La précision de l'OCR sur des documents fictifs réalistes n'a pas été mesurée.
- L'appairage d'un téléphone par QR code nécessite HTTPS, que le lanceur local ne fournit pas encore.
- Les textes en arabe, en dialecte tunisien et en français doivent encore être relus par des locuteurs natifs.

**Prévu ou envisagé (sous réserve de validation par le MAS, les ULPS et les décideurs du projet) :**

- Des intégrations réelles, chacune soumise à autorisation : e-Houwiya, Madaniya, agenda des ULPS, passerelle SMS.
- Une vraie reconnaissance vocale du dialecte tunisien, à la place de la démonstration scénarisée.
- L'évaluation de petits modèles locaux pour comprendre les demandes (banc d'essai POC-17), et éventuellement d'une recherche dans les règles officielles (RAG). Rien n'est décidé.
- Une interface pour les travailleurs sociaux, la gestion des dossiers, la traçabilité et des accès par rôle.
- Une revue d'accessibilité, un déploiement HTTPS sur site et un rapport sur les résultats et les limites de l'OCR.

## Collaboration et gouvernance

Les contributeurs choisissent une Issue ciblée et se l'assignent avant de commencer. Chaque Issue a sa propre branche et une Pull Request liée. Une revue et une validation humaines sont requises. Une proposition d'architecture ne devient une décision qu'après revue humaine et un ADR marqué **Accepted** ; une capacité proposée ne devient une exigence qu'après validation explicite par les décideurs du projet. Voir [Contributing](CONTRIBUTING.md) et la [documentation](docs/README.md), en anglais.

## Licence

`TBD` : aucune licence logicielle n'a été choisie ni accordée.
