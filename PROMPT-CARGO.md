# PROMPT CLAUDE CODE — Container Optimizer
## Déploiement complet — cargo.sasfr.com
## Version finale avec toutes fonctionnalités

---

## RÈGLES D'EXÉCUTION

1. `git add -A && git commit -m "..." && git push` après chaque étape
2. Résoudre les erreurs de manière autonome sans confirmation
3. Ne jamais attendre — pusher en temps réel
4. Générer `MAJ-CARGO.txt` à la fin

---

## CONTEXTE — CE QUE CONTIENT index.html

Fichier source : `~/mactell/cargo/index.html` (~143KB, version finale validée).
**Ne pas modifier le HTML** sauf pour les insertions Firebase ci-dessous.

### Fonctionnalités déjà intégrées :
- Algorithme EP-3DBP (Extreme Point 3D Bin Packing)
- Vue 3D WebGL Three.js — ouvre par défaut, labels gravés sur les 6 faces
- Filtres de couches multi-sélection (Sol / C2 / C3 / Gauche / Droite)
- Labels PORTE / FOND / GAUCHE / DROITE sur le sol 3D
- Dupliquer une ligne colis (bouton ⧉)
- Archive colis 30 jours + Archive chargements 30 jours
- Toast annuler sur toute suppression
- Plan de chargement Excel 3 onglets (ordre physique de chargement)
- Enregistrer manuel Ctrl+S
- Bilingue FR/中文

---

## INFRASTRUCTURE

```
Firebase Project ID  : cargo-optimizer-fca82
API Key              : AIzaSyDyk2XwTDY2tjbnUsVRI-hg3jptd9sNe2Y
Auth Domain          : cargo-optimizer-fca82.firebaseapp.com
Storage Bucket       : cargo-optimizer-fca82.firebasestorage.app
Messaging Sender ID  : 89903431919
App ID               : 1:89903431919:web:52c9e432d3f4839b03db1a

GitHub User          : parisb2b
Repo                 : cargo (PUBLIC)
Domaine              : cargo.sasfr.com (IONOS)
```

---

## ÉTAPE 1 — Git init + GitHub

```bash
cd ~/mactell/cargo
git init
git branch -M main
echo "node_modules/" > .gitignore
echo ".vercel/" >> .gitignore
echo "*.DS_Store" >> .gitignore
echo "# Container Optimizer" > README.md
```

Créer `vercel.json` :
```json
{
  "version": 2,
  "builds": [{ "src": "index.html", "use": "@vercel/static" }],
  "routes": [{ "src": "/(.*)", "dest": "/index.html" }]
}
```

```bash
gh repo create parisb2b/cargo --public --source=. --push
```

---

## ÉTAPE 2 — Modifier index.html : Firebase Auth + Firestore

### 2a. Ajouter dans `<head>` après les scripts existants :

```html
<script type="module">
  import { initializeApp } from 'https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js';
  import {
    getAuth, signInWithPopup, signOut, onAuthStateChanged,
    GoogleAuthProvider
  } from 'https://www.gstatic.com/firebasejs/10.12.0/firebase-auth.js';
  import {
    getFirestore, collection, doc, setDoc, deleteDoc,
    onSnapshot, query, where, orderBy, serverTimestamp
  } from 'https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js';

  const app = initializeApp({
    apiKey: "AIzaSyDyk2XwTDY2tjbnUsVRI-hg3jptd9sNe2Y",
    authDomain: "cargo-optimizer-fca82.firebaseapp.com",
    projectId: "cargo-optimizer-fca82",
    storageBucket: "cargo-optimizer-fca82.firebasestorage.app",
    messagingSenderId: "89903431919",
    appId: "1:89903431919:web:52c9e432d3f4839b03db1a"
  });

  const auth = getAuth(app);
  const db = getFirestore(app);
  const provider = new GoogleAuthProvider();

  window._firebase = {
    auth, db, provider,
    signInWithPopup, signOut, onAuthStateChanged,
    collection, doc, setDoc, deleteDoc,
    onSnapshot, query, where, orderBy, serverTimestamp
  };
  window.dispatchEvent(new Event('firebase-ready'));
</script>
```

### 2b. Dans la nav `.nav-right`, ajouter avant `</div>` :

```html
<div id="auth-section" style="display:flex;align-items:center;gap:8px;margin-left:8px;padding-left:8px;border-left:1px solid var(--border)">
  <div id="auth-user" style="display:none;align-items:center;gap:8px">
    <img id="auth-avatar" style="width:28px;height:28px;border-radius:50%;object-fit:cover" src="" alt="">
    <span id="auth-name" style="font-size:12px;font-weight:500;max-width:100px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap"></span>
    <button class="btn btn-sm btn-ghost" onclick="doSignOut()">
      <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" y1="12" x2="9" y2="12"/></svg>
    </button>
  </div>
  <button id="btn-signin" class="btn btn-sm btn-primary" onclick="doSignIn()" style="display:none">
    Connexion Google
  </button>
</div>
```

### 2c. Juste avant le premier `<div class="page"`, ajouter l'overlay :

```html
<div id="auth-wall" style="display:none;position:fixed;inset:0;background:linear-gradient(135deg,#0f172a 0%,#1e3a8a 100%);z-index:500;align-items:center;justify-content:center;flex-direction:column">
  <div style="text-align:center;color:#fff;max-width:360px;padding:40px">
    <div style="font-size:48px;margin-bottom:16px">📦</div>
    <h1 style="font-size:28px;font-weight:800;margin-bottom:8px">Container<span style="color:#60a5fa">Optimizer</span></h1>
    <p style="font-size:14px;color:rgba(255,255,255,.6);margin-bottom:32px;line-height:1.6">Optimiseur de remplissage de conteneurs maritimes 3D.</p>
    <button onclick="doSignIn()" style="display:inline-flex;align-items:center;gap:10px;background:#fff;color:#1a1a18;padding:12px 24px;border-radius:8px;border:none;cursor:pointer;font-size:14px;font-weight:600;box-shadow:0 4px 20px rgba(0,0,0,.3)">
      <svg width="20" height="20" viewBox="0 0 24 24"><path fill="#4285F4" d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"/><path fill="#34A853" d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"/><path fill="#FBBC05" d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l3.66-2.84z"/><path fill="#EA4335" d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"/></svg>
      Connexion avec Google
    </button>
  </div>
</div>
```

### 2d. Dans le `<script>` principal — remplacer/ajouter ces fonctions :

**Trouver et remplacer `saveAll()` :**
```javascript
function saveAll(){
  const cut=Date.now()-TRASH_DAYS*86400000;
  sessionTrash=sessionTrash.filter(function(x){ return x.deletedAt>cut; });
  Object.keys(colisTrash).forEach(function(k){
    colisTrash[k]=(colisTrash[k]||[]).filter(function(x){ return x.deletedAt>cut; });
  });
  try{
    localStorage.setItem('clo_sessions', JSON.stringify(sessions));
    localStorage.setItem('clo_session_trash', JSON.stringify(sessionTrash));
    localStorage.setItem('clo_colis_trash', JSON.stringify(colisTrash));
    showSaveBadge();
    updateArchiveBadges();
  }catch(e){}
  // Firestore sync
  var uid=getUserId();
  if(!uid||!window._firebase) return;
  var fb=window._firebase;
  sessions.forEach(function(s){
    fb.setDoc(fb.doc(fb.db,'cargo_sessions',uid+'_'+s.id),
      Object.assign({},s,{ownerId:uid,updatedAt:fb.serverTimestamp()})
    ).catch(console.error);
  });
}
```

**Trouver et remplacer `loadAll()` :**
```javascript
function loadAll(){
  try{ var d=localStorage.getItem('clo_sessions'); if(d)sessions=JSON.parse(d); }catch(e){ sessions=[]; }
  try{ var d2=localStorage.getItem('clo_session_trash'); if(d2)sessionTrash=JSON.parse(d2); }catch(e){ sessionTrash=[]; }
  try{ var d3=localStorage.getItem('clo_colis_trash'); if(d3)colisTrash=JSON.parse(d3); }catch(e){ colisTrash={}; }
}
```

**Ajouter juste avant `function loadAll()` :**
```javascript
var _currentUser=null;
function getUserId(){ return _currentUser?_currentUser.uid:null; }

function doSignIn(){
  var fb=window._firebase;
  fb.signInWithPopup(fb.auth,fb.provider).catch(console.error);
}
function doSignOut(){
  if(_firestoreUnsub){ _firestoreUnsub(); _firestoreUnsub=null; }
  window._firebase.signOut(window._firebase.auth).then(function(){
    sessions=[]; currentSessionId=null; renderSessions();
  });
}

var _firestoreUnsub=null;
function startFirestoreSync(){
  var uid=getUserId();
  if(!uid||!window._firebase) return;
  if(_firestoreUnsub) _firestoreUnsub();
  var fb=window._firebase;
  _firestoreUnsub=fb.onSnapshot(
    fb.query(fb.collection(fb.db,'cargo_sessions'),fb.where('ownerId','==',uid),fb.orderBy('updatedAt','desc')),
    function(snap){
      sessions=[];
      snap.forEach(function(d){ sessions.push(d.data()); });
      try{ localStorage.setItem('clo_sessions',JSON.stringify(sessions)); }catch(e){}
      renderSessions(); updateArchiveBadges();
    },
    function(err){ console.error('Firestore sync error:',err); }
  );
}
```

**Dans `deleteSession()`, après `sessions=sessions.filter(...)`, ajouter :**
```javascript
  var uid=getUserId();
  if(uid&&window._firebase){
    var fb=window._firebase;
    fb.deleteDoc(fb.doc(fb.db,'cargo_sessions',uid+'_'+id)).catch(console.error);
  }
```

**Remplacer la section INIT à la toute fin du script (les lignes `loadAll(); if(!window._firebaseReady) renderSessions(); applyLang();`) par :**
```javascript
applyLang();
document.getElementById('auth-wall').style.display='flex';

window.addEventListener('firebase-ready', function(){
  var fb=window._firebase;
  fb.onAuthStateChanged(fb.auth, function(user){
    _currentUser=user;
    var wall=document.getElementById('auth-wall');
    var authUser=document.getElementById('auth-user');
    var btnSignin=document.getElementById('btn-signin');
    if(user){
      wall.style.display='none';
      authUser.style.display='flex';
      if(btnSignin) btnSignin.style.display='none';
      var av=document.getElementById('auth-avatar');
      var nm=document.getElementById('auth-name');
      if(av) av.src=user.photoURL||'';
      if(nm) nm.textContent=user.displayName||user.email||'';
      loadAll();
      startFirestoreSync();
    } else {
      wall.style.display='flex';
      authUser.style.display='none';
      if(btnSignin) btnSignin.style.display='inline-flex';
      sessions=[]; currentSessionId=null;
    }
  });
});

setTimeout(function(){
  if(!window._firebase){
    document.getElementById('auth-wall').style.display='none';
    loadAll(); renderSessions();
  }
}, 3000);
```

---

## ÉTAPE 3 — Règles Firestore

Firebase Console > `cargo-optimizer-fca82` > Firestore > Règles :

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /cargo_sessions/{docId} {
      allow read, write: if request.auth != null
        && request.auth.uid == resource.data.ownerId;
      allow create: if request.auth != null
        && request.auth.uid == request.resource.data.ownerId;
    }
  }
}
```

Déployer les règles.

---

## ÉTAPE 4 — Déployer sur Vercel

```bash
cd ~/mactell/cargo
npx vercel --prod --yes
```

---

## ÉTAPE 5 — Domaine cargo.sasfr.com

**Dans Vercel :**
- Settings > Domains > Ajouter `cargo.sasfr.com`
- Copier le CNAME fourni

**Dans IONOS :**
- https://my.ionos.fr/domain-details/cargo.sasfr.com
- DNS > CNAME : `cargo` → valeur Vercel, TTL 3600

**Dans Firebase Auth :**
- Authentication > Paramètres > Domaines autorisés > Ajouter `cargo.sasfr.com`

```bash
dig cargo.sasfr.com CNAME
```

---

## ÉTAPE 6 — Vérifications (12 points)

1. `https://cargo.sasfr.com` → overlay connexion bleu
2. Connexion Google → popup → succès → avatar dans nav
3. Créer chargement → colis → visualiser → **vue 3D s'ouvre directement**
4. Labels sur 6 faces de chaque colis (N° position + ordre chargement)
5. Filtres couches multi-sélection + PORTE/FOND sur sol 3D
6. Bouton ⧉ duplique une ligne colis
7. Supprimer ligne → toast 5s → annuler → ligne restaurée
8. Archive colis → lignes supprimées visibles et restaurables
9. Supprimer chargement → archive chargements → restaurer
10. Export Excel plan chargement → 3 onglets corrects
11. 2 navigateurs même compte → sync Firestore temps réel
12. Toggle FR/中文 → tout traduit

---

## ÉTAPE 7 — Rapport

Créer `~/mactell/cargo/MAJ-CARGO.txt` :
- URL Vercel
- cargo.sasfr.com opérationnel
- Firebase : cargo-optimizer-fca82
- Collection : cargo_sessions
- 12 vérifications : résultats

---

## ARCHITECTURE

```
cargo.sasfr.com
├── Vercel (static)
│   └── index.html (143KB, SPA)
├── Firebase cargo-optimizer-fca82
│   ├── Auth (Google)
│   └── Firestore: cargo_sessions/{uid}_{id}
└── GitHub parisb2b/cargo
```

## EN CAS D'ERREUR

| Erreur | Solution |
|---|---|
| `auth/unauthorized-domain` | Firebase Auth > Domaines > ajouter cargo.sasfr.com |
| `permission-denied` | Vérifier règles Firestore déployées |
| DNS ne propage pas | Attendre 30 min, vérifier CNAME IONOS |
| `gh` not found | `brew install gh && gh auth login` |
| `vercel` not found | `npm i -g vercel && vercel login` |
