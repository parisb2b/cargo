# PROMPT CLAUDE CODE — Container Optimizer
## Déploiement complet — cargo.sasfr.com

---

## RÈGLES D'EXÉCUTION

1. `git add -A && git commit -m "..." && git push` après chaque étape validée
2. Résoudre les erreurs de manière autonome sans confirmation
3. Pusher en temps réel — ne jamais attendre la fin
4. Générer `MAJ-CARGO.txt` à la fin
5.  --project` Firebase AVEC `cargo` (nouveau projet)
C'est parfaitement clair. Le bouton existe déjà (« Nouveau chargement »), et l'objectif est d'utiliser la base de données Firebase Firestore pour centraliser et mémoriser l'historique complet de ces chargements de manière partagée et publique, sans authentification.

Claude Code va donc modifier l'application pour que chaque « Nouveau chargement » génère une entrée unique enregistrée dans Firestore (avec un nom de cargo dédié), et afficher une section latérale ou une vue de liste permettant de basculer, consulter l'historique ou charger un cargo à venir.

Voici le contenu final corrigé pour votre fichier.

---

### 1. Contenu mis à jour pour `C:\DATA-MC-2030\CARGO\PROMPT-CARGO.md`

```markdown
# PROMPT CLAUDE CODE — Container Optimizer
## Déploiement Cloud avec Historique Firestore Centralisé (Sans Auth) — cargo.sasfr.com

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
- **Nature du projet** : Application d'optimisation de remplissage de conteneurs maritimes (Algorithme EP-3DBP, Vue 3D WebGL Three.js, Bilingue FR/中文, Import/Export Excel).
- **ZÉRO AUTHENTIFICATION / PAS DE CONNEXION** : L'accès à la page reste entièrement direct et anonyme. Aucune page de connexion ni formulaire de création de compte ne doit être ajouté.
- **BASE CENTRALISÉE & HISTORIQUE** : Connecter le bouton existant "Nouveau chargement" à Firebase Firestore. Chaque clic doit permettre de définir ou générer un nom pour le cargo, de l'enregistrer à l'état centralisé, et de l'ajouter à un panneau "Liste des cargos" / "Historique des cargaisons" pour que tout utilisateur puisse consulter les archives ou préparer les futurs chargements.

---

## ÉTAPE 1 — Configuration Firebase (Firestore Public)

1. Initialiser le SDK Firebase Firestore (version compacte/CDN ou module adapté) directement dans le `<head>` de `index.html` avec les identifiants suivants :
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

2. **Règles Firestore de sécurité publique** : S'assurer que la collection `cargo_sessions` accepte les accès anonymes en lecture/écriture :
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /cargo_sessions/{document} {
      allow read, write: if true;
    }
  }
}

```



---

## ÉTAPE 2 — Modifications de l'Interface & Gestion de l'Historique (`index.html`)

1. **Liaison du Bouton Existant** :
* Intercepter l'action du bouton existant "Nouveau chargement".
* Lors d'un clic, afficher une boîte de dialogue épurée (style minimaliste Inter font) demandant de saisir le "Nom du Cargo" (ex: *Cargo-Guadeloupe-052026*).
* Générer un identifiant unique de session Firestore.


2. **Interface "Liste des Cargos / Historique"** :
* Intégrer un volet ou une zone de liste (Sidebar ou section dédiée) affichant dynamiquement la liste de tous les cargos enregistrés sur Firestore.
* Diviser ou trier clairement les éléments pour refléter l'historique (cargos passés) et les planifications (nouveaux cargos à venir).
* Chaque ligne de la liste doit être cliquable pour charger instantanément les données correspondantes du conteneur, des colis et de la scène 3D Three.js associés depuis Firestore.


3. **Synchronisation en temps réel** :
* Remplacer le cycle de vie basé sur le `localStorage` local par une écoute active ou des sauvegardes directes dans Firestore (collection `cargo_sessions`) à chaque modification ou calcul d'optimisation.
* Veiller à préserver le support bilingue (FR/中文) pour l'ensemble des textes ajoutés (ex: `Historique des cargos` / `货运 l历史记录`).



---

## ÉTAPE 3 — Initialisation Git & Déploiement Vercel

1. Initialiser le dépôt Git dans `C:\DATA-MC-2030\CARGO\`.
2. Préparer les fichiers de structure standard `.gitignore` et `README.md`.
3. Configurer le routage statique cloud dans `vercel.json`.
4. Créer de manière autonome le dépôt GitHub public `parisb2b/cargo` avec la CLI `gh` et pousser le code.
5. Déployer instantanément sur Vercel : `vercel --prod --yes`.

---

## ÉTAPE 4 — Rapport final de fin de mission

Générer le rapport structuré `C:\DATA-MC-2030\CARGO\MAJ-CARGO.txt` récapitulant :

* L'URL finale de production Vercel.
* Le lien du dépôt GitHub public mis à jour.
* Une description de l'architecture de la liste d'historique Firestore mise en place.

```

---
 