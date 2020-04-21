# Déploiement de Ghost à partir de Gitlab

L'objectif de cette partie est de découvrir les pipelines de Gitlab pour qu'à chaque commit sur une branche spécifique, un déploiement soit déclenché sur Clever Cloud. 

## Variables d'environnement

De la même manière que pour les déploiements à travers Github, il faut que enregistrer nos variables d'environnement dans Gitlab. Pour ce faire, vous pouvez allez dans `Paramètres > Intégration et Livraison Continues -> Variables`

![Image](/images/secrets-gitlab.png)

Lors de l'ajout, n'oubliez pas de masquer vos variables ;)

Je vous encourage à regarder également la [documentation](https://gitlab.com/help/ci/variables/README) sur les variables pour bien les utiliser dans votre pipeline après.


## Création de notre Pipeline

Pour créer une pipeline, il suffit d'avoir un fichier `gitlab-ci.yml` à la racine du repository. C'est dans celui-là, qu'on va détailler le fonctionnement de notre pipeline. 

Avant de commencer à l'écrire, répondez à quelques questions : 

- *Quelle machine pour l'environnement ?*
- *Quelle événement qui déclenche le script ?*
- *Quelle branche est concernée ?* 
- *Quels sont les jobs à faire ? Les étapes à l'intérieur ? Et les commandes à effectuer ?*

Cela permet de commencer l'écriture du script en se posant les bonnes questions ;) 

À noter que le cheminement est sensiblement le même que pour Github.

Pour tester votre déploiement, il vous suffit de faire un commit sur la branche en question. Dans la partie `Intégration et livraison continue > Pipelines` sur Gitlab, vous verrez ainsi votre pipeline s'éxecuter. Vous pouvez aussi voir les logs. 

![Image](/images/pipeline-1.png)

Pour vous aider, utilisez la [documentation](https://gitlab.com/help/ci/README.md). 

## Et après ? 

Si votre déploiement a bien été fait sur Clever Cloud, félicitations ! Sinon n'hésitez pas à vous aider de la solution plus bas. 

Rendez-vous dans le déploiement via Gitlab de [Strapi](strapi.md).

## Solution

Si vous bloquez sur le premier script, voici un exemple de solution. 

<details>
  <summary>Voir une solution</summary>
  
  ````yml 
# On va utiliser un environnement avec node
image: node:12.16.1

# On déclare nos stages
stages:
  - deploy

# On créé notre stage deployment
deployment:
  stage: deploy

  # On donne un nom à notre environnement
  environment:
    name: production

  # On précise quelle branche
  only:
    - production

  # Installation de Clever Tools et déploiement
  script: |
    npm install -g clever-tools
    clever login --token $CLEVER_TOKEN --secret $CLEVER_SECRET
    clever link $CLEVER_APP_ID --alias ubeers-ghost
    clever deploy --force

  ````
</details>

