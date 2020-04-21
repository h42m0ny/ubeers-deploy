# Express

## Création de l'application en local

Pour commencer, nous allons simplement démarrer une application Express.

Pour cela, nous devons créer un dossier `ubeers-deploy` et insérer un package.json à l'intérieur.

Voici une version de base :

```json
{
  "name": "ubeers-deploy",
  "private": true,
  "description": "A simple web app for deploy an app from github or gitlab repository",
  "version": "0.0.1",
  "author": "ubeers",
  "dependencies": {
    "express": "^4.17.1"
  },
  "license": "MIT",
  "scripts": {
    "start": "node server.js"
  }
}
```

Comme vous pouvez le voir, on va créer un fichier `server.js` à la racine dans lequel nous allons faire fonctionner le serveur Express.

Pour pouvoir le faire, il faut bien faire un `npm install` avant.

Pour créer le serveur sur le port 8080, suivez la [documentation](https://expressjs.com/en/starter/hello-world.html) d'Express.

Enfin, il faut initialiser `git` à l'intérieur du dossier et créer un `.gitignore` excluant les `node_modules`.

![Image](/images/express-hello.png)

### Installation de Nodemon

Je vous encourage à installer en dépendance du projet dans le `package.json`, l'outil Nodemon qui va permettre de reload le serveur Express dès qu'une modification va être apportée au fichier `server.js`. Plus pratique !

Ensuite, insérer cette commande dans la partie script :

`"dev": "nodemon server.js"`

Lancez un `npm run dev`, modifiez le fichier `server.js` par exemple en changeant le `Hello World` et voyez le résultat en temps réel ;)

## Création de l'application sur Clever Cloud

Dans un premier temps, nous allons créer une application NodeJS au sein de la console de Clever Cloud.

Pour la scalabilité de l'application, je vous conseille de choisir le plan Pico qui est largement suffisant pour faire tourner notre application.

## Création du repository sur Github

Ensuite, nous allons créer le repository sur Github.

## Création du repository sur Gitlab

Faites de même sur Gitlab.

## Et après ?

Avant d'aller plus loin sur l'app Express, voyons comment l'api de [Github](github.md) fonctionne.
