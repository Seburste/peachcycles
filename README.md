# peachcycles.com

Site vitrine statique de Peach Cycles, servi par nginx derrière le Traefik partagé du VPS
`vps-f3743124` (dépôt `Seburste/vps-edge`). Remplace l'ancien WordPress de l'hébergement OVH
`se-b.net`, piraté en 2026 (porte dérobée `wp-includes/widgets/mzmnvfih.php`).

**Copie fidèle de l'ancien site (thème Twenty Seventeen), sans PHP ni base de données.**
Les 8 pages sont le HTML public du 02/11/2026, nettoyé : plus de scripts WordPress (emoji,
Contact Form 7, statistiques Jetpack), plus de liens vers l'API WordPress. Les CSS et JS du thème
et de WordPress viennent du miroir officiel `github.com/WordPress/WordPress` (contrôlés identiques
octet pour octet à ceux du site). La police Libre Franklin est hébergée ici (plus d'appel à Google).
La vidéo YouTube passe par `youtube-nocookie.com`. Une photo contenait des coordonnées GPS : retirées
sans toucher aux pixels. Contrôle : captures d'écran identiques au pixel près à l'ancien site,
sur ordinateur et téléphone.

## Modifier le site

Les pages sont dans `site/<page>/index.html` (l'accueil : `site/index.html`). Le texte se modifie
directement dans le HTML, dans le bloc `<div class="entry-content">`. Les images sont dans
`site/wp-content/uploads/`. Pas d'étape de construction.

Si l'un des deux petits scripts en ligne du `<head>` change, recalculer son empreinte dans la
`Content-Security-Policy` de `nginx.conf` (sinon le navigateur le bloque).

## Déploiement sur le VPS

Le dossier sur le VPS est `~/peachcycles`. Mise en ligne ou mise à jour :

```
cd ~/peachcycles && docker compose -f docker-compose.yml -f docker-compose.le.yml up -d
```

Première mise en ligne, en deux temps (le certificat Let's Encrypt exige que le DNS pointe déjà
vers le VPS, challenge HTTP-01) :

1. Test avant le DNS : `docker compose up -d`, puis sur le PC ajouter
   `51.210.8.72 peachcycles.com www.peachcycles.com` au fichier `hosts`. Le navigateur affiche
   un avertissement de certificat : c'est normal à cette étape.
2. Après la bascule DNS : la commande avec `docker-compose.le.yml` ci-dessus, puis
   `docker logs traefik 2>&1 | grep -i peachcycles` pour voir les certificats obtenus.

## Sécurité

- Aucun port publié : seul Traefik joint le conteneur (réseau `edge`). Conteneur en lecture seule.
- Pas de PHP, pas de base, pas de cookie, pas de mesure d'audience. CSP stricte : seuls les scripts du site.
- Les adresses de l'ancien WordPress (`/wp-login.php`, `/xmlrpc.php`…) répondent 410.

## À compléter

- Mentions légales : la page d'origine est vide (obligation légale pour une entreprise : éditeur, SIRET, hébergeur).
