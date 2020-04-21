# Déploiement de Strapi à partir de Gitlab

L'objectif de cette partie est d'aller un peu plus loin avec les pipelines de Gitlab. Dans le cas de Strapi, nous allons devoir modifier notre job afin de builder l'administration de Strapi. C'est notamment ce qu'on fait avec Github qu'on reproduit là ausi.

Mais dans un premier temps, il faut déployer l'application. Par la suite, nous ajouterons le build.

## Déploiement de Strapi

La première étape est identique à notre déploiement de Ghost. Je vous invite à relire la [documentation](ghost.md). N'oubliez pas de créer les variables d'environnement ;) 

## Ajout du build

Nous allons modifier le nom de notre job par `build-and-deployment` pour qu'il soit plus réaliste. À l'intérieur, il faut ajouter de nouvelles commande pour construire l'administration et la déployer.

De la même manière que sur la première partie, posez vous les bonnes questions sur l'écriture de ces nouvelles étapes ;) 

Quelques questions : 

- *Comment exécuter le build sur dans le job ?*

- *Comment allez vous faire pour déployer les dossiers /build et /.cache dans la pipeline ?* 

Pour vous aider, utilisez la [documentation](https://gitlab.com/help/ci/README.md/) de Gitlab. N'hésitez pas aussi à utiliser la documentation de [Git](https://git-scm.com/docs/) également.

À noter que l'approche est similaire à ce qu'on a fait dans Github sur Strapi ;) 

## Et après ? 

Si votre déploiement a bien été fait sur Clever Cloud, félicitations ! Sinon n'hésitez pas à vous aider de la solution plus bas. 

Rendez-vous dans le déploiement via Github de [Gatsby](gatsby.md).

## Solution

Si vous bloquez sur l'ajout du job, voici un exemple de solution. 

<details>
  <summary>Voir une solution</summary>
  
  ````yml 
# On va utiliser un environnement avec node
image: node:12.16.1

# On déclare nos stages
stages:
  - deploy

# On créé notre stage deployment
build-and-deployment:
  stage: deploy

  # On donne un nom à notre environnement
  environment:
    name: production

  # On précise quelle branche
  only:
    - production

  # On configure git
  before_script:
    - git config --global user.email YOUR_EMAIL
    - git config --global user.name YOUR_NAME

  # On build notre administration strapi
  script:
    - npm install
    - NODE_ENV=production npm run build
    - git add --force ./build/
    - git add --force ./.cache/
    - git commit -m 'strapi admin build - Github Build'

  # On lance les scripts
  after_script:
    - npm install -g clever-tools
    - clever login --token $CLEVER_TOKEN --secret $CLEVER_SECRET
    - clever link $CLEVER_APP_ID --alias ubeers-ghost
    - clever deploy --force

  ````
</details>


 
