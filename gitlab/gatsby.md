# Déploiement de Gatsby à partir de Gitlab

L'objectif de cette partie est d'aller encore un peu plus loin avec les pipelines de Gitlab. Dans le cas de Gatsby, nous allons créer un deuxième job et voir comment nous passons des données entre nos jobs. 

À noter que nous aurions pu très bien utiliser le même job `build-and-deploy` de la partie précédente. Pour autant, il est intéressant de découvrir une autre fonctionnalité du CI/CD de Gitlab : les Artifacts. 

Mais dans un premier temps, il faut déployer l'application et tester le build. Par la suite, nous ajouterons le build.

## Déploiement de Gatsby

La première étape est identique à notre déploiement de Ghost. Je vous invite à relire la [documentation](ghost.md). N'oubliez pas de créer les variables d'environnement ;) 

## Ajout du build 

La deuxième étape est identique à notre déploiement de Strapi. Je vous invite à relire la [documentation](strapi.md). N'oubliez pas d'adapter les commandes à Gatsby et les variables d'environnement propre à Gatsby (cf Clever Cloud) ;) 

### Comment partager des données entre les jobs ? 

C'est là qu'intervient la notion `Artifact`. Il s'agit d'un dossier/fichier que l'on peut sauvegarder grâce à une commande de la pipeline Gitlab. Contrairement à Github, le stockage de l'Artifact dans le cache de la pipeline permettra de récupérer le dossier sans faire appel à une méthode particulière.  

Pour en savoir plus, regardez la [documentation](https://docs.gitlab.com/ee/ci/yaml/#artifacts). 

### Création du second job

Pour commencer, nous allons ajouter un nouveau job `Build` qui va reprendre l'environnement de `Deploy`. Pour ajouter un second job, il suffit de suivre cette syntaxe : 

```jobs:
  stages:
  - build
  - deploy

  build:
    stage: build
    ...

  deploy:
    stage: deploy
    ...
```

Ensuite, il faut répartir les taches de build présent dans `Deploy` sur celui de la `Build`.

N'hésitez pas aussi à regarder le fonctionnement de celui de Github, il y a des similarités ;) 

![Image](/images/build-deploy-gitlab.png)

### Besoin d'aide ?

Pour vous aider, utilisez la [documentation](https://docs.gitlab.com/ee/ci/yaml/) de Gitlab. N'hésitez pas aussi à utiliser la documentation de [Git](https://git-scm.com/docs/) également. Et n'hésitez pas également sur le Discord ;)  

## Et après ? 

Si votre déploiement a bien été fait sur Clever Cloud, félicitations ! Sinon n'hésitez pas à vous aider de la solution plus bas. 

Avant de passer à la suite, il faut qu'on discute de la problématique du temps de déploiement. 

Comme vous avez pu le voir, l'utilisation des deux jobs ici va consommer davantage de temps que notre déploiement de Strapi. Et c'est tout à fait normal puisqu'il y a la création d'une archive, le dépot sur un serveur et sa récupération dans un autre job. 

Ici dans cet exemple, l'intérêt de l'utilisation d'Artifacts n'est pas évident, à part une meilleure clareté sur Gitlab dans la partie Pipelines. Pourtant, l'utilisation des Artifacts sur un projet avec différents builds ou encore sur un monorepo a tous son sens. Ici l'idée était davantage de vous faire découvrir les Artifacts que de jouer sur les performances de déploiements ;) 

Pour la suite, on va évoquer la question de la mise à jour des contenus de Strapi / Ghost vers Gatsby. Nous voulons en effet qu'à chaque nouvelle publicaiton, cela déclenche un nouveau build ;) Pour commencer, on va discuter [Webhook](./../webhook/start.md).


## Solution

Si vous bloquez sur l'ajout du job, voici un exemple de solution. 

<details>
  <summary>Voir une solution</summary>
  
  ````yml 
# On va utiliser un environnement avec node
image: node:12.16.1

# On déclare nos stages
stages:
  - build
  - deploy

# On créé notre job build
build:
  stage: build

  # On donne un nom à notre environnement
  environment:
    name: production

  # On précise quelle branche
  only:
    - production

  # On build notre gatsby
  script:
    - npm install
    - npm run build

  # On sauvegarde notre artefact
  artifacts:
    paths:
      - ./public/

# On créé notre job deploy
deploy:
  stage: deploy

  # On donne un nom à notre environnement
  environment:
    name: production

  # On précise quelle branche
  only:
    - production

  # On configure git
  before_script:
    - git fetch --prune --unshallow
    - git config --global user.email YOUR_EMAIL
    - git config --global user.name YOUR_NAME

  # On build notre gatsby
  script:
    - git add --force ./public/
    - git commit -m 'Gatsby Build - Github Build'

  # On lance les scripts
  after_script:
    - npm install -g clever-tools
    - clever login --token $CLEVER_TOKEN --secret $CLEVER_SECRET
    - clever link $CLEVER_APP_ID --alias ubeers-gatsby
    - clever deploy --force
  
  ````
</details>


 
