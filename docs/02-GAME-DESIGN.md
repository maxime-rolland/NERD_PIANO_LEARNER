# 02 — Game Design Document (GDD)

## 1. Pitch et univers

**Archipel des Résonances** : des îles et des donjons ont perdu leur harmonie. Les habitants parlent en motifs musicaux. Le héros récupère les notes qui manquent aux mélodies, libère les instruments et restaure chaque région. Univers attachant mais légèrement étrange, pixel art JRPG 16-bit moderne, combats au tour par tour **sans minuteur par défaut**.

Le joueur n'incarne pas un virtuose. Ses premiers exploits consistent à retrouver un DO, puis une phrase de trois notes. La narration célèbre le progrès ; elle n'invente jamais de capacités musicales déjà acquises.

**Rythme de jeu** : hub 30–60 s -> salle / conversation 20 s -> combat 30–120 s selon l'élève -> coffre / retour pédagogique 15 s -> révision. Navigation simplifiée : pas d'exploration chronophage dans le premier donjon.

## 2. Boucle de combat principale

État du combat :

`ENTER -> INTRO -> DEMO_LISTEN -> AWAIT_PLAYER -> VERIFY_EVENT -> (CORRECT | INCORRECT | UNKNOWN) -> (NEXT_STEP | DEFEAT | VICTORY) -> REWARD`.

- `INTRO` : monstre + thème musical, type de défi, jauges et instructions.
- `DEMO_LISTEN` : le monstre joue une note ou phrase (audio synthétisé local). **Entrée de jeu suspendue** durant la démo pour empêcher l'auto-validation acoustique.
- `AWAIT_PLAYER` : le personnage attend indéfiniment la réponse. Demander une réécoute ou une aide n'inflige pas de dégâts, mais est comptabilisé dans l'autonomie.
- `VERIFY_EVENT` : accepte seulement événement musical stable et nouveau de l'adaptateur d'entrée.
- `CORRECT` : une note exacte (MIDI / tolérance de tuning) remplit un segment d'attaque héros, effet visuel/sfx court ; avance sur la phrase.
- `INCORRECT` : attaque volontaire mais fausse -> +1 jauge ennemie, animation douce, éventuel message « Essaie plus aigu / grave » lorsque confiance suffisante ; reste sur la même étape.
- `UNKNOWN` : silence/bruit/f0 incertain/hors plage/écho -> aucun dégât et aucun progrès ; afficher diagnostic discret si besoin.
- `VICTORY` : dernière note / phrase achevée avant le seuil de défaite, animation, loot et bilan pédagogique.
- `DEFEAT` : seuil d'erreurs atteint. Déclencher séquence « résonance suspendue ».

**Règle d'or** : pas de points pour un son du jeu, ni pour le sustain d'une note, ni pour les dizaines de frames de traitement. Un événement `note_on` stable = **une tentative**, indépendamment de la durée. Rejouer deux fois une note identique exige un relâchement ou une réattaque acoustique détectée ; le jeu explique le cas d'un piano réverbérant.

## 3. Barres, points, états et récompenses

Chaque séquence a `N` notes. Le héros possède `N` segments à remplir ; **une note juste = un segment**. Les monstres possèdent une jauge d'erreurs (valeur par défaut du premier donjon : 4 erreurs autorisées, défaite à la 4e). Le seuil est visible et configurable pour le mode entraînement (illimité) ; aucun point ennemi par unité de temps. La réussite termine la rencontre même si la jauge adverse a été partiellement remplie. Les statistiques enregistrent nombre de vraies erreurs, indices, répétitions, tentatives et résultat.

Quand plusieurs monstres apparaissent : l'attaque juste est dirigée vers la créature correspondant à la note/phrase, l'erreur peut remplir une jauge ennemie commune au MVP. En V1, le combat multi-monstres pourra partager ou répartir ces points, mais devra conserver le contrat « une erreur stable = un point ennemi ».

**Récompenses** : fragments de partitions, instrumentarium décoratif, apparences du héros, lore, nouvelles zones et un « carnet des sons ». XP de maîtrise par compétence, non XP de grind infini. Seules les réussites autonomes contribuent pleinement au déblocage d'une compétence ; la pratique guidée reste encouragée.

## 4. Les trois styles de combat (non interchangeables)

### « Écho » — entendre, chercher, rejouer

1. Un monstre chante une note ou une phrase de 1–4 notes.
2. L'app arrête la démo, protège le micro d'un écho et **cache** le nom de la note et sa touche exacte. Affiche une portée en attente (ou seulement la structure).
3. Le joueur teste sur son piano ; à chaque réponse sûre, le jeu réagit.
4. À la première erreur ou sur demande, montrer d'abord une **direction** (plus haut/bas) ou l'intervalle ; puis un indice de touche ; puis la position de la note sur portée.
5. À la fin, afficher la phrase sur portée avec notes surlignées et possibilité de l'écouter/rejouer.

**Important** : les erreurs d'exploration restent possibles ; l'option « entraînement sans dégâts » est activable. L'exactitude **avant tout indice** détermine la progression d'oreille.

### « Rune » — lire une note / une phrase

Une vraie partition montre une note courante en surbrillance et une clé. Le joueur recherche puis joue ; audio indicatif sur demande. Après chaque note, la surbrillance avance ; pas de défilement imposé.

### « Duo » — combiner écouter + lire

Portée complète, note courante en surbrillance et démo sonore. C'est le mode tutoriel et rééducation, avec clavier et doigt conseillés si demandés.

## 5. Défaite : le « Temps suspendu »

Objectif précis : **faire de l'échec une leçon visible**. Ne pas confondre avec un QTE ; le ralentissement est **cinématographique**.

Story-board :
1. Dernière erreur : le monstre commence son attaque, les particules, ennemis et projectile passent à ~20 % de la vitesse de leur animation pendant 0,8–1,2 s (paramètre visuel, non gameplay).
2. Le temps s'arrête ; bruitages du monde baissés, aucun score/dégât pendant la pause.
3. La portée montre le passage raté ; note attendue lumineuse, notes précédentes atténuées. Un clavier d'une octave marque précisément la touche (blanche/noire), numéro d'octave et doigt **conseillé** (1–5).
4. Animation lente d'un doigt fantôme vers la touche ; audio de référence uniquement sur action « écouter », jamais quand la détection est active.
5. Boutons accessibles : **Réessayer**, **S'entraîner sans dégâts**, **Écouter**, **Voir le doigté**, **Quitter**. Relancer la même séquence, éventuellement plus courte.
6. Sur nouvelle tentative : redémarrer sans délai, sauvegarder erreur/indice, ne pas supprimer les acquis.

L'affichage du doigté est une suggestion technique **et non une mesure de quel doigt a été utilisé** : aucune entrée mic/MIDI ne fournit cette information.

## 6. Progression RPG et ennemis

### Premières familles d'ennemis
- **Slimes tonals** : une note puis répétitions simples ; décor « bosquet des touches ».
- **Chauves-souris écho** : notes mémorisées à l'oreille ; la longueur augmente.
- **Golems de portée** : notes à identifier dans la clé affichée, lecture pure.
- **Sorciers d'intervalles** : deux notes avec écart montant/descendant, entraînement relatif.
- **Boss « Silence »** : combine lecture, écoute et mémoire sur une mini-mélodie originale.

Aucune créature ne possède un mécanisme empêchant d'attendre ; les animations d'impatience ne modifient jamais le score.

### Monde / missions
1. **Prologue : le premier DO** — découverte du clavier, micro, victoire en quelques minutes.
2. **Bosquet des Touches** — DO4–SOL4, main droite, 3 familles.
3. **Grottes des Échos** — mémoriser 2–4 notes, répéter sans visuel.
4. **Archives des Runes** — lire portée et lignes supplémentaires.
5. **Rivière des Intervalles** — secondes, tierces, quarte, quinte.
6. **Mines des Basses** — main gauche et clé de fa.
7. **Citadelle des Accords** — accords décomposés puis simultanés sur MIDI.
8. **Tour des Deux Mains** — coordination et indépendance, sans obliger le micro à reconnaître deux sons en parallèle.
9. **Horloge de la Pulsation** — rythme libre puis bonus de tempo.
10. **Observatoire des Mélodies** — transposition, dictée, motifs, reconstruction de thèmes.
11. **Royaume des Modulations** — demi-tons, tonalités, altérations.
12. **Temple de l'Improvisation** — jeu libre guidé, écoute et accompagnement.

Ce découpage est une **vision pédagogique**. Seul le Prologue/Bosquet initial est inclus au MVP ; les autres ne doivent pas faire passer le projet au statut fini.

## 7. Motivation sans mauvaises incitations

- Parties courtes et possibilité de répéter un passage isolé.
- Erreurs expliquées et révisions intelligentes, pas seulement punition.
- **Pas** de pression artificielle sur une « streak » quotidienne, pas de publicités pendant une séance, pas de paywall interrompant un apprentissage.
- Pour les retours visuels, distinguer : « note juste », « note différente », « son non identifié », « manque d'entrée ».
- Boss final mêlant compétences déjà pratiquées plutôt que difficulté arbitraire.
- Codex de mélodies personnel lisible et réécoutable.

## 8. Exemples de trois rencontres MVP

| ID | Mode | Démonstration | Attendu | Indice gradué |
| --- | --- | --- | --- | --- |
| `BOS-001` | Duo | DO4 | DO4 (MIDI 60) | portée puis touche DO4, doigt droit 1 |
| `BOS-002` | Rune | Optionnelle | MI4 (64) puis SOL4 (67) | nom puis position doigt |
| `BOS-003` | Écho | DO4-MI4-SOL4 | phrase ordonnée en trois attaques | direction/intervalle -> note -> touche |

Toutes les notes du jeu sont définies en MIDI interne et libellées en français par l'interface, sans deviner l'octave à partir de l'affichage. Leçon « DO central » = MIDI 60 selon la convention choisie.
