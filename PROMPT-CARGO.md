# PROMPT CLAUDE CODE — PROTOCOLE DE TEST LOCAL ET VALIDATION MANUELLE
## Objectif : Réparation définitive du bouton Enregistrer et injection des tests 01 & 02

---

## RÈGLES D'EXÉCUTION ET MÉTHODOLOGIE DE SÉCURITÉ

1. **TEST LOCAL PRIORITAIRE** : Toutes les modifications et injections de données de test doivent être validées localement sur `C:\DATA-MC-2030\CARGO\` avant toute tentative de déploiement en production.
2. **PAS DE BOUTON GRISÉ** : Analyser pourquoi le bouton "Enregistrer" en haut à droite est grisé ou désactivé (vérifier les attributs `disabled`, les classes CSS, ou les conditions JavaScript liées à l'état de l'initialisation de la base de données). Le rendre pleinement actif et fonctionnel.
3. **SUPPORT NATIVE DES CARACTÈRES** : Travailler exclusivement avec l'encodage UTF-8.
4. **PUBLICATION PAR UTILISATION DE /goal** : Une fois la totalité des fonctionnalités locales validées (y compris la persistance fonctionnelle du bouton Enregistrer), utiliser le mode `/goal` pour valider le déploiement.

---

## ÉTAPE 1 — Initialisation et Réparation Complète de l'Infrastructure Firebase/Firestore

1. Inspecter et corriger le script d'initialisation Firebase dans la balise `<head>` de `index.html`. S'assurer que les objets `window._db` et `window._firestoreUtils` sont correctement exposés, sans bloquer le rendu ou le cycle de vie de l'interface graphique.
2. Éliminer toute condition qui grise ou désactive indûment le bouton "Enregistrer". Le bouton doit être cliquable à tout moment dès qu'une session (cargo) est active.
3. Configurer une écoute en temps réel (`onSnapshot`) sur la collection `cargo_sessions` sans authentification pour mettre à jour instantanément le tableau JavaScript global `sessions` de l'application et appeler automatiquement `renderSessions()`.

---

## ÉTAPE 2 — Scénario de Test Local et Injection Automatique de 2 Cargos

Pour prouver que le bouton "Enregistrer", la base de données et la liste de gauche fonctionnent parfaitement, vous devez simuler et injecter par programmation de test deux cargos distincts dans la base de données locale/Firestore :

1. **Cargo Test 01** :
   - Nom : `test01`
   - Conteneur : dimensions standards (ex: 20 pieds GP).
   - Colis : Remplir avec au moins 2 ou 3 lignes de colis fictifs complets (référence, dimensions, quantité).
2. **Cargo Test 02** :
   - Nom : `test02`
   - Conteneur : dimensions standards (ex: 40 pieds HC).
   - Colis : Remplir avec une configuration différente de colis de test.

*Vérification attendue* : Exécuter la logique de sauvegarde pour ces deux configurations. Ils doivent impérativement apparaître de manière stable dans la « Liste des cargos » en haut à gauche et pouvoir être rechargés au clic sur leur ligne respective.

---

## ÉTAPE 3 — Validation Finale et Déploiement via le Plugin /goal

1. Valider manuellement ou via script que le bouton « Enregistrer » applique les modifications de manière instantanée, fluide, et met à jour l'historique sans erreur dans la console de développement.
2. Une fois le succès validé à 100% en local, synchroniser les sources avec le dépôt GitHub `parisb2b/cargo` (branche `main`).
3. Déployer sur Vercel (`vercel --prod --yes`).
4. **Appeler l'option de commande `/goal`** pour confirmer à l'environnement que l'ensemble des critères fonctionnels (Bouton d'enregistrement opérationnel, synchronisation Firestore sans auth, historique dynamique à gauche avec les tests) sont remplis.

---

## ÉTAPE 4 — Rapport de Clôture

Générer le rapport final `C:\DATA-MC-2030\CARGO\MAJ-CARGO.txt` indiquant :
- L'URL définitive sur Vercel.
- Le statut des tests de chargement pour `test01` et `test02`.
- La confirmation que le bouton d'enregistrement n'est plus bloqué ni grisé.
