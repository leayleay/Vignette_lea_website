---
title: Études préliminaires
---

<style>
    @media screen and (min-width: 76em) {
        .md-sidebar--primary {
            display: none !important;
        }
    }
</style>

# Études préliminaires

## Compréhension du problème

Le projet Vignette s'adresse aux linguistes et aux communautés qui travaillent sur des langues à tradition orale. La plupart des outils existants sont pensés pour des langues écrites, mais pour des langues sans forme écrite standardisée, le point de départ c'est l'oral et le visuel.

Le besoin : créer des ressources pédagogiques à partir d'images et d'enregistrements, avec pour chaque voix une annotation linguistique complète (transcription, gloss morphème par morphème, traduction libre). C'est le format interlinéaire standard en linguistique de terrain.

Mon rôle c'est la partie studio : l'interface de création de storyboard, le panneau audio, la glose. Le reste de l'app (backend, auth, gestion des scénarios, publication) était déjà en place quand j'ai commencé.

---

## Analyse des solutions existantes

J'ai regardé ce qui existe avant de commencer à coder. Deux catégories d'outils sont pertinentes ici.

### Outils de documentation linguistique

**ELAN** est l'outil le plus utilisé en linguistique de terrain. Il fait des annotations multi-niveaux synchronisées sur de l'audio et de la vidéo, avec transcription, gloss et traduction. Le problème c'est que c'est du desktop, l'interface est vraiment complexe, et il n'y a aucune notion de scène visuelle. C'est clairement un outil pour chercheurs.

**FLEx** (FieldWorks Language Explorer) est orienté lexique et interlinéarisation, bien pour la morphologie, mais encore une fois desktop et centré sur le texte.

**SayMore** est plus simple, conçu pour les premières étapes de documentation avec un workflow d'enregistrement et de transcription. Mais pas d'élément visuel, pas de scènes, desktop seulement.

**Aikuma** c'est une app mobile pour l'enregistrement communautaire et la traduction orale. Bien pour la collecte sur le terrain mais pas d'annotation linguistique complète et aucune composante visuelle.

En gros ces outils sont soit trop complexes pour des non-spécialistes, soit limités au desktop, et aucun propose une interface visuelle séquencée.

### Outils de création de storyboard

Les outils de storyboard classiques (Boords, Storyboarder, Canva, Adobe Firefly Boards) sont faits pour la production vidéo ou le design et n'ont aucune notion d'annotation linguistique. Le plus proche c'est **Storyboard That** qui permet d'enregistrer de l'audio par scène (60 secondes max), mais c'est très basique et il n'y a rien pour les gloses.

### Ce qui manque

Aucun outil combine les trois choses dont on a besoin :

1. Composition visuelle séquencée (scènes avec images, réorganisables)
2. Enregistrement et gestion audio par locuteur dans une scène
3. Annotation interlinéaire par voix (transcription, gloss, traduction)

L'option la plus proche serait ELAN combiné avec un outil de storyboard, mais jongler entre deux apps sans intégration c'est pas viable pour un usage pédagogique ou communautaire.

---

## Contraintes et besoins

### Techniques

- L'app est une SPA Vue.js, mon travail se fait entièrement côté frontend dans les composants Vue et les fichiers CSS.
- Ça doit fonctionner dans un navigateur sans installation, pour que l'outil soit accessible même hors d'un contexte académique.
- Le studio doit être visuellement séparé du reste de l'app, c'est un espace de création à part.
- La base de langues contient plus de 8000 entrées, la sélection doit être rapide, une pagination manuelle c'est pas utilisable.

### Humaines

- L'outil doit être utilisable par des linguistes mais aussi par des locuteurs natifs ou des enseignants communautaires qui ne sont pas forcément à l'aise avec des interfaces complexes.

### Temporelles

- Le projet c'est un trimestre (mai–août 2026), le studio doit être fonctionnel avant la présentation finale en août.

### Organisationnelles

- Le développement est itératif, chaque semaine une partie est construite et ajustée. Les décisions architecturales prises au début (palette, structure CSS, nommage) doivent tenir sur la durée.

---

## Explorations techniques et conceptuelles

Avant de coder j'ai réfléchi à la direction visuelle et à l'organisation des composants.

### Direction artistique

La question c'était : à quoi doit ressembler le studio ? L'app avait déjà un design mais le studio devait se sentir différent, comme un espace de travail créatif. J'ai testé quelques pistes avant de partir sur une direction **BD/comic** avec une palette warm (beige, brun, terracotta). Ça fait écho au côté artisanal de la documentation de langues orales et ça donne une identité claire par rapport au reste de l'app.

### Organisation du CSS

L'ancienne version de `storyboard.css` faisait 67 lignes, quelques styles basiques sans structure. J'ai organisé par préfixes de composants :

- `.vg-*` pour la structure globale du studio (viewer, navigation, onglets)
- `.bd-*` pour la barre d'outils du storyboard
- `.studio-*` pour le layout trois colonnes de l'éditeur
- `.v-*` pour les composants voix/locuteur
- `.mic-*` pour l'interface d'enregistrement

### Grille de storyboard

L'ancienne grille utilisait un nombre de colonnes variable. J'ai préféré une grille fixe 12 colonnes avec rangées de 56px, plus prévisible et plus proche d'une vraie mise en page BD, avec la possibilité de faire des scènes de taille variable.

### Annotation interlinéaire

Pour la glose j'ai suivi le format interlinéaire standard en linguistique de terrain : transcription, gloss morphologique, traduction libre. C'est aussi ce qu'utilisent ELAN et FLEx, donc les linguistes qui connaissent ces outils retrouveront quelque chose de familier.

---

## Choix retenus

**Palette warm BD** : pour l'identité visuelle du studio et pour le séparer clairement du reste de l'app. Les couleurs sont en variables CSS pour pouvoir les changer facilement.

**Organisation CSS par préfixes** : pour que le code reste lisible à mesure que le fichier grandit.

**Grille 12 colonnes, rangées 56px** : pour des layouts BD variés sans complexité supplémentaire.

**Format interlinéaire standard** : pour la compatibilité avec les pratiques existantes en linguistique de terrain.

**Combobox avec debounce** : pour remplacer le `<select>` paginé et rendre la sélection parmi 8000+ langues utilisable.

**Upload multi-fichiers avec drag & drop** : importer plusieurs images en une fois est beaucoup plus naturel que les ajouter une par une.

---

## Références

- [ELAN (MPI Nijmegen)](https://archive.mpi.nl/tla/elan)
- [FieldWorks Language Explorer / FLEx (SIL)](https://software.sil.org/fieldworks/)
- [SayMore (SIL)](https://software.sil.org/saymore/)
- [Aikuma Project](https://www.aikuma.org/technologies)
- [Boords](https://boords.com/)
- [Storyboard That](https://www.storyboardthat.com/)
- [Leipzig Glossing Rules](https://www.eva.mpg.de/lingua/resources/glossing-rules.php)
