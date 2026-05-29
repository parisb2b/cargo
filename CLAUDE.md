# CLAUDE.md — Container Optimizer (CARGO)

## Environnement
- OS : Windows 11 | Utilisateur : miche
- Répertoire : `C:\DATA-MC-2030\CARGO\`
- Production : https://cargo-pied-kappa.vercel.app
- Python : `C:\DATA-MC-2030\python\python.exe`

## Langue
- Réponses : **français par défaut**, chinois (中文) sur demande
- Jamais d'anglais sauf noms techniques

## Encodage
- Tous les fichiers : UTF-8
- Git : `git config core.quotepath false`

## Architecture du projet
- **index.html** : Application monopage (Container Optimizer)
- **Firebase Firestore** : Collection `cargo_sessions` (publique, sans auth)
- **Algorithme** : EP-3DBP (Extreme Point 3D Bin Packing)
- **3D** : Three.js r128
- **Export** : SheetJS (Excel), PNG, PDF

## Règles absolues
1. **NE PAS modifier le code source** — la version en production est stable et validée
2. Travailler sur `main` uniquement
3. Avant toute modification : `git fetch origin && git reset --hard origin/main`
4. `git add -A && git commit -m "..." && git push` après chaque étape
5. Logger dans `MAJ-CARGO.txt`

## État production (24 Mai 2026)
- Bouton Enregistrer : ACTIF et fonctionnel
- Liste des cargos : test01 et test02 injectés
- Firestore : règles publiques `allow read, write: if true;` sur `cargo_sessions`
- Synchronisation locale = production Vercel
