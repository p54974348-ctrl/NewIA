# NewIA — Veille IA francophone

Veille technologique IA quotidienne, publiée automatiquement sur GitHub Pages :
**https://p54974348-ctrl.github.io/NewIA/**

## Architecture de publication

| Élément | Rôle |
|---|---|
| `index.html` | Page unique autonome (HTML + CSS + JS inline). Charge `data.json` côté client et affiche les 7 derniers jours. **Ne pas modifier lors de la veille quotidienne.** |
| `data.json` | Données de la veille — le **seul** fichier modifié chaque jour par la routine. |
| `.github/workflows/deploy-pages.yml` | CI : à chaque push de `index.html` ou `data.json` sur `main`, déploie le site sur GitHub Pages. |
| Routine Claude (quotidienne, 05:00 UTC) | Recherche web (FR/EN), construit la journée du jour, met à jour `data.json`, commit + push sur `main`, vérifie la publication. |

## Direction artistique — « Presse technique éditoriale »

La page suit une charte UX stricte (bloc stable de la routine, distinct des
règles de contenu) :

- **Typographie** : Fraunces (titres) + IBM Plex Mono (métadonnées) — aucune autre police.
- **Palette clair** : fond `#F6F1E7`, encre `#1A1712`, secondaire `#5C554A`, filets `#C9C0AE`, accent vermillon `#D0421B` (numérotation, filtre actif, tag de langue, libellés de section uniquement).
- **Palette sombre** (`prefers-color-scheme`) : `#12100D` / `#EDE6D8` / `#9A9184` / `#3A352C` / `#E05A31`.
- **Layout journal** : masthead (titre + « n° X — date »), règles épaisses 2.5 px, filets fins 0.5 px, entrées numérotées (01, 02…), méta `type · source · langue · date`, colonne principale (primaires / articles / podcasts) + colonne latérale (projets & méthodes), une colonne sous 720 px.
- **Interdits** : emojis, box-shadow, dégradés, border-radius > 4 px, cartes, pilules, frameworks CSS, couleurs hors palette.
- Numéro d'édition calculé en JS depuis `LAUNCH_DATE = 2026-07-12`.

## Schéma de `data.json`

```jsonc
{
  "generatedAt": "AAAA-MM-JJ…",     // date ISO de génération
  "days": [                          // 7 jours max, tri décroissant
    {
      "date": "AAAA-MM-JJ",
      "label": "dimanche 19 juillet 2026",
      "notes": "…",                  // notes finales (sans emoji) ou ""
      "primaires": [ /* item */ ],   // max 5 — annonces officielles 24–48 h
      "articles":  [ /* item */ ],   // max 10 par langue — 24–48 h
      "podcasts":  [ /* item */ ],   // max 10 par langue — 7 jours
      "projets":   [ /* itemProjet */ ],  // max 6 — dépôts open source IA, 7 jours
      "methodes":  [ /* itemMethode */ ]  // max 8 — méthodes de dev avec IA, 7 jours
    }
  ]
}
```

- `item` : `{ titre, source, lang: "fr|en", date?: "JJ/MM/AAAA", lien, resume, imagePrompt?, image? }`
  (illustrations 16:9 pollinations pour primaires/articles ; podcasts : pochette officielle sinon rien).
- `itemProjet` : `{ titre, source, lang, date?, lien, licence, stars?, categorie: "agents|outils|rag|apps|autre", resume }`.
- `itemMethode` : `{ titre, type: "repo|template|guide|REX", lang, date?, lien, categorie: "spec|boucles|contexte|workflows|prompting|autre", pratique, applicabilite, maturite }`.

Règles de contenu (bloc « données » de la routine) : liens directs uniquement,
dates de publication vérifiées, aucun contenu inventé (« non vérifié » sinon),
déduplication (max 2 contenus par sujet), catégorie B5 (prompting) événementielle.

## Déploiement

Pousser sur `main` (fichiers `index.html` ou `data.json`) suffit : la CI
construit `_site/` et déploie. Prérequis GitHub : *Settings → Pages →
Source = « GitHub Actions »*.
