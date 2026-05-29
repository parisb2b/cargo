# PROMPT CLAUDE CODE — Supprimer l'auth Google du Container Optimizer

## RÈGLE ABSOLUE
NE PAS ajouter de code Firebase Auth.
NE PAS ajouter de auth-wall.
NE PAS ajouter de Google Sign-In.
NE PAS modifier index.html autrement que ce qui est demandé ci-dessous.

---

## CONTEXTE

Le fichier index.html sur le repo parisb2b/cargo contient une
auth-wall Google non désirée. Il faut la supprimer.

---

## ÉTAPES

### 1. Récupérer le repo

```bash
cd ~/mactell/cargo
git fetch origin && git reset --hard origin/main
```

### 2. Supprimer tout le code auth de index.html

Exécuter ce script Python pour nettoyer :

```bash
python3 << 'PYEOF'
with open('index.html', 'r', encoding='utf-8') as f:
    content = f.read()

import re

# Supprimer le bloc firebase Auth du script type=module
# Garder seulement Firestore (pas Auth)
old_module = re.search(r'<script type="module">.*?</script>', content, re.DOTALL)
if old_module:
    print("Module script trouvé, longueur:", len(old_module.group()))
    # Remplacer par version Firestore uniquement sans Auth
    new_module = '''<script type="module">
  import { initializeApp } from 'https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js';
  import {
    getFirestore, collection, doc, setDoc, deleteDoc,
    onSnapshot, query, orderBy, serverTimestamp
  } from 'https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js';
  const app = initializeApp({
    apiKey: "AIzaSyDyk2XwTDY2tjbnUsVRI-hg3jptd9sNe2Y",
    authDomain: "cargo-optimizer-fca82.firebaseapp.com",
    projectId: "cargo-optimizer-fca82",
    storageBucket: "cargo-optimizer-fca82.firebasestorage.app",
    messagingSenderId: "89903431919",
    appId: "1:89903431919:web:52c9e432d3f4839b03db1a"
  });
  const db = getFirestore(app);
  window._db = db;
  window._fs = { collection, doc, setDoc, deleteDoc, onSnapshot, query, orderBy, serverTimestamp };
  window._firebaseReady = true;
  window.dispatchEvent(new Event('firebase-ready'));
</script>'''
    content = content[:old_module.start()] + new_module + content[old_module.end():]

# Supprimer auth-wall div
content = re.sub(r'<div id="auth-wall".*?</div>\s*(?=<div)', '', content, flags=re.DOTALL)

# Supprimer fonctions auth
for fn in ['doSignIn', 'doSignOut', 'startFirestoreSync', '_currentUser', 'getUserId']:
    content = re.sub(r'(var|let|const|function)\s+' + fn + r'[^;{]*[;{][^}]*}', '', content, flags=re.DOTALL)

# Supprimer auth-section dans nav
content = re.sub(r'<div id="auth-section".*?</div>\s*</div>', '</div>', content, flags=re.DOTALL)

# Supprimer onAuthStateChanged listener
content = re.sub(r'window\.addEventListener\([\'"]firebase-ready[\'"].*?(?=loadAll|renderSessions)', '', content, flags=re.DOTALL)

# Garder init propre
if 'loadAll()' not in content:
    content = content.rstrip() + '\nloadAll();\nrenderSessions();\napplyLang();\n'

# Ajouter sync Firestore simple sans auth
if 'onSnapshot' not in content or 'firebase-ready' not in content:
    insert = '''
window.addEventListener('firebase-ready', function(){
  if(!window._fs||!window._db) return;
  var fs=window._fs;
  fs.onSnapshot(
    fs.query(fs.collection(window._db,'cargo_sessions'),fs.orderBy('updatedAt','desc')),
    function(snap){
      snap.forEach(function(d){
        var data=d.data();
        var idx=sessions.findIndex(function(s){ return s.id===data.id; });
        if(idx===-1) sessions.push(data); else sessions[idx]=data;
      });
      try{ localStorage.setItem('clo_sessions',JSON.stringify(sessions)); }catch(e){}
      renderSessions();
    },
    function(err){ console.warn('Firestore:',err); }
  );
});
'''
    content = content.replace('document.getElementById(\'new-name\').addEventListener', insert + '\ndocument.getElementById(\'new-name\').addEventListener')

with open('index.html', 'w', encoding='utf-8') as f:
    f.write(content)

# Verifier
print("auth-wall present:", 'auth-wall' in content)
print("doSignIn present:", 'doSignIn' in content)
print("onAuthStateChanged present:", 'onAuthStateChanged' in content)
print("Firestore present:", 'cargo_sessions' in content)
print("RESULTAT OK:", 'auth-wall' not in content and 'doSignIn' not in content)
PYEOF
```

### 3. Vérifier le résultat

```bash
grep -c "auth-wall" index.html
grep -c "doSignIn" index.html
grep -c "cargo_sessions" index.html
```

Les deux premiers doivent être **0**.
Le troisième doit être **> 0**.

### 4. Committer et pousser

```bash
git add index.html
git commit -m "fix: supprimer auth Google definitivement - Firestore seul"
git push origin main
```

### 5. Redéployer sur Vercel

```bash
npx vercel --prod --yes
```

### 6. Tester

Ouvrir l'URL Vercel dans le navigateur.
La page doit s'afficher DIRECTEMENT sans aucun écran de connexion.

### 7. Rapport MAJ-CARGO.txt

```
Fix auth supprimé : OUI
auth-wall count   : 0
doSignIn count    : 0
Firestore actif   : OUI
URL production    : [URL]
Page sans login   : OUI/NON
```
