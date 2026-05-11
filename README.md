# Gantt — Suivi de projet

Application web monofichier (`gantt.html`) pour piloter un planning de projet sous forme de diagramme de Gantt interactif. Aucune installation, aucun serveur : ouvrez le fichier dans un navigateur et tout fonctionne en local.

```console
/!\ html & readme générés par utilisation de prompts avec Claude Opus 4.7 adaptatif /!\
```

---

## Démarrage

1. Téléchargez `gantt.html`.
2. Ouvrez-le dans Chrome, Firefox, Safari ou Edge (versions récentes).
3. Un projet de démonstration s'affiche immédiatement avec 25 tâches réparties en 5 phases.

Toutes les données restent dans la page. Pour conserver votre travail entre deux sessions, utilisez **Exporter** ; pour le reprendre, utilisez **Importer**.

---

## Interface

### En-tête

- **Nom du projet** — cliquez pour éditer (titre en italique).
- **Début / Fin** — bornes temporelles du projet. Modifiez-les pour étendre ou réduire la fenêtre globale.
- **PM** — nom du chef de projet (libre).
- Boutons d'action à droite : Paramètres, Importer, Exporter, Ajouter une tâche.

### Barre de contrôle

- **Fenêtre** — réglette horizontale qui contrôle la portion du projet affichée dans le Gantt. Glissez le curseur ou cliquez sur le rail pour recentrer.
- **Zoom** — trois niveaux :
  - **Jour** — une colonne = un jour calendaire (week-ends estompés)
  - **Semaine** — une colonne = une semaine ISO (lundi à dimanche)
  - **Mois** — une colonne = un mois calendaire

Le calcul est *aligné sur le calendrier réel* : en mode Semaine, les colonnes correspondent à la S23, S24, etc. ; en mode Mois, aux mois complets (28 à 31 jours selon le cas).

### Tableau des tâches (colonne figée à gauche)

| Colonne | Rôle |
|---|---|
| `ID` | Identifiant numérique (chevron `▼` pour les phases) |
| `Tâche / Phase` | Nom — éditable |
| `Resp.` | Responsable — éditable |
| `Début` / `Fin` | Dates — éditables (modifier `Fin` ajuste la durée) |
| `J` | Durée en jours (lecture seule, dérivée des dates) |
| `%` | Avancement — éditable (0 à 100) |
| `Statut` | Liste déroulante : Terminé, En cours, Non démarré, En attente, En retard |
| `Préc.` | Prédécesseurs — voir [Dépendances](#dépendances) |
| `📄` | Note — voir [Notes](#notes) |
| `×` | Supprimer la ligne (apparaît au survol) |

Toutes les modifications sont prises en compte instantanément ; le Gantt et les flèches de dépendances se recalculent automatiquement.

### Zone Gantt (scroll horizontal)

- **Barres colorées** selon le statut, avec barre de progression interne, étiquette du nom si la largeur le permet, et tooltip détaillé au survol.
- **Phases** rendues en barres noires avec triangles aux extrémités.
- **Ligne du jour** verticale orange.
- **Aujourd'hui** mis en évidence dans l'en-tête de colonne.
- **Jours fériés** marqués par un trait pointillé rouge.
- **Périodes sensibles** affichées en bandes hachurées (couleur selon type) + badge en haut.
- **Dépendances** matérialisées par des flèches noires (rouge pointillé si la dépendance est violée).

---

## Phases — Pliage / Dépliage

Chaque ligne `PHASE` affiche un chevron `▼` à gauche.

- **Clic** sur le chevron → replie ou déplie la phase
- **Maj + clic** → applique l'action à *toutes* les phases d'un coup

Quand une phase est repliée :
- Ses tâches enfants disparaissent du tableau ET du Gantt
- Les dépendances pointant vers une tâche cachée sont automatiquement *redirigées vers la phase parente* — la relation reste visible

L'état déplié/replié est sauvegardé à l'export.

---

## Ajouter une tâche ou une phase

Trois boutons ouvrent la **popup d'insertion** :
- `+ Tâche` (en-tête)
- `+ Nouvelle tâche` / `+ Nouvelle phase` (bas du tableau)

La popup propose :

- **Type** — toggle Tâche / Phase (les champs spécifiques s'adaptent)
- **Nom** — auto-focus à l'ouverture
- **Insérer** — dropdown listant *toutes les positions possibles* :
  - `— Au début —`
  - `Après · 01 · ▸ PHASE PHASE 1 — Cadrage`
  - `Après · 02 · · Lancement projet`
  - …
  - `— À la fin —`
- **Responsable** *(tâche uniquement)*
- **Durée** en jours (défaut : 5 pour une tâche, 14 pour une phase)
- **Statut** et **Avancement** *(tâche uniquement)*
- **Prédécesseurs** *(tâche uniquement)* — voir [Dépendances](#dépendances)
- **Note** *(optionnelle)*

**Raccourcis** : `Entrée` valide, `Échap` annule. Dans la zone Note, utilisez `Ctrl/Cmd + Entrée` pour valider sans insérer de retour à la ligne.

La date de début de la nouvelle tâche est calculée automatiquement à partir de la position choisie ; vous pouvez ensuite l'ajuster dans le tableau.

---

## Dépendances

Saisissez dans la colonne `Préc.` les ID des tâches prédécesseurs, séparés par des virgules. Suffixez par un type pour autre chose que **FS** :

| Syntaxe | Type | Sens |
|---|---|---|
| `3` | FS | Finish-to-Start (défaut) — `3` doit finir avant que cette tâche commence |
| `3SS` | SS | Start-to-Start — `3` doit commencer avant ou en même temps |
| `3FF` | FF | Finish-to-Finish — `3` doit finir avant ou en même temps |
| `3SF` | SF | Start-to-Finish — `3` doit commencer avant la fin de cette tâche |
| `3, 5SS, 7` | mixte | plusieurs prédécesseurs |

Les flèches sont tracées entre les barres avec un routage orthogonal (en L ou en S si le successeur précède le prédécesseur dans le temps).

**Violation** : si une dépendance n'est pas respectée (ex : un successeur FS qui commence avant la fin de son prédécesseur), la flèche apparaît en **rouge pointillé**.

---

## Notes

Chaque tâche et chaque phase peut porter une note libre (commentaires, contexte, décisions, points d'attention).

- **Icône** dans le tableau :
  - Note absente → icône grisée, visible uniquement au survol de la ligne
  - Note présente → icône orange opaque avec pastille en haut à droite
- **Clic sur l'icône** → ouvre la popup d'édition avec :
  - Badge `#ID` + type (Tâche / Phase) + titre de la tâche
  - Zone de texte redimensionnable, compteur de caractères
  - Boutons *Supprimer la note*, *Annuler*, *Enregistrer*
- **Raccourcis** : `Ctrl/Cmd + Entrée` enregistre, `Échap` annule
- **Tooltip** : passer la souris sur l'icône affiche la première ligne de la note

Les retours à la ligne sont préservés dans le JSON exporté.

---

## Paramètres (bouton ⚙)

Tiroir latéral qui regroupe deux configurations transversales :

### Jours fériés

- **Préréglages FR** : un bouton par année du projet (`+ Fériés FR 2025`, `+ Fériés FR 2026`…) injecte automatiquement les 11 jours fériés français (calcul de Pâques compris).
- **Ajout / édition manuelle** d'une date avec son libellé
- **Effacer tout** d'un clic

Affichage sur le Gantt : trait pointillé rouge vertical, en-tête de colonne teintée.

### Périodes sensibles

Plages temporelles à signaler (gel des changements, fermeture annuelle, vacances scolaires, vigilance accrue, etc.).

Chaque période a :
- Un nom
- Des dates début / fin
- Une **couleur** parmi 5 codes :
  - 🔴 **Critique** — rouge
  - 🟠 **Vigilance** — orange foncé
  - 🟡 **Attention** — ambre
  - 🔵 **Information** — bleu
  - ⚫ **Fermeture** — gris

Affichage : bande hachurée diagonale sur toute la hauteur du Gantt + badge en haut indiquant le nom.

---

## Import / Export JSON

### Exporter

Bouton **Exporter** → téléchargement d'un fichier `<nom_du_projet>_<date>.json` contenant :
- Métadonnées de l'app
- Le projet complet (nom, PM, dates, toutes les tâches, fériés, périodes sensibles)
- L'état de la vue (zoom et fenêtre)

L'export est *sanitisé* : valeurs invalides corrigées, deps mal formées filtrées. Le fichier est garanti relisible.

### Importer

Bouton **Importer** → sélection d'un fichier `.json`.

L'importeur accepte :
- Le format complet `{_meta, project, view}`
- Un objet projet seul `{name, tasks, …}`
- Les fichiers avec ou sans BOM UTF-8

En cas de JSON invalide, le message d'erreur indique la ligne, la colonne et un fragment du contexte problématique.

### Structure du JSON

```json
{
  "_meta": {"app": "gantt-html", "version": "1.2", "exportedAt": "…"},
  "project": {
    "name": "…",
    "pm": "…",
    "start": "2025-06-01",
    "end": "2025-11-30",
    "tasks": [
      {
        "id": 1,
        "name": "PHASE 1 — Cadrage",
        "owner": "",
        "startOffset": 0,
        "duration": 14,
        "progress": 100,
        "status": "done",
        "deps": [],
        "phase": true,
        "collapsed": false,
        "notes": "…"
      }
    ],
    "holidays": [{"id": 1, "date": "2025-07-14", "name": "Fête nationale"}],
    "freezes": [
      {"id": 1, "name": "Code freeze", "start": "2025-11-15", "end": "2025-11-25", "color": "risk"}
    ]
  },
  "view": {"zoom": "week", "windowStart": 0, "windowDays": 84}
}
```

Champs des tâches :
- `startOffset` — nombre de jours depuis `project.start`
- `duration` — durée en jours (≥ 1)
- `progress` — 0 à 100
- `status` — `done` | `progress` | `notstarted` | `pending` | `risk`
- `deps` — tableau d'objets `{id, type}` où `type` ∈ `FS | SS | FF | SF`
- `phase` — `true` pour une phase, absent ou `false` pour une tâche normale
- `collapsed` — `true` si la phase est repliée
- `notes` — texte libre, retours à la ligne préservés

---

## Raccourcis clavier

| Touche | Action |
|---|---|
| `←` / `→` | Décale la fenêtre temporelle d'un pas de zoom |
| `1` / `2` / `3` | Zoom Jour / Semaine / Mois |
| `Maj + molette` | Pan horizontal de la fenêtre |
| `Échap` | Ferme la popup ou le tiroir Paramètres |
| `Entrée` | Valide la popup d'insertion (sauf dans la zone Note) |
| `Ctrl/Cmd + Entrée` | Valide la popup Note ou la zone Note de l'insertion |

---

## Notes techniques

- **100 % hors-ligne, zéro dépendance réseau** : le fichier ne charge aucune ressource externe (ni polices, ni scripts, ni styles). Ouvrez-le sans connexion, il fonctionnera à l'identique.
- **Pas de backend, pas de stockage local** : le navigateur ne conserve rien entre deux sessions. Utilisez Export / Import pour la persistance.
- **Polices** : la page utilise les piles de polices natives du système, déclarées via trois variables CSS dans `:root` (`--font-sans`, `--font-mono`, `--font-serif`). Les choix concrets selon l'OS :
  - **Sans-serif** (corps de texte) — `ui-sans-serif`, puis `system-ui` / San Francisco (macOS, iOS), Segoe UI (Windows), Roboto (Android, Linux)…
  - **Monospace** (libellés techniques, dates) — `ui-monospace`, puis SF Mono, Cascadia Code, Menlo, Consolas…
  - **Serif** (titres en italique, noms de phases) — `ui-serif`, puis Cambria, Georgia…

  Pour changer l'identité typographique, éditez les trois variables `--font-*` au début du CSS — toutes les déclarations en héritent.
- **Calcul des jours fériés français** : algorithme de Gauss pour Pâques, dates fixes pour les autres.
- **Numéros de semaine** : norme ISO 8601 (semaines commençant le lundi, la S01 contient le 4 janvier).
- **Compatibilité** : navigateurs modernes (CSS Grid, position sticky, marker SVG, `Intl.DateTimeFormat`).
