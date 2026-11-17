# Chibi Autobattler — contexte général

Jeu mobile de cartes en « autobattler chess » (mélange Yu-Gi-Oh × Clash Mini), univers dark fantasy chibi. On place des cartes qui deviennent des chibis sur un plateau, puis ils se battent seuls. Auteur : Germain (intégrateur, maîtrise Illustrator et Photoshop, débutant en 3D et sur GitHub). Réponses en français.

## Règles actuelles (V3)

- **Terrain :** grille 5×10, soit 5×5 par joueur. Pendant la préparation, la moitié adverse est cachée ; elle est révélée au début du combat.
- **Préparation :** simultanée, avec un minuteur (40 s dans le prototype).
- **Main et coûts :** main de 5 cartes, coût de 0 à 5. Ressource de départ 5, puis 5 + (manche − 1)² à chaque manche. Ce qui n'est pas dépensé se reporte, plafonné à 30.
- **Combat :** les chibis ciblent l'ennemi le plus proche. La manche se termine quand un camp n'a plus de chibis vivants, ou après 40 s : le camp avec le plus de chibis vivants gagne, puis le plus de PV restants en cas d'égalité, sinon nul (aucun point).
- **Persistance :** les plateaux restent d'une manche à l'autre ; les chibis morts reviennent à PV pleins. Premier à 3 manches gagnées.
- **Niveaux :** fusionner des cartes identiques donne +10 % PV et dégâts par niveau, maximum niveau 3. Les unités à distance ont −30 % PV.
- **Limites de stats :** 70 PV maximum, dégâts entre 2 et 10.
- **Deck :** 20 cartes, 3 copies maximum d'un même modèle, pioche avec remise. 12 modèles de cartes (coût 0 à 5 × mêlée ou distance).
- **Modes visés :** PvE contre un adversaire caché, et PvP. Piste retenue : un seul moteur de combat déterministe, PvP asynchrone d'abord.

## Valeurs du prototype

| Coût | PV | Dégâts |
| --- | --- | --- |
| 0 | 20 | 2 |
| 1 | 30 | 3 |
| 2 | 40 | 5 |
| 3 | 50 | 6 |
| 4 | 60 | 8 |
| 5 | 70 | 10 |

Portée mêlée 1,15 case, distance 3,2 cases ; vitesse 1,5 case/s ; une attaque par seconde ; pas de simulation fixe de 1/30 s. Deck de départ : `0M:2, 0D:1, 1M:2, 1D:2, 2M:2, 2D:2, 3M:2, 3D:2, 4M:2, 4D:1, 5M:1, 5D:1`.

## Direction artistique

Univers dark fantasy avec des chibis. Les illustrations de cartes seront dessinées par Germain (Illustrator, Photoshop). Les chibis sont en 3D générée par code (Three.js, ombrage toon à 3 paliers, contours en coque inversée), avec un anneau bleu sous les alliés et rouge sous les ennemis. Les modèles procéduraux servent de placeholders avant de vrais modèles (glTF).

## Fichiers et contrat entre modules

- **`index.html` (prototype) :** prototype 2D en cercles, avec menu et gestionnaire de deck puis combat contre une IA. La simulation tient entre `/*SIM_START*/` et `/*SIM_END*/` et se teste sans navigateur (Node). Fonctions clés : `newGame`, `canPlay`, `play`, `moveUnit`, `aiPlay`, `startRound`, `startCombat`, `stepCombat(dt)`, `finishRound`, `tally`.
- **`chibis3d.html` :** galerie 3D (onglets Fiche et Terrain) des 24 personnages générés. Le code des personnages tient entre `/*CHAR_START*/` et `/*CHAR_END*/`. Contrat : `buildChibi(card, side)` avec `card = {cost, type}` (`type` vaut `'M'` ou `'D'`) et `side` valant `'P'` ou `'E'`. Il renvoie `{root, setState(état), update(dt)}`, avec les états `idle`, `walk`, `attack`, `hit`, `dead`.
- **`three.min.js` :** Three.js r128, chargé en local par `chibis3d.html`.
- **Règle de collaboration :** chaque conversation modifie son propre module et garde ce contrat. Si un changement le casse, elle le dit explicitement.

## Questions encore ouvertes

- Comment on obtient les cartes : catalogue complet ou collection à débloquer ?
- Comment on améliore une unité sur le plateau, et comment la main se recharge entre les manches.
- Si le plafond de 30 concerne le stock ou le gain par manche.
- Comment brancher les chibis 3D dans le prototype, puis emballer le tout pour mobile.
