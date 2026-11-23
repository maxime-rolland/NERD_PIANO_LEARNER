# 00 — Vision produit et proposition de valeur

**Nom de travail** : NERD PIANO LEARNER (nom commercial à valider). **Plateforme** : Android en premier. **Nature** : jeu d'apprentissage avec campagne RPG pixel art. **Statut** : intention et cahier des charges, aucune implémentation attestée.

## 1. Problème à résoudre

Un adulte débutant voudrait entendre un morceau et pouvoir, à terme, retrouver sa mélodie et l'interpréter au piano, sans transformer chaque séance en cours de théorie déconnecté de son plaisir. Les apps d'apprentissage privilégient souvent la copie visuelle et/ou le tempo. Il faut entraîner **oreille, mémoire auditive, repérage spatial, lecture de la portée et coordination**, sans les confondre.

**Promesse contrôlable** : le jeu entraîne ces compétences par de petites tâches reliées à des combats et mesure leurs progrès dans un contexte défini. **Non-promesse** : devenir capable de rejouer n'importe quelle musique complexe à partir d'un enregistrement sans apprentissage long, ni transcription automatique parfaite d'un morceau commercial.

## 2. Public / situations

- **Principal** : adulte débutant à faux débutant, dispose d'un piano acoustique ou clavier numérique et d'un smartphone Android posé à proximité.
- **Secondaire** : pianiste qui sait lire mais veut entraîner sa mémoire et jouer d'oreille ; famille souhaitant apprendre en s'amusant.
- **Contrainte d'usage** : appareil au-dessus ou à côté du clavier, écran horizontal ou vertical selon les tests, bruit ambiant variable, mains sur l'instrument, parties courtes (5–20 min), connexion Internet facultative.
- **Accessibilité** : pas d'exigence de vitesse au début ; présentation lisible de la portée, contraste fort, mise en évidence non fondée uniquement sur la couleur ; contrôles accessibles entre les séquences.

## 3. Compétences à distinguer

| Axe | Exercice type | Observation objective |
| --- | --- | --- |
| Oreille relative | Écouter puis retrouver une seconde/tierce, puis une mélodie | Notes justes sans assistance visuelle initiale |
| Lecture | Voir une note sur portée, jouer sa touche | Exactitude avec puis sans nom/touche affichés |
| Géographie clavier | Localiser DO central, groupes de 2/3 touches noires | Note dans l'octave correcte |
| Technique | Main droite/gauche, poignet et doigté suggérés | Exercices guidés ; **pas** de mesure de doigté par micro |
| Harmonie | Identifier puis jouer triades/renversements | MIDI fiable, ou exercice décomposé via micro |
| Rythme | Jouer pulsation et durées | **Bonus optionnel** jusqu'à maîtrise de la hauteur |
| Répertoire | Rejouer une phrase entendue, puis la transposer | Mémoire et transfert vers une mélodie nouvelle |

## 4. Trois modes complémentaires

1. **Écoute d'abord** (« Écho ») : ennemi joue sa note/phrase, aide initiale réduite. Portée révèle les notes **après tentative, demande d'indice ou échec** ; indispensable pour le jeu à l'oreille.
2. **Lecture d'abord** (« Rune ») : une note de la portée est mise en surbrillance ; le joueur cherche au clavier ; audio disponible en indice.
3. **Hybride** (« Duo ») : écoute + portée + retour instantané ; entraînement guidé, notamment pour les débutants.

La campagne répartit explicitement ces modes ; les résultats sont suivis séparément. Un excellent score de « lecture d'abord » n'est pas une preuve de compétence à l'oreille.

## 5. Boucle de plaisir et de progression

Écouter/voir un défi -> essayer sur le vrai piano -> détection -> effet d'attaque/retour -> récompense utile -> déblocage d'une petite compétence -> défi réinvestissant une notion précédente.

Les composantes RPG doivent rendre l'essai plaisant **sans prendre la priorité sur l'apprentissage**. Une séance de 10 minutes doit permettre de mesurer au moins une compétence ciblée.

## 6. MVP (« donjon jouable »)

Le premier livrable inclut : lancement Android ; calibration et permission mic ; une boucle de combat complète ; 5 notes DO4–SOL4 (C4–G4 ; définition des octaves dans le modèle) ; un prototype robuste de reconnaissance monophonique ; lecture guidée sur portée ; mode écoute ; aides et défaite en temps suspendu ; enchaînement de plusieurs combats ; sauvegarde locale ; une direction artistique pixel art cohérente et trois types d'ennemis ; compte rendu de tests sur appareil physique.

**MVP hors périmètre** : transcription automatique de MP3/YouTube, reconnaissance polyphonique garantie par micro, statistiques cloud, multijoueur, génération IA en ligne, catalogue commercial sous droits, système de boutique, 10 mondes finalisés, précision du doigté par audio.

## 7. Vision après MVP

- Parcours pédagogiques étendus : deux clés, mains alternées puis ensemble, accords, gammes, intervalles et transposition.
- Clavier MIDI Android, d'abord USB, puis éventuellement BLE MIDI.
- Modes de difficulté/historique adaptatifs, morceaux originaux et domaine public édités/validés.
- Export/import de contenu structuré ; MusicXML/MIDI en version future avec validation et garde-fous.
- Option « reconstruire un thème entendu » dans une tonalité, d'abord sur phrases contrôlées, puis répertoire personnel/licite.
- Une vraie campagne et ses environnements, boss musicaux, objets cosmétiques, personnalisation.

## 8. Indicateurs à vérifier lors des tests

**Indicateur principal** : évolution de l'exactitude sur des *mélodies nouvelles non présentées précédemment*, tentées à l'oreille avec le même nombre d'indices. Compléments : justesse de lecture sans aide, recours aux indices, notes mal détectées, abandon en début de séance, durée pour rejouer une phrase, envies de relancer.

Les seuils de performances DSP dans `09-VALIDATION.md` sont des **cibles de décision à mesurer**, pas des résultats déjà obtenus.

## 9. Arbitrages produit par défaut

- Débutant zéro prérequis ; français initial, noms **Do Ré Mi** visibles, conventions MIDI internes normalisées.
- Pas de limite de temps dans le mode principal ; tempo récompensé uniquement s'il est activé.
- Mode « guidage » qui n'humilie pas : erreur = point adverse, mais retour explicite et rejouable sans perdre son acquis.
- Jeu gratuit et local au prototype ; monétisation éventuelle postérieure et jamais conçue comme obstacle pédagogique.
- L'utilisateur choisit les aides, dont certaines s'atténuent progressivement sur les missions de progression.
