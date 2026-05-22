# PROMPT CLAUDE CODE — Container Optimizer
## Déploiement Cloud avec Bouton Enregistrer & Firestore Centralisé (Sans Auth) — cargo.sasfr.com

---

## RÈGLES D'EXÉCUTION

1. `git add -A && git commit -m "..." && git push` après chaque étape validée.
2. Résoudre les erreurs de manière autonome sans confirmation (`--dangerously-skip-permissions`).
3. Pusher sur GitHub en temps réel — ne jamais attendre la fin.
4. Travailler exclusivement avec l'encodage UTF-8 pour tous les fichiers.
5. Générer le rapport `MAJ-CARGO.txt` à la fin dans le répertoire courant.

---

## CONTEXTE & OBJECTIF

- **Fichier source** : `C:\DATA-MC-2030\CARGO\container-optimizer.html` à renommer/copier en `index.html`.
- **ZÉRO AUTHENTIFICATION** : L'accès à l'application reste direct, public et anonyme. Aucun écran de connexion ou formulaire de création de compte ne doit être ajouté.
- **CONNEXION FIRESTORE** : Centraliser tous les chargements sur Firebase Firestore pour qu'ils soient partagés et sauvegardés.

---

## ÉTAPE 1 — Configuration Firebase (Firestore Public)

1. Initialiser le SDK Firebase Firestore directement dans le `<head>` de `index.html` avec les identifiants existants :
```javascript
   const firebaseConfig = {
     apiKey: "AIzaSyDyk2XwTDY2tjbnUsVRI-hg3jptd9sNe2Y",
     authDomain: "cargo-optimizer-fca82.firebaseapp.com",
     projectId: "cargo-optimizer-fca82",
     storageBucket: "cargo-optimizer-fca82.firebasestorage.app",
     messagingSenderId: "89903431919",
     appId: "1:89903431919:web:52c9e432d3f4839b03db1a"
   };
```

2. Règles Firestore publiques pour la collection `cargo_sessions` : lecture/écriture sans authentification.

---

## ÉTAPE 2 — Interface : Bouton Enregistrer & Navigation

1. **Bouton Enregistrer visible** :
   - Ajouter un bouton **« Enregistrer »** / **« 保存 »** bien visible dans la barre de navigation supérieure (à côté de « + Nouveau chargement »).
   - Ce bouton doit enregistrer manuellement et immédiatement l'état complet du cargo en cours dans Firestore.
   - Retour visuel : le badge "Sauvegardé" / "已保存" doit s'afficher brièvement après chaque sauvegarde réussie.
   - Le bouton doit être désactivé visuellement (grisé) quand aucun cargo n'est sélectionné.

2. **Sauvegarde automatique conservée** :
   - La sauvegarde auto à chaque modification (colis, conteneur) reste active en complément du bouton manuel.

3. **Onglet Liste des cargos** :
   - L'onglet « Liste des cargos » / « 货运列表 » reste dans le menu supérieur gauche.
   - Sections « En cours » et « Historique » conservées.

---

## ÉTAPE 3 — Initialisation Git & Déploiement Vercel

1. `git init` dans `C:\DATA-MC-2030\CARGO\`.
2. Créer `.gitignore` et `vercel.json`.
3. Créer le dépôt GitHub public `parisb2b/cargo` via `gh` et pousser.
4. Déployer sur Vercel : `vercel --prod --yes`.

---

## ÉTAPE 4 — Rapport final

Générer `C:\DATA-MC-2030\CARGO\MAJ-CARGO.txt` avec URL Vercel, lien GitHub, et récapitulatif des modifications.
