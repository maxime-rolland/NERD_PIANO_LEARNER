# 11 — Confidentialité, propriété intellectuelle et publication

## 1. Principes de conception
MVP **hors ligne**, sans compte, télémétrie ni serveur, sans publicité ni SDK tiers de tracking. Le microphone sert à comparer des hauteurs musicales **en local**. **Aucun audio brut stocké par défaut**, ni envoyé sur réseau. Les événements de notes et les statistiques restent locaux. Toute future option d'export WAV de diagnostic nécessite une action explicite et permet suppression. Vérifier la déclaration de traitement et les mentions de confidentialité au regard du produit distribué, particulièrement si de futures fonctions cloud sont ajoutées.

## 2. Android et Google Play
- Déclarer `RECORD_AUDIO` uniquement pour le mode mic, avec explication au moment de la demande ; refus n'interdit pas le mode tactile.
- Vérifier permission et comportement versions Android ciblées, route micro, indicateur système, fonctionnement en arrière-plan (captation **arrêtée** à la sortie du jeu).
- Ne demander ni localisation ni contacts ni accès fichiers sans besoin. Les règles Google Play / target SDK doivent être revérifiées **au moment de publier**.
- Si le jeu vise des enfants : reconsidérer politique sur données, consentement, publicité, profils et obligations de catégorie d'âge. Ne pas supposer qu'une absence de cloud exclut toutes obligations légales.

## 3. Œuvres musicales
- **Code du projet MIT** déjà présent : conserver avis de copyright, en-têtes applicables et attribution.
- Écrire et composer des phrases/mélodies **originales** pour le jeu, ou utiliser une œuvre effectivement dans le **domaine public** avec provenance et vérification par pays ; un arrangement ou un enregistrement précis peut avoir ses propres droits.
- Un utilisateur peut importer ses propres fichiers **uniquement dans une version future** avec avertissement sur droits. Aucun scraping YouTube/Spotify et aucune promesse de transcrire/exploiter commercialement un catalogue protégé.
- Éviter samples de piano, sprites, maps, animations et fonts piochés sans licence ; documenter source, auteur, licence, conditions de redistribution dans `THIRD_PARTY_NOTICES.md`.
- Pour Bravura, respecter la licence **SIL OFL 1.1** distincte de MIT : conserver notices et ne pas transférer la police dans le domaine public. Ne jamais livrer des fonts sous un en-tête MIT erroné.

## 4. Sécurité et données locales
- Valider tous imports de données musique et progression ; plafonner tailles et valeurs ; écriture de sauvegarde atomique / migration.
- Ne pas intégrer de secret API, de keystore release, d'analytics dissimulé ni de mécanisme d'appel réseau.
- Les logs de débogage audio peuvent révéler du matériel/OS ; exporter seulement sur action et anonymiser si partage.
- Une extension native Android se limite au micro/MIDI nécessaires : aucune permission cachée.

## 5. Publication
Avant Google Play, prévoir : test appareils, stabilité de release, politique de confidentialité réellement conforme au comportement, déclaration de sécurité des données, âge cible, licences d'assets, analyse des exigences Play du moment, AAB signé, support et procédure de suppression/export des données locales.

Ce document pose des **exigences produit**, pas une consultation juridique définitive. Les exigences règlementaires sont à revalider lors de la distribution.
