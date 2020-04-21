# Déploiement de Strapi à partir de Github

L'objectif de cette partie est d'aller un peu plus loin avec les Actions de Github. Dans le cas de Strapi, nous allons devoir modifier notre job afin de builder l'administration de Strapi. C'est notamment ce qu'on fait à travers un `npm run postinstall` et qui est fait actuellement coté Clever Cloud. 

Mais dans un premier temps, il faut déployer l'application. Par la suite, nous ajouterons le build.

## Déploiement de Strapi

La première étape est identique à notre déploiement de Ghost. Je vous invite à relire la [documentation](ghost.md). N'oubliez pas de créer les variables d'environnement ;) 

## Ajout du build

Nous allons modifier le nom de notre job par `deploy-and-build` pour qu'il soit plus réaliste. À l'intérieur, il faut ajouter de nouvelles étapes pour construire l'administration et la déployer.

De la même manière que sur la première partie, posez vous les bonnes questions sur l'écriture de ces nouvelles étapes ;) 

Quelques questions : 

- *Comment exécuter le build sur dans le job ?*

- *Comment allez vous faire pour déployer les dossiers /build et /.cache dans le workflow ?* 

Pour vous aider, utilisez la [documentation](https://help.github.com/en/actions/) de Github Actions. N'hésitez pas aussi à utiliser la documentation de [Git](https://git-scm.com/docs/) également.

## Et après ? 

Si votre déploiement a bien été fait sur Clever Cloud, félicitations ! Sinon n'hésitez pas à vous aider de la solution plus bas. 

Rendez-vous dans le déploiement via Github de [Gatsby](gatsby.md).

## Solution

Si vous bloquez sur l'ajout du job, voici un exemple de solution. 

<details>
  <summary>Voir une solution</summary>
  
  ````yml 
# Nom de notre workflow
name: Deploy

# Sur un événement "push" sur la branche production
on:
  push:
    branches: [production]

# Création de notre job deploy
jobs:
  deploy-and-build:
    # On sélectionne un ubuntu
    runs-on: ubuntu-latest

    steps:
      # On utilise une action de Github pour récupérer le repo local
      - uses: actions/checkout@v2

      # Utilisation de notre version de node
      - name: Use Node.js 12.16.1
        uses: actions/setup-node@v1
        with:
          node-version: 12.16.1

      # Récupération de l'ensemble des commits du repository. Par défaut, le clone ne récupére pas un historique profond. Or pour Clever Cloud, c'est indispensable
      - name: Configure Git
        run: |
          git fetch --prune --unshallow
          git config --global user.email YOUR_EMAIL
          git config --global user.name YOUR_NAME

      # Build de notre administration strapi
      - name: Build Admin Panel
        run: |
          npm install 
          NODE_ENV=production npm run build

      # Commit des fichiers de build
      - name: Commit build folder
        run: |
          git add --force ./build/
          git add --force ./.cache/
          git commit -m 'strapi admin build - Github Build'

      # Installation de clever-tools and deploy app
      - name: Instal and Deploy to Clever
        run: |
          npm install -g clever-tools
          clever login --token ${{ secrets.CLEVER_TOKEN }} --secret ${{ secrets.CLEVER_SECRET }}
          clever link ${{ secrets.CLEVER_APP_ID }} --alias ubeers-strapi
          clever deploy --branch production
  
  ````
</details>


 
