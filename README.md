# NERD PIANO LEARNER

**RPG pixel art Android pour apprendre le piano à l'oreille, lire la musique et jouer sur un vrai instrument.**

Statut : **spécifications et préparation du développement** — aucun jeu n'est encore implémenté. Licence du dépôt : MIT. Langue produit initiale : français.

## Le concept

Dans un monde dont la musique a disparu, le joueur restaure des mélodies en combattant des créatures. Un ennemi émet une note ou une courte phrase ; le joueur l'écoute, la retrouve sur son piano et la joue. Les bonnes notes déclenchent les attaques du héros ; les erreurs font progresser la jauge adverse. Une portée musicale met en évidence la note courante ; ses aides s'effacent peu à peu pour apprendre réellement à écouter, lire et jouer.

La campagne débute **sans pression de temps** ; le rythme ne devient qu'un objectif facultatif puis progressif. En cas de défaite, une séquence de « temps suspendu » montre les touches et les doigtés à reprendre avant une nouvelle tentative.

Le jeu privilégie un **vrai piano acoustique ou numérique via le microphone Android**, avec prise en charge MIDI et clavier tactile suivant les étapes de développement.

## Commencer

1. Lire [la vision](docs/00-VISION-PRODUIT.md) et [le cahier des charges](docs/01-CAHIER-DES-CHARGES.md).
2. Lire [le game design](docs/02-GAME-DESIGN.md) et [le programme pédagogique](docs/03-PEDAGOGIE.md).
3. Pour les risques principaux : [audio sur Android](docs/04-AUDIO-ANDROID.md), [architecture](docs/05-ARCHITECTURE.md) et [validation](docs/09-VALIDATION.md).
4. Dérouler [la roadmap](docs/08-ROADMAP.md).
5. Lancer Claude Code dans le dépôt et utiliser **`/goal`** (skill inclus sous `.claude/skills/goal/`) ou copier le [prompt autonome](prompts/DEMARRAGE-CLAUDE-OPUS-5-5.md).

## Index des documents

| Document | Rôle |
| --- | --- |
| [00 — Vision produit](docs/00-VISION-PRODUIT.md) | Problème, cible, promesse, MVP, métriques |
| [01 — Cahier des charges](docs/01-CAHIER-DES-CHARGES.md) | Exigences vérifiables et critères d'acceptation |
| [02 — Game design](docs/02-GAME-DESIGN.md) | Boucle de jeu, combats, boss, défaite/ralenti, économie |
| [03 — Pédagogie](docs/03-PEDAGOGIE.md) | Oreille, lecture, doigtés, mains, rythme, compétences |
| [04 — Audio Android](docs/04-AUDIO-ANDROID.md) | Microphones, DSP, tolérance, MIDI, limitations |
| [05 — Architecture](docs/05-ARCHITECTURE.md) | Godot, composants, flux, interfaces, déploiement |
| [06 — Direction artistique et UX](docs/06-UX-PIXEL-ART.md) | Pixel art, interface de combat, accessibilité |
| [07 — Données et contenu](docs/07-DONNEES-CONTENU.md) | Schémas, notation, niveaux, sauvegardes |
| [08 — Roadmap](docs/08-ROADMAP.md) | Épopées, ordre, livrables, dépendances |
| [09 — Validation](docs/09-VALIDATION.md) | Tests, banc audio, appareils, jalons go/no-go |
| [10 — Benchmark](docs/10-BENCHMARK-SOURCES.md) | Jeux comparés et sources vérifiables |
| [11 — Confidentialité et licences](docs/11-JURIDIQUE-CONFIDENTIALITE.md) | RGPD, copyright, licences, enfants |
| [12 — Développement autonome](docs/12-DEVELOPPEMENT-AUTONOME.md) | Consignes de délégation, vérifications et continuité |
| [ADR](docs/13-DECISIONS-ET-RISQUES.md) | Décisions explicites, inconnues et risques |
| [CLAUDE.md](CLAUDE.md) | Instructions courtes chargées automatiquement |
| [Skill /goal](.claude/skills/goal/SKILL.md) | Exécution incrémentale et autonome |
| [Prompt de démarrage](prompts/DEMARRAGE-CLAUDE-OPUS-5-5.md) | Prompt prêt à coller dans Claude Code |

## Priorités non négociables

- Détection de **notes isolées** sur un vrai piano, validée **sur au moins un appareil Android réel** avant un développement artistique massif.
- Aucune punition sur du silence, bruit indéterminé, note du jeu entendue par le micro ou faux positif non confirmé.
- Apprentissage de l'oreille **distinct** de la lecture de partition : les aides se révèlent au bon moment.
- Un premier donjon complet et utile avant toute extension du monde.
- Jeu utilisable hors ligne, données audio traitées localement, pas de backend nécessaire au MVP.
- Différencier strictement les capacités du micro (monophonie cible MVP) de celles du MIDI (polyphonie fiable).

> Les choix d'implémentation sont des hypothèses documentées, non des promesses de reconnaissance universelle. Le banc d'essai Android est une condition de passage obligatoire.
