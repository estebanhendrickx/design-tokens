# DESIGN.md

Ce fichier est la référence de design du projet. Il est lu par les agents (Claude Code,
Claude Design) et par les humains. Toute règle qui n'est pas écrite ici n'existe pas.

> **À compléter avant usage** — les zones marquées `⟨à remplir⟩` doivent être renseignées
> avec les valeurs réelles du projet. Une valeur inventée est pire qu'une valeur absente.

---

## 1. Règle fondamentale

**La source de vérité est `tokens/tokens.json`.**

Ces tokens sont édités dans Figma via Tokens Studio, poussés sur Git, puis compilés en
variables CSS par Style Dictionary, elles-mêmes consommées par Tailwind.

Conséquences, sans exception :

- Aucune valeur de couleur, de rayon ou de typographie en dur dans le code.
- Aucune modification directe des variables CSS générées — elles sont écrasées à chaque build.
- Un besoin non couvert par un token existant se traite en ajoutant un token, pas en
  contournant le système.

Si une tâche semble exiger une valeur en dur, c'est le signal qu'il manque un token.
Le signaler plutôt que le contourner.

---

## 2. Ce qui vient de Tailwind, ce qui vient de nous

| Domaine | Source | Règle |
|---|---|---|
| Couleurs | **Nos tokens** | La palette Tailwind par défaut est désactivée. `bg-blue-500` n'existe pas. |
| Typographie | **Nos tokens** | Familles, tailles et graisses définies par le système. |
| Rayons | **Nos tokens** | Un rayon par usage sémantique, pas par valeur. |
| Espacement | **Tailwind** | On garde l'échelle par défaut (`p-4`, `gap-6`, `space-y-8`…). |
| Breakpoints | **Tailwind** | Valeurs par défaut conservées. |
| Ombres | ⟨à remplir⟩ | Préciser si tokenisées ou héritées de Tailwind. |

Le raisonnement : les couleurs et la typographie portent l'identité de marque et doivent
être verrouillées. L'espacement est une convention de mise en page — l'échelle de Tailwind
est éprouvée, la réécrire n'apporte rien.

---

## 3. Tokens

### Couleurs

Structure sémantique. **Toujours utiliser le niveau sémantique**, jamais la primitive.

| Token | Usage |
|---|---|
| `action/primary` | Action principale — un seul par écran |
| `action/primary-hover` | État survol de l'action principale |
| `action/secondary` | Action secondaire |
| `text/default` | Texte courant |
| `text/muted` | Texte secondaire, légendes |
| `text/on-action` | Texte posé sur une surface d'action |
| `surface/default` | Fond de page |
| `surface/raised` | Cartes, panneaux |
| `border/default` | Séparateurs, contours |
| `feedback/error` · `feedback/success` · `feedback/warning` | États système |

⟨à remplir : valeurs hexadécimales de référence⟩

**Interdit :** `bg-blue-500`, `text-gray-700`, `#2563EB`, `rgb(...)` et toute couleur
littérale. Si la couleur voulue n'a pas de token, la demander.

### Typographie

| Rôle | Token | Valeur |
|---|---|---|
| Titres | ⟨famille⟩ | ⟨tailles⟩ |
| Corps | ⟨famille⟩ | ⟨tailles⟩ |
| Monospace | ⟨famille⟩ | ⟨tailles⟩ |

⟨à remplir : échelle typographique complète et graisses autorisées⟩

### Rayons

| Token | Usage |
|---|---|
| `radius/button` | Boutons, champs de saisie |
| `radius/card` | Cartes, panneaux, modales |
| `radius/full` | Pastilles, avatars |

Nommer par usage, jamais par valeur. `radius/8` est un mauvais nom : le jour où le rayon
passe à 10, le nom ment.

---

## 4. Composants

Les composants du design system vivent dans `⟨chemin du package UI⟩`.

**Règle d'or : réutiliser, ne jamais recréer.**

Avant de construire un élément d'interface, vérifier s'il existe déjà. Un bouton
reconstruit en `<div>` stylée est un défaut, même s'il est visuellement correct.

### Inventaire

| Composant | État | Variantes |
|---|---|---|
| `Button` | Disponible | `primary`, `secondary`, `ghost` × `sm`, `md`, `lg` |
| ⟨suivants⟩ | | |

### Correspondance Figma ↔ code

Les noms sont alignés dans les deux sens :

```
Figma : Button/Primary/Medium
Code  : <Button variant="primary" size="md" />
```

Cette correspondance est ce qui permet à Code Connect de fonctionner. Renommer d'un côté
sans renommer de l'autre casse la chaîne.

### Si un composant manque

Ne pas improviser. Composer à partir des composants existants, ou signaler le manque au
propriétaire du design system. Un composant créé en local devient une dette immédiate.

---

## 5. Principes de mise en page

- Une seule action principale par écran. Si tout est mis en avant, rien ne l'est.
- Hiérarchie par la taille et l'espacement avant la couleur.
- Densité cohérente à l'intérieur d'une même vue.
- Contraste texte/fond conforme WCAG AA au minimum (4.5:1 pour le corps de texte).
- Tous les états interactifs sont définis : repos, survol, focus, actif, désactivé,
  chargement. Un composant sans état focus visible est incomplet.

⟨à remplir : conventions de grille, largeurs de conteneur, densité⟩

---

## 6. Ton éditorial

⟨à remplir⟩ Exemples de dimensions à préciser :

- Tutoiement ou vouvoiement
- Libellés de boutons : à l'infinitif (« Enregistrer ») ou à la première personne
- Messages d'erreur : ce qui s'est passé, puis quoi faire — jamais de blâme
- Ponctuation : point final dans les messages courts, oui ou non
- Termes bannis et leurs remplacements

---

## 7. Anti-patterns

Ce que nous ne faisons pas, et qu'aucune génération ne doit produire :

- Couleurs, rayons ou tailles de police en dur
- Classes de couleur Tailwind par défaut
- Composants recréés alors qu'ils existent dans le système
- `!important` pour forcer un style
- Styles inline sauf valeur calculée dynamiquement
- Marges négatives pour corriger un espacement
- Un nouveau gris, un nouveau bleu, un nouveau rayon « juste pour ce cas-là »
- Texte centré pour les paragraphes et les listes
- Animations supérieures à 300 ms sur une interaction directe

---

## 8. Instructions pour les agents

Quand tu génères ou modifies de l'interface dans ce projet :

1. Lis `tokens/tokens.json` avant d'écrire du style. C'est le vocabulaire disponible.
2. Réutilise les composants existants. Ne recrée jamais un équivalent.
3. N'utilise que les tokens sémantiques listés en section 3.
4. Garde l'échelle d'espacement Tailwind par défaut.
5. Si une valeur nécessaire n'a pas de token, **arrête-toi et signale-le** plutôt que
   d'inventer. Un token manquant est une information utile ; une valeur en dur est un bug
   silencieux.
6. Vérifie les six états interactifs sur tout élément cliquable.
7. À la fin, liste les tokens utilisés — cela permet de vérifier qu'aucune valeur littérale
   ne s'est glissée dans le rendu.

---

## 9. Workflow

```
Designer (Tokens Studio, dans Figma)
        ↓ Push sur la branche tokens/design
   tokens/tokens.json  ← SOURCE DE VÉRITÉ
        ↓ revue en Pull Request
        ↓ Style Dictionary
   variables CSS → Tailwind (@theme)
        ↓
   Produits · Storybook · Claude Code · Claude Design
```

Commande de recompilation : `⟨à remplir⟩`

---

## 10. Gouvernance

| Rôle | Personne |
|---|---|
| Propriétaire du design system | ⟨à remplir⟩ |
| Valideur des PR de tokens | ⟨à remplir⟩ |
| Référent bibliothèque Figma | ⟨à remplir⟩ |

**Versionnement du package :** patch = correction sans impact · minor = ajout
rétrocompatible · major = rupture, nécessite communication et fenêtre de migration.

**Revue trimestrielle de dérive :** recenser les composants recréés hors système. La dérive
n'est pas un échec des équipes — c'est le signal qu'il manque quelque chose au système ou
qu'il est trop pénible à utiliser. Dans les deux cas, c'est le système qu'on corrige.

---

*Dernière mise à jour : ⟨date⟩ · Modifications par PR uniquement.*
