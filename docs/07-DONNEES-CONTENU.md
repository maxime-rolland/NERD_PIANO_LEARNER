# 07 — Données, notation, sauvegarde, niveaux

## Invariants musicaux
Source unique de vérité : hauteur **MIDI int 0..127**, convention **C4/DO4 central = 60**, A4/LA4 = 69, accordage A4 = 440 Hz par défaut et ajustable. `f(m)=A4*2^((m-69)/12)`. Noms FR Do/Ré/Mi/Fa/Sol/La/Si pour l'affichage et `C D E F G A B` dans la sérialisation ; `pitch_spelling` distinct de hauteur acoustique (C#4/Db4 enharmoniques). Durée et tempo possibles dans contenu même si **non évalués** au niveau débutant.

Trois types strictement distincts :
- `MusicNote` : hauteur, durée pédagogique, graphie dans la partition, doigt conseillé/optionnel.
- `NoteEvent` : hauteur effectivement déclarée par micro/MIDI/tactile, confiance, source, événement attaque/relâchement, timestamp monotone. Aucun champ « vrai doigt joué ».
- `CombatState` : état du combat, index attendu, points héros/ennemis, aides utilisées, événement déjà consommé.

## Exemple de rencontre (contrat cible)

~~~json
{
  "schema_version": 1,
  "id": "bosquet.echo.003",
  "world_id": "bosquet",
  "title_fr": "La chauve-souris répétitrice",
  "mode": "echo",
  "input_capability": "monophonic",
  "skill_ids": ["ear.memory.three_notes"],
  "prerequisite_ids": ["bosquet.duo.001"],
  "enemy_ids": ["echo_bat"],
  "error_limit": 4,
  "time_limit_ms": null,
  "phrase": [
    {"type":"note", "pitch":60, "spelling":"C4", "duration_beats":1, "fingering":{"hand":"right","finger":1}},
    {"type":"note", "pitch":64, "spelling":"E4", "duration_beats":1, "fingering":{"hand":"right","finger":3}},
    {"type":"note", "pitch":67, "spelling":"G4", "duration_beats":2, "fingering":{"hand":"right","finger":5}}
  ],
  "notation":{"clef":"treble","key_signature":"C_major","time_signature":"4/4"},
  "hints":["relisten","contour","pitch_name","staff","keyboard_fingering"],
  "evaluation":{"initial_answer_hidden":true, "count_timing":false, "octave_strict":true},
  "assets":{"enemy_sprite":"echo_bat","victory_sfx":"resonance"},
  "content_license":"original"
}
~~~

Chaque contenu doit être validé : unicité des IDs, prérequis acycliques, notes en tessiture enseignée, orthographe concordante, asset et licence valides, aucune note simultanée requise par niveau micro, mode Écho sans fuite d'indice par défaut, pas de timer pénalisant dans MVP, séquences finies, clés et notes compatibles. Un **outil de validation** échoue le build si le contenu est incohérent.

## Contrat d'entrée musical
~~~json
{
  "event_id":"session-unique-id",
  "kind":"note_on",
  "source":"microphone",
  "pitch":64,
  "frequency_hz":329.6,
  "cents":-0.2,
  "confidence":0.93,
  "timestamp_ms":451230,
  "velocity":null
}
~~~
`source`: microphone|midi_usb|midi_ble|touch ; `kind`: note_on|note_off. `confidence` signifie confiance de **classification acoustique** seulement pour microphone ; sur MIDI/tactile la hauteur est fournie directement, sans estimation. Les événements portent un ID consommé **au plus une fois**. `unknown`/silence/bruit **n'émettent pas** de `note_on` scorable.

## États de combat
`INTRO → DEMO_LISTEN → AWAITING_INPUT → VALIDATING → CORRECT/INCORRECT → NEXT/AWAITING_INPUT/Victory/DEFEAT_SLOWMO → RETRY`. Aucun score actif durant démo ou ralenti. La progression dépend des **attaques valides**, pas des frames de traitement audio.

## Sauvegarde locale versionnée
~~~json
{
  "schema_version": 1,
  "locale":"fr",
  "unlocked_encounter_ids":["bosquet.duo.001"],
  "skill_mastery":{"reading.treble.c4":{"guided_correct":3,"independent_correct":1,"transfer_correct":0}},
  "settings":{"animations_reduced":false,"rhythm_bonus_enabled":false,"notation":"do_si"},
  "device_calibration_profiles":[],
  "recent_runs":[]
}
~~~
Définir migration de schéma, export volontaire en JSON, effacement complet, plafonnement de l'historique, sauvegarde atomique. Sauvegarde corrompue : restaurer backup si possible et préserver source pour diagnostic, ne pas écraser silencieusement.

## Contenus évolutifs
Le MVP utilise JSON versionné, chansons **originales** et phrases courtes. Import MusicXML / fichiers MIDI utilisateur seulement après validation, licence et traitement d'ambiguïtés de notation (un MIDI ne connaît pas automatiquement la graphie enharmonique). Séparer banque d'entraînement de **phrases inédites** servant à mesurer le transfert auditif. Aucun téléchargement de partitions commerciales sans droits.
