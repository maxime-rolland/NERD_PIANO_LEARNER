# 10 — Benchmark marché et sources techniques (recherche 23/11/2026)

**Important** : comparaison sur documents publics vérifiables, sans prétendre avoir installé, mesuré ou « reverse engineeré » les applications propriétaires. Les fonctions, tarifs, plateformes et catalogues peuvent évoluer. Réutiliser les **idées et problèmes résolus**, jamais le code, la musique ou le design protégé d'un concurrent.

## Comparatif produit

| Produit | Ce que sa documentation publique met en avant | Inspiration utile | Différence proposée |
| --- | --- | --- | --- |
| [Yousician](https://support.yousician.com/hc/en-us/articles/204738372-Get-started-with-Yousician-piano) | Écoute au micro, retour immédiat et parcours pédagogique | Instrument réel, démarrage C central, tutoriel progressif | RPG/narration ; oreille vs lecture évaluées séparément ; rythme non contraignant |
| [Simply Piano](https://piano-help.hellosimply.com/en/articles/12647864-enable-microphone-access-for-simply-piano) | Reconnaissance micro et parcours sur piano ; aide permissions Android | Calibration/diagnostic et réduction friction | Détection explicite « je n'entends pas » ; apprentissage auditif et combats sans tempo |
| [Ivory Quest](https://ivory-quest.com/support.html) | Jeu de reconnaissance de notes et d'accords sur portée, clavier MIDI sur certains systèmes | Indices visuels, progression de lecture, ennemis | Piano acoustique Android au micro, défaite éducative et entraînement de mélodie à l'oreille |
| [Piano Monsters](https://play.google.com/store/apps/details?id=com.gamelorestudio.melodymonsters) | Univers créatures, notes musicales, harmonie et combats relaxants | Positionnement cosy fantasy, niveau court | Démo audible → tentative micro sur vrai instrument, réelle notation et compétences mesurées |
| [Fuyomin](https://play.google.com/store/apps/details?id=com.fuyomin.FuyominApp) | Créatures, RPG et lecture de partitions, progression | Collection et ludiﬁcation au service de lecture | Pas de timing imposé au début, oreille relative, capture sonore en priorité |
| [Piano Score Fighter](https://play.google.com/store/apps/details?id=com.higashideya.piano_score_fighter) | Combats RPG par bonnes notes, écran ou MIDI, lecture/oreille | Lisibilité du lien note → attaque | Un vrai piano acoustique via Android mic prioritaire, correction ralentie et progression pas à pas |
| [Melodics](https://melodics.com/) | Pratique instrumentale interactive et feedback | Aides guidées, feedback technique, séquences | RPG sans pression temporelle initiale |

**Conclusion benchmark** : le thème « RPG + notes + monstres » **n'est pas nouveau** et ne doit pas être présenté comme une invention inédite. La différenciation pertinente à tester est un ensemble cohérent : écoute d'abord + lecture graduelle + vrai piano acoustique + tolérance au temps + retour pédagogique sur la défaite + respect des limites micro.

## Inspirations artistiques (références, pas assets)

- [Viola: The Heroine's Melody](https://gamingtrend.com/reviews/playing-to-a-different-beat-viola-the-heroines-melody-review/) : scènes RPG chaleureuses intégrant un instrument et une portée.
- [Keylocker](https://www.analogstickgaming.com/game-reviews/2024/9/17/keylocker) : combat musical avec HUD original, mais rythme central non repris au MVP.
- [Fretless: The Wrath of Riffson](https://www.consolecreatures.com/fretless-review/) : feedback d'impact musical dans un univers pixel art.
- Ne pas copier sprites, palettes exactes, personnages, interfaces reconnaissables, musiques, narration ou marques.

## Documents techniques de référence

- Godot capture microphone : https://docs.godotengine.org/en/stable/tutorials/audio/recording_with_microphone.html ; capture PCM : https://docs.godotengine.org/en/stable/classes/class_audioeffectcapture.html
- Godot extension Android v2 : https://docs.godotengine.org/en/stable/tutorials/platform/android/android_plugin.html
- Android MidiManager : https://developer.android.com/reference/android/media/midi/MidiManager
- Android AudioSource : https://developer.android.com/reference/android/media/MediaRecorder.AudioSource
- SMuFL/Bravura sous OFL : https://smufl.formats.music/latest/about/implementations.html et https://github.com/steinbergmedia/bravura
- Guide Anthropic aux outils Claude Code : https://claude.com/resources/articles/steering-claude-code-skills-hooks-rules-subagents-and-more
- Bonnes pratiques Anthropic, plan/implémentation/revue/tests : https://www.anthropic.com/research/claude-code-best-practices

## Réseaux / retours de développeurs (témoignages, pas garanties)

- Reddit, retour empirique long terme (14 nov. 2026) : https://www.reddit.com/r/ClaudeCode/comments/1xw7ylh/i_spent_4500_hours_coding_with_ai_this_is_the/ : risques d'auto-validation, overengineering, tests faussement déclarés et changements hors scope ; d'où une **revue indépendante + preuves avant « done »**.
- Reddit, séparation hub / agents par worktrees / PR (10 nov. 2026) : https://www.reddit.com/r/ClaudeCode/comments/1xscwkp/how_i_run_claude_code_like_a_small_engineering/ ; utile **après** un squelette stable, pas besoin de paralléliser un dépôt vide.
- Reddit, retour sur sessions longues (10 nov. 2026) : https://www.reddit.com/r/ClaudeCode/comments/1xt2iun/sessions_and_token_usage/ ; mémoire de progression **dans le dépôt** pour retrouver le contexte après session.
- Reddit, workflow CLAUDE.md + MISTAKES.md (13 août 2026) : https://www.reddit.com/r/ClaudeCode/comments/1vmey7d/my_claude_code_workflow_after_months_of_daily_use/ ; enregistrer les erreurs reproductibles.

**Ne pas suivre aveuglément** les pratiques risquées : `--dangerously-skip-permissions`, hooks qui empêchent toute supervision, push forcé, suppression automatique de fichiers, parallel coding sur fichiers communs. L'autonomie est subordonnée aux contrôles réels et aux droits.
