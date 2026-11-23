# 01 — Cahier des charges fonctionnel et non fonctionnel

Version initiale : 2026-11-23. **MUST** = obligatoire au MVP ; **SHOULD** = V1 ; **COULD** = ultérieur. Toute exigence possède un ID stable, une priorité et un test d'acceptation.

## A. Installation, configuration et vie privée

| ID | Priorité | Exigence et critère d'acceptation |
| --- | --- | --- |
| CFG-01 | MUST | APK Android installable sur appareil de référence documenté ; ouverture hors réseau, aucun crash durant une session de 15 min |
| CFG-02 | MUST | Accueil propose instrument acoustique/numérique + microphone ; autorisation RECORD_AUDIO demandée seulement quand utile ; refus conduit au mode démo/tactile |
| CFG-03 | MUST | Assistant micro visualise niveau, bruit, note, confiance et octaves ; teste DO4/MI4/SOL4, explique placement du téléphone |
| CFG-04 | MUST | Audio brut calculé localement ; pas d'envoi vers réseau ; pas de stockage permanent sans action volontaire |
| CFG-05 | SHOULD | Choix de notation FR/EN, volume SFX, annonces d'aide, réglage référence A4 440 Hz et calibrage |
| CFG-06 | MUST | Détection silence/bruit trop élevé/permission coupée ; pause éducative, jamais dégâts automatiques |
| CFG-07 | SHOULD | Sensibilité et tolérance configurables dans des limites sûres ; afficher avertissement si calibration insuffisante |
| CFG-08 | SHOULD | Sauvegarde locale versionnée, reprise après mise en arrière-plan, export/suppression de progression |

## B. Reconnaissance d'une note / clavier

| ID | Priorité | Exigence et critère d'acceptation |
| --- | --- | --- |
| AUD-01 | MUST | Capture PCM de micro Android (Godot validé sur vrai appareil), taux réel connu, bus isolé, permissions explicites |
| AUD-02 | MUST | Note candidate = hauteur MIDI, fréquence, écart cents, énergie, confiance, horodatage ; aucune décision sur fenêtre PCM seule |
| AUD-03 | MUST | Une attaque monophonique stable devient un événement unique (note_on), puis note_off/réarmement ; tenir une touche ne multiplie pas les scores |
| AUD-04 | MUST | Mauvaise note uniquement si attaque **intentionnelle et suffisamment sûre** ; bruit/silence/incertitude sans pénalité |
| AUD-05 | MUST | Éviter que la démo audible par le téléphone soit acceptée comme note du joueur ; phase d'écoute sans scoring et suppression/reprise validées |
| AUD-06 | MUST | Test d'octave ; si DO3 attendu et DO4 joué, le comportement du niveau est explicite (octave stricte par défaut) |
| AUD-07 | MUST | Micro : MVP note isolée uniquement ; accords simultanés non promis comme reconnus |
| AUD-08 | SHOULD | MIDI USB via Android MIDI APIs ; note_on/note_off et vélocité convertis en mêmes événements |
| AUD-09 | SHOULD | Clavier tactile complet pour démo et continuité, marqué comme mode différent dans les statistiques |
| AUD-10 | COULD | BLE MIDI et polyphonie micro évalués sur preuves, jamais sur simple démonstration artificielle |
| AUD-11 | SHOULD | Support micro filaire USB externe si reconnu par Android ; diagnostic route d'entrée |

## C. Combat et apprentissage

| ID | Priorité | Exigence et critère d'acceptation |
| --- | --- | --- |
| GAM-01 | MUST | Tour musical sans limite de temps : l'ennemi propose un événement, le joueur peut attendre, réécouter/consulter une aide |
| GAM-02 | MUST | Une bonne note validée donne +1 progression héros, animation d'attaque et avance sur séquence |
| GAM-03 | MUST | Une mauvaise note validée donne +1 jauge ennemie ; même note tenue et attaques instables ne multiplient pas ce point |
| GAM-04 | MUST | Fin de séquence correcte = victoire ; seuil d'erreurs atteint = défaite ; indicateurs et seuils toujours visibles |
| GAM-05 | MUST | Défaite -> « temps suspendu » animé, partition en surbrillance, clavier dessiné avec touche correcte, doigt conseillé, démo audio facultative, retry immédiat |
| GAM-06 | MUST | Trois modes Écho (oreille), Rune (lecture), Duo (hybride) ; la visibilité des indices diffère réellement |
| GAM-07 | MUST | Progression mémorisée : monde, niveau, note(s) réussie(s), erreurs, indice consulté, mode entrée ; offline |
| GAM-08 | MUST | Tutoriel jouable, écran diagnostic mic, victoire, échec, reprise ; aucune impasse si microphone indisponible |
| GAM-09 | SHOULD | Types de monstres avec motifs acoustiques/musique et compétences, boss sur phrases de notes |
| GAM-10 | SHOULD | Petites récompenses cosmétiques et badges de maîtrise ; aucune loterie, monétisation ou énergie punitive |
| GAM-11 | SHOULD | Révision adaptative sur erreurs récurrentes, sans supprimer la possibilité de rejouer un niveau |
| GAM-12 | COULD | Tempo/régularité comme bonus opt-in ; mode rythme séparé sans altérer les règles débutant |
| GAM-13 | SHOULD | Message visuel approprié à la source : ne pas prétendre vérifier les doigtés avec un micro |

## D. Partition, théorie et instrument

| ID | Priorité | Exigence et critère d'acceptation |
| --- | --- | --- |
| MUS-01 | MUST | Afficher correctement portée de 5 lignes, clé de sol, notes DO4–SOL4, lignes supplémentaires et surbrillance de note courante |
| MUS-02 | MUST | Pour chaque note : valeur sonore, touche visuelle et libellé FR ; le contenu est la source unique de vérité |
| MUS-03 | MUST | L'aide est progressive, le mode Écho préserve une phase sans révélation de réponse ; résultats par mode distincts |
| MUS-04 | MUST | Fonction d'écoute du modèle et de réécoute ciblée sans interférence avec détection mic |
| MUS-05 | SHOULD | Doigté indiqué 1 à 5, main droite/gauche, représentation visuelle du repositionnement ; avertissement si doigté non observable |
| MUS-06 | SHOULD | Clé de fa, les deux portées, gammes et intervalles, chromatisme et accords, selon curriculum |
| MUS-07 | SHOULD | Enchaînements main gauche et droite et micro-exercices de coordination ; 2 mains simultanées via MIDI prioritairement |
| MUS-08 | COULD | Éditeur/import de phrases, validation MusicXML/MIDI, autorisations sur les œuvres |

## E. Ergonomie, performances et qualité

| ID | Priorité | Exigence et critère d'acceptation |
| --- | --- | --- |
| UX-01 | MUST | Pixel art original : héros, trois familles d'ennemis, HUD clair ; la partition reste lisible et **n'est pas dégradée par la pixelisation** |
| UX-02 | MUST | Aucun bouton indispensable pendant que les deux mains jouent ; rejouer et pause accessibles par geste simple entre tours |
| UX-03 | MUST | Les états écoute / jeu / résultat / aide sont distinguables par forme, texte et animation (pas couleur seule) |
| UX-04 | SHOULD | Paramètres taille des notes, réduction animations, haptique/volume ; orientation et zones sûres testées |
| UX-05 | MUST | Prévenir affichage trompeur : si micro faible, afficher « Je n'entends pas clairement » au lieu de « faux » |
| NFR-01 | MUST | Séparation de l'audio temps réel, du moteur musical, des règles de combat et du rendu |
| NFR-02 | MUST | Fixtures audio et tests de contrat entre note détectée -> événement -> score -> partition |
| NFR-03 | MUST | Pas de réseau nécessaire, pas de cloud, pas de permissions superflues au MVP |
| NFR-04 | MUST | Bench matériel réel, version OS, modèle, placement, piano, taux d'échantillonnage et résultats publiés dans la recette |
| NFR-05 | SHOULD | Fluidité visée 60 FPS sans audio processing bloquant la boucle d'affichage ; pas d'underruns réguliers |
| NFR-06 | MUST | Crash, perte de micro, arrière-plan/avant-plan, Android refuse permission : gestion explicite et récupération |
| NFR-07 | MUST | Code source sous licence MIT du projet ; assets, SFX et fontes avec provenance et droits réutilisables |
| NFR-08 | SHOULD | APK de test archivé par CI, suite de tests automatisée, règles de format et de validation contenu |
| NFR-09 | MUST | Pas d'affirmation « reconnaissance des accords via micro » avant tests fonctionnels fiables, variés et répétés |

## F. Scénarios d'acceptation bout en bout (Gherkin simplifié)

**SC-01 — note juste** : étant donné un monstre demandant MI4 en mode Duo et un mic calibré ; quand une attaque MI4 claire est jouée une fois ; alors héros +1, monstre inchangé, note validée/surlignée puis étape suivante. Maintenir MI4 ne réattaque pas.

**SC-02 — vraie erreur** : étant donné SOL4 attendu ; quand FA4 stable est joué ; alors monstre +1 et indice facultatif, **une seule** fois. Un bruit fort ou un rire ne donne pas de point adverse.

**SC-03 — temps libre** : étant donné un monstre affiché et aucune note jouée pendant 60 s ; alors ni héro ni ennemi ne marquent et aucun chronomètre ne met fin au tour.

**SC-04 — défaite éducative** : si jauge adverse atteint le seuil ; alors animation « ralentissement/freeze » sans contrainte d'input ; mise en évidence sur la partition, visuel touche correspondante et doigt suggéré ; retry sans nouveau onboarding.

**SC-05 — apprendre l'oreille** : en mode Écho, après démo, le jeu masque le nom et la position de la note au clavier ; le joueur cherche puis peut demander de l'aide ; les résultats différencient jeu aidé/non aidé.

**SC-06 — écho acoustique** : le son produit par l'app elle-même lors d'une démo ne déclenche aucune réussite ; l'input n'est actif qu'après fin de la démo et garde anti-écho.

**SC-07 — microphone refusé** : l'app affiche une explication et un mode tactile, sans crash ni répétition incessante de la demande de permission.

**SC-08 — appareil réel** : test de latence/justesse sur cinq notes et bruit ambiant, fiche de recette remplie ; impossible de déclarer MVP validé sur la seule base du simulateur.

## G. Priorité et changements

Un changement incompatible avec un MUST exige mise à jour du présent cahier des charges, justification ADR et nouvelle recette. Les SHOULD/COULD ne bloquent pas le premier donjon mais ne doivent pas être annoncés comme livrés sans preuve. Pour la planification : `docs/08-ROADMAP.md` ; pour les indicateurs : `docs/09-VALIDATION.md`.
