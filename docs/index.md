---
title: Vue d'ensemble du projet
---

<style>
    @media screen and (min-width: 76em) {
        .md-sidebar--primary {
            display: none !important;
        }
    }
</style>

# Vignette

!!! info "Informations générales"
    **Session**: Été 2026  
    **Auteur(s)**: Lea Hemidj (20241318)  
    **Thème(s)**: Langues en danger, documentation linguistique, storyboard, audio, interfaces de création  
    **Superviseur(s)**: Louis-Edouard Lafontant (DIRO, Université de Montréal)  
    **Collaborateur(s):** <!-- Nom de(s) collaborateur(s) et partenaire(s) -->  

## Description du projet

### Contexte

De nombreuses langues dans le monde ne possèdent pas ou peu de forme écrite standardisée. Les linguistes, locuteurs natifs et organismes de revitalisation linguistique cherchent des outils adaptés pour documenter, enseigner et transmettre ces langues. Les outils existants sont souvent conçus pour des langues écrites et ne correspondent pas aux besoins spécifiques de la documentation orale et visuelle.

### Problématique

Comment permettre à des linguistes et à des communautés de locuteurs de créer des ressources pédagogiques structurées pour des langues à tradition orale, sans passer par une interface centrée sur le texte écrit ?

### Proposition et objectifs

Vignette est une application web de création de storyboards destinée à la documentation et à l’enseignement de langues peu ou pas écrites. Elle permet de :

- composer des scènes visuelles séquencées (storyboard) dans un studio de création;
- associer à chaque scène des voix (personnes A, B, C, etc.) avec leurs enregistrements audio;
- lier à chaque voix une glose (transcription, gloss et traduction libre);
- publier les scénarios directement depuis le studio.

### Méthodologie

Le développement est itératif et centré sur l’usage réel. Les fonctionnalités sont construites en partant des besoins concrets identifiés avec les collaborateurs linguistes. Chaque semaine, une partie du système (audio, storyboard, glose, interface) est développée, testée et ajustée.

### Validation et Évaluation

La validation se fait de façon fonctionnelle et itérative : chaque fonctionnalité est testée manuellement en parcourant des scénarios d'usage concrets (création d'un scénario, ajout de scènes, enregistrement audio, annotation linguistique). La prochaine étape sera de valider l'interface avec des utilisateurs réels pour s'assurer que l'outil est compréhensible sans explication préalable.

## Équipe

| Membre            | Rôle principal                                      |
|-------------------|-----------------------------------------------------|
| Lea Hemidj        | Frontend — studio de création (storyboard, audio)   |
| Ariane Laverrière | Frontend — interface communautaire                  |
| Marina Traoré     | Backend — API, base de données, gestion des langues |

## Échéancier

!!! info
    Le suivi complet est disponible dans la page [Suivi de projet](suivi.md).

| Activités                                        | Début   | Fin     | Livrable                                      | Statut         |
|--------------------------------------------------|---------|---------|-----------------------------------------------|----------------|
| Ouverture de projet                              | 4 mai   | 15 mai  | Proposition de projet                         | ✅ Terminé     |
| Études préliminaires                             | 4 mai   | 22 mai  | Document d'analyse                            | ✅ Terminé     |
| Fondations visuelles du studio                   | 1 mai   | 14 mai  | `storyboard.css` complet, palette warm, tuiles BD | ✅ Terminé  |
| Audio, voix et annotations linguistiques         | 14 mai  | 21 mai  | Gloss interlinéaire, Personnes A/B/C, play/pause | ✅ Terminé  |
| Interface de création et upload                  | 21 mai  | 28 mai  | Combobox langues, upload multi-fichiers, transitions | ✅ Terminé |
| Intégration backend et publication               | 28 mai  | 18 juin | Studio connecté à l'API, publication fonctionnelle | 🔄 En cours |
| Tests et ajustements                             | 18 juin | 10 juil | Retours d'usage, corrections, polish           | ⏳ À venir     |
| Préparation de la présentation                   | 10 juil | 7 aout  | Démo, rapport                                 | ⏳ À venir     |
| Présentation + Rapport final                     | 7 aout  | 14 aout | Présentation + Rapport                        | ⏳ À venir     |
