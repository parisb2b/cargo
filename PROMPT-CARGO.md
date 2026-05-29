# PROMPT CLAUDE CODE — Container Optimizer
## Mise à jour simple — remplacer index.html et redéployer

---

## RÈGLES STRICTES

1. NE PAS modifier index.html — utiliser tel quel
2. NE PAS ajouter d'authentification
3. NE PAS ajouter de login / auth-wall / Google Sign-In
4. Juste copier, committer, déployer
5. Générer MAJ-CARGO.txt à la fin

---

## CE QUE CONTIENT index.html (déjà intégré, ne pas toucher)

- Firestore SDK (sans auth) — sync temps réel multi-navigateurs
- Firebase config : cargo-optimizer-fca82
- Toutes les fonctionnalités : 3D, archive, plan Excel, etc.
- AUCUNE authentification Google — la page s'ouvre directement

---

## ÉTAPES

### 1. Aller dans le repo existant

```bash
cd ~/mactell/cargo
git fetch origin && git reset --hard origin/main
```

### 2. Remplacer index.html par la nouvelle version

```bash
cp ~/Downloads/container-optimizer-final.html ~/mactell/cargo/index.html
```

### 3. Vérifier qu'il n'y a PAS de auth-wall dans le fichier

```bash
grep -c "auth-wall" ~/mactell/cargo/index.html
```

Le résultat doit être **0**. Si c'est autre chose, stopper et signaler.

### 4. Committer et pusher

```bash
cd ~/mactell/cargo
git add index.html
git commit -m "fix: supprimer auth Google - Firestore seul sans login"
git push origin main
```

### 5. Déployer sur Vercel

```bash
npx vercel --prod --yes
```

### 6. Mettre à jour les règles Firestore (accès public sans auth)

Dans Firebase Console > cargo-optimizer-fca82 > Firestore > Règles, publier :

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

### 7. Vérifications

1. Ouvrir l'URL Vercel → page s'affiche DIRECTEMENT sans login
2. Créer un chargement → Enregistrer
3. Ouvrir dans un 2e navigateur → le chargement apparaît (sync Firestore)
4. Vue 3D s'ouvre en premier avec labels sur les colis

---

## RAPPORT

Créer MAJ-CARGO.txt avec :
- URL Vercel finale
- Confirmation : aucun auth-wall présent
- Résultat des 4 vérifications
