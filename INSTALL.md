# Procyo Onboarding — Guide d'installation

## Ce que tu as reçu

| Fichier | Rôle |
|---|---|
| `procyo-webapp.html` | La webapp courtier (héberger sur GitHub Pages / Cloudflare) |
| `procyo_onboarding/` | Le module Odoo à installer sur ton instance |

---

## Étape 1 — Installer le module Odoo

1. Copier le dossier `procyo_onboarding` dans le répertoire addons de ton instance :
   ```
   /odoo/addons/procyo_onboarding/
   ```
2. Redémarrer le service Odoo
3. Activer le mode développeur : **Paramètres → Activer le mode développeur**
4. **Applications → Mettre à jour la liste des applications**
5. Rechercher "Procyo" → **Installer**
6. Un menu **Procyo → Onboardings** apparaît dans la barre de navigation

---

## Étape 2 — Créer un compte technique Odoo pour l'API

1. **Paramètres → Utilisateurs → Nouvel utilisateur**
2. Email : `api.procyo@procyo.com` (ou ce que tu veux)
3. Accès : **Utilisateur interne**
4. Donner accès au module Procyo
5. Noter l'URL de l'instance, le nom de la base de données, et le mot de passe

---

## Étape 3 — Configurer la webapp

Ouvrir `procyo-webapp.html` et modifier le bloc CONFIG en haut du script :

```javascript
const ODOO_CONFIG = {
  url:      'https://TON_URL_ODOO',    // ex: https://procyo.mondomaine.be
  db:       'NOM_DE_TA_BASE',          // visible dans Paramètres → Technique → Bases
  username: 'api.procyo@procyo.com',
  password: 'MOT_DE_PASSE_API',
};
const ADMIN_EMAILS = ['toi@procyo.com'];  // ton email pour accès admin
```

---

## Étape 4 — Activer le CORS sur Odoo (obligatoire pour les appels API depuis la webapp)

Dans le fichier de configuration Odoo (`odoo.conf`) :

```ini
[options]
; Ajouter l'URL de ta webapp dans les origines autorisées
cors = https://TON_DOMAINE_WEBAPP.com
```

Ou temporairement pour les tests, installer le module `web_cors` disponible sur OCA.

---

## Étape 5 — Héberger la webapp

**Option A — GitHub Pages (recommandé, tu l'as déjà)**
1. Créer un repo `procyo-onboarding` sur ton GitHub `cedricjaumot-cloud`
2. Déposer `procyo-webapp.html` renommé en `index.html`
3. Activer GitHub Pages sur la branche main
4. Pointer un sous-domaine OVH : `onboarding.procyo.be` → CNAME vers `cedricjaumot-cloud.github.io`

**Option B — Cloudflare Pages**
1. Créer un projet dans Cloudflare Pages
2. Déposer le fichier
3. Connecter le domaine OVH

---

## Fonctionnement des modules

| Situation | Comportement |
|---|---|
| Inscription courtier | Module 1 automatiquement accessible |
| Module 1 complété (100%) | Module 2 se débloque automatiquement |
| Module 2 complété (100%) | Module 3 se débloque automatiquement |
| Admin peut débloquer | Manuellement depuis la vue Admin de la webapp |

---

## Vue Odoo — ce que tu vois

Dans **Procyo → Onboardings** :
- Liste de tous les courtiers avec leur progression par module (barres de progression)
- Fiche détaillée avec toutes les réponses organisées par section (A→O)
- Statuts colorés : vert = clôturé, orange = en cours, gris = verrouillé

Les données se synchronisent à chaque sauvegarde (automatique ou manuelle).
En cas d'échec de la connexion Odoo, les données restent dans le localStorage et seront renvoyées à la prochaine sauvegarde.

---

## Note sécurité (production)

Le hash de mot de passe actuel est côté client (simple, pour démo).
En production, il faudra déléguer l'auth à Odoo lui-même via l'API `/web/session/authenticate`
ou utiliser un service d'auth dédié. Je peux faire cette évolution quand tu es prêt.
