# PROMPT CLAUDE CODE — Container Optimizer
## Synchronisation locale, validation et clôture — cargo.sasfr.com

---

## RÈGLES D'EXÉCUTION

1. `git add -A && git commit -m "..." && git push` après chaque étape validée.
2. **NE PAS modifier le code source** — la version en production est stable.
3. Travailler exclusivement avec l'encodage UTF-8.
4. Exécuter `/goal` en fin de session pour clore proprement.

---

## ÉTAT ACTUEL (24 Mai 2026)

- **Production Vercel** : https://cargo-pied-kappa.vercel.app — OPÉRATIONNEL
- **GitHub** : https://github.com/parisb2b/cargo
- **Firebase** : Collection `cargo_sessions` publique (sans auth)
- **Bouton Enregistrer** : Actif et fonctionnel
- **Liste des cargos** : test01 et test02 injectés

---

## ÉTAPE 1 — Vérification de la synchronisation locale

1. Lire le fichier `RAPPEL-SAUVEGARDE.txt` pour connaître l'état verrouillé.
2. Vérifier que `index.html` local est identique à la version déployée sur Vercel.
3. Aucune modification du code n'est nécessaire — l'application fonctionne.

---

## ÉTAPE 2 — Validation des données de test

1. Ouvrir https://cargo-pied-kappa.vercel.app dans le navigateur.
2. Vérifier que `test01` et `test02` apparaissent dans la « Liste des cargos ».
3. Vérifier que le bouton « Enregistrer » est actif (non grisé).
4. Cliquer sur `test01` → les 3 colis doivent se charger.

---

## ÉTAPE 3 — Clôture avec /goal

1. Une fois la synchronisation locale confirmée, exécuter `/goal`.
2. Générer `MAJ-CARGO.txt` avec le statut final.
