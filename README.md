# Y4I Academy — Site web

> Centre de formation aux métiers de la santé et services d'aide à domicile à **Odza, Yaoundé (Cameroun)**.

Site PHP complet : vitrine, fiches formations, services à domicile, carte de Yaoundé avec suivi GPS, inscription en ligne, fonds de garantie et paiement Mobile Money (Campay / Orange Money / MTN MoMo), espace d'administration.

![PHP](https://img.shields.io/badge/PHP-8.0%2B-777BB4?style=flat-square&logo=php&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL%20%2F%20MariaDB-5.7%2B-4479A1?style=flat-square&logo=mysql&logoColor=white)
![Mobile Money](https://img.shields.io/badge/Paiement-Campay%20%C2%B7%20Orange%20%C2%B7%20MTN-10B981?style=flat-square)
![Statut](https://img.shields.io/badge/statut-pr%C3%AAt%20%C3%A0%20d%C3%A9ployer-F2A900?style=flat-square)

---

## Sommaire

- [Fonctionnalités](#fonctionnalités)
- [Structure du projet](#structure-du-projet)
- [Prérequis](#prérequis)
- [Installation locale (XAMPP)](#installation-locale-xampp)
- [Configuration](#configuration)
- [Paiement Mobile Money](#paiement-mobile-money)
- [Base de données](#base-de-données)
- [Administration](#administration)
- [Sécurité avant mise en ligne](#sécurité-avant-mise-en-ligne)
- [Sauvegarde et restauration](#sauvegarde-et-restauration)
- [Déploiement GitHub](#déploiement-github)

---

## Fonctionnalités

- **Site vitrine responsive** : accueil, diaporama, formations, services, témoignages, contact.
- **Formations** : fiches détaillées (`formation.php?slug=...`), prérequis, durée, certification, tarifs.
- **Services à domicile** : aide à domicile, accompagnement hospitalier, soins, etc., avec pages dédiées (`service.php?service=...`).
- **Carte de Yaoundé** : recherche d'adresses/quartiers/hôpitaux, itinéraire, distance, durée, suivi GPS en direct.
- **Inscription client / prestataire** : sans formation obligatoire, avec dépôt facultatif de la pièce d'identité et du diplôme (JPG, PNG, WebP ou PDF, 4 Mo max).
- **Vérification des prestataires** : pièce d'identité + diplôme/certificat uniquement. **Aucun casier judiciaire n'est demandé.**
- **Fonds de garantie** : minimum 5 000 FCFA, réservé aux prestataires, bloqué puis remboursé par l'administration.
- **Paiement Mobile Money** : Campay (Orange Money + MTN MoMo), MTN direct ou Orange direct. **Mode simulation** tant que les clés ne sont pas renseignées.
- **Espace admin** : comptes, paiements, messages, vérification, témoignages, tarifs, formations, journal d'activité.
- **Reçus imprimables** : `receipt.php?id=...&token=...` (PDF via impression navigateur).
- **Exports CSV** : comptes, paiements, messages, journal.
- **SEO & accessibilité** : `sitemap.xml`, `robots.txt`, Open Graph, Twitter Card, page 404, lien d'évitement, CTA mobile.
- **Mesure d'audience** : GA4 ou Matomo, chargée **uniquement après consentement**.

---

## Structure du projet

```text
.
├── index.php              # Accueil + formations + services + carte + inscription + contact + admin
├── formation.php          # Fiche formation (?slug=...)
├── service.php            # Page service (?service=...)
├── receipt.php            # Reçu imprimable (?id=...&token=...)
├── 404.php                # Page 404 personnalisée
├── inc_public.php         # En-tête/pied communs aux pages secondaires
├── api.php                # API (inscription, contact, rappel, paiement, carte, suivi, admin)
├── pay.php                # Passerelles Campay / MTN MoMo / Orange Money (+ simulation)
├── lib.php                # Base de données, schéma auto, helpers, sécurité, e-mails
├── places.php             # Données locales de la carte
├── config.php             # ★ Toute la configuration (clés, société, e-mail, analytics)
├── export.php             # Exports CSV admin (?type=accounts|payments|messages|journal)
├── sitemap.php            # Génère sitemap.xml (réécrit par .htaccess)
├── robots.php             # Génère robots.txt (réécrit par .htaccess)
├── check.php              # Diagnostic local uniquement (404 en production / hors local)
├── .htaccess              # Sécurité, réécritures, cache, compression
├── LISEZ-MOI.md           # Documentation détaillée de déploiement
├── README.md              # Ce fichier
├── assets/
│   ├── css/style.css
│   ├── js/app.js
│   └── img/               # Logo, photos, flyer, image de partage (og-cover.jpg)
└── data/                  # Données locales (protégé)
    └── uploads/           # Pièces d'identité et diplômes (accès HTTP bloqué)
```

---

## Prérequis

- **PHP 8.0+** avec les extensions : `curl`, `pdo_mysql` (ou `pdo_sqlite`), `fileinfo`, `mbstring`, `json`.
- **MySQL 5.7+** / **MariaDB 10.3+** (ou SQLite en secours).
- **Apache** avec `mod_rewrite`, `mod_headers`, `mod_deflate`.
- **HTTPS** obligatoire en production.
- Un compte **Campay** (recommandé) pour encaisser réellement.

---

## Installation locale (XAMPP)

```bash
# Démarrer XAMPP
sudo /opt/lampp/lampp start

# Copier le site dans htdocs
cp -r . /opt/lampp/htdocs/y4i

# Rendre le dossier data inscriptible
chmod -R 775 /opt/lampp/htdocs/y4i/data
```

Ouvrir ensuite :

- Site : <http://localhost/y4i/>
- Diagnostic : <http://localhost/y4i/check.php> (uniquement en local)
- Administration : icône cadenas dans le menu, ou `#admin`

La base `y4i_academy` est **créée automatiquement** au premier chargement (MySQL, sinon SQLite dans `data/y4i.sqlite`).

---

## Configuration

Tout se règle dans **`config.php`** :

| Section | Rôle |
|---|---|
| `debug` | `true` en local, **`false` en production** |
| `app_url` | URL du site (détectée automatiquement, surchargeable) |
| `db` | Connexion MySQL : `host`, `port`, `name`, `user`, `pass` |
| `admin_password` | Mot de passe admin — **à changer** (`ChangeMoi!2026` par défaut) |
| `whatsapp`, `phone_secondary`, `email` | Coordonnées affichées |
| `min_guarantee` | Fonds de garantie minimum (5 000 FCFA) |
| `company` | Nom légal, RCCM/NIU, adresse, responsable, horaires, carte |
| `mail` | Notifications e-mail (`enabled` = `false` par défaut) |
| `analytics` | Mesure d'audience : `''`, `ga4` ou `matomo` |
| `security` | `force_https`, sessions, limites anti-abus |
| `campay` | Identifiants Mobile Money (username / password) |
| `mtn`, `orange` | Intégrations directes optionnelles |

### Exemple minimal pour la production

```php
'debug'   => false,
'app_url' => 'https://votre-domaine.cm',

'admin_password' => 'UnMotDePasseFort!2026',

'campay' => [
    'base'        => 'https://www.campay.net/api',
    'username'    => 'VOTRE_APP_USERNAME',
    'password'    => 'VOTRE_APP_PASSWORD',
    'webhook_key' => '',
],

'security' => [
    'force_https' => true,
],
```

> Les valeurs `company.*` sont des **exemples** : remplacez-les avant la mise en ligne.

---

## Paiement Mobile Money

### Campay (recommandé — Orange + MTN en une seule intégration)

1. Créer un compte marchand sur <https://campay.net> et faire valider l'entreprise.
2. Créer une application, puis copier **App Username** et **App Password** dans `config.php`.
3. Pour tester sans argent réel : `base = https://demo.campay.net/api` avec les identifiants de démonstration.
4. En production : `base = https://www.campay.net/api`.
5. Webhook à déclarer dans Campay : `https://votre-domaine.cm/api.php?action=webhook_campay`.

**Fonctionnement :** le site demande le paiement → Campay envoie la demande sur le téléphone → le prestataire saisit son **code secret uniquement sur son téléphone** → le site confirme et active le compte.

> Le code secret ne transite jamais par le site. Sans identifiants Campay, le paiement reste en **mode simulation** (aucun argent débité).

### Autres passerelles

- **MTN MoMo direct** : `config.php` → `mtn` (sandbox ou production `proxy.momoapi.mtn.com`).
- **Orange Money direct** : `config.php` → `orange` (sandbox ou production `.../cm/v1`).

---

## Base de données

- Base par défaut : **`y4i_academy`** (MySQL/MariaDB).
- Les tables sont créées automatiquement : `accounts`, `payments`, `formations`, `tariffs`, `messages`, `requests`, `testimonials`, `tracking`, `activity_log`, `settings`, `geocache`, `rate_limits`.
- En cas d'indisponibilité de MySQL, le site bascule sur **SQLite** (`data/y4i.sqlite`) si `pdo_sqlite` est disponible.
- Le schéma et les données de démonstration (formations, tarifs, témoignages d'exemple) sont initialisés au premier lancement.

---

## Administration

Accès : icône **cadenas** du menu ou `#admin`, avec le mot de passe `admin_password`.

Fonctions disponibles :

- Tableau de bord et statistiques.
- Comptes clients/prestataires et fonds bloqués.
- **Vérification des prestataires** : consulter la pièce d'identité et le diplôme, puis statut *Vérifié / Refusé / En attente*.
- Paiements et remboursements.
- Messages et demandes de rappel.
- **Témoignages** : créer, modifier, publier/dépublier, supprimer.
- Tarifs et formations.
- Journal d'activité + exports CSV.
- Changement du mot de passe administrateur.

---

## Sécurité avant mise en ligne

- [ ] Changer `admin_password` (12 caractères minimum, jamais `ChangeMoi!2026`).
- [ ] `debug` = `false`.
- [ ] `force_https` = `true` + certificat SSL valide + redirection HTTP → HTTPS.
- [ ] Renseigner les vraies informations `company` et remplacer les témoignages d'exemple.
- [ ] Configurer Campay en production et tester un vrai paiement de 5 000 FCFA.
- [ ] Vérifier que `config.php`, `lib.php`, `pay.php`, `inc_public.php`, `data/` renvoient **403/404**.
- [ ] Sauvegardes automatiques de la base et de `data/uploads/`, avec test de restauration.
- [ ] Déclarer le site dans Google Search Console et créer une fiche Google Business Profile.

---

## Sauvegarde et restauration

### Sauvegarder la base

```bash
mysqldump -u root -p --default-character-set=utf8mb4 \
  --single-transaction --triggers \
  --databases y4i_academy > y4i_academy.sql
```

### Sauvegarder les uploads

```bash
tar -czf y4i-uploads.tar.gz -C /opt/lampp/htdocs/y4i data/uploads
```

### Restaurer la base

```bash
mysql -u root -p < y4i_academy.sql
```

Ou via phpMyAdmin : **Importer** → sélectionner le fichier `.sql`.

> Les documents de `data/uploads/` contiennent des données personnelles : **ne les déposez jamais sur GitHub**.

---

## Déploiement GitHub

Ce dépôt contient le **code du site**. Trois règles importantes :

1. **Ne commitez jamais** les vraies clés (`campay`, `mtn`, `orange`, SMTP) ni le mot de passe admin. Gardez `config.php` avec des placeholders et renseignez les vraies valeurs directement sur le serveur.
2. **Ne commitez jamais** `data/uploads/` (pièces d'identité, diplômes) ni les exports `.sql` (données personnelles).
3. Le fichier `.gitignore` fourni exclut automatiquement les données locales, sauvegardes et journaux.

### Mettre à jour le dépôt

```bash
git add -A
git commit -m "Mise à jour du site Y4I Academy"
git push
```

### Déploiement sur l'hébergement

1. Envoyer les fichiers dans `public_html/` (ou le dossier du site).
2. Rendre `data/` inscriptible (`755`/`775`) et vérifier que `data/uploads/` est protégé.
3. Renseigner `config.php` sur le serveur (base, `app_url`, `admin_password`, Campay, `company`).
4. Activer HTTPS et `force_https`.
5. Vérifier `https://votre-domaine.cm/sitemap.xml` et `robots.txt`.

---

## Licence

Projet privé — **© Y4I Academy**. Tous droits réservés.

Pour toute question : `contact@y4i-academy.cm` · +237 622 142 496 · WhatsApp +237 696 500 949
