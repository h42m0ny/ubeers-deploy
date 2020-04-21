# Avant d'aller plus loin

L'objectif de cette partie est d'automatiser le déploiement de nos applications sur Clever Cloud en utilisant les Github Actions. 

Mais avant de rentrer dans le vif du sujet, nous allons faire quelques étapes communes à nos applications et voir au passage quelques outils que nous utiliserons à partir de maintenant.

## Création du repository 

Dans un premier temps, il nous faut créer un repository sur Github pour chaque application, relier notre repository local et pousser notre code. 

Je vous recommande de nommer le remote `github`, pour plus de clareté pour la suite.

`git add remote github uri_repo`.

Il faut suffit alors de pousser votre code.

Dans le même esprit, je vous recommande de créer une branche `production` sur chaque application qui nous servira par la suite de branche pour le déploiement de nos applications. 

## Découverte de Clever Tools

Par la suite, nous utiliserons un package fourni par l'équipe de Clever Cloud pour déployer nos applications : [clever-tools](https://github.com/CleverCloud/clever-tools).

Il s'agit d'un package pour gérer à distance l'ensemble de notre compte Clever Cloud. Ainsi via la ligne de commande, nous pourrons déployer/redémarrer nos applications. 

Pour installer Clever Tools sur votre machine, le moyen le plus simple est de passer par NPM.

`npm install -g clever-tools`

Ensuite, faites un `clever login` dans votre terminal et connectez vous à votre compte Clever Cloud. Vous obtiendrez alors un `CLEVER_SECRET` et un `CLEVER_TOKEN` qui vont nous faciliter les déploiements par la suite dans le CI/CD de Github.

Mais en attendant, n'hésitez pas à découvrir l'outil. Essayez par exemple de rédéployer une de vos applications depuis le terminal avec la commande `clever deploy`. Bien entendu, il faut avoir relier votre application avant ;) 


## Découverte de Github Actions

Grâce aux Workflows de Github, il est possible de mettre en place un CI/CD facilement par le biais d'Actions développées soit même ou par la communauté.

Dans le cadre de cette découverte, nous n'allons pas ou très peu utilisé ceux de la commuanuté. L'objectif étant de pratiquer et de comprendre la mécanique ;)

En attendant de les voir par la pratique, n'hésitez pas à consulter le site de [Github Actions](https://fr.github.com/features/actions) et sa [documentation](https://help.github.com/en/actions) notamment la partie sur la syntaxe d'un [Workflow](https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions).  

## Et après ? 

Une fois que vous regarder la documentation, vous pouvez passer au premier déploiement depuis Github, à savoir [Ghost](/github/ghost.md)

