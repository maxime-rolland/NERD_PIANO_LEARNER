# NERD PIANO LEARNER — consignes Claude Code

Projet : RPG pixel art Android d'apprentissage du piano à l'oreille. **Dépôt initialement documentaire**. Lire les documents avant de créer l'application.

## Source de vérité
1. `docs/00-VISION-PRODUIT.md` et `docs/01-CAHIER-DES-CHARGES.md` : objectifs et exigences.
2. `docs/04-AUDIO-ANDROID.md` et `docs/09-VALIDATION.md` : réalité du micro et preuve des performances.
3. `docs/05-ARCHITECTURE.md`, `docs/07-DONNEES-CONTENU.md` : contrats et architecture.
4. `docs/08-ROADMAP.md` : priorités et critères de sortie.
5. `docs/12-DEVELOPPEMENT-AUTONOME.md` : processus de livraison.
6. `docs/13-DECISIONS-ET-RISQUES.md` : ADR et inconnues.

## Principes absolus
- **Faire fonctionner le microphone sur un appareil Android réel en tout premier** ; ne jamais simuler ce résultat.
- Distinguer test automatisé, test manuel et test matériel **non effectué**.
- Le **silence n'est jamais une faute**. Une détection incertaine n'est pas une mauvaise note.
- Le mode débutant n'impose aucun timing ; l'horloge n'inflige ni dégâts ni expiration.
- Une erreur musicale = un événement d'attaque réellement confirmé, pas une image audio ni un sustain.
- Ne pas affirmer reconnaître le doigté exact avec le microphone ou le MIDI.
- Monophonie micro au MVP ; n'étendre aux accords/two hands micro que si prouvé par un banc de tests reproductible.
- Pas de micro envoyé sur réseau, pas de compte/SDK de tracking ou de contenu commercial non autorisé.
- Respecter licence MIT du code propre et licences des ressources tierces.
- La musique originale du jeu ne doit pas polluer le microphone pendant l'écoute.
- Empêcher les modifications hors périmètre, ne jamais exposer de clé ni contourner les autorisations.

## Stack envisagée
- Godot 4.x **version stable explicitement épinglée** au démarrage, renderer Compatibility si testé sur appareil cible, GDScript typé.
- Détection DSP monophonique isolée de l'UI, tests sur fixtures PCM synthétiques et enregistrements consentis.
- Extension Android Kotlin **uniquement si justifiée par prototype** (`AudioRecord` brut / MIDI Android).
- Contenus structurés et vérifiables, sauvegarde locale versionnée.
- Pas de service distant au MVP.

## Workflow
- Faire une étape verticale de la roadmap, démontrable bout en bout.
- Avant modification : inspecter code et état Git, vérifier doublons, documenter le choix si ambigu.
- Après : tests ciblés, import Godot headless, export APK si outillage disponible, preuve et limites.
- Une petite modification = un commit cohérent ; changements architecturaux = ADR.
- Ne pas marquer `done` sans preuve ; maintenir `docs/08-ROADMAP.md` et `docs/09-VALIDATION.md` à jour.
- Ne jamais modifier des tests uniquement pour masquer un défaut.
- Indiquer sans détour les tests non exécutables faute de piano, micro, câble ou téléphone.

Démarrage autonome : `/goal`, skill sous `.claude/skills/goal/SKILL.md`. Document détaillé : `docs/12-DEVELOPPEMENT-AUTONOME.md`.
