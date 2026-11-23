# 12 — Développement autonome avec Claude Code / Opus 5.5

## Objectif
Livrer un **premier donjon jouable Android** avec microphone fiable sur piano réel, sans supervision quotidienne de détails triviaux. La doc fait foi, les preuves commandent l'avancement, les blocages de matériel ne se « résolvent » pas fictivement.

## Modèle d'exécution
- **CLAUDE.md bref** = règles fondamentales en contexte constant.
- **Skill `/goal`** = procédure à la demande, conservée dans `.claude/skills/goal/SKILL.md` ; les interfaces de custom skills de Claude Code changent parfois, vérifier la version installée.
- **Roadmap `08`** = backlog avec IDs stables, dépendances, critères de sortie.
- **Validation `09`** = preuves et gates réels, non affirmations.
- **ADR `13`** = arbitrages techniques et risques non résolus.
- **Git commits / PRs** = points de reprise d'un contexte à l'autre.

Cette structure rejoint les conseils officiels Anthropic (explore → plan → code → verify, hooks déterministes et subagents ciblés) et les expériences publiques de développeurs consultées dans `10-BENCHMARK-SOURCES.md`.

## Boucle à exécuter sur chaque ticket
1. **Inspecter** : `git status`, docs, fichier cible, dernière recette, dépendances et risques ; détecter travail déjà fait pour éviter de le refaire.
2. **Planifier brièvement** : ticket ID, fichiers attendus, invariants, scénarios positifs/négatifs, test exécutable.
3. **Implémenter** une tranche cohérente : code lisible et typé, pas d'abstraction prématurée, pas de nouvelles features non requises.
4. **Prouver** : exécuter tests et scripts disponibles, inspecter logs et captures, vérifier comportement négatif ; ne pas modifier un test pour cacher un défaut.
5. **Revoir** : petit contrôle d'architecture et jeu/pédagogie via autre sous-agent si impact significatif ; corriger objections fondées.
6. **Consigner** : mise à jour roadmap (TODO/IN_PROGRESS/PASS/BLOCKED), `09`, ADR si nécessaire ; commit précis.
7. **Poursuivre** avec le prochain ticket sans redemander validation sur les choix mineurs. Arrêter seulement pour risque irréversible, accès manquant ou mesure physique impossible.

## Gardes strictes
- Ne pas utiliser `--dangerously-skip-permissions` ni modifier des politiques d'autorisation sans accord explicite. Ne pas inventer d'exécution « illimitée » ; la session s'arrête quand l'outil ou le système l'impose.
- Pas de push force, suppression de dépôt, publication Google Play, dépenses de cloud ou envoi de données personnelles sans décision humaine.
- Pas de tests hardware inventés. Une fixture audio et une exécution headless n'équivalent jamais à une note jouée sur un smartphone posé près du piano.
- **Micro G0 bloquant** avant production de grande quantité de graphismes ; si indisponible, implémenter le cœur testable et signaler BLOQUÉ au gate, sans déclarer DONE.
- Contrôler que chaque phrase d'exemple enseignée puis évaluée possède un sens musical ; une API qui passe des tests n'est pas nécessairement un cours efficace.
- Le rythme n'est pas obligatoire, les bonnes/mauvaises notes n'existent que sur `NoteOn` confirmé. Ne jamais classer le silence en mauvaise note.
- Vérifier sources/versions/licences et éviter toute dépendance non maintenue ou GPL incompatible avec distribution MIT sans analyse de compatibilité.

## Parallelisation et économie de contexte
- Dépôt vide : **un agent principal**, recherche courte par sous-agent pour vérifier API Godot/DSP/licences.
- Après G0 et contrats stables : déléguer en parallèle seulement des fichiers/sous-domaines disjoints (p. ex. contenu, sprites originaux, tests de modèle), idéalement en worktrees indépendants et PRs. **Sérialiser** modifications au moteur audio et au contrat musical partagés.
- Résumé durable de fin de session : ticket terminé, commit, tests réellement lancés, problèmes restants, action suivante, risques, état des outils. Reprendre en relisant le dépôt, ne pas supposer que le contexte précédent survit.
- Préférer un cycle « test ciblé → implémentation → test → intégration » au lancement d'une suite de tests longue sans nécessité.

## Exemple de compte rendu de tranche
~~~md
### P0-03 — DSP de notes isolées — statut PASS / PARTIAL / BLOCKED
- Commit : <sha>
- Fichiers modifiés :
- Scénarios vérifiés :
- Commandes exécutées et sorties :
- Preuves réelles (téléphone, piano, n attaques, dates) :
- Tests non exécutés et raison :
- Limitations et risque résiduel :
- Prochaine tâche :
~~~

## Condition finale du mandat initial
Il doit exister une APK de debug installable et un parcours complet avec le piano **réel**, **seulement si** les mesures G0 et G1 ont réellement pu être effectuées. Sinon, livrer le code, les tests et un rapport **BLOCKED HARDWARE** explicitant ce que l'humain doit mesurer avant de juger le produit utilisable. Ne jamais substituer un succès virtuel à une validation réelle.
