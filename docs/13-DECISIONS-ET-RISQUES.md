# 13 — ADR : décisions, hypothèses, risques, arbitrages

Les décisions ci-dessous sont celles du **cahier des charges**, non une validation d'implémentation. Toute évolution notable : date, contexte, alternatives, décision, conséquences, tests et preuve.

| ADR | Statut | Choix actuel / décision à confirmer | Pourquoi / risques |
| --- | --- | --- | --- |
| ADR-001 | PROPOSÉ | Godot 4.x stable, GDScript typé et extension Kotlin **seulement si besoin** | RPG pixel art/Android et micro ; tester sur vrai téléphone avant verrouillage |
| ADR-002 | ACCEPTÉE produit | Temps libre au début ; rythme facultatif plus tard | Il faut pouvoir trouver une note sans pression |
| ADR-003 | ACCEPTÉE produit | Trois modes Écho/Rune/Duo, résultats séparés | Voir la réponse et jouer d'oreille sont deux capacités différentes |
| ADR-004 | ACCEPTÉE produit | Micro monophonique pour MVP ; MIDI pour accords simultanés | Impossibilité de garantir polyphonie micro sans corpus/tests |
| ADR-005 | ACCEPTÉE produit | Traitement audio local, sans cloud, permissions minimales | Vie privée et indépendance au réseau |
| ADR-006 | PROPOSÉ | C4/Do4 = MIDI60, A4=440, notation musicale indépendante | Conventions d'octave ambiguës ; tests stricts |
| ADR-007 | PROPOSÉ | Godot capture → YIN/autocorrélation → gate onset + confiance | Piano riche en harmoniques, erreurs d'octave, latence |
| ADR-008 | ACCEPTÉE produit | Défaite = slow motion **visuel**, correction sur partition + clavier + doigt conseillé | Transformer erreur en apprentissage, sans QTE |
| ADR-009 | PROPOSÉ | Vue combat paysage, pixel world + portée nette | Écran sur pupitre ; test lisibilité avant verrouillage |
| ADR-010 | PROPOSÉ | Android min API 29, révisable après bench ; build cible selon exigences Play courantes | Accessibilité matérielle vs coût maintenance |
| ADR-011 | ACCEPTÉE produit | Aucun catalogue de morceaux commerciaux sous droits au MVP | Protéger projet et utilisateurs |

## Risques classés

| ID | Gravité | Risque | Mesure / sortie |
| --- | --- | --- | --- |
| R-01 | CRITIQUE | Micro prend harmonique pour note réelle ou octave incorrecte | Bench 100 attaques, accordage/tolérance, pitch algorithm testé |
| R-02 | CRITIQUE | L'app entend sa propre démo et score à tort | Suspendre scoring en playback, garde post-playback, bench idle |
| R-03 | HAUT | Sustain/reverb = points adverses multiples | Machine d'état onset/hold/rearm, tests répétitions |
| R-04 | HAUT | Un projet joli mais non entraînant l'oreille | Modes et métriques séparés, phrases de transfert inédites |
| R-05 | HAUT | Fonction 2 mains/accords vendue comme validée par micro | Contrats capacité d'entrée, MIDI ou arpège seulement |
| R-06 | MOYEN | Partition illisible ou fausse musicalement | Font SMuFL/notation nette, mapping MIDI, tests visuels |
| R-07 | MOYEN | Incompatibilités Godot/Android microphone/latence | Spike G0, fallback AudioRecord natif documenté |
| R-08 | MOYEN | Trop d'assets/architecture avant validation | Tranches verticales gates G0→G1 |
| R-09 | HAUT | Mauvais droits sur morceaux/samples/polices | Inventaire, licence originale/OFL/domain public vérifié |
| R-10 | MOYEN | Autonomie agent « teste » sans matériel | Rapport explicitement BLOCKED, aucune revendication Android |
| R-11 | MOYEN | Solfège au détriment du but d'écoute | Mesurer progrès sur phrases inconnues sans visuel |
| R-12 | MOYEN | UX punitive décourageant les débutants | Option entraînement sans dégâts, indices graduels, retry |
| R-13 | MOYEN | Utilisation enfant, stockage et publication | Mode local par défaut, réévaluer catégorie d'âge |
| R-14 | MOYEN | Changement de route/permission en cours de partie | Pause sûre, nouvelle calibration et tests d'interruption |

## Questions à trancher plus tard sans bloquer le squelette
- Instruments de test disponibles (acoustique/numérique), nombre de touches et latence micro acceptable.
- Orientation finale selon téléphone/usage pupitre.
- Préférence palette/narration/âge du joueur.
- Quand ajouter MIDI (priorité forte si instrument supporte USB MIDI).
- Modalité de diffusion et monétisation après prototype.

**Règle** : en l'absence de réponse, appliquer les valeurs proposées et avancer sur les composants testables ; ne jamais inventer preuve ou performance. Le premier vrai test matériel tranche ADR-001, -007, -009 et -010.
