# 05 — Architecture technique recommandée

## 1. Décision de départ

**Godot 4.x stable épinglée dans le projet**, GDScript typé, architecture 2D offline. Préférer renderer Compatibility / Mobile selon essais sur 2 appareils, valider lisibilité pixel art + consommation. Android en priorité (APK debug, puis AAB si publication). Développer un **vertical slice** complet avant de construire la carte entière.

**Pourquoi Godot** : pixel art 2D, animation et scènes, source ouverte, export Android, système audio avec capture, outils testables en headless. **Pourquoi ne pas imposer React/Capacitor** malgré l'expérience PixelDoku : audio micro à latence modérée et gestion des routes/permissions sont cruciaux ; un moteur natif/offline réduit les composants. Ce choix est un **ADR amendable** après un vrai spike mesuré, pas un dogme.

Pas de serveur ni d'API réseau au MVP ; tout le contenu est livré avec l'app. Assets pixel art originaux ou dûment licenciés.

## 2. Architecture en couches

~~~text
Android device (mic, optional USB input/MIDI)
  | audio PCM / raw MIDI
  v
Input adapters: GodotMicCapture / AndroidAudioRecord? / AndroidMIDI? / TouchKeyboard
  | NoteEvent, InputHealth
  v
AudioDSP (onset, pitch, confidence, gate) -> Normalizer / Deduplicator
  | normalized musical intent
  v
MusicDomain (Pitch, Interval, Chord, Phrase, Staff, Lesson, fingering metadata)
  | typed commands/events
  v
CombatEngine (deterministic FSM, score, hints, victory/defeat)
  | immutable state + events
  v
Presentation (pixel RPG, staff sharp vector, HUD, touch, animation, sound)
  |
Persistence (local versioned profile, progress, settings)
~~~

**Séparation obligatoire** : ne jamais décider une attaque dans l'algorithme FFT ou dans la scène de l'ennemi. Tests de combat sans microphone. Pas d'import de node UI dans le DSP. Les dépendances vont du rendu vers le domaine, pas l'inverse.

## 3. Contrats entre modules

- `IInputSource` : `start(), stop(), get_health(), signal note_event(event: NoteEvent)`. Adapter micro/USB MIDI/tactile derrière le même contrat.
- `IPitchTracker` : `process_pcm(frame, timestamp) -> PitchEstimate?` et `reset()`. Ses sorties peuvent être indéterminées.
- `NoteEvent` : décrit attaque ou relâchement normalisé, source, pitch MIDI, confiance, moment monotone ; le mic peut avoir fréquence/cents.
- `LessonRepository` : lit un manifeste versionné et valide les compétences, phrases, transitions, assets.
- `CombatEngine` : `start(encounter, input_mode, accessibility_settings)` et `apply(event|hint|replay|retry) -> CombatState + DomainEvents`.
- `MusicNotationRenderer` : `render(phrase, highlighted_index, aids, viewport)` ; n'interprète pas la logique du combat.
- `ProgressRepository` : événements/synthèses de progression, migration sur version de schéma, pas de données audio brut.
- `AudioPlayback` : joue la phrase de référence et signale précisément fin et réarmement sûr.

**Important** : nom et organisation de classes sont proposés ; adapter aux idiomes Godot et ne pas créer des interfaces abstraites inutilisées. La cohérence des contrats importe plus que le pattern exact.

## 4. Répertoire cible

~~~text
/README.md
/CLAUDE.md
/docs/                 # exigences, design, recherche, recette
/prompts/              # démarrage autonome
/.claude/skills/goal/  # procédure /goal
/game/
  project.godot
  export_presets.cfg   # sans secrets de signature
  autoload/
  scenes/
    boot/ onboarding/ hub/ battle/ tutorial/ results/
  ui/
    staff/ piano_overlay/ hud/ accessibility/
  domain/
    music/ combat/ lessons/ scoring/ adaptation/
  audio/
    capture/ dsp/ input/ playback/ calibration/
  content/
    encounters/ worlds/ skills/ phrases/ locale/
  assets/
    sprites/ fonts/ sfx/ music/ shaders/
  persistence/
  android/             # optionnel, plugin Kotlin v2 + build
/tests/
  domain/
  dsp/
  fixtures/            # PCM synthétiques + vrais sons autorisés
  content/
  integration/
/tools/
  validate_content.*
/.github/workflows/
  check.yml
/LICENSE
/THIRD_PARTY_NOTICES.md
~~~

Ne pas créer le module Kotlin tant qu'un prototype Godot pur ne montre pas un besoin réel. Ne pas ajouter un backend pour stocker 12 niveaux locaux.

## 5. Modèle musical, notation et fidélité

Conserver dans le domaine des hauteurs **MIDI int 0..127**, pas seulement `"DO"`. Référence : MIDI 60 = **C4 / Do4 central** ; MIDI 69 = A4 / La4 440 Hz, avec option d'accordage. Les conventions d'octave divergent suivant bibliothèques : les adaptateurs doivent convertir explicitement.

Décorréler :
- note audio détectée (pitch absolu) ;
- orthographe de la note dans la partition (DO# vs REb) ;
- durée musicale et métrique (facultatives au début) ;
- doigt conseillé (main, numéro) ;
- état d'aide/révélation.

La portée doit afficher correctement lignes, espaces, clés, altérations et à terme plusieurs voix. MVP peut utiliser placement géométrique et glyphes issus de police musicale SMuFL **sous licence compatible** (Bravura OFL) plutôt que dessiner des ovales pixels illisibles ; une portée demeure nette sous le pixel art.

## 6. Synchronisation et ordre des événements

- Le moteur musical ne s'appuie pas sur la fréquence d'images.
- Tous les événements audio/contrôle utilisent une horloge monotone ; conserver leur ordre.
- Un événement d'attaque possède `event_id` unique ; déduplication idempotente au niveau CombatEngine.
- La lecture de référence émet `playback_started` et `playback_finished` ; la capture réarmée seulement après un état sûr. Réponse mic jamais acceptée pendant `DEMO_LISTEN` / `DEFEAT_SLOWMO`.
- Reprendre l'application en foreground : vider événements périmés ; proposer recalibration si route changée.

## 7. Config / plateformes

- Godot stable et composants versionnés par manifeste, SHA si archive toolchain.
- Gradle/JDK 17 + Android SDK suivant guides Godot, paramètres Android export vérifiés à la date de livraison.
- Android minimum proposé : **API 29 (Android 10)** ; à arbitrer selon la cible, tests et fonctionnalités effectivement utilisées. Les exigences Play Store/target SDK varient dans le temps, les revérifier à chaque release.
- Paysage proposé pour le combat (partition + monstres + clavier), onboarding accessible et respect de safe areas, posture téléphone sur pupitre ; documenter résultats de tests ergonomiques.
- Aucun secret de keystore dans Git, en CI ou dans l'APK ; les builds release sont une étape séparée de la démonstration debug.

## 8. Pipeline d'automatisation proposé

1. validation JSON/YAML des niveaux et contrôles musicaux (note dans plage / compétence acquise / aucun asset manquant / invariants de combat) ;
2. tests de domaine déterministes, propriété « silence ne score pas », erreur unique, mode sans timer ;
3. tests DSP sur fichiers PCM reproductibles + tableaux de précision ;
4. import headless Godot et test lancement scène principale ;
5. export APK debug quand SDK présent, `adb install` et smoke test sur vrai appareil ;
6. captures d'écran comparées aux maquettes et inspection des états d'erreur ;
7. enregistrement du résultat dans `docs/09-VALIDATION.md` avec limites.

Une CI verte n'est **pas** une preuve que le micro reconnaît un piano réel. Le protocole manuel instrumenté reste un gate.

## 9. Décisions différées

- Godot audio pure vs Kotlin `AudioRecord` : décider après mesures d'écho, latence, routes.
- Bibliothèque DSP externe : éviter d'ajouter aubio/TarsosDSP sans vérification précise des licences (risque GPL avec MIT).
- Notation complexe : n'investir dans un moteur de gravure complet qu'après MVP.
- Synchronisation compte/cloud : pas MVP ; documenter besoin avant d'introduire RGPD et sécurité backend.
