# PROMPT CLAUDE CODE — Audit complet Container Optimizer
## cargo.sasfr.com / cargo-lnj5xa27n-parisb2bs-projects.vercel.app

---

## RÈGLES

1. NE PAS modifier index.html avant d'avoir terminé l'audit complet
2. Identifier chaque bug avec sa cause exacte dans le code
3. Proposer et appliquer les corrections une par une
4. Valider chaque correction avant de passer à la suivante
5. Générer AUDIT-CARGO.txt avec le rapport complet

---

## CONTEXTE

Application : optimiseur de remplissage de conteneurs maritimes
Repo : parisb2b/cargo (branche main)
Firebase : cargo-optimizer-fca82 (Firestore sans auth)
Stack : HTML/JS pur, XLSX.js, Three.js, Firebase SDK module

---

## ÉTAPE 1 — Récupérer le code

```bash
cd ~/mactell/cargo
git fetch origin && git reset --hard origin/main
```

---

## ÉTAPE 2 — Audit statique du code (sans navigateur)

### 2a. Vérifier la syntaxe JS

```bash
# Extraire le script principal et vérifier
python3 << 'PYEOF'
import re
with open('index.html','r',encoding='utf-8') as f:
    content = f.read()
scripts = list(re.finditer(r'<script[^>]*>', content))
ends = list(re.finditer(r'</script>', content))
for i,(s,e) in enumerate(zip(scripts,ends)):
    js = content[s.end():e.start()]
    ticks = js.count('`')
    b_open = js.count('{')
    b_close = js.count('}')
    p_open = js.count('(')
    p_close = js.count(')')
    print(f"Script {i} ({len(js)} chars):")
    print(f"  Backticks: {ticks} ({'OK' if ticks%2==0 else 'DESEQUILIBRE'})")
    print(f"  Braces: {b_open}/{b_close} ({'OK' if b_open==b_close else 'DESEQUILIBRE'})")
    print(f"  Parens: {p_open}/{p_close} ({'OK' if p_open==p_close else 'DESEQUILIBRE'})")
PYEOF
```

### 2b. Vérifier la config Firebase

```bash
python3 << 'PYEOF'
with open('index.html','r',encoding='utf-8') as f:
    content = f.read()

checks = {
    'Firebase SDK module': 'type="module"' in content,
    'initializeApp': 'initializeApp' in content,
    'getFirestore': 'getFirestore' in content,
    'cargo-optimizer-fca82': 'cargo-optimizer-fca82' in content,
    'window._db assigné': 'window._db = db' in content or 'window._db=db' in content,
    'window._fs assigné': 'window._fs' in content,
    'firebase-ready event': 'firebase-ready' in content,
    'onSnapshot présent': 'onSnapshot' in content,
    'setDoc présent': 'setDoc' in content,
    'cargo_sessions collection': 'cargo_sessions' in content,
    'SANS auth (auth-wall absent)': 'auth-wall' not in content,
    'SANS doSignIn': 'doSignIn' not in content,
    'SANS onAuthStateChanged': 'onAuthStateChanged' not in content,
}

print("=== FIREBASE CONFIG ===")
all_ok = True
for label, result in checks.items():
    status = "✅" if result else "❌"
    if not result: all_ok = False
    print(f"  {status} {label}")
print(f"\nRésultat global: {'✅ OK' if all_ok else '❌ PROBLÈMES DÉTECTÉS'}")
PYEOF
```

### 2c. Vérifier les fonctions critiques

```bash
python3 << 'PYEOF'
with open('index.html','r',encoding='utf-8') as f:
    content = f.read()

import re
fns_required = [
    # Core
    'saveAll', 'loadAll', 'getSession', 'createSession', 'loadSession',
    'deleteSession', 'renderSessions', 'showPage',
    # Colis
    'renderColisTable', 'updateCell', 'deleteRow', 'duplicateRow', 'addRows',
    'importExcel', 'exportExcel',
    # Visualisation
    'buildViz', 'switchViz', 'draw3DWebGL', 'placeColis', 'getValidOrientations',
    'computeLoadingNumbers', 'makeTopFaceTex', 'makeLabelSprite',
    'setLayerFilter', 'toggleXray', 'setPreset', 'resetCamera',
    'stepPlay', 'stepNext', 'stepPrev',
    # Archive
    'renderArchiveSessions', 'renderArchiveColis',
    'purgeSessionTrash', 'purgeColisTrash', 'updateArchiveBadges',
    # Export
    'exportLoadingPlan', 'exportPDF',
    # UI
    'openNewModal', 'closeModal', 'manualSave', 'showSaveBadge',
    'showToastUndo', 'showToastInfo',
    'applyLang', 'toggleLang', 't',
    # Firestore
    'syncFirestore',
]

present = re.findall(r'function (\w+)\(', content)
print(f"=== FONCTIONS ({len(present)} trouvées) ===")
missing = []
for fn in fns_required:
    if fn not in present:
        missing.append(fn)
        print(f"  ❌ MANQUANT: {fn}")

if not missing:
    print(f"  ✅ Toutes les {len(fns_required)} fonctions requises sont présentes")
else:
    print(f"\n  {len(missing)} fonction(s) manquante(s) — à corriger")
PYEOF
```

### 2d. Vérifier la structure HTML

```bash
python3 << 'PYEOF'
with open('index.html','r',encoding='utf-8') as f:
    content = f.read()

pages_required = [
    'page-sessions', 'page-container', 'page-colis',
    'page-viz', 'page-export',
    'page-archive-sessions', 'page-archive-colis'
]
elements_required = [
    'webgl-container', 'webgl-canvas', 'main-canvas',
    'viz-legend', 'viz-alerts',
    'layer-filter-wrap', 'layer-btn-all',
    'layer-btn-0', 'layer-btn-1', 'layer-btn-2',
    'layer-btn-left', 'layer-btn-right',
    'xray-btn', 'step-btn', 'preset-btns',
    'btn-save-manual', 'save-badge',
    'weight-bar-wrap',
    'auth-section',
    '_toast-overlay',
]

print("=== PAGES HTML ===")
for p in pages_required:
    ok = f'id="{p}"' in content
    print(f"  {'✅' if ok else '❌'} {p}")

print("\n=== ÉLÉMENTS UI ===")
missing_els = []
for el in elements_required:
    ok = f'id="{el}"' in content
    if not ok: missing_els.append(el)
    print(f"  {'✅' if ok else '❌'} #{el}")

if missing_els:
    print(f"\n  ❌ {len(missing_els)} élément(s) manquant(s)")
PYEOF
```

### 2e. Vérifier la persistance session

```bash
python3 << 'PYEOF'
with open('index.html','r',encoding='utf-8') as f:
    content = f.read()

checks = {
    'Restore session au chargement': 'clo_active_session' in content,
    'Restore page active': 'clo_active_page' in content,
    'loadSession sauvegarde id': 'clo_active_session' in content and 'loadSession' in content,
    'showPage sauvegarde page': 'clo_active_page' in content and 'showPage' in content,
    'updateCell debounce': 'updateCell._t' in content,
    'Firestore debounce': '_fsTimer' in content or 'syncFirestore' in content,
    'onSnapshot optimisé': 'changed' in content and 'onSnapshot' in content,
}
print("=== PERSISTANCE & PERFORMANCE ===")
for label, ok in checks.items():
    print(f"  {'✅' if ok else '❌'} {label}")
PYEOF
```

---

## ÉTAPE 3 — Audit Firestore en live

```bash
# Installer firebase-admin si nécessaire
npm list -g firebase-tools 2>/dev/null || npm install -g firebase-tools --quiet

# Vérifier les règles Firestore actuelles
node << 'NJEOF'
const https = require('https');
const url = 'https://firestore.googleapis.com/v1/projects/cargo-optimizer-fca82/databases/(default)/documents/cargo_sessions?pageSize=3&key=AIzaSyDyk2XwTDY2tjbnUsVRI-hg3jptd9sNe2Y';
https.get(url, (res) => {
  let data = '';
  res.on('data', chunk => data += chunk);
  res.on('end', () => {
    const parsed = JSON.parse(data);
    if(parsed.documents) {
      console.log('✅ Firestore accessible - ' + parsed.documents.length + ' document(s) trouvé(s)');
      parsed.documents.forEach(doc => {
        const name = doc.name.split('/').pop();
        console.log('  - ' + name);
      });
    } else if(parsed.error) {
      console.log('❌ Erreur Firestore: ' + parsed.error.message);
      console.log('   Code: ' + parsed.error.code);
    } else {
      console.log('⚠️  Collection vide ou règles bloquantes');
      console.log(JSON.stringify(parsed).substring(0,200));
    }
  });
}).on('error', e => console.log('❌ Connexion impossible: ' + e.message));
NJEOF
```

---

## ÉTAPE 4 — Vérifier les règles Firestore

```bash
# Tester l'écriture dans Firestore via l'API REST
node << 'NJEOF'
const https = require('https');
const data = JSON.stringify({
  fields: {
    id: {stringValue: 'test-audit-' + Date.now()},
    name: {stringValue: 'Test Audit Claude Code'},
    ownerId: {stringValue: 'audit'},
    updatedAt: {timestampValue: new Date().toISOString()}
  }
});

const options = {
  hostname: 'firestore.googleapis.com',
  path: '/v1/projects/cargo-optimizer-fca82/databases/(default)/documents/cargo_sessions?key=AIzaSyDyk2XwTDY2tjbnUsVRI-hg3jptd9sNe2Y',
  method: 'POST',
  headers: { 'Content-Type': 'application/json', 'Content-Length': data.length }
};

const req = https.request(options, res => {
  let body = '';
  res.on('data', chunk => body += chunk);
  res.on('end', () => {
    if(res.statusCode === 200) {
      console.log('✅ Écriture Firestore OK - règles ouvertes');
    } else {
      console.log('❌ Écriture Firestore BLOQUÉE - statut ' + res.statusCode);
      console.log('   Règles Firestore trop restrictives!');
      console.log('   ' + body.substring(0, 300));
    }
  });
});
req.on('error', e => console.log('❌ ' + e.message));
req.write(data);
req.end();
NJEOF
```

---

## ÉTAPE 5 — Détecter et corriger les bugs

Sur la base des résultats des étapes 2, 3 et 4, appliquer les corrections nécessaires.

### Corrections prioritaires à appliquer si détectées :

**BUG A — Firestore règles bloquantes**
Si l'étape 4 retourne une erreur 403, aller dans Firebase Console et publier :
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

**BUG B — Firebase SDK ne charge pas (type=module conflit)**
Si `window._db` n'est pas défini au chargement, remplacer le script module Firebase
par la version CDN compat :
```html
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-firestore-compat.js"></script>
<script>
  const app = firebase.initializeApp({
    apiKey: "AIzaSyDyk2XwTDY2tjbnUsVRI-hg3jptd9sNe2Y",
    authDomain: "cargo-optimizer-fca82.firebaseapp.com",
    projectId: "cargo-optimizer-fca82",
    storageBucket: "cargo-optimizer-fca82.firebasestorage.app",
    messagingSenderId: "89903431919",
    appId: "1:89903431919:web:52c9e432d3f4839b03db1a"
  });
  const db = firebase.firestore();
  window._db = db;
  window._fs = {
    collection: (db, col) => db.collection(col),
    doc: (db, col, id) => db.collection(col).doc(id),
    setDoc: (ref, data) => ref.set(data),
    deleteDoc: (ref) => ref.delete(),
    onSnapshot: (q, ok, err) => q.onSnapshot(ok, err),
    query: (col) => col,
    orderBy: (field, dir) => col => col.orderBy(field, dir||'asc'),
    serverTimestamp: () => firebase.firestore.FieldValue.serverTimestamp()
  };
  window._firebaseReady = true;
  window.dispatchEvent(new Event('firebase-ready'));
</script>
```

**BUG C — Three.js ne charge pas (visualisation vide)**
Vérifier que le CDN Three.js est bien dans le head :
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
```
Si absent, l'ajouter avant le script principal.

**BUG D — Backtick échappé casse le JS**
Chercher `</td>\;→\`` dans renderColisTable et corriger.

**BUG E — activeVizMode pas à '3d' par défaut**
Vérifier : `let activeVizMode = '3d';`
Corriger si c'est `'side'`.

**BUG F — firebase-ready listener absent du script principal**
Vérifier que le script principal écoute l'événement pour démarrer onSnapshot.

---

## ÉTAPE 6 — Redéployer si corrections appliquées

```bash
cd ~/mactell/cargo
git add index.html
git commit -m "fix: corrections audit - [liste des bugs corrigés]"
git push origin main
npx vercel --prod --yes
```

---

## ÉTAPE 7 — Rapport AUDIT-CARGO.txt

Créer `~/mactell/cargo/AUDIT-CARGO.txt` :

```
=== AUDIT CONTAINER OPTIMIZER ===
Date : [date]
URL  : [URL Vercel]

=== RÉSULTATS AUDIT STATIQUE ===
Syntaxe JS         : [OK/ERREUR]
Config Firebase    : [OK/ERREUR - détails]
Fonctions requises : [N présentes / M manquantes - liste manquantes]
Pages HTML         : [OK/ERREUR]
Persistance session: [OK/ERREUR]

=== RÉSULTATS FIRESTORE ===
Lecture Firestore  : [OK/ERREUR]
Écriture Firestore : [OK/ERREUR]
Documents présents : [N]
Règles             : [ouvertes/restrictives]

=== BUGS DÉTECTÉS ===
[Liste numérotée de chaque bug avec sa cause]

=== CORRECTIONS APPLIQUÉES ===
[Liste de ce qui a été corrigé]

=== FONCTIONNALITÉS VÉRIFIÉES ===
[ ] Vue 3D WebGL s'affiche avec colis colorés et labels
[ ] Labels sur 6 faces des colis (N° position + ordre)
[ ] Filtres couches multi-sélection
[ ] PORTE / FOND visibles sur sol 3D
[ ] Import Excel fonctionne
[ ] Export plan de chargement Excel (3 onglets)
[ ] Dupliquer une ligne colis
[ ] Archive colis (30j)
[ ] Archive chargements (30j)
[ ] Restore session après refresh
[ ] Sync Firestore multi-navigateurs
[ ] Bilingue FR/中文
[ ] Enregistrer manuel (Ctrl+S)
```
