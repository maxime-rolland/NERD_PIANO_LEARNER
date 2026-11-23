# 04 — Spécification audio Android : le point critique

**Principe de sûreté pédagogique :** ne punir que des attaques de notes **clairement reconnues comme différentes**. Un bruit, un écho, un sustain ou une détection incertaine n'est pas une mauvaise note. La capacité du microphone à décoder plusieurs notes simultanées n'est **pas garantie**.

## 1. Sources et route d'entrée

Priorité d'implémentation :
1. **Microphone intégré Android** via Godot 4 `AudioStreamMicrophone` + bus muet muni d'un `AudioEffectCapture`, en activant `audio/driver/enable_input` ; autorisation Android `RECORD_AUDIO` requise. Les échantillons capturés par l'effet sont PCM float 32 bits, valeurs gauche/droite ; consommer le ring buffer et surveiller les frames perdues. Ne jamais relayer le microphone à la sortie haut-parleur.
2. **Microphone externe USB** si Android expose cette route et que le protocole de capture le sélectionne ; commencer par vérifier l'entrée système, pas supposer que l'app peut choisir chaque micro.
3. **Plugin Android v2 Kotlin** optionnel, après preuve d'insuffisance de la capture Godot : `AudioRecord`, choix de source `UNPROCESSED` si effectivement prise en charge (sinon repli), diagnostic route/audio effects, mesures PCM, sélection du périphérique. Éviter `VOICE_COMMUNICATION` (traitements de voix et annulation d'écho peuvent altérer le piano).
4. **MIDI USB** : `MidiManager` Android, commandes `note_on`/`note_off`, vélocité, canal, horodatages, ré-connexion à chaud. **MIDI Bluetooth LE** à tester ensuite (latence/permission). Micro toujours utilisable sans MIDI.
5. **Clavier virtuel** : entrée tactile de secours ; aucune équivalence statistique avec prestation sur piano acoustique.

L'Android MIDI API couvre USB, Bluetooth LE et appareils virtuels (niveau API 23+). Déclarer les fonctionnalités MIDI comme **non obligatoires** dans le manifeste afin de ne pas exclure les téléphones sans MIDI. L'activation de la permission mic doit être différée, refus récupérable.

Documentation : [Godot microphone](https://docs.godotengine.org/en/stable/tutorials/audio/recording_with_microphone.html), [capture](https://docs.godotengine.org/en/stable/classes/class_audioeffectcapture.html), [Android AudioSource](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource), [Android MIDI](https://developer.android.com/reference/android/media/midi/package-summary).

## 2. Pipeline de traitement proposé

`Micro / AudioRecord -> PCM buffer -> channel mixdown -> high-pass doux / normalisation d'énergie NON destructrice -> détecteur d'onset -> analyse de hauteur -> candidat + confiance -> filtre temporel / réarmement -> NoteEvent -> moteur de jeu`.

**Découpler** :
- acquisition/audio I/O (non bloquante) ;
- pitch/DSP (thread de travail si nécessaire, horodatages monotones) ;
- décision (état, notes justes/mauvaises, filtrage son du jeu) ;
- UI/FX (main thread à 60 FPS cible).

Ne jamais faire de FFT lourde dans `_process` à chaque frame de rendu. Si Godot/GDScript devient insuffisant, profiler d'abord, puis migrer le seul DSP vers extension native correctement testée.

## 3. Algorithmes et expérimentations

Pour MVP monophonique :
- Mesurer le bruit de fond, amplitude RMS, plage musicale utile, onset d'une nouvelle frappe ; ne pas juger chaque échantillon.
- Tester un détecteur **YIN / autocorrélation normalisée** (fréquence fondamentale) et un second estimateur spectral (FFT / sommation des harmoniques) pour identifier les ambiguïtés d'octave.
- Le spectre d'un piano peut avoir une **fondamentale plus faible que ses harmoniques** ; prendre simplement le pic FFT maximum est insuffisant.
- Rejeter les estimations instables, les fréquences hors tessiture attendue ou le bruit large bande. Le silence n'émet aucun `note_on`.
- À partir de la fréquence estimée `f` (Hz), pour accordage `A4` de référence `A`, calculer `m = 69 + 12*log2(f/A)`, `midi = round(m)` et `cents = 100*(m-midi)`. Préserver f et cents.
- Double validation : note candidate cohérente sur plusieurs fenêtres et onset (ou véritable réattaque acoustique). Sur sustain, aucun score supplémentaire.
- En cas de mélodie répétant DO4-DO4, exiger nouvelle attaque et période de réarmement réaliste, sans pénaliser un instrument à longue résonance.
- Comparer les candidats à la hauteur **attendue** pour aider au diagnostic d'octave, sans trafiquer la note observée. Ne jamais forcer un DO4 si le micro entend MI4 pour augmenter artificiellement la précision.

**Paramètres d'amorçage expérimentaux, non acquis** :
- capture 44,1 ou 48 kHz selon route Android, traiter le taux **réel** ;
- fenêtres d'analyse de l'ordre de 60–120 ms, pas de 10–25 ms ; à calibrer selon graves et CPU ;
- stabilité 2–4 analyses rapprochées, nombre de cents admissible ~35–50 pour acceptation d'une hauteur tempérée, selon calibration et réverbération ;
- latence cible perçue pour débutant ~150–300 ms après attaque ; les seuils finaux sont définis par les tests réels, surtout au p95 ;
- profil plage DO4–SOL4 au MVP ; élargissement graves/aigus seulement après caractérisation.

Ne pas filtrer une note à 49 cents comme « fausse » si l'instrument acoustique est légèrement désaccordé et que la marge pédagogique la couvre ; ne pas confondre détection d'une **classe** de note et justesse stricte d'accordeur. Évaluer accordage par rapport à A4/440 Hz, ajustable si nécessaire.

## 4. Machine à états des événements de notes

`QUIET` : énergie et confiance insuffisantes, pas de sortie.
`ATTACK_CANDIDATE` : onset et hauteur candidate ; accumuler stabilité, sinon retour QUIET.
`NOTE_ACTIVE` : émettre un unique `NoteOn(pitch, confidence, source, timestamp)`, pas de réémission sur sustain.
`RELEASE_CANDIDATE` : énergie en décroissance / changement stable ; réarmer après relâchement.
`REARMED` : revenir QUIET ou accepter une nouvelle attaque sur même note.

En cas de legato, autoriser un vrai changement de hauteur avec confiance validée sans imposer silence total. Déduplication et refroidissement liés au **même événement physique**, pas un cooldown global arbitraire qui bloquerait une gamme rapide.

Référence de normalisation côté jeu :
- type `NoteEvent { event_id, source: microphone|midi|touch, kind: on|off, midi_pitch: 0..127, velocity?: 0..127, frequency_hz?, cents?, confidence: 0..1, time_monotonic_ms }` ;
- `InputHealth { permission, route, sample_rate, rms, noise_floor, dropped_frames, listening_state, latency_estimate }`.
- Les valeurs `confidence` de sources MIDI/tactile signifient **certitude de la touche déclarée**, pas reconnaissance acoustique. Éviter de les comparer sans séparation.

## 5. Calibration de l'instrument

Parcours de première utilisation :
1. Autorisation au besoin, route audio active ; vérifier échantillons non nuls.
2. Tester 2 secondes de silence et estimation du bruit ambiant.
3. Jouer **DO4, MI4, SOL4** séparément (avec exemples audibles quand le micro est suspendu), 2–3 frappes par note ; estimer octave, stabilité et temps de réponse.
4. Afficher un indicateur « micro correctement positionné / trop loin / bruit » basé sur mesures, proposer déplacer le téléphone.
5. Mémoriser profil **local** par appareil et type instrument, avec bouton recalibrer ; ne pas utiliser ce profil pour cacher les erreurs.
6. Contrôle « test libre » : affiche note entendue, fréquence, cents et niveau, sans combat ni score.

Sur piano à volume faible, bruit de fond, réverbération, sustain, smartphone retourné ou case couvrant le mic : gestion explicite et conseils pratiques.

## 6. Isoler son de référence et entrée micro

Risque critique : le jeu joue DO4 via le haut-parleur puis le microphone le détecte, validant l'exercice **sans piano**.

Mesures combinées :
- Aucun scoring ni classification d'input durant les démonstrations audio.
- Après lecture, attendre la **fin de l'échantillon réellement joué + décroissance acoustique** avant d'armer le détecteur ; durée adaptée au profil, non constante magique.
- Séparer les bus de jeu de la capture et éviter tout « monitoring »/feedback acoustique.
- Pendant phase d'écoute, couper/atténuer BGM et effets pouvant générer des hauteurs confondues avec le piano ; éventuellement proposer écouteurs filaires.
- Si relecture sollicitée pendant `AWAIT_PLAYER` : repasser `DEMO_LISTEN`, bloquer scoring, vider/re-synchroniser ring buffer correctement, puis réarmer.
- Si volume BGM, appel entrant ou notification perturbent la mesure, afficher état d'écoute et suspendre la punition.
- Ne pas prétendre qu'une simple comparaison de signaux supprime parfaitement l'écho acoustique ; tester haut-parleur, casque et écouteurs BT séparément.

## 7. Polyphonie : limites et stratégie

**Constat** : reconnaître de manière fiable un accord joué sur piano acoustique via micro de smartphone est un problème de transcription polyphonique avec harmoniques chevauchantes et contraintes matérielles. La capture ne fournit pas la touche exacte ni quel doigt l'a frappée.

- MVP : monophonie sur la région définie.
- Accords en mode micro : les transformer en arpèges pédagogiques **déclarés comme tels** ; ne pas faire croire que l'accord simultané est vérifié.
- Accords simultanés et deux mains : **MIDI** prioritaire, avec tolérance d'agrégation des `note_on` d'un même accord et prise en charge des touches tenues.
- R&D ultérieure micro-poly : corpus réel accord/arpège, algorithmes multi-pitch, faux positifs, bruit; active uniquement avec performances démontrées et statut expérimental explicite.

## 8. Banc de tests audio reproductible (obligatoire)

Matrice : DO4, RE4, MI4, FA4, SOL4 ; 10–20 attaques chacune ; pianissimo / mezzo / forte ; piano acoustique et clavier si disponibles ; téléphone proche/loin ; silence/bureau/bruit ; résonance et pédale ; note unique, réattaque même note, séquence rapide ; démo sonore du jeu **seule** ; micro externe USB ; permissions refusées ; Android arrière-plan puis retour.

Métriques **sur événements, pas sur frames PCM** :
- `precision` = vrais NoteOn / NoteOn émis ;
- `recall` = attaques valides détectées / attaques réelles ;
- `wrong-note-rate` = mauvaises hauteurs attribuées à une attaque valide ;
- `idle-false-onsets` = événements déclenchés pendant silence/bruit/démo ;
- latence médiane et p95, taux de buffers perdus, CPU, batterie.
- Séparer données synthétiques (DSP) et mesures Android/piano réels, avec taille d'échantillon et biais.

**Critère initial de passage proposé** : en pièce calme sur appareil et instrument de référence, sur ≥ 100 attaques étiquetées, ≥ 90 % de rappel des notes isolées et ≥ 95 % de justesse parmi les événements émis, zéro faux score sur 5 minutes de démo/silence, p95 de réponse < 350 ms. Ce sont des **cibles à valider / renégocier selon matériel**, pas des valeurs atteintes. Conditions plus bruyantes caractérisées séparément ; si échec, ne pas lancer une app punitive.

## 9. Diagnostics et journalisation

Journal local minimal optionnel (sans PCM par défaut) : `sample_rate`, route, note attendue, note détectée, confidence, délai, événement faux/juste/unknown, build, modèle/OS, **aucune information identifiante** dans les exports. Export volontaire d'un **court WAV de diagnostic** uniquement après consentement explicite et suppression possible.

## 10. Pannes à prévoir

Permission refusée, micro coupé par Android, téléphone en appel, plus de route audio, périphérique USB débranché, changement de sortie audio, fréquence d'échantillonnage différente, surcharges CPU, discontinuïtés timestamps, buffer overrun, instrument désaccordé, notes avec pédale, son du haut-parleur, voix humaine, mains jouant plusieurs notes. À chacune : retour non punitif et récupération documentée.
