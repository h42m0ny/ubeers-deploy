# Déploiement de Ghost sur Clever Cloud

## Création de l'application

Dans un premier temps, nous allons créer une application NodeJS au sein de la console de Clever Cloud.

Pour le bon fonctionnement de l'application, il faut au minimum chosir le plan `Nano`. L'instanciation de Ghost demandant plus de travail que sur Strapi par exemple.

## Liaison des repos

Maintenant, que notre application est créée, il va falloir relier les repository entre eux. Ajoutez un nouveau repository distant avec les informations de Clever Cloud à votre projet en local via le `git remote add clever CLEVER_REPO`

## Création de notre FS Buckets

![Image](/images/FSBuckets.png)

Ensuite, nous allons devoir créer un FS Buckets pour stocker nos données (images, sqlite3). Il s'agit simplement d'un espace disque indépendant qui peut être liée à des applications. Pour ce faire, vous devez ajouter un add-on.

Au moment de la création de l'application, reliez votre add-on à l'application Ghost que vous avez créé un peu plus tôt.

## Préparation de Ghost

### Déplacement de fichiers

Dans le répertoire de votre application, vous devez vous poser une question. Où est le fichier `package.json` ? Comment mon application démarre t-elle ?

En réalité, Ghost démarre votre application par le biais d'un symlink `current` que vous retrouver à la racine du projet. Il contient l'instanciation de votre ghost qui pointe vers la version courrante de votre Ghost.

C'est un peu complexe, et pour que cela soit plus clair, nous allons supprimer ce dossier puisque nous allons plus en avoir besoin par la suite.

Ce qui nous intéresse (package.json, node_modules, ...) se trouve dans le dossier `versions/X.X.X`. Une fois supprimé le `current`, nous allons devoir déplacer le contenu du répertoire `versions/X.X.X` à la racine de votre projet. Vous allez devoir merger à la main les fichiers. Cependant, nous n'avons pas fait beaucoup de modification et à part notre base et les images, il n'y a pas grand chose comme différences.

Vous devriez avoir comme arborescence cela :

![Image](/images/files-ghost.png)

### Création du gitignore

On va devoir faire un peu de ménage dans notre dossier sur ce qu'on va déployer. Pour ce faire, vous devez créer un fichier `.gitignore` pour enlever tout ce qui ne concerne pas la production.

Voici un aperçu du mien :

```
node_modules/
/content/
yarn.lock
config.development.json
```

Comme vous le voyez, on va exclure aussi notre dossier `content` puisque les données vont être sur le bucket créé juste avant.

### Création du fichier de configuration

Maintenant, nous allons créer un fichier `config.production.json` qui est une copie de celui `config.development.json`.

Voici un exemple :

```js
{
  "url": "URI_CLEVER_CLOUD",
  "server": {
    "port": 8080,
    "host": "0.0.0.0"
  },
  "database": {
    "client": "sqlite3",
    "connection": {
      "filename": "content/data/ghost-prod.db"
    }
  },
  "mail": {
    "transport": "Direct"
  },
  "logging": {
    "transports": [
      "file",
      "stdout"
    ]
  },
  "process": "local",
  "paths": {
    "contentPath": "content/"
  }
}
```

## Modification du `package.json`

Avant de déployer, vous devez juste modifier certaines commandes du fichier `package.json`.

Nous devons ajouter une variable d'environnement à la commande `start`, il s'agit de `NODE_ENV=production`. Je vous encourage également à la créer sur Clever Cloud.

## Configuration du FS Buckets

Maintenant, nous allons faire une étape indispensable pour que Ghost communique bien avec notre bucket. Il faut créer une variable d'environnement `CC_FS_BUCKET` où vous allez stocker : `/content:BUCKET_HOST`

On dit à Clever que nous voulons "monter" notre bucket sur un dossier qui s'appelle `content` et qui sera disponible à la racine du projet.

## Téléversement des fichiers dans le bucket

Avant d'uploader notre dossier `content`, je vous invite à faire une copie de votre db présente dans `content/data` dans un fichier appelé `ghost-prod.db` pour faire le lien avec notre `config.production.json` et pour que les données soient bien présentes en production.

Il faut maintenant mettre notre dossier `content` sur le bucket. Pour cela, connectez-vous en ftp et téléverser le dossier simplement.

### Déploiement

C'est parti pour le déploiement, et rien de plus simple avec Clever, puisqu'un simple `git push clever master` suffit pour déployer votre application.

Vérifiez bien les logs de votre application pendant le déploiement.

Une fois que l'application est déployée, rendez-vous sur l'URI pour vérifier que tout fonctionne. Connectez-vous à la partie administration de Ghost.

## Et après ?

Rendez-vous sur le déploiement de [Gatsby](gatsby.md)
