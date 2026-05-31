---
title: Travail réalisé
---

<style>
    @media screen and (min-width: 76em) {
        .md-sidebar--primary {
            display: none !important;
        }
    }
</style>

# Réalisation

## Architecture et structure générale

Vignette suit une architecture client-serveur découplée en deux parties.

### Backend (Spring Boot / Java 23)

Le backend expose une API REST documentée via SpringDoc/OpenAPI. Il est structuré en couches classiques :

```
src/main/java/org/titiplex/
├── api/                  # Contrôleurs REST (endpoints)
│   ├── AuthApiController.java
│   ├── ScenarioApiController.java
│   ├── ThumbnailApiController.java
│   ├── AudioApiController.java
│   └── ...
├── service/              # Logique métier
├── persistence/
│   ├── model/            # Entités JPA (Scenario, Thumbnail, Audio...)
│   └── repository/       # Repositories Spring Data
├── config/               # Sécurité, JWT, OpenAPI, Storage
└── bootstrap/            # Chargement initial des données (langues Glottolog)
```

### Frontend (Vue 3 + Vite)

```
vite/src/
├── views/                # Pages principales (CreateScenario, ScenarioDetail...)
├── components/           # Composants réutilisables
│   ├── ui/               # Composants génériques (BaseAlert, BaseBadge...)
│   ├── AudioPanel.vue
│   ├── EmergencyAudioRecorder.vue
│   └── StudioRecorderPanel.vue
├── composables/          # Logique réutilisable (useAuth, useToast, useScenarioAutoplay)
├── api/                  # Fonctions d'appel à l'API REST
├── styles/               # Feuilles de style globales et thèmes
└── utils/                # Utilitaires (scenarioStoryboard.js)
```

### Technologies utilisées

| Couche | Technologie | Version |
|---|---|---|
| Backend | Spring Boot | 3.5.4 |
| Langage backend | Java | 23 |
| Build backend | Maven | 3.9+ |
| Base de données (dev) | H2 (fichier) | 2.3 |
| Base de données (prod) | PostgreSQL | |
| ORM | Hibernate / JPA | 6.6 |
| Authentification | JWT + Spring Security | 6.5 |
| Documentation API | SpringDoc OpenAPI | 2.8 |
| Framework frontend | Vue 3 | |
| Bundler | Vite | 6.4 |
| Styles | CSS modulaire (pas de framework) | |
| Données linguistiques | Glottolog | |

### Environnement de développement

Le profil dev est activé via `--spring.profiles.active=dev`. Il active la base H2 en mode fichier, la console H2 sur `/h2-console`, Swagger UI sur `/api/docs`, et les limites multipart pour les fichiers audio et images.

```bash
# Lancement backend
APP_JWT_SECRET=dev-secret \
APP_STORAGE_ROOT=./data/storage \
mvn spring-boot:run -Dspring-boot.run.profiles=dev

# Lancement frontend
cd vite && npm install && npm run dev
```

---

## Fonctionnalités réalisées

### 1. Enregistrement audio d'urgence (EmergencyAudioRecorder)

Un bouton flottant REC / STOP est intégré globalement dans `AppShell.vue` pour être accessible depuis toutes les pages. L'idée c'est de pouvoir capturer une idée audio immédiatement, sans avoir à créer un scénario au préalable.

Le mécanisme repose sur l'API `MediaRecorder` du navigateur. Les enregistrements sont convertis en data URL et sauvegardés dans le `localStorage` sous forme de brouillons :

```js
const DRAFT_AUDIO_KEY = "vignette:unclaimed-draft-audios"

recorder.onstop = async () => {
  const blob = new Blob(chunks, { type: recorder.mimeType || "audio/webm" })
  const dataUrl = await blobToDataUrl(blob)
  const draft = {
    id: `draft-${Date.now()}`,
    title: `Audio draft ${drafts.value.length + 1}`,
    createdAt: new Date().toISOString(),
    mimeType: blob.type,
    dataUrl,
  }
  drafts.value = [draft, ...drafts.value]
  localStorage.setItem(DRAFT_AUDIO_KEY, JSON.stringify(drafts.value))
}
```

Le panneau latéral liste les brouillons avec possibilité de les renommer, les écouter, les supprimer ou créer une vignette directement depuis le brouillon.

### 2. Studio audio (StudioRecorderPanel)

Un panneau latéral de 230px s'ouvre dans la vue scénario. Il centralise toutes les fonctions d'enregistrement pour la scène sélectionnée :

- Sélecteur de voix (A, B, C, D) avec code couleur
- Bouton microphone animé avec trois anneaux concentriques (`.mic-rings`)
- Lecture rapide de l'audio actif
- Outils : Stop, Replay, Trim (couper), Restart
- Import d'un fichier audio existant
- Panneau de coupe avec deux curseurs (début / fin en %)
- Réglages audio : volume, vitesse, réduction de bruit
- Paramètres de visibilité : Private / Group / Community

### 3. Points de repère sur image (Markers)

Chaque fichier audio peut être associé à une position (X, Y en pourcentage) sur l'image de la scène. L'utilisateur clique sur l'image pour placer le point :

```js
function onImageClick(event) {
  const rect = event.currentTarget.getBoundingClientRect()
  const x = ((event.clientX - rect.left) / rect.width) * 100
  const y = ((event.clientY - rect.top) / rect.height) * 100
  markerX.value = Math.max(0, Math.min(100, x)).toFixed(2)
  markerY.value = Math.max(0, Math.min(100, y)).toFixed(2)
}
```

Le point est stocké en base via une migration Flyway (V2) sur la table `audio` :

```sql
ALTER TABLE audio ADD COLUMN IF NOT EXISTS marker_x DOUBLE PRECISION;
ALTER TABLE audio ADD COLUMN IF NOT EXISTS marker_y DOUBLE PRECISION;
ALTER TABLE audio ADD COLUMN IF NOT EXISTS marker_label VARCHAR(120);
```

Dans le storyboard les points s'affichent avec une animation de pulsation CSS :

```css
@keyframes markerPulse {
  0%  { transform: scale(0.8); opacity: 0.9; }
  70% { transform: scale(1.8); opacity: 0; }
  100%{ transform: scale(1.8); opacity: 0; }
}
```

### 4. Annotations linguistiques (Gloss)

Trois champs d'annotation ont été ajoutés à chaque entrée audio via la migration V3 :

```sql
ALTER TABLE audio ADD COLUMN IF NOT EXISTS transcription TEXT;
ALTER TABLE audio ADD COLUMN IF NOT EXISTS gloss TEXT;
ALTER TABLE audio ADD COLUMN IF NOT EXISTS free_translation TEXT;
```

Ces champs permettent une annotation interlinéaire standard : transcription phonémique, gloss morphème par morphème (ex. `1SG-PRES-like-FV`), et traduction libre. Un bouton "Gloss" sur chaque audio ouvre un formulaire inline avec les trois champs. Si un gloss existe déjà il s'affiche directement sous l'audio.

### 5. Page de création de scénario redessinée

La page `CreateScenarioView.vue` a été entièrement refaite. Le changement principal c'est le remplacement du sélecteur de langue paginé (qui parcourait 8000+ langues page par page) par un combobox avec recherche en temps réel :

```js
function onLangInput() {
  selectedLanguage.value = null
  comboOpen.value = true
  clearTimeout(debounceTimer)
  debounceTimer = setTimeout(() => loadLanguages(langQuery.value), 220)
}
```

La page adopte un layout deux colonnes : formulaire à gauche, carte de prévisualisation live à droite qui reflète le titre, la langue et la description en temps réel. Sur mobile la prévisualisation passe sous le formulaire.

### 6. État vide du storyboard

L'écran d'état vide (aucune scène ajoutée) a été revu pour donner un contexte visuel immédiat. Trois tuiles factices dans le style BD du projet (bordure 3px brun, ombre décalée, gradients warm) montrent à quoi ressemblera le storyboard une fois rempli. Un guide en trois étapes accompagne le bouton d'ajout.

### 7. Dialogue d'upload multi-fichiers

Le dialogue d'ajout de scènes a été refondu pour accepter plusieurs images en même temps :

```js
function addUploadFiles(fileList) {
  const incoming = Array.from(fileList).filter(f => f.type.startsWith("image/"))
  const entries = incoming.map(file => ({
    file,
    previewUrl: URL.createObjectURL(file),
    title: file.name.replace(/\.[^.]+$/, ""),
  }))
  uploadFiles.value = [...uploadFiles.value, ...entries]
}
```

Les images sélectionnées s'affichent dans une grille de prévisualisations. Chaque carte a un champ titre éditable et un bouton de suppression. Une barre de progression indique l'avancement lors de l'upload séquentiel.

### 8. Transition animée entre les vues

Le studio a deux onglets (Storyboard et Studio). La transition était instantanée parce que `v-if / v-else` détruit et recrée le DOM sans laisser le temps d'animer quoi que ce soit. La solution c'est `<Transition mode="out-in">` avec des attributs `key` explicites pour que Vue reconnaisse les deux éléments comme distincts :

```html
<Transition name="vg-view" mode="out-in">
  <div v-if="storyboardView === 'global'" key="global" class="vg-view ...">...</div>
  <div v-else key="studio" class="vg-view ...">...</div>
</Transition>
```

```css
.vg-view-enter-active { transition: opacity 160ms ease, transform 160ms ease; }
.vg-view-leave-active { transition: opacity 120ms ease, transform 120ms ease; }
.vg-view-enter-from   { opacity: 0; transform: translateY(8px); }
.vg-view-leave-to     { opacity: 0; transform: translateY(-6px); }
```

Le timing asymétrique (120ms sortie / 160ms entrée) est intentionnel : la sortie doit être rapide, l'entrée peut se permettre d'être un peu plus douce.

---

## Difficultés rencontrées

**Conflit de ports au démarrage.** Au premier lancement le backend refusait de démarrer sur le port 8081, déjà occupé par une autre instance du projet. Le port 5173 du frontend était aussi pris. Il a fallu identifier les processus (`lsof -ti :8081`) et les terminer avant de relancer.

**Guillemets courbes Unicode dans les templates Vue.** Lors de la traduction des textes français, certains contenaient des apostrophes typographiques Unicode (`'` U+2018/U+2019) au lieu d'apostrophes droites. Ces caractères étaient invisibles dans l'éditeur mais cassaient le compilateur Vue :

```
Error parsing JavaScript expression: Unexpected character '''.
```

Corrigé avec un script Python qui remplace les octets `\xe2\x80\x98` et `\xe2\x80\x99` dans tous les fichiers concernés.

**Révocation prématurée des URLs d'objets.** Après l'ajout de plusieurs images, elles disparaissaient du storyboard dès la fermeture du dialogue d'upload. La cause : le même `ObjectURL` servait à la fois de prévisualisation dans le dialogue et de source dans le storyboard. La fermeture révoquait l'URL et détruisait la source des images. Solution : créer deux URLs distinctes, une pour la prévisualisation (révocable) et une nouvelle pour le thumbnail (conservée).

**Balise fermante orpheline après remplacement de template.** Suite à la refonte du dialogue d'upload, une `</div>` correspondant à l'ancien `dialog-card__body` est restée dans le fichier. Elle cassait l'arbre DOM et provoquait une erreur de compilation :

```
Element is missing end tag.
<section class="ud-card">
```

**Décalage des markers.** En cliquant sur un marker positionné sur une image, les coordonnées calculées étaient fausses. `event.target` retournait l'élément enfant (le dot) au lieu du container image. Corrigé en utilisant `event.currentTarget` qui pointe toujours vers l'élément sur lequel l'écouteur est enregistré.

---

## Décisions et ajustements

**Mode sandbox frontend.** Une décision structurante a été de passer le studio en mode `studioFrontendOnly = true`. Toutes les opérations dans le studio sont simulées localement sans appel API, les données sont dans des `ref()` Vue et dans le `localStorage`. Ça permet de développer et tester l'interface de façon autonome, sans dépendre d'un backend opérationnel à chaque itération.

**Remplacement du sélecteur de langue paginé.** Le sélecteur initial avec pagination (Précédent / Suivant sur 8000+ langues) était inutilisable. Remplacé par un combobox avec recherche debounced à 220ms, ce qui élimine la pagination tout en maintenant les performances.

**Séparation des ObjectURLs de prévisualisation et de stockage.** Suite au bug de disparition des images, la règle a été d'always créer un `ObjectURL` dédié pour tout usage persistant, distinct de celui utilisé pour la prévisualisation temporaire dans les dialogues.

**`<Transition mode="out-in">` plutôt que `v-show`.** `v-show` aurait maintenu les deux vues en mémoire en même temps, avec des risques de conflits (deux instances du recorder, deux storyboards actifs). `<Transition mode="out-in">` garantit qu'une seule vue est active à la fois tout en permettant une animation fluide.

**Palette warm cohérente pour le studio.** Les composants du studio utilisent une palette codée en dur (`#fbf5ec`, `#fff8f0`, `#3d2818`, `#e07856`) plutôt que les variables CSS globales de l'app. Ce choix délibéré crée une identité visuelle distincte pour l'espace de création.
