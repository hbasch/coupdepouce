# Mettre Coup de pouce en ligne — guide pas à pas

Durée : environ 30 à 45 minutes. Tout se fait dans ton navigateur, sans rien installer.
Coût : gratuit pour l'hébergement. L'IA est payée à l'usage (tu fixes un plafond).

Tu vas créer 3 comptes : **Mistral** (l'IA), **GitHub** (où sont rangés les fichiers) et **Cloudflare** (qui met le site en ligne).

> Les écrans de ces services changent de temps en temps. Si un bouton n'a pas exactement le même nom, cherche le plus proche. En cas de blocage, fais une capture d'écran et envoie-la à Claude.

---

## Ce que contient le dossier

```
coupdepouce-site/
├── public/                  ← le site lui-même
│   ├── index.html           ← les 4 outils
│   ├── confidentialite.html
│   ├── mentions-legales.html
│   ├── _legal.css
│   ├── _headers             ← réglages de sécurité
│   └── ocr/                 ← la lecture des photos sur le téléphone
└── functions/
    └── api/ask.js           ← le petit serveur qui parle à l'IA
```

---

## Étape 1 — Obtenir ta clé Mistral (10 min)

1. Va sur **console.mistral.ai** et crée un compte.
2. Choisis une offre. Pour démarrer, une offre d'essai gratuite peut suffire si elle est proposée. Sinon, ajoute un moyen de paiement.
3. **Fixe un plafond de dépense mensuel** dans la partie facturation (par exemple 10 €). C'est ta protection : même en cas d'abus, tu ne paieras jamais plus.
4. Va dans **API Keys** → **Create new key**. Donne-lui le nom `coupdepouce`.
5. **Copie la clé** et garde-la de côté (dans une note privée). Elle ne s'affiche qu'une fois.

⚠️ Ne mets jamais cette clé dans un fichier du site, ni dans un mail, ni sur GitHub.

---

## Étape 2 — Déposer les fichiers sur GitHub (10 min)

1. Va sur **github.com** et crée un compte.
2. En haut à droite, clique sur **+** → **New repository**.
   - Nom : `coupdepouce`
   - Laisse **Public**. Le code ne contient aucun secret, et le rendre public rassure les associations.
   - Coche **Add a README file**.
   - Clique sur **Create repository**.
3. Sur la page du projet : **Add file** → **Upload files**.
4. Ouvre le dossier `coupdepouce-site` sur ton ordinateur. **Glisse les dossiers `public` et `functions`** dans la page GitHub (pas le dossier `coupdepouce-site` lui-même, mais ce qu'il contient).
5. Attends la fin de l'envoi, puis clique sur **Commit changes**.
6. Vérifie : sur GitHub, tu dois voir les dossiers `public` et `functions` à la racine du projet.

---

## Étape 3 — Mettre en ligne avec Cloudflare (10 min)

1. Va sur **dash.cloudflare.com** et crée un compte.
2. Dans le menu : **Workers & Pages** → **Create** → onglet **Pages** → **Connect to Git**.
3. Connecte ton compte GitHub, puis choisis le projet `coupdepouce`.
4. Réglages :
   - **Project name** : `coupdepouce` (ce sera l'adresse `coupdepouce.pages.dev`, ou un nom proche si celui-ci est déjà pris)
   - **Framework preset** : `None`
   - **Build command** : laisse vide
   - **Build output directory** : `public`
5. Clique sur **Save and Deploy**. Au bout d'une minute, le site est en ligne. Mais l'IA ne marche pas encore, et c'est normal.

### Ajouter la clé Mistral

6. Dans ton projet Cloudflare : **Settings** → **Variables and Secrets** → **Add**.
   - Type : **Secret**
   - Nom : `MISTRAL_API_KEY`
   - Valeur : colle ta clé Mistral
   - Clique sur **Save**.
7. Va dans **Deployments**, clique sur les **…** du dernier déploiement, puis sur **Retry deployment**. La clé n'est prise en compte qu'après un nouveau déploiement.

### Activer la limite par personne (fortement conseillé)

8. Dans le menu de gauche : **Storage & Databases** → **KV** (ou **Workers & Pages** → **KV**, selon l'écran) → **Create namespace**, puis nomme-le `coupdepouce-limites`.
9. Retourne dans ton projet Pages : **Settings** → **Bindings** → **Add** → **KV namespace**.
   - Variable name : `RATE_LIMIT`
   - Namespace : `coupdepouce-limites`
   - Clique sur **Save**.
10. Refais **Retry deployment** comme à l'étape 7.

Par défaut, chaque personne peut faire **20 demandes par jour**. Pour changer ce chiffre, ajoute une variable (type texte, pas secret) nommée `DAILY_LIMIT`, par exemple avec la valeur `10`.

---

## Étape 4 — Tester (5 min)

1. Ouvre `https://coupdepouce.pages.dev` (ou l'adresse affichée par Cloudflare).
2. Clique sur **Essayer avec un exemple**, puis sur **Envoyer le texte caché et expliquer**. Une explication doit apparaître en 5 à 20 secondes.
3. Sur ton téléphone, prends en photo un vrai courrier (sans importance) pour tester la lecture. La première fois, la préparation prend environ 20 secondes.
4. Teste **Facile à lire** et **Mes droits oubliés**.

---

## Étape 5 — Compléter les pages légales (5 min)

Sur GitHub, ouvre `public/mentions-legales.html`, clique sur le crayon ✏️ et remplace les passages entre crochets : `[Prénom Nom]`, `[ton email de contact]`. Fais pareil dans `public/confidentialite.html` (email et date). Clique sur **Commit changes** : Cloudflare remet le site à jour tout seul en une minute.

Conseil : crée une adresse email dédiée au projet (par exemple `coupdepouce.contact@…`) plutôt que ton adresse personnelle.

---

## Plus tard (facultatif) — Un vrai nom de domaine

1. Achète un nom en `.fr` chez OVH ou Gandi (environ 10 €/an), par exemple `coupdepouce-courrier.fr`.
2. Dans Cloudflare : ton projet → **Custom domains** → **Set up a custom domain**, puis suis les instructions.

---

## En cas de problème

| Ce que tu vois | Ce qu'il faut faire |
|---|---|
| « Le service n'est pas encore configuré » | La clé Mistral manque. Refais l'étape 3, points 6 et 7. |
| « Un problème est survenu » à chaque essai | Vérifie que la clé Mistral est valide et que ton compte Mistral a un moyen de paiement ou une offre active. |
| « Le service est très demandé » | Mistral limite le nombre de demandes de ton compte. Réessaie plus tard, ou augmente ta limite chez Mistral. |
| La page s'affiche mais la photo n'est pas lue | Vérifie que le dossier `public/ocr` est bien sur GitHub avec ses sous-dossiers `core` et `lang`. |
| Page 404 sur Cloudflare | Le **Build output directory** doit être exactement `public`. |

---

## Changer d'IA plus tard

Le modèle utilisé par défaut est `mistral-small-latest`. Pour en essayer un autre, ajoute une variable `MISTRAL_MODEL` dans Cloudflare avec le nom du modèle, puis refais un déploiement.
