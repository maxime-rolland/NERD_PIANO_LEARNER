# 08 — Roadmap autonome, priorités, gates

**État à l'initialisation de cette documentation :** toutes les tâches de code sont **TODO** ; la rédaction de ce document n'est ni un prototype ni un APK. Les chiffres de performance sont des **cibles**, pas des mesures réalisées. Tâches par tranches verticales, pas par gros sous-systèmes indépendants.

## Gate G0 — faisabilité Android et micro (BLOQUANT avant jeu massif)

| ID | Taille | Action | Critère de passage |
| --- | --- | --- | --- |
| P0-01 | S | Créer squelette Godot 4 Android, versions fixées | APK debug démarre sur téléphone réel, capture vérifiée |
| P0-02 | M | Capture micro/permission, waveform et visualiseur niveau | PCM Android réel non nul, refus/reprise sans crash |
| P0-03 | L | DSP isolé, note DO4–SOL4, onset, dédup, confidence | tests synthétiques + piano réel ; sustain non doublé |
| P0-04 | M | Calibration, playback de note, anti-écho | démo de l'app seule ne score jamais ; route et bruit détectés |
| P0-05 | M | Banc de mesures audio + journal de recette | métriques par appareil, instrument et conditions ; décision GO/NO-GO |
| P0-06 | S | ADR Godot AudioEffectCapture vs plugin Kotlin AudioRecord | choix justifié par mesures documentées |

Ordre : P0-01 → P0-02 → P0-03 et P0-04 → P0-05 → P0-06. **Ne pas indiquer « micro validé » sans appareil physique**. Si le banc n'est pas accessible, continuer seulement les composants indépendants en signalant G0 matériel bloqué.

## Gate G1 — vertical slice « première victoire » (MVP)

| ID | Taille | Action | Critère |
| --- | --- | --- | --- |
| P1-01 | M | Moteur de combat FSM déterministe | tests : bonne note, erreur unique, silence neutre, aucun timer |
| P1-02 | M | Notes MIDI, JSON des missions, validation contenu | DO4=60 partout ; toutes données valides |
| P1-03 | M | Clé de sol/portée nette/surbrillance | positions C4..G4 correctes sur téléphone |
| P1-04 | M | Pixel art héros + 3 ennemis + HUD lisible | animation 60FPS cible sans DSP dans boucle UI |
| P1-05 | M | Écho, Rune, Duo et système d'indices | révélation différée en Écho ; métriques distinctes |
| P1-06 | M | Défaite/temps suspendu/clavier/doigté/retry | scénario d'échec complet sans timer ni fausse promesse |
| P1-07 | S | Onboarding, diagnostic, repli tactile | permission refusée → mode utilisable |
| P1-08 | M | Prologue + Bosquet, ~8 rencontres + boss | parcours court et progressif jouable de bout en bout |
| P1-09 | S | Sauvegarde locale & reprise | progression après fermeture/redémarrage |
| P1-10 | M | QA, captures, smoke test Android instrument réel | scénarios SC-01 à SC-08 documentés |

Ordre : contrats P1-01/02 → UI musique/combat → modes/aides → niveaux → recette et sauvegarde. **Gate G1 :** APK réel, micro calibré, démonstration puis écoute active, au moins un combat gagné/perdu avec un piano réel, zéro punition pour silence/incertitude, reprise, rendu lisible.

## Gate G2 — V1 curriculum et maîtrise

P2-01 : maîtrise par compétence (oreille/lecture/geste/rythme séparés) ; P2-02 : révisions adaptées et transfert sur nouvelles phrases ; P2-03 : intervalles, mémoire, clé de fa, main gauche ; P2-04 : doigtés conseillés, gammes et altérations ; P2-05 : mode piano libre/carnet ; P2-06 : accessibilité testée / ergonomie pupitre. Pour chaque item : contenu testé, pas uniquement interface.

## Gate G3 — MIDI et polyphonie honnête

P3-01 : plugin MIDI USB + hotplug ; P3-02 : reconnaissance d'accords simultanés **MIDI** (fenêtre de regroupement documentée) ; P3-03 : double portée + deux mains sur MIDI ; P3-04 : accords décomposés/arpégés pour micro ; P3-05 : BLE MIDI évalué sur latence et reconnexion. **Aucune annonce accords micro fiables sans preuve de corpus réel**.

## Gate G4 — extensions optionnelles

P4-01 : rythme comme bonus opt-in ; P4-02 : morceaux originaux/domaine public ; P4-03 : import MIDI/MusicXML avec validation et droits ; P4-04 : campagne longue et cosmétiques ; P4-05 : recherche polyphonie micro ; P4-06 : autres langues et appareils.

## Definition of Done (par tâche)
- Code compilable et import Godot sans erreur ; tests de domaine/DSP pertinents passent.
- Aucun comportement contraire aux invariants de `docs/01-CAHIER-DES-CHARGES.md`.
- Test négatif ajouté pour chaque bug audio/score.
- Niveau, UI ou contenu testés avec capture ou fixture, provenance des assets renseignée.
- Conditions de test documentées : **simulé, émulateur, Android réel, instrument réel** ne sont pas interchangeables.
- Documentation et registre des risques mis à jour ; commit cohérent ; non testable marqué **BLOCKED**, non **DONE**.

## Priorisation si outils absents
Pas de SDK Android : construire game core et tests headless puis indiquer « export APK non testé ». Pas de piano : jouer des fixtures PCM et clavier tactile, mais **G0-05 et G1-10 restent bloqués**. Pas de design final : sprites originaux temporaires avec licence claire ; les remplacer après validation des mécaniques. Les agents ne doivent pas inventer des rapports de bench, dispositifs ou mesures.

## Règles de tâches
Au début de chaque tranche, ouvrir les documents concernés, écrire un mini-plan et établir 3–5 critères de succès. Préférer modifications petites et vérifiables, pas d'implémentation d'une V2 à l'occasion d'une correction MVP. Lancer un reviewer indépendant sur la sécurité de l'audio et la justesse pédagogique. Voir `docs/12-DEVELOPPEMENT-AUTONOME.md` et `.claude/skills/goal/SKILL.md`.
