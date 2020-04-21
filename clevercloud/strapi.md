# Déploiement de Strapi sur Clever Cloud

## Création de l'application
Dans un premier temps, nous allons créer une application NodeJS au sein de la console de Clever Cloud. 

Pour la scalabilité de l'application, je vous conseille de choisir le plan Pico qui est largement suffisant pour faire tourner un Strapi. 

![Image](/images/pico.png)


## Liaison des repos

Maintenant, que notre application est créée, il va falloir relier les repository entre eux. Ajoutez un nouveau repository distant avec les informations de Clever Cloud à votre projet en local via le `git remote add clever CLEVER_REPO`

## Création de notre base de donnée

Nous devons aussi créer un add-on MongoDB sur Clever Cloud, prenez le plan gratuit qui suffit amplement aussi à notre besoin.

## Préparation de Strapi

Nous pouvons passer à la préparation du déploiement de Strapi. 

Vous devez modifier le fichier `database.json` se trouvant dans le répertoire `config/environnements/production/` pour prendre en compte les informations de la base MongoDB distante. 

Pour cela, vous devez utiliser des variables d'environnements afin de ne pas stocker vos informations en clair dans le repository. Je vous recommande de créer 5 variables : 

- DATABASE_HOST
- DATABASE_NAME
- DATABASE_PASSWORD
- DATABASE_PORT
- DATABASE_USERNAME

Il faut aussi que vous gériez le cas où les variables ne seraient pas disponible. 

Exemple : 

` ${process.env.DATABASE_HOST || '127.0.0.1'} `

Maintenant, vous devez également sur Clever Cloud dans les informations de l'application Strapi, insérez votre variables d'environnement afin que Clever injecte ces variables au lancement du déploiement. 

## Modification du `package.json` 

Avant de déployer, vous devez juste modifier certaines commandes du fichier `package.json`.

Nous devons ajouter une variable d'environnement à la commande `start`, il s'agit de `NODE_ENV=production`. Je vous encourage également à la créer sur Clever Cloud. 

Enfin, pour que l'application fonctionne, il faut aussi ajouter la commande `postinstall` pour signifier qu'après l'installation des node_modules, nous allons effectuer un build de l'administration de Strapi. N'oubliez pas le `NODE_ENV` également.

### Déploiement

C'est parti pour le déploiement, et rien de plus simple avec Clever, puisqu'un simple `git push clever master` suffit pour déployer votre application. 

Vérifiez bien les logs de votre application pendant le déploiement. 

Une fois que l'application est déployée, rendez-vous sur l'URI pour vérifier que tout fonctionne. Par exemple, faites une requête sur Postman pour vérifier la récupération des données.

## Et après ? 

Rendez-vous sur le déploiement de [Ghost](ghost.md)