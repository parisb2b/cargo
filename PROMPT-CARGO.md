# PROMPT CLAUDE CODE — CODE REPAIR & FIRESTORE INTEGRATION
## Correctif sauvegarde et synchronisation historique — cargo.sasfr.com

---

## RÈGLES D'EXÉCUTION

1. `git add -A && git commit -m "..." && git push` après chaque étape validée.
2. Résoudre les erreurs de manière autonome sans confirmation (`--dangerously-skip-permissions`).
3. S'assurer de forcer l'encodage UTF-8 sur tous les fichiers modifiés ou générés.
4. Générer le rapport `MAJ-CARGO.txt` à la fin dans le répertoire courant.

---

## DIAGNOSTIC ET OBJECTIF PRIORITAIRE

- **Problème constaté** : Le bouton "Enregistrer" en haut à droite est visible graphiquement mais inactif. Les clics n'envoient rien à Firebase Firestore et l'onglet "Liste des cargos" reste bloqué sur "Aucun chargement enregistré".
- **Mission** : Réparer les fonctions de persistance de `index.html`. Injecter proprement le SDK Firebase, lier le bouton "Enregistrer" à une fonction d'écriture Firestore fonctionnelle, et forcer la lecture en temps réel de la collection publique `cargo_sessions` pour alimenter l'historique sans nécessiter de connexion.

---

## ÉTAPE 1 — Injection et Initialisation Robustes du SDK Firebase

Vérifier et injecter au tout début du `<head>` l'importation des modules Firebase via CDN :

```html
<script type="module">
  import { initializeApp } from 'https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js';
  import { getFirestore, collection, doc, setDoc, onSnapshot, serverTimestamp, orderBy, query } from 'https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js';

  const firebaseConfig = {
    apiKey: "AIzaSyDyk2XwTDY2tjbnUsVRI-hg3jptd9sNe2Y",
    authDomain: "cargo-optimizer-fca82.firebaseapp.com",
    projectId: "cargo-optimizer-fca82",
    storageBucket: "cargo-optimizer-fca82.firebasestorage.app",
    messagingSenderId: "89903431919",
    appId: "1:89903431919:web:52c9e432d3f4839b03db1a"
  };

  try {
    const app = initializeApp(firebaseConfig);
    window._db = getFirestore(app);
    window._firestoreUtils = { collection, doc, setDoc, onSnapshot, serverTimestamp, orderBy, query };
    console.log("Firebase Firestore initialisé.");
  } catch (e) {
    console.error("Erreur d'initialisation Firebase:", e);
  }
</script>
```

---

## ÉTAPE 2 — Réécriture des Fonctions de Sauvegarde et d'Écoute Temps Réel

1. **Écouteur temps réel** : `onSnapshot` sur `cargo_sessions` → alimente `sessions[]` → appelle `renderSessions()`.
2. **Bouton Enregistrer** : `saveCurrentCargoToFirestore()` → `setDoc(doc(window._db, 'cargo_sessions', id), {...})`
3. **Fallback localStorage** : si Firestore échoue, sauvegarder dans `localStorage` en secours.

---

## ÉTAPE 3 — Déploiement et Rapports

1. Synchroniser avec GitHub `parisb2b/cargo`.
2. Déployer Vercel : `vercel --prod --yes`.
3. Générer `MAJ-CARGO.txt`.
