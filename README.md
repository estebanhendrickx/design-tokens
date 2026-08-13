# Design tokens → Tailwind CSS v4

Ce projet transforme tes **design tokens** Figma (au format standard **W3C DTCG**,
c.-à-d. `$value` / `$type`) en un CSS final utilisable dans un site, via **Tailwind CSS v4**.

> Format : le fichier `tokens/tokens.json` suit le standard **DTCG**. Il est lu
> **nativement** par Style Dictionary v4 — aucune surcouche spécifique Tokens Studio
> n'est nécessaire. Les blocs `$themes` / `$metadata` de l'export sont ignorés
> automatiquement, et le nom du set (ex. `Base/Values`) est retiré des variables.

## Le circuit, en une image

```
tokens/tokens.json                (tes tokens DTCG, synchronisés depuis Figma)
        │
        ▼  Style Dictionary  (npm run tokens)
build/css/tokens.css              (--token-colors-primary, --token-radius-button, …)
        │
        ▼  branché dans src/input.css via @theme
        │
        ▼  Tailwind CSS v4  (npm run css)
dist/app.css                      ◀── index.html (page de démo du bouton)
```

## Ce qui est branché sur tes tokens

- ✅ **Couleurs** — remplacent la palette par défaut de Tailwind (`bg-primary`, `text-default`, …)
- ✅ **Rayon** — remplace les rayons par défaut (`rounded-button`)
- ✅ **Typographie** — police **Rajdhani** (Google Fonts) + graisses (400/500/600/700)
  et échelle de tailles (12→30px) issues des tokens. Les tailles reprennent l'échelle
  de base de Tailwind.
- 🔒 **Espacement et breakpoints** — **inchangés** : ce sont les valeurs par défaut de Tailwind
  (`p-4`, `p-6`, `md:`, `lg:`…), comme demandé.

## Prérequis (une seule fois)

1. Installer **Node.js** (version LTS) depuis https://nodejs.org
2. Dans le Terminal, se placer dans ce dossier et installer les outils :

   ```bash
   npm install
   ```

## La commande à retenir

Pour tout recompiler (tokens **puis** CSS Tailwind) en une seule fois :

```bash
npm run build
```

Puis ouvre **`index.html`** dans ton navigateur pour voir le résultat.

### Les autres commandes

```bash
npm run refresh   # récupère les tokens à jour depuis GitHub PUIS fait le build complet
npm run tokens    # compile seulement les tokens (Style Dictionary)
npm run css        # compile seulement le CSS (Tailwind)
npm run pull      # télécharge seulement le dernier tokens.json depuis GitHub
```

> 💡 Après un changement de tokens dans Figma **poussé sur GitHub**,
> la commande la plus complète est **`npm run refresh`**.

## Pousser des tokens vers GitHub, puis les récupérer dans Figma

Ici, `tokens/tokens.json` a été enrichi **en local** (typographie). Pour partager ces
tokens et les faire remonter dans Figma :

### A. Mettre le fichier à jour sur GitHub (via le site, sans Git)

1. Ouvre `https://github.com/estebanhendrickx/design-tokens/blob/main/tokens.json`
2. Clique l'icône **crayon** (Edit this file).
3. Remplace tout le contenu par celui de ton `tokens/tokens.json` local.
4. En bas : **Commit changes**.

> ⚠️ Tant que tu n'as pas fait ça, ne lance pas `npm run pull` : il écraserait
> ta version locale (typo comprise) par l'ancienne version de GitHub.

### B. Récupérer dans Figma (plugin Tokens Studio)

1. Dans Figma, ouvre **Tokens Studio**.
2. **Settings → Sync → GitHub** : renseigne le repo `estebanhendrickx/design-tokens`,
   la branche `main` et le fichier `tokens.json` (un *personal access token* GitHub est requis).
3. Clique **Pull** pour importer les tokens du repo.
4. Applique-les : Tokens Studio peut créer les **variables Figma** correspondantes
   (couleurs, nombres) et utiliser la police/les tailles dans des **styles de texte**.

> Note : Figma ne connaît que 4 types de variables (couleur, nombre, texte, booléen).
> Les couleurs et tailles arrivent donc en variables ; la **police/les graisses** se
> matérialisent surtout via des **styles de texte** côté Figma.

## Où regarder si besoin

| Fichier             | Rôle                                             | À modifier à la main ? |
| ------------------- | ------------------------------------------------ | ---------------------- |
| `tokens/tokens.json`| Tes tokens (copie locale de GitHub)              | Non (via Tokens Studio)|
| `src/input.css`     | Branche les tokens sur Tailwind (`@theme`) + bouton | Oui, si besoin      |
| `index.html`        | Page de démo                                     | Oui                    |
| `build/css/tokens.css` | Tokens compilés (généré)                      | **Jamais**             |
| `dist/app.css`      | CSS final du site (généré)                       | **Jamais**             |
```
