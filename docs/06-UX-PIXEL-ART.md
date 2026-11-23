# 06 — Direction artistique, UX et accessibilité

## Concept visuel
Une **fantasy musicale originale**, RPG 2D pixel art expressif inspiré des standards de qualité des JRPG 16-bit, sans copier les personnages, la musique ou les assets des jeux existants. Univers : **Archipel des Résonances**, îles musicales, Bosquet des Touches, Grottes des Échos, Archives des Runes. Palette de départ (à valider sur device) : nuit/prune et accents turquoise, or et corail. Personnages 24–48 px, animation 4–8 frames. Éviter parallaxe intense et particules pendant écoute.

**Contraste fondamental** : le monde est en pixel art, mais **la portée, les notes et le texte pédagogique sont dessinés avec des glyphes vectoriels nets** et accessibles (fontes SMuFL compatibles, p. ex. Bravura/OFL avec notice). Un écran posé à 40–80 cm sur un pupitre doit permettre d'identifier une note. L'UI musicale prime sur le spectacle.

## Écran de combat proposé (paysage)

~~~text
┌─────────────────────────────────────────────────────────────────────┐
│ ÎLE 1 • BOSQUET   ÉCHO • 2/3 notes   [Source : micro]  [Pause] [⚙]│
├──────────────────────────────────┬──────────────────────────────────┤
│  HÉROS                           │ MONSTRE SLIME   ▰▰▰▱ (erreurs)   │
│     onde musicale ———>           │           Sprite et réactions    │
├─────────────────────────────────────────────────────────────────────┤
│        CLÉ DE SOL • PORTÉE TRÈS LISIBLE • NOTE COURANTE            │
│     Révélation selon mode : ÉCHO / RUNE / DUO                      │
├─────────────────────────────────────────────────────────────────────┤
│ [Réécouter] [Indice]   « À toi de jouer » / « Micro trop bruyant »  │
│ [Clavier de référence / doigt 1..5] si indice explicitement ouvert │
└─────────────────────────────────────────────────────────────────────┘
~~~

En **Écho**, masquer note exacte, nom et touche avant essai/indice ; une surbrillance de curseur ou une portée vide reste visible pour expliquer le fonctionnement sans révéler l'objectif. En Rune, note complète et surbrillance, audio facultatif. En Duo, audio + note visible.

## Animations
- Juste : héros projette une onde lumineuse vers le monstre, +1 segment.
- Faux confirmé : monstre réplique, +1 à sa jauge ; aucune animation de punition sur bruit ou incertitude.
- Défaite : projectile ennemi en slow motion purement **cinématographique**, arrêt, apparition de la portée, clavier illustré, doigt conseillé et note de référence. Les scores sont gelés pendant cette scène. Réessayer immédiatement.
- Mode « réduire les animations » désactive flash, tremblement et ralentissement, mais conserve toutes les aides textuelles.

## Écrans (MVP)
Accueil; choix instrument/tactile; demande de permission contextualisée; calibration DO4/MI4/SOL4 et écran de diagnostic; prologue; petit hub de donjon; bataille en Écho/Rune/Duo; indice gradué; victoire et bilan d'apprentissage; défaite et temps suspendu; carnet de progression; paramètres accessibilité et son.

Flux sans friction : lancement → piano réel/tactile → permission mic justifiée → test bruit et notes → premier combat assisté DO4 → victoire en quelques interactions. Ne jamais redemander indéfiniment une permission refusée.

## Accessibilité, appareils, utilisabilité
- Texte plus lisible que police pixel de titre ; contrastes suffisants ; tailles de portée ×1/1.25/1.5 ; état reconnu/non reconnu exprimé en **texte et forme, pas couleur seule**.
- Pas de geste chronométré obligatoire, pas de défilement musical imposé au début.
- Commandes utilisables **entre** deux phrases avec les mains sur l'instrument ; grandes zones tactiles.
- Paramètres séparés pour volume de la démo, SFX et musique ; possibilité de couper BGM pendant micro.
- Supports visés : téléphones compacts, smartphones longs, tablette ; respecter découpes/encoches et orientation choisie ; mesures sur écran de smartphone posé à distance réelle.
- Lecteur de contenu en français initialement ; notation Do Ré Mi ou C D E ; octave affichée explicitement.

## Design system / assets
Définir des tokens pour palette, tailles, marges, focus, animations et HUD, avec quelques composants réutilisables. Sprite original pour héros, slime tonal, chauve-souris écho et golem de portée ; quelques paysages et animations de combat. Chaque asset a `source`, `auteur`, `licence` et `usage` dans l'inventaire, avec fichiers sources éditables lorsqu'ils sont redistribuables. Le build ne doit pas inclure d'assets « trouvés sur Google ».

## Recette visuelle
Captures comparables : permission/échec de permission, calibration, état du micro silencieux vs actif, mode Écho réponse masquée, Rune réponse visible, juste, faux, victoire, défaite, taille portée maximale, animations réduites, écran 16:9 et 20:9. La position des notes sur la portée est validée contre le modèle MIDI et la clef, pas seulement « jolie ».
