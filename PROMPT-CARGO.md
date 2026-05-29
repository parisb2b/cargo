# PROMPT CLAUDE CODE — Container Optimizer
## Mise à jour — cargo.sasfr.com
## Firestore uniquement — SANS authentification

---

## RÈGLES D'EXÉCUTION

1. `git add -A && git commit -m "..." && git push` après chaque fichier modifié
2. Résoudre les erreurs de manière autonome
3. Générer `MAJ-CARGO.txt` à la fin

---

## CONTEXTE

Le repo `parisb2b/cargo` existe déjà sur GitHub (branche `main`).
Le projet Vercel est déjà déployé sur `cargo-341k24uy1-parisb2bs-projects.vercel.app`.

**Objectif** : remplacer `index.html` par la nouvelle version avec Firestore (sans auth)
et redéployer sur Vercel → `cargo.sasfr.com` sera opérationnel.

---

## ÉTAPE 1 — Copier le nouveau index.html et pusher

```bash
cd ~/mactell/cargo
git fetch origin && git reset --hard origin/main
```

Le fichier `~/mactell/cargo/index.html` est déjà la bonne version (avec Firestore intégré).

```bash
git add index.html
git commit -m "feat: Firestore sync sans auth - version finale"
git push origin main
```

---

## ÉTAPE 2 — Firestore Rules (SANS auth)

Dans Firebase Console > `cargo-optimizer-fca82` > Firestore > Règles,
remplacer tout par :

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /cargo_sessions/{docId} {
      allow read, write: if true;
    }
  }
}
```

Publier les règles.

**Note** : règles ouvertes (pas d'auth). Si tu veux restreindre plus tard,
on pourra ajouter des règles par IP ou token custom.

---

## ÉTAPE 3 — Redéployer sur Vercel

```bash
cd ~/mactell/cargo
npx vercel --prod --yes
```

---

## ÉTAPE 4 — DNS cargo.sasfr.com (si pas encore fait)

Dans Vercel > projet cargo > Settings > Domains > Ajouter `cargo.sasfr.com`
Vercel indique : ajouter un enregistrement **A** dans IONOS :
```
Type : A
Nom  : cargo
IP   : 76.76.21.21
TTL  : 3600
```

Vérifier :
```bash
curl -I https://cargo.sasfr.com
```

---

## ÉTAPE 5 — Vérifications

1. Ouvrir `https://cargo.sasfr.com` → page s'affiche directement **sans login**
2. Créer un chargement → ajouter des colis → **Enregistrer**
3. Ouvrir dans un **2e navigateur** → le chargement apparaît (sync Firestore)
4. Modifier un colis dans le 1er navigateur → mis à jour dans le 2e
5. Vue 3D s'ouvre directement avec labels sur les colis
6. Export Excel plan de chargement → 3 onglets

---

## ÉTAPE 6 — Rapport

Créer `~/mactell/cargo/MAJ-CARGO.txt` :
```
URL Vercel   : cargo-341k24uy1-parisb2bs-projects.vercel.app
URL domaine  : cargo.sasfr.com
Firebase     : cargo-optimizer-fca82
Collection   : cargo_sessions (accès public, pas d'auth)
Vérifications: [résultats des 6 points]
```

---

## ARCHITECTURE FINALE

```
cargo.sasfr.com (ou URL Vercel)
├── index.html — SPA pure JS, 144KB
│   ├── Firestore SDK (type=module, CDN)
│   ├── saveAll() → localStorage + Firestore sync
│   ├── onSnapshot() → sync temps réel multi-navigateurs
│   └── deleteDoc() → suppression sync
└── Firebase cargo-optimizer-fca82
    └── Firestore: cargo_sessions/{sessionId}
        ├── id, name, container, colis[]
        ├── sessionTrash, colisTrash
        └── updatedAt (serverTimestamp)
```

## EN CAS D'ERREUR

| Erreur | Solution |
|---|---|
| `Missing or insufficient permissions` | Firestore rules pas encore publiées — refaire étape 2 |
| Page blanche | Vider le cache navigateur (Cmd+Shift+R) |
| DNS pas résolu | Attendre 30 min après modif IONOS |
| Vercel deploy fail | `vercel login` puis réessayer |
