# Déploiement de Gatsby sur Clever Cloud

## Quelques précisions

Dans la suite, nous fait le choix de déployer notre application sur Clever Cloud. Ce choix a été fait pour priviligier une cohérence dans l'environnement de déploiements.

Pour autant, nous aurions très bien pu déployer notre répertoire `public` après un `gatsby build` sur un CDN, sur Netlify ou encore sur un Github Pages. Si cela vous intéresse, il y a une [documentation](https://www.gatsbyjs.org/docs/deploying-to-netlify/) sur le site de Gatsby qui contient beaucoup d'exemples en fonction du provider.


## Création de l'application
Dans un premier temps, nous allons créer une application NodeJS au sein de la console de Clever Cloud. 

Pour la scalabilité de l'application, je vous conseille de choisir le plan Pico qui est largement suffisant pour faire tourner un Gatsby. 

## Préparation de Gatsby

### Utilisation d'Express

Pour faire tourner Gatsby sur Clever, nous allons simplement ajouter un petit serveur `Express` qui va servir le dossier `public` qui contiendra notre site static. 

Lorsque vous faite un `gatsby serve`, Gatsby va servir votre répertoire sur un Express. On reproduit le même schéma mais pour un environnement de production. 

Pour mettre en place un Express au dessus de Gatsby, nous allons utiliser un package fait pour ça et qui va nous simplifier la tâche. Il s'agit de `gatsby-plugin-express`.

Pour l'utiliser, il suffit de suivre la procédure décrite dans le [repository](https://github.com/TonyCois/gatsby-plugin-express). Et de bien faire l'install aussi d'Express dans votre `package.json`.

A noter que si vous n'avez pas de CSS, c'est qu'il vous manque `gatsby-plugin-styled-components` dans le `gatsby-config`.

ATTENTION : La 404 n'est plus gérée par le package, n'inserez donc pas la partie `template: 'public/404/index.html'` dans le `server.js`

### Configuration d'Express pour Clever

Le port par défaut est de 3000. Pour que le déploiement fonctionne sur Clever, conformément à la documentation, il faut changer le port pour le 8080. 

Modifier aussi la commande npm start pour prendre en compte le lancement de votre serveur. 

### Environnements

Maintenant que nous avons notre `Express`, il nous reste à bien gérer notre environnement de développement et de production. En effet, pour le moment, le build de notre site utilise l'api local de Ghost et de Strapi. 

Pour modifier cela, nous allons adapter un peu notre `gatsby-config` en ajoutant des variables d'environnements : 

- GHOST_URI qui contiendra l'uri de Ghost
- GHOST_TOKEN qui contient le token d'accès
- STRAPI_URI qui contiendra l'uri de Strapi

Pour pouvoir les utiliser dans notre `gatsby-config`, nous allons simplement les déclarer au tout début du fichier en prenant en compte si la valeur n'est pas défini. 

Par exemple : `const GHOST_URI = process.env.GHOST_URI || "http://localhost:2368"`

Ensuite, il faut adapter le resolve du plugin correspondant. 

Enfin, pour que notre application fonctionne en production, nous allons aussi créer ces variables d'environnements dans notre application Gatsby sur Clever Cloud où nous ferons la correspondance avec notre Ghost et Strapi sur Clever.

À noter que nous aurions pu très bien utiliser les fichiers d'environnements `.env.*`. Pour plus d'informations, voici la [documentation](https://www.gatsbyjs.org/docs/environment-variables/). Je trouvais plus simple dans un premier temsp d'adapter notre `gatsby-config`.



## Modification du `package.json` 

Avant de déployer, nous allons juste ajouter la commande `postinstall` pour signifier qu'après l'installation des node_modules, nous allons effectuer un build de Gatsby via la méthode `gatsby build`.

### Déploiement

C'est parti pour le déploiement, et rien de plus simple avec Clever, puisqu'un simple `git push clever master` suffit pour déployer votre application. 

Vérifiez bien les logs de votre application pendant le déploiement. 

Une fois que l'application est déployée, rendez-vous sur l'URI pour vérifier que tout fonctionne. Vous devriez voir le site apparaitre. 

Par exemple, ci-dessous la page `Bières` :

![Image](/images/beers-search.png)

## Et après ? 

Nous allons découvrir le workflow de Github pour déployer sur Clever Cloud.

Rendez-vous dans un premier temps sur le début de la partie [Github](./../github/start.md).