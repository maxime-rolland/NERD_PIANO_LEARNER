# 09 — Stratégie de test, qualité, recette de matériel

## Gates de validation
**G0 : microphone viable sur Android réel** avant d'investir dans des cartes/monstres élaborés. **G1 : premier donjon complet sur instrument réel**. Une CI verte ne peut pas remplacer un micro réel.

## Tests automatisés obligatoires
1. Modèle : conversion MIDI/Hz, A4=440, C4=60, octaves, spelling et graphie.
2. Combat : même `event_id` idempotent ; correct +1 héros ; attaque fausse +1 ennemi ; 1 note tenue=1 événement ; silence/bruit incertain=0 point ; pas de fin par attente même 60 s ; état d'écoute/défaite ne score pas ; seuil d'échec et victoire ; retry.
3. Curriculum : prérequis enseignés, graphe sans cycles involontaires, niveaux micro mono, mode Écho réponse masquée, mêmes pitches dans audio/portée/aides.
4. Audio synthétique : sinusoïdes C4/D4/E4/F4/G4 + harmoniques piano synthétiques, variations d'amplitude/bruit, attaques répétées, octaves, accord faussement pris pour note unique, son démo.
5. Sauvegarde : création, reprise, upgrade de schéma, sauvegarde invalide et récupération.
6. Android smoke : démarrage/permission refusée/pause/retour/retrait micro USB + orientation.

La suite synthétique prouve des propriétés de l'algorithme, **pas** sa performance sur un piano réel.

## Protocole expérimental piano + téléphone
**Relevé obligatoire** : modèle Android, version OS, appareil micro, route, fréquence PCM réelle, distance/angle du téléphone, niveau de volume, type de piano, acoustique de pièce, instrument et date; build/commit SHA.

Matrice MVP :
- Notes C4, D4, E4, F4, G4 ; ≥20 attaques/hauteur, étiquetées manuellement : **≥100 attaques**.
- Répétitions de même hauteur (C4-C4), legato, staccato, touches tenues et pédale.
- Trois intensités ; piano numérique/acoustique si disponibles ; 20 cm/60 cm ; mode calme et bruit raisonnable.
- Silence prolongé, voix parlée, sonnerie/notification et **démo du jeu jouée sans piano**.
- Haut-parleur vs casque ; micro externe USB si disponible ; réouverture après pause, route audio changée.
- Scénario « fausse note » attendu G4 joué F4 ; compteur doit monter **une** fois.
- Métriques calculées sur attaques réelles étiquetées, pas sur fenêtres PCM.

Définitions :
- `precision = note_on_correctement_corrélés / tous_les_note_on_émis` ; un événement de hauteur incorrecte compte comme erreur dans la mesure de justesse.
- `recall = attaques_valides_reconnues / attaques_réelles` ;
- `pitch_accuracy = hauteurs_correctes / note_on_corrélés` ;
- `idle_false_onsets = note_on durant silence, bruit ou lecture du jeu` ;
- latence médiane et `p95` de l'attaque à validation, jank UI et perte de buffers.

**Seuil de départ PROPOSÉ (à ajuster selon tests)** : sur appareil/instrument de référence en pièce calme, ≥90 % de rappel, ≥95 % de pitch accuracy parmi événements corrélés, **zéro score sur 5 min de silence/démo**, latence p95 <350 ms et aucun double comptage d'une note tenue. Documenter taille d'échantillon et incertitude. En dehors de ces conditions : rendre l'app non punitive en cas de détection ambiguë ; accepter un passage en « prototype limité » plutôt que prétendre la généralité.

## Scénarios E2E
SC-01 MI4 joué une fois = +1 héros, sustain sans score supplémentaire.
SC-02 FA4 joué à la place de SOL4 = +1 ennemi, bruit/voix indéterminés sans pénalité.
SC-03 ne rien jouer pendant 60 s = aucun point ni game over.
SC-04 défaite = ralenti visuel, touche et doigt recommandés, partition juste, retry.
SC-05 mode Écho = aucune réponse en clair avant tentative/indice, après reveal mémoire distincte.
SC-06 la démo audible **ne s'auto-valide pas**.
SC-07 refus permission = diagnostic et alternative tactile.
SC-08 progression persiste ; aucune capture PCM stockée par défaut.

## Journal de recette — TEMPLATE À REMPLIR, JAMAIS INVENTER
~~~md
### Bench G0 — [date] — [commit] — état NOT_RUN / PASS / FAIL / BLOCKED
- Téléphone / OS :
- Piano / accordage / distance :
- Micro et fréquence réellement captée :
- Notes étiquetées : n=
- Recall : … / n=…
- Pitch accuracy : … / n=…
- False idle onsets (5 min) :
- Latence médiane/p95 :
- Démo seule déclenche score ? :
- Réattaque de même note / sustain :
- Permissions / reprise :
- Bugs observés + logs / liens :
- Verdict : GO / NO-GO / BLOCKED
- Testeur/date :
~~~

### État initial au 2026-11-23
**NOT_RUN** — aucun code Godot, APK, appareil ou enregistrement testé à la création du cahier des charges. Ne pas confondre cette ligne avec une validation future.

## CI recommandée
Version de Godot épinglée ; `godot --headless --path game --editor --quit` pour import, `--headless --path game --script tests/...` pour scénarios, validateur de contenu et fixtures PCM, vérification de licence/artefacts, build Android **seulement si outillage disponible**. Publier artefacts de diagnostic/screenshot sans informations personnelles. Refuser le passage à G1 si test hardware requis absent.
