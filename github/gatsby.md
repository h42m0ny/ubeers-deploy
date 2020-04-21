# Déploiement de Gatsby à partir de Github

L'objectif de cette partie est d'aller encore un peu plus loin avec les Actions de Github. Dans le cas de Gatsby, nous allons créer un deuxième job et voir comment nous passons des données entre nos jobs. 

À noter que nous aurions pu très bien utiliser le même job `build-and-deploy` de la partie précédente. Pour autant, il est intéressant de découvrir une autre fonctionnalité du CI/CD de Github : les Artifacts. 

Mais dans un premier temps, il faut déployer l'application et tester le build. Par la suite, nous ajouterons le build.

## Déploiement de Gatsby

La première étape est identique à notre déploiement de Ghost. Je vous invite à relire la [documentation](ghost.md). N'oubliez pas de créer les variables d'environnement ;) 

## Ajout du build 

La deuxième étape est identique à notre déploiement de Strapi. Je vous invite à relire la [documentation](strapi.md). N'oubliez pas d'adapter les commandes à Gatsby et les variables d'environnement propre à Gatsby (cf Clever Cloud) ;) 

## Ajout d'un nouveau job

Chaque job dans un workflow est indépend de l'autre, et chaque job tourne en parallèle sauf si on lui dit le contraire avec l'instruction `need: job_name` comme la documentation l'explique. 

### Comment partager des données entre les jobs ? 

C'est là qu'intervient la notion `Artifact`. Il s'agit d'un dossier/fichier que l'on peut sauvegarder grâce à une Action officielle et si besoin utiliser son contenu dans un autre job, grâce là aussi à une autre Action officielle.

C'est exactement ce qu'on recherche ici, on va faire une archive du dosser `./public/` à travers un Artifact dans le job `Build` puis remettre en place le dossier dans le job `Deploy` avant de déployer notre application. 

Pour en savoir plus, regardez la [documentation](https://help.github.com/en/actions/configuring-and-managing-workflows/persisting-workflow-data-using-artifacts). 

### Création du second job

Pour commencer, nous allons ajouter un nouveau job `Build` qui va reprendre l'environnement de `Deploy`. Pour ajouter un second job, il suffit de suivre cette syntaxe : 

```jobs:
  Build:
    name: Build de Gatsby
  Deploy:
    name: Deploiement
```

Dans le job `Build`, il va falloir reprendre le même environnement au niveau de l'OS et aussi au niveau des variables d'environnement.

Ensuite, il faut répartir les taches de build présent dans `Deploy` sur celui de la `Build`. 

### Besoin d'aide ?

Pour vous aider, utilisez la [documentation](https://help.github.com/en/actions/) de Github Actions. N'hésitez pas aussi à utiliser la documentation de [Git](https://git-scm.com/docs/) également. Et n'hésitez pas également sur le Discord ;)  

## Et après ? 

Si votre déploiement a bien été fait sur Clever Cloud, félicitations ! Sinon n'hésitez pas à vous aider de la solution plus bas. 

Avant de passer à la suite, avec notamment la découverte des pipelines de Gitlab, il faut qu'on discute de la problématique du temps de déploiement. 

Comme vous avez pu le voir, l'utilisation des deux jobs ici va consommer davantage de temps que notre déploiement de Gatsby. Et c'est tout à fait normal puisqu'il y a la création d'une archive, le dépot sur un serveur et sa récupération dans un autre job. 

Ici dans cet exemple, l'intérêt de l'utilisation d'Artifacts n'est pas évident, à part une meilleure clareté sur Github dans la partie Actions. Pourtant, l'utilisation des Artifacts sur un projet avec différents builds ou encore sur un monorepo a tous son sens. Ici l'idée était davantage de vous faire découvrir les Artifacts que de jouer sur les performances de déploiements ;) 

Pour la suite, on va commencer le déploiement de [Ghost](../gitlab/start.md) sur Gitlab.


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
  build:
    # On sélectionne un ubuntu
    runs-on: ubuntu-latest
    env:
      GHOST_URI: ${{ secrets.GHOST_URI }}
      GHOST_TOKEN: ${{ secrets.GHOST_TOKEN }}
      STRAPI_URI: ${{ secrets.STRAPI_URI }}

    steps:
      # On utilise une action de Github pour récupérer le repo local
      - uses: actions/checkout@v2

      # Utilisation de notre version de node
      - name: Use Node.js 12.16.1
        uses: actions/setup-node@v1
        with:
          node-version: 12.16.1

      # On lance le build de Gatsby
      - name: Build Gatsby
        run: |
          npm install
          npm run build

      # Création d'un artifacts comprenant notre dossier public
      - name: Archive build artifacts
        uses: actions/upload-artifact@v1
        with:
          name: public
          path: ./public/

  deploy:
    # On dit que ce job ne peut pas se passer sans l'autre
    needs: [build]

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

      # Récupération de l'Artifact et donc du dossier public
      - uses: actions/download-artifact@master
        with:
          name: public
          path: ./public/

      # Récupération de l'ensemble des commits du repository. Par défaut, le clone ne récupére pas un historique profond. Or pour Clever Cloud, c'est indispensable
      - name: Run git commands
        run: |
          git fetch --prune --unshallow
          git config --global user.email YOUR_EMAIL
          git config --global user.name YOUR_NAME
          git add --force ./public/
          git commit -m 'gatsby build prepare to deploy'

      # Installation de clever-tools and deploy app
      - name: Instal and Deploy to Clever
        run: |
          npm install -g clever-tools
          clever login --token ${{ secrets.CLEVER_TOKEN }} --secret ${{ secrets.CLEVER_SECRET }}
          clever link ${{ secrets.CLEVER_APP_ID }} --alias ubeers-strapi
          clever deploy --branch production --force
  
  ````
</details>


 
