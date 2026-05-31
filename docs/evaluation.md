---
title: Évaluation & Discussion
---

<style>
    @media screen and (min-width: 76em) {
        .md-sidebar--primary {
            display: none !important;
        }
    }
</style>

# Évaluation

## Méthodes de validation

Le projet n'est pas encore terminé, donc l'évaluation est encore partielle. Pour l'instant la validation se fait surtout de façon fonctionnelle : est-ce que ce que j'ai construit marche comme prévu ?

Concrètement j'ai testé chaque fonctionnalité au fur et à mesure en parcourant les scénarios d'usage concrets :

- créer un scénario, ajouter des scènes, réorganiser les tuiles
- enregistrer une voix, l'associer à une scène, ajouter un gloss
- placer un marker sur une image et vérifier que la position s'affiche correctement
- tester l'upload multi-fichiers avec drag & drop
- vérifier que la transition entre les vues est fluide

C'est du test manuel pour l'instant, pas de tests automatisés côté frontend. Les bugs que j'ai trouvés et corrigés (voir Difficultés rencontrées) sont tous apparus pendant ces sessions de test.

Pour la suite, l'idée serait d'avoir des retours d'utilisation réels avec des personnes qui utilisent l'outil pour un vrai scénario de langue. Ça permettrait de valider si l'interface est compréhensible pour quelqu'un qui n'a pas participé au développement.

---

## Résultats obtenus

Ce qui fonctionne à ce stade :

- Le studio s'affiche correctement avec la palette warm et le style BD
- On peut créer un scénario, choisir une langue parmi 8000+ avec le combobox, et arriver dans le studio
- L'upload multi-fichiers fonctionne, les scènes apparaissent dans la grille
- Les voix s'assignent automatiquement (Personne A, B, C...) et le bouton play/pause est cohérent dans toute l'interface
- Le formulaire de gloss s'ouvre, les trois champs (transcription, gloss, traduction) se sauvegardent et s'affichent correctement
- Les markers se placent sur l'image et la position est bien calculée
- Les transitions entre les vues du studio sont animées

Ce qui n'est pas encore validé :

- L'enregistrement audio d'urgence (EmergencyAudioRecorder) dans tous les cas limites
- Le comportement du studio sur mobile
- La publication complète d'un scénario depuis le studio
- Les performances avec un scénario qui a beaucoup de scènes et d'audios

---

## Analyse critique

Ce qui fonctionne bien c'est la direction que le studio a prise visuellement. L'interface est cohérente, le style BD donne une identité claire, et les fonctionnalités de base (ajout de scènes, audio, gloss) sont utilisables.

Ce qui manque encore c'est surtout la validation par des utilisateurs réels. Tout ce que j'ai testé c'est moi qui utilise l'outil en sachant exactement comment il marche, ce qui n'est pas vraiment représentatif. Il faudrait voir si un linguiste qui découvre l'interface comprend comment créer un scénario sans explication.

Le format interlinéaire (transcription / gloss / traduction) est bien implémenté techniquement, mais je ne sais pas encore si les labels dans l'interface sont assez clairs pour quelqu'un qui ne connaît pas cette notation.

---

## Limites du projet

**Temps.** Le projet se termine en août et il reste encore des fonctionnalités à implémenter et à tester. Certaines parties du studio sont encore en mode sandbox (données locales, sans appel API réel) ce qui veut dire qu'elles ne sont pas encore connectées au backend.

**Tests utilisateurs.** Faute de temps, les retours d'usage réels n'ont pas encore eu lieu. L'évaluation reste pour l'instant basée sur les scénarios imaginés en début de projet.

**Mobile.** L'interface du studio n'a pas été optimisée pour les petits écrans. La grille trois colonnes et le panneau audio latéral sont pensés pour du desktop.

**Accessibilité.** L'interface n'a pas été auditée pour l'accessibilité. Certains éléments interactifs (markers, poignées de redimensionnement) seraient probablement difficiles à utiliser au clavier.
