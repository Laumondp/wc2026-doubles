# WC2026 Doubles — Gestionnaire de collection Panini

Application web pour gérer sa collection de cartes **Panini Adrenalyn XL FIFA World Cup 2026** : suivi des doubles, wishlist, synchronisation cloud et coordination des échanges entre collectionneurs.

🌐 **[wc2026-doubles.vercel.app](https://wc2026-doubles.vercel.app)**

---

## Stack technique

- **Vanille JS + HTML5 + CSS3** — zéro framework, zéro dépendance npm
- **Firebase** (Auth email/password + Firestore) — sync cloud multi-appareils
- **Vercel** — déploiement statique automatique

---

## Fonctionnalités

### Onglet Accueil
Navigation hiérarchique en 3 niveaux :
1. **Types de cartes** (LIMITED / ICON / HERO / BASE) avec barre de progression
2. **Pays par type** — grille avec % de complétion, badge vert si 100%
3. **Cartes du pays** — contrôles quantité (±), aperçu photo joueur au survol, wishlist (❤️)

### Onglet Collection
- Toutes les cartes possédées (qty > 0)
- Filtre par type + recherche par nom / équipe / numéro

### Onglet Mes Doubles
- Cartes avec qty ≥ 2
- Résumé par type : compteur + valeur en points (LIMITED 15pts, ICON 10pts, HERO 5pts, BASE 1pt)

### Profil & Sync cloud
- Compte email/password (Firebase Auth)
- Photo de profil (upload + redimensionnement 256×256)
- Synchronisation automatique Firestore à chaque modification
- Indicateur de statut : ☁ / ↻ / ☁✓ / ☁✗
- Affichage des doubles des autres collectionneurs connectés

### Gestion des échanges
- Ajouter des partenaires d'échange (nom, email, contact)
- Saisir leurs doubles et cartes recherchées
- Importer/exporter une collection en JSON pour coordonner les échanges
- Vue deck par partenaire : onglets Doubles / Recherchées / Collection

### Paramètres
- Import / export JSON complet
- Réinitialisation collection ou profil
- Notifications push via **ntfy.sh** (canal privé, sans compte)
- Valeurs des points éditables (admin uniquement)

---

## Catalogue de cartes

**630 cartes** issues de la checklist officielle Panini Adrenalyn XL FIFA 2026.

| Type | Icône | Description | Points |
|---|---|---|---|
| LIMITED | ⭐ | Superstars mondiales (Messi, Mbappé, Vinícius Jr…) | 15 |
| ICON | 💎 | Icône nationale, 1 par pays | 10 |
| HERO | 🌟 | Fan Favourite, 1 par pays | 5 |
| BASE | 🃏 | Joueurs standards + Team Crests | 1 |

**48 nations qualifiées** avec drapeaux emoji, codes FIFA 3 lettres et photos joueurs (cache TheSportsDB).

---

## Structure des données

```javascript
// État local (LocalStorage + Firestore)
{
  cards: [...],               // 630 cartes du catalogue
  collection: { "C1": 2 },   // quantités possédées par carte
  wishlist: ["C45", "C78"],  // cartes recherchées
  players: [...],             // partenaires d'échange
  values: { LIMITED:15, ICON:10, HERO:5, BASE:1 }
}
```

---

## Effets visuels

- **Hologramme 3D** sur les cartes (canvas 2D : étoiles procédurales, mouse tracking, scanlines, blend mode screen)
- **Blister card animée** en page d'accueil (floating, tilt 3D, foil shimmer rainbow)
- **Animations au rendu** : pop + stagger delay par carte (cubic-bezier custom)
- **Mode clair / sombre** via variables CSS, persisté en localStorage

---

## Déploiement

Déploiement automatique sur Vercel à chaque push. Pas de build step — fichiers statiques servis directement.

```bat
rem deploy.bat
git add -A && git commit -m "update" && git push
```

### Firestore Rules
```
match /users/{userId} {
  allow read: if request.auth != null;
  allow write: if request.auth != null && request.auth.uid == userId;
}
```
Chaque utilisateur peut lire les profils des autres (pour voir leurs doubles), mais modifier uniquement le sien.

---

## Génération du catalogue

Le catalogue `cards_data.js` est généré depuis la checklist Excel officielle Panini via des scripts PowerShell :

```powershell
.\generate_cards.ps1   # extrait les cartes de l'Excel → cards_data.js
.\inject.ps1           # injecte les drapeaux emoji dans index.html
.\find_gaps.ps1        # détecte les cartes manquantes dans le catalogue
```
