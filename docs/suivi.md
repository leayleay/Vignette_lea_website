---
title: Suivi du projet
---

<style>
    @media screen and (min-width: 76em) {
        .md-sidebar--primary {
            display: none !important;
        }
    }
</style>

# Suivi de projet

> :bulb: Cette page documente l'évolution du projet dans le temps.
> Elle sert à rendre visibles les décisions, ajustements et apprentissages.
> Les entrées peuvent être hebdomadaires ou bi-hebdomadaires.  
> N'oubliez pas d'effacer ou de mettre en commentaires les notes (`>`) avant la remise finale.

---

## Semaine 1 (1–14 mai 2026)

### Travail réalisé — `storyboard.css` : transformation complète

C'est la semaine où j'ai posé les fondations visuelles du studio. L'ancienne version de `storyboard.css` faisait environ 67 lignes — des styles très basiques, sans vraie identité. J'ai tout reconstruit pour arriver à 3521 lignes. Ce n'est pas du gonflement : chaque section correspond à un composant réel du studio.

---

#### Système de couleurs warm

La première décision a été de définir une palette propre au studio, distincte du reste de l'application. J'ai choisi une direction beige/brun/terracotta — "warm" — pour donner au studio une atmosphère différente, plus artisanale, qui fait écho au travail de documentation de langues orales. Ce n'est pas juste esthétique : ça permet aussi de clairement délimiter visuellement l'espace du studio par rapport au reste de l'interface.

Les couleurs principales codées en dur comme variables :

| Variable | Valeur | Rôle |
|---|---|---|
| fond général | `#fbf5ec` | beige très clair, fond de base |
| fond éléments | `#fff8f0` | blanc cassé, fonds de panneaux |
| brun foncé | `#3d2818` | bordures, textes forts, style BD |
| terracotta | `#e07856` | accents, boutons actifs, badges |

Ces couleurs sont utilisées partout dans les composants `.vg-*`, `.bd-*`, `.studio-*`, `.v-*` et `.mic-*`. Le fait de les définir comme variables en tête de fichier permet de changer toute la palette d'un coup si on décide d'ajuster le thème plus tard.

---

#### Composants `.vg-*` — le viewer/éditeur studio

Le préfixe `.vg-` (pour *vignette*) regroupe tout ce qui concerne la structure globale du studio : le container, la navigation interne, les onglets, et le bouton de publication.

- **`.vg-root`** — c'est le container racine du studio. Il applique la palette warm sur tout ce qui est à l'intérieur, de façon isolée. Ça permet de ne pas polluer les styles globaux de l'app.
- **`.vg-nav`** — la barre de navigation interne du studio. Elle contient le logo à gauche, les onglets au centre, et le bouton "Publier" à droite. Elle est indépendante de la nav principale de l'app.
- **`.vg-brand` + `.vg-brand-dot`** — le logo du studio avec son point orange caractéristique. Le point est un élément séparé pour pouvoir l'animer ou le changer de couleur indépendamment.
- **`.vg-tabs` / `.vg-tab`** — les onglets qui permettent de naviguer entre les vues du studio (Storyboard, Audio, etc.). L'onglet actif a un fond brun et du texte blanc, les autres sont transparents avec du texte brun.
- **`.vg-pub`** — le bouton "Publier". Arrondi, fond brun `#3d2818`, texte blanc. Il est placé à droite de la nav pour être accessible en permanence pendant le travail.
- **`.vg-view--with-recorder`** — un modificateur qui passe la vue principale en grille 2 colonnes (contenu + panneau enregistrement). S'active dynamiquement quand le studio audio est ouvert.

---

#### Barre d'outils `.bd-*`

La barre d'outils au-dessus de la grille de storyboard. Le préfixe `.bd-` vient de *board*.

- **`.bd-toolbar`** — la barre elle-même, fond `#fff8f0`, légèrement surélevée par rapport au fond de page. Contient tous les contrôles de la grille.
- **`.bd-add`** — le bouton "Ajouter une scène". Bordure en pointillé orange, fond transparent. Le style pointillé signale visuellement que c'est un espace à remplir, pas un élément déjà là.
- **`.bd-presets` / `.preset-btn`** — une série de petits boutons pour choisir une mise en page prédéfinie (1 colonne, 2 colonnes, libre, etc.). Chaque `preset-btn` affiche une icône représentant la grille correspondante.
- **`.bd-hint`** — texte d'aide discret à droite de la barre, qui donne des indications contextuelles à l'utilisateur selon l'état du studio.

---

#### Layout studio `.studio-*`

C'est la structure principale de l'éditeur, en trois colonnes.

- **`.studio-wrap`** — le container flex principal, hauteur fixe de 580px. Cette hauteur fixe est importante : elle garantit que les trois colonnes restent alignées et visibles sans scroll vertical sur la plupart des écrans.
- **`.studio-list`** — sidebar gauche de 200px. Elle affiche la liste des scènes sous forme de petites cartes. Scrollable indépendamment du reste.
- **`.studio-center`** — zone centrale, scrollable horizontalement. C'est là que se trouve la grille de storyboard.
- **`.studio-audio`** — sidebar droite de 230px. Elle contient le panneau d'enregistrement audio. Sa largeur est légèrement plus grande que la liste gauche parce que les contrôles audio prennent plus de place.
- **`.s-item`** — une carte de scène dans la liste gauche. Elle affiche une miniature de l'image de la scène et ses métadonnées (titre, durée). Un clic sur `.s-item` sélectionne la scène et met à jour `.s-hero` au centre.
- **`.s-hero`** — la grande image de la scène actuellement sélectionnée, affichée en bas de la zone centrale. Hauteur 160px, bordure brun 3px, avec un reflet radial subtil en surimpression pour donner de la profondeur.
- **`.s-title-input`** — le champ de titre de la scène, directement inline sur l'image. Sans bordure visible à l'état normal, bordure fine qui apparaît au focus. L'idée est de pouvoir éditer le titre sans sortir du contexte visuel.

---

#### Voix `.v-*`

Les composants qui représentent les voix (personnes) dans le panneau latéral.

- **`.v-item`** — une ligne représentant une voix. Affiche le badge de la personne, la mini waveform, et le bouton play. Le fond change légèrement au hover et quand la voix est sélectionnée.
- **`.v-sp`** — le badge rond qui identifie le speaker. `.v-sp.a` est orange (Personne A), `.v-sp.b` est vert (Personne B), et ainsi de suite. Chaque couleur est fixe pour qu'on reconnaisse visuellement les personnes dans toute l'interface.
- **`.v-w`** — une mini waveform statique faite de barres de hauteur variable en orange. Ce n'est pas une vraie waveform audio dynamique, c'est une représentation visuelle qui signale qu'un audio est associé à cette voix.
- **`.v-p`** — le bouton play minuscule, rond, à droite de chaque voix. 24px de diamètre, fond blanc, icône triangulaire. Passe en icône pause quand l'audio joue.

---

#### Micro `.mic-*`

L'interface d'enregistrement au micro, dans la sidebar droite.

- **`.mic-rings`** — trois cercles concentriques qui pulsent en animation CSS quand l'enregistrement est actif. Ça donne un feedback visuel immédiat que le micro capte du son. Les cercles s'arrêtent complètement quand l'enregistrement est inactif.
- **`.mic-btn`** — le bouton principal d'enregistrement. Grand, circulaire, gradient orange. Quand l'enregistrement est actif, il passe à un fond rouge avec le texte "STOP" et une ombre pulsante. Le changement de couleur et d'ombre est important pour qu'on sache qu'on est en train d'enregistrer sans avoir à lire un texte.
- **`.mic-tm`** — le timer d'enregistrement affiché en "0:00". Utilise `font-variant-numeric: tabular-nums` pour que les chiffres ne bougent pas lateralement pendant le décompte.
- **`.mic-st`** — le texte d'état sous le bouton : "Appuyer pour enregistrer" en gris par défaut, qui passe à "Enregistrement" en orange quand le micro est actif.

---

#### Redesign complet des tuiles `.storyboard-tile`

C'est le changement le plus visible. L'ancienne version ressemblait à n'importe quel outil de storyboard générique avec des styles bleus. La nouvelle version a une direction artistique assumée : **style comic/BD**, avec des bordures épaisses, des ombres décalées, et des fonds de couleur chaude.

| Propriété    | Ancienne version                       | Nouvelle version                                            |
|--------------|----------------------------------------|-------------------------------------------------------------|
| Bordure      | `1px solid var(--border)` (gris fin)  | `3px solid #3d2818` (brun épais, style BD)                 |
| Ombre        | aucune                                 | `2px 2px 0 #3d2818` (ombre décalée, aussi style BD)        |
| Hover        | aucun effet                            | `translate(-1px, -1px)` + ombre agrandie à `4px 4px`       |
| Background   | gradient bleu clair uniforme           | 5 gradients warm alternés (rose, vert sauge, violet, jaune) |
| Bouton play  | bas-droite, fond sombre semi-transparent, 42px | haut-droite, fond blanc, 24px, style flat et discret |
| Overlay info | haut-droite                            | haut-gauche                                                 |

L'effet hover (`translate` + ombre plus grande) donne l'impression que la tuile "se soulève" légèrement quand on la survole. C'est cohérent avec le style BD où les cases ont du relief.

Les 5 gradients de fond warm alternent automatiquement selon l'index de la tuile via `:nth-child()`. Chaque scène a donc visuellement sa couleur, ce qui aide à les distinguer dans la grille sans avoir à lire les titres.

**Nouveaux éléments ajoutés sur les tuiles :**

- **`.storyboard-tile__caption`** — un bandeau en bas de chaque tuile avec le titre de la scène et ses métadonnées (durée, nombre de voix). Bordure top brun qui le sépare de l'image. Ce bandeau n'existait pas dans l'ancienne version.
- **`.storyboard-tile__delete`** — bouton ✕ en haut à droite qui apparaît au hover. Fond blanc, bordure brun, pour supprimer la scène. Intentionnellement discret à l'état normal pour éviter les suppressions accidentelles.
- **`.storyboard-tile__order-tools`** — deux boutons fléchés (← →) qui apparaissent au hover pour réordonner les scènes. Ils n'apparaissent que si la grille est en mode libre (pas en mode preset fixe).
- **`.storyboard-tile__handle`** (right / bottom / corner) — trois poignées de redimensionnement sur le bord droit, le bord bas et le coin bas-droit. Visibles uniquement en mode CUSTOM. Permettent de créer des tuiles de taille variable (ex : une scène qui prend 2 colonnes et 1 rangée).
- **`.storyboard-tile__size-badge`** — un petit badge "2×1" qui apparaît pendant qu'on redimensionne une tuile, pour indiquer sa taille en colonnes × rangées. Disparaît dès qu'on lâche.

---

#### Grille `.storyboard-grid` repensée

L'ancienne grille utilisait un nombre de colonnes variable défini par une variable CSS `--storyboard-columns`, avec des rangées libres. Ça semblait flexible mais ça rendait les layouts imprévisibles.

La nouvelle grille est une **grille 12 colonnes avec rangées fixes de 56px**, comme un système de grille typographique :

| | Ancienne version | Nouvelle version |
|---|---|---|
| Colonnes | `repeat(var(--storyboard-columns, 3), 1fr)` — variable libre | `repeat(12, 1fr)` — grille fixe 12 colonnes |
| Rangées | hauteur automatique selon le contenu | `grid-auto-rows: 56px` — rangées de 56px |
| Contrôle layout | une seule variable | chaque tuile peut occuper N colonnes × M rangées |

Le passage à 12 colonnes permet de faire des layouts où une scène occupe 4 colonnes (1/3 de la largeur), une autre en occupe 6 (moitié), etc. — exactement comme une mise en page de BD professionnelle.

---

## Semaine 2 (14–21 mai 2026)

*Notes de développement prises au fur et à mesure — Partie audio / voix / glose / enregistrement / éditeur Couper*

**Définitions de travail établies en début de semaine**

Avant de corriger les bugs, j'ai pris le temps de clarifier ce que signifient les termes qu'on utilise partout dans le code :

- **une voix / personne** : Personne A, B, C, etc. — une voix représente un locuteur dans une scène.
- **un audio** : le fichier enregistré ou importé associé à une voix — c'est le son concret.
- **une glose** : les métadonnées linguistiques attachées à une voix : transcription phonétique, gloss morphologique, et traduction libre.

Une fois que j'ai eu ces définitions claires en tête, presque tous les bugs existants sont devenus beaucoup plus évidents à comprendre et à corriger. Beaucoup d'incohérences venaient du fait qu'on mélangeait "voix" et "audio" dans le code.

---

### 1. Le bouton Play / Pause

**Problème :** quand un audio était en lecture, le bouton restait figé sur l'icône triangle play. Il n'y avait aucun retour visuel pour savoir si l'audio jouait ou non. C'était particulièrement confus quand plusieurs voix étaient visibles en même temps — impossible de savoir laquelle était active.

**Cause :** l'état de lecture n'était pas correctement propagé au composant du bouton. Le bouton lisait une propriété statique au lieu d'observer l'état réactif de l'audio en cours.

**Correction :** j'ai revu le comportement pour couvrir tous les cas possibles :

- quand aucun audio ne joue → le bouton affiche le triangle play ;
- quand l'audio de cette voix joue → le bouton affiche l'icône pause (deux barres) ;
- si on clique pendant la lecture → ça met en pause au lieu de relancer depuis le début ;
- si la voix est un draft (aucun audio enregistré ou importé) → le bouton n'essaie pas de jouer, il déclenche l'action d'enregistrement à la place.

Ce comportement est maintenant cohérent dans les trois endroits où le bouton apparaît : dans les fiches de scène, dans le panneau audio latéral, et dans les tuiles du storyboard.

Les trois fonctions qui gèrent ça :

```js
isAudioPlaying(audio, thumb)       // retourne true si cet audio précis est en lecture
toggleAudioPlayback(audio, thumb)  // play si arrêté, pause si en lecture
toggleSelectedAudioPlayback()      // même chose mais pour la voix actuellement sélectionnée
```

Le paramètre `thumb` permet de savoir dans quel contexte le bouton est appelé (tuile du storyboard vs panneau audio), car la logique de sélection de scène change légèrement selon l'endroit.

---

### 2. Les personnes A, B, C

**Problème :** les voix existaient dans le code comme de simples enregistrements audio sans identité de locuteur. Il n'y avait aucune notion de "Personne A", "Personne B", etc. Du coup dans l'interface, toutes les voix avaient l'air identiques — pas moyen de savoir qui parle dans quelle scène.

**Ce que je voulais :** que la première voix d'un scénario soit automatiquement Personne A, la deuxième Personne B, la troisième Personne C, et ainsi de suite — sans que l'utilisateur ait à les nommer manuellement. C'est important pour les linguistes qui travaillent avec des langues où les locuteurs ont des rôles définis dans un dialogue ou une scène.

**Solution :** j'ai ajouté une logique d'attribution automatique basée sur l'ordre des voix dans la scène. Les états ajoutés :

```js
const selectedVoiceId = ref(null)      // l'ID de la voix actuellement active
const selectedVoice = computed(...)    // l'objet voix complet correspondant
const selectedSpeaker = ref("A")       // la lettre de la personne (A, B, C...)
```

La lettre du speaker est calculée automatiquement selon la position de la voix dans la liste : la première voix de la scène est toujours A, la deuxième toujours B, etc. Si une voix est supprimée, les lettres se réattribuent automatiquement.

Quand on clique sur une voix, `selectVoice(audio, thumb)` est appelé. Cette fonction :

- sélectionne la scène correspondante (si on vient d'une tuile du storyboard) ;
- sélectionne la voix dans l'état `selectedVoiceId` ;
- met à jour `selectedSpeaker` avec la lettre correspondante ;
- ouvre le panneau audio si nécessaire.

---

### 3. `AudioPanel.vue` — ajouts fonctionnels

#### Gloss linguistique

C'est la fonctionnalité la plus importante de la semaine du point de vue linguistique. Une glose permet d'annoter chaque audio avec trois niveaux d'information :

1. **Transcription** — la représentation phonétique ou orthographique de ce qui est dit.
2. **Gloss morphologique** — l'annotation morphème par morphème (ex : `1SG-PRES-like-FV` pour "j'aime" en notation interlinéaire).
3. **Traduction libre** — la traduction naturelle dans la langue cible.

Ce format suit la convention interlinéaire standard utilisée en linguistique de terrain. C'est central au projet Vignette : sans ça, les scénarios ne seraient que des enregistrements sans structure linguistique exploitable.

**Implémentation :**

- Un bouton "Gloss" apparaît sur chaque audio (visible uniquement au propriétaire du scénario).
- Il ouvre un formulaire inline avec les trois champs.
- Les trois fonctions qui gèrent le cycle de vie du gloss :

```js
beginGlossEdit(audioId)    // ouvre le formulaire pour cet audio
cancelGlossEdit()          // ferme sans sauvegarder
saveGlossEdit(audioId)     // sauvegarde et appelle updateAudioGloss()
```

- `updateAudioGloss()` envoie les données au backend et met à jour l'état local.
- Si un gloss existe déjà, il s'affiche directement sous l'audio en mode lecture, avec les trois lignes visibles (`.audio-item__gloss` / `.gloss-row`). L'utilisateur peut cliquer pour l'éditer.

#### Upload en onglets Record / File

Avant, il n'y avait qu'un seul mode : l'enregistrement direct au micro. J'ai ajouté la possibilité d'importer un fichier audio existant, ce qui est indispensable pour les linguistes qui ont déjà des enregistrements de terrain.

Le panel est maintenant divisé en deux onglets :

- **Record** — l'interface d'enregistrement au micro (composants `.mic-*` de la semaine 1).
- **File** — un formulaire d'upload avec sélection de fichier et champ de description.

L'état du mode actif est géré par :

```js
const uploadMode = ref('record')  // 'record' | 'file'
```

Pendant un enregistrement actif, un point rouge animé (`.audio-panel__recording-dot`) apparaît dans le titre du panel pour signaler visuellement que l'enregistrement est en cours, même si l'utilisateur a fait défiler la page et ne voit plus le bouton micro.

#### Marker stage — fix du décalage de position

**Problème :** quand on cliquait sur un marker (un point d'annotation positionné sur l'image d'une scène), le dot se déplaçait à une mauvaise position. En cliquant sur le dot lui-même, les coordonnées calculées étaient décalées par rapport à l'endroit cliqué.

**Cause :** `event.target` retournait l'élément enfant (le dot) au lieu du container image, donc les coordonnées relatives étaient calculées par rapport au mauvais élément.

**Correction :**

```js
// Avant (incorrect)
const rect = event.target.getBoundingClientRect()

// Après (correct)
const rect = event.currentTarget.getBoundingClientRect()
```

`currentTarget` pointe toujours vers l'élément sur lequel l'écouteur est enregistré (`.marker-stage`), indépendamment de l'enfant cliqué. Le conteneur `.marker-stage` a aussi reçu `position: relative` explicitement (il remplace l'ancien `.marker-image-stage` qui ne l'avait pas), ce qui est nécessaire pour que les coordonnées soient correctes.

En parallèle, j'ai ajouté une ouverture automatique du panel audio quand on sélectionne une thumbnail :

```js
if (thumb) panelOpen.value = true
```

Ça évite à l'utilisateur de devoir ouvrir manuellement le panneau après chaque sélection de scène.

#### Retiré

- **Section "Scenario player"** — un composant texte statique qui affichait l'état du scénario mais n'était plus pertinent avec la nouvelle architecture du studio.
- **Bouton "Discuss"** sur les audios — la fonctionnalité de discussion par audio a été retirée pour simplifier l'interface. Le feedback sur les audios passera par un autre mécanisme plus tard.
- **`focusedDiscussionAudioId`** et toute la logique de discussion associée — nettoyage complet, plus de code mort.

---

## Semaine 3 (21–28 mai 2026)

Cette semaine a été dense. Beaucoup de redesigns et pas mal de bugs à régler. Je vais noter tout ça dans l'ordre où ça s'est passé.

---

### 1. Refonte de la page de création de scénario

La page `CreateScenarioView.vue` était franchement inutilisable. Le sélecteur de langue était un `<select>` avec pagination manuelle — tu cliquais "Next" pour parcourir plus de 8000 langues une page à la fois. Personne ne va faire ça.

```html
<label>
  Language filter
  <input v-model="filter" placeholder="Filter languages" @input="refreshLanguages"/>
</label>
<div class="toolbar">
  <button @click="prevPage">Previous</button>
  <span>{{ page + 1 }} / {{ totalPages }}</span>
  <button @click="nextPage">Next</button>
</div>
```

J'ai tout réécrit. La page est maintenant en deux colonnes : le formulaire à gauche, une carte de prévisualisation en direct à droite qui se met à jour pendant que tu tapes. Pour la langue, j'ai remplacé le `<select>` par un vrai combobox avec debounce :

```js
let debounceTimer = null

function onLangInput() {
  selectedLanguage.value = null
  form.value.languageId = ""
  comboOpen.value = true
  clearTimeout(debounceTimer)
  debounceTimer = setTimeout(() => loadLanguages(langQuery.value), 220)
}

function selectLanguage(lang) {
  selectedLanguage.value = lang
  form.value.languageId = lang.id
  langQuery.value = lang.name
  comboOpen.value = false
}
```

Le debounce à 220ms évite de spammer l'API à chaque frappe. Le dropdown se ferme quand on clique ailleurs grâce à un listener sur `document` qu'on monte/démonte proprement :

```js
function onClickOutside(e) {
  if (langFieldRef.value && !langFieldRef.value.contains(e.target)) {
    comboOpen.value = false
  }
}

onMounted(async () => {
  document.addEventListener("mousedown", onClickOutside)
  await loadLanguages()
})

onUnmounted(() => {
  document.removeEventListener("mousedown", onClickOutside)
  clearTimeout(debounceTimer)
})
```

Quand une langue est sélectionnée, le contour du champ passe au vert — petit détail mais ça donne un retour clair :

```css
.cs-combo__input-wrap--selected {
  border-color: #4a8a62;
  background: #f0faf4;
}
```

La carte de prévisualisation à droite reprend exactement le style BD des tuiles du storyboard (même bordure, même ombre) et s'actualise en temps réel :

```html
<h3 class="cs-preview__title">
  {{ form.title.trim() || "Untitled scenario" }}
</h3>
<span class="cs-preview__pill--lang">
  {{ selectedLanguage ? selectedLanguage.name : "No language" }}
</span>
```

```css
.cs-preview__card {
  border: 3px solid #3d2818;
  border-radius: 14px;
  background: #fff8f0;
  box-shadow: 3px 3px 0 #3d2818;
}
```

Pour la validation, j'ai ajouté un flag `attempted` pour que les erreurs n'apparaissent pas pendant que l'utilisateur est encore en train de taper — seulement après la première tentative de soumission :

```js
const attempted = ref(false)

async function submit() {
  attempted.value = true
  if (!isFormValid.value) {
    error.value = titleError.value || "Please select a language."
    return
  }
  // ...
}
```

Le bouton submit a une petite flèche SVG qui glisse vers la droite au hover :

```css
.cs-submit:hover:not(:disabled) svg {
  transform: translateX(3px);
}
```

Sur mobile, j'avais mis la prévisualisation en premier (`order: -1`) mais j'ai finalement retiré ça — ça faisait bizarre d'avoir la carte avant le formulaire sur petit écran.

---

### 2. Refonte de l'état vide du storyboard

Quand on crée un scénario tout neuf et qu'on arrive dans le studio sans aucune image, il y avait ça :

```html
<div v-else-if="isOwner" class="storyboard-empty-owner">
  <p>Your storyboard is empty</p>
  <p class="muted">Click the <strong>+</strong> button above...</p>
  <button @click="openUploadDialog">Add first scene</button>
</div>

<BaseEmptyState v-else title="No thumbnails yet" message="..."/>
```

Générique, sans contexte, on ne comprend pas vraiment ce qu'on est censé faire. J'ai remplacé les deux blocs par un seul composant `sb-empty` qui fonctionne pour tout le monde (le bouton d'ajout est juste caché pour les non-propriétaires) :

```html
<div v-else class="sb-empty">
  <div class="sb-empty__grid" aria-hidden="true">
    <div class="sb-empty__tile sb-empty__tile--1"><span>01</span></div>
    <div class="sb-empty__tile sb-empty__tile--2"><span>02</span></div>
    <div class="sb-empty__tile sb-empty__tile--3"><span>03</span></div>
  </div>

  <div class="sb-empty__body">
    <p class="sb-empty__eyebrow">Empty storyboard</p>
    <h2 class="sb-empty__title">Start with your first scene</h2>
    <p class="sb-empty__sub">
      Upload an image to create your first scene.<br>
      You'll then be able to record voices and annotate the whole thing.
    </p>

    <button v-if="isOwner" type="button" class="sb-empty__cta" @click="openUploadDialog">
      Add a scene
    </button>

    <div class="sb-empty__steps">
      <div class="sb-empty__step"><span class="sb-empty__step-num">1</span><span>Upload an image</span></div>
      <div class="sb-empty__step-arrow">→</div>
      <div class="sb-empty__step"><span class="sb-empty__step-num">2</span><span>Record voices</span></div>
      <div class="sb-empty__step-arrow">→</div>
      <div class="sb-empty__step"><span class="sb-empty__step-num">3</span><span>Publish the vignette</span></div>
    </div>
  </div>
</div>
```

Les trois fausses tuiles en haut donnent un aperçu du style BD avant même qu'on ait ajouté quoi que ce soit. Elles ont les mêmes bordures et ombres que les vraies tuiles, mais avec une opacité réduite :

```css
.sb-empty__tile {
  border: 3px solid #3d2818;
  border-radius: 10px;
  box-shadow: 3px 3px 0 #3d2818;
  opacity: 0.55;
}

.sb-empty__tile--1 { width: 120px; height: 90px;
  background: linear-gradient(135deg, #ffe5d4, #ffd3b8); }
.sb-empty__tile--2 { width: 80px; height: 110px;
  background: linear-gradient(135deg, #ffb8c8, #ffccd8); }
.sb-empty__tile--3 { width: 100px; height: 76px;
  background: linear-gradient(135deg, #c5e8d5, #a8d4c0); }
```

---

### 3. Refonte de la dialog d'upload

L'ancienne dialog pour ajouter une image de scène c'était :

```html
<label>Title <input v-model="uploadTitle"/></label>
<label>Image file <input type="file" @change="onImageChange"/></label>
<button @click="uploadImage">Upload image</button>
```

Pas de prévisualisation, pas de glisser-déposer, un fichier à la fois.

La nouvelle version a son propre système de classes `ud-*` entièrement séparé. D'abord une zone de drag & drop :

```html
<label
  class="ud-dropzone"
  :class="{ 'ud-dropzone--over': uploadDragOver }"
  @dragover.prevent="uploadDragOver = true"
  @dragleave="uploadDragOver = false"
  @drop.prevent="onUploadDrop"
>
  <p class="ud-dropzone__label">Drop images here</p>
  <p class="ud-dropzone__sub">or click to browse · multiple files supported</p>
  <input type="file" accept="image/*" multiple class="ud-file-input" @change="onImageChange"/>
</label>
```

L'attribut `multiple` sur l'input suffit pour activer la sélection multi-fichiers. Pour gérer ça, je suis passée d'un `ref` simple à un tableau :

```js
const uploadFiles = ref([])  // [{ file, previewUrl, title }, ...]

function addUploadFiles(fileList) {
  const incoming = Array.from(fileList).filter(f => f.type.startsWith("image/"))
  const entries = incoming.map(file => ({
    file,
    previewUrl: URL.createObjectURL(file),
    title: file.name.replace(/\.[^.]+$/, ""),
  }))
  uploadFiles.value = [...uploadFiles.value, ...entries]
}

function onImageChange(event) {
  addUploadFiles(event.target.files ?? [])
  event.target.value = ""  // reset pour pouvoir re-sélectionner le même fichier
}

function onUploadDrop(event) {
  uploadDragOver.value = false
  addUploadFiles(event.dataTransfer?.files ?? [])
}
```

Chaque image ajoutée apparaît dans une grille de prévisualisation avec un champ titre modifiable et un bouton de suppression :

```html
<div class="ud-grid">
  <div v-for="(entry, index) in uploadFiles" :key="index" class="ud-grid-item">
    <div class="ud-grid-thumb">
      <img :src="entry.previewUrl" :alt="entry.title" class="ud-grid-img"/>
      <button type="button" class="ud-grid-remove" @click="removeUploadEntry(index)">×</button>
    </div>
    <input v-model="entry.title" class="ud-grid-title" placeholder="Scene title…"/>
  </div>
</div>
```

```css
.ud-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(130px, 1fr));
  gap: 10px;
  max-height: 340px;
  overflow-y: auto;
}
.ud-grid-thumb {
  position: relative;
  border: 2px solid #3d2818;
  border-radius: 10px;
  overflow: hidden;
  aspect-ratio: 4 / 3;
  box-shadow: 2px 2px 0 #3d2818;
}
```

L'upload se fait en boucle avec une barre de progression :

```js
for (let i = 0; i < entries.length; i++) {
  const fd = new FormData()
  fd.append("scenarioId", String(scenario.value.id))
  fd.append("title", entries[i].title || "")
  fd.append("image", entries[i].file)
  await uploadScenarioThumbnail(props.id, fd)
  uploadingProgress.value = Math.round(((i + 1) / entries.length) * 100)
}
```

Le label du bouton s'adapte dynamiquement :

```html
<button class="ud-submit" :disabled="!uploadFiles.length" @click="uploadImage">
  Add {{ uploadFiles.length > 1 ? uploadFiles.length + ' scenes' : 'scene' }} to storyboard
</button>
```

La card elle-même reprend le style warm BD :

```css
.ud-card {
  border: 3px solid #3d2818;
  border-radius: 20px;
  padding: 24px;
  background: #fff8f0;
  box-shadow: 6px 6px 0 #3d2818;
}
```

---

### 4. Les bugs de la semaine

#### Bug 1 — les images disparaissent juste après l'upload

J'ajoute 3 images, la dialog se ferme, le storyboard est vide. Ça m'a pris un moment à comprendre.

Quand on sélectionne un fichier, on crée une URL de prévisualisation :

```js
previewUrl: URL.createObjectURL(file)
```

Quand la dialog se ferme, `clearUploadFile()` révoque toutes ces URLs pour libérer la mémoire :

```js
uploadFiles.value.forEach(e => URL.revokeObjectURL(e.previewUrl))
```

Le problème : on utilisait exactement cette même `previewUrl` pour afficher la tuile dans le storyboard. Une fois révoquée, l'image disparaissait. La correction : créer une nouvelle URL indépendante pour la tuile :

```js
// CASSÉ
added.push({ previewUrl: entry.previewUrl })  // ← révoquée à la fermeture

// CORRIGÉ
added.push({ previewUrl: URL.createObjectURL(entry.file) })  // ← nouvelle URL, safe
```

#### Bug 2 — des tags aléatoires sur chaque scénario

J'ouvre un scénario tout neuf, le studio affiche "Atikamekw", "Débutant", "Vie quotidienne". J'ai jamais mis ces tags.

Dans le template, il y avait des placeholders de design qui n'avaient jamais été remplacés par de vraies données :

```html
<!-- CASSÉ -->
<div class="studio-chip-row">
  <span>{{ languageName || "Atikamekw" }}</span>
  <span>Débutant</span>
  <span>Vie quotidienne</span>
</div>

<!-- CORRIGÉ -->
<div class="studio-chip-row">
  <span v-if="languageName">{{ languageName }}</span>
  <template v-if="scenario.tags?.length">
    <span v-for="tag in scenario.tags" :key="tag">#{{ tag }}</span>
  </template>
</div>
```

#### Bug 3 — l'état vide reste visible même après avoir ajouté des images

J'ajoute 5 images, elles s'affichent toutes dans le storyboard, mais en scrollant vers le bas l'état vide était encore là en dessous.

Le bloc `sb-empty` n'avait pas de condition du tout — il était toujours dans le DOM. Un seul mot à ajouter :

```html
<!-- CASSÉ -->
<div class="sb-empty"> ← toujours rendu

<!-- CORRIGÉ -->
<div v-else class="sb-empty">
```

#### Bug 4 — crash du compilateur Vue : Unexpected character `'`

Après avoir traduit les textes français, Vite a crashé :

```
Error parsing JavaScript expression: Unexpected character '''.
EmergencyAudioRecorder.vue:120:17
```

Les textes contenaient des apostrophes typographiques Unicode — `'` (U+2018) et `'` (U+2019) — qui passent bien dans du texte affiché, mais qui cassent quand elles se retrouvent dans des liaisons Vue comme :

```html
:title="status === 'recording' ? 'Arrêter l'enregistrement' : '...'"
```

Le `'` au milieu était interprété comme le délimiteur de fermeture. Corrigé avec un script Python sur tous les fichiers modifiés :

```python
with open(path, 'rb') as f:
    content = f.read()
fixed = content.replace(b'\xe2\x80\x98', b"'").replace(b'\xe2\x80\x99', b"'")
with open(path, 'wb') as f:
    f.write(fixed)
```

#### Bug 5 — balise fermante orpheline

Après la refonte de la dialog d'upload :

```
Element is missing end tag.
ScenarioDetailView.vue:1991:17
```

En remplaçant `dialog-card__body` par `ud-card`, j'avais oublié de supprimer l'ancien `</div>` de fermeture qui traînait encore dans le fichier :

```html
<!-- CASSÉ -->
<BaseAlert v-if="uploadError" type="error">{{ uploadError }}</BaseAlert>
</div>      ← orphelin
</section>

<!-- CORRIGÉ -->
<BaseAlert v-if="uploadError" type="error">{{ uploadError }}</BaseAlert>
</section>
```

---

### 5. La transition entre les vues

Le studio a deux onglets — "Storyboard" et "Studio". Avant, passer de l'un à l'autre était un swap instantané et brutal.

La cause : les deux vues utilisaient `v-if` / `v-else`. `v-if` détruit et recrée les noeuds DOM — aucune transition CSS ne peut s'animer sur quelque chose qui n't existe pas encore. J'aurais pu utiliser `v-show` (qui garde les éléments dans le DOM), mais ça aurait gardé les deux vues montées en même temps, avec des conflits potentiels d'événements. J'ai opté pour `<Transition mode="out-in">` — l'ancienne vue sort complètement avant que la nouvelle entre :

```html
<Transition name="vg-view" mode="out-in">
  <div v-if="storyboardView === 'global'" key="global" class="vg-view vg-view--with-recorder">
    ...
  </div>
  <div v-else key="studio" class="vg-view">
    ...
  </div>
</Transition>
```

Les attributs `key` sont importants — sans eux Vue ne sait pas que ce sont deux éléments différents et n'anime rien.

Le CSS dans `storyboard.css` :

```css
.vg-view-enter-active  { transition: opacity 160ms ease, transform 160ms ease; }
.vg-view-leave-active  { transition: opacity 120ms ease, transform 120ms ease; }
.vg-view-enter-from    { opacity: 0; transform: translateY(8px); }
.vg-view-leave-to      { opacity: 0; transform: translateY(-6px); }
```

La sortie est plus rapide (120ms) que l'entrée (160ms) — intentionnel. La sortie doit paraître vive, l'entrée plus douce. C'est une technique classique pour que les transitions ne semblent pas lentes.

Les boutons d'onglets ont aussi une transition pour que l'indicateur d'actif glisse proprement :

```css
.vg-tab {
  transition: background 180ms ease, color 180ms ease, box-shadow 180ms ease;
}
```

Et le petit label à côté du fil d'Ariane ("Studio" / "Storyboard") a son propre cross-fade indépendant pour ne pas hard-swapper pendant que la vue est en train de transitionner :

```html
<Transition name="vg-kicker" mode="out-in">
  <span :key="storyboardView" class="vg-kicker">
    {{ storyboardView === "studio" ? "Studio" : "Storyboard" }}
  </span>
</Transition>
```

```css
.vg-kicker-enter-active,
.vg-kicker-leave-active { transition: opacity 120ms ease; }
.vg-kicker-enter-from,
.vg-kicker-leave-to     { opacity: 0; }
```

---

### Fichiers touchés cette semaine

| Fichier | Ce qui a changé |
|---|---|
| `CreateScenarioView.vue` | Réécriture complète — combobox, layout 2 colonnes, prévisualisation en direct |
| `ScenarioDetailView.vue` | État vide, dialog d'upload multi-fichiers, corrections de bugs, transition de vue |
| `EmergencyAudioRecorder.vue` | Traduction des textes en anglais, fix apostrophes Unicode |
| `StudioRecorderPanel.vue` | Traduction des textes en anglais |
| `storyboard.css` | Classes de transition `vg-view` et `vg-kicker` |
