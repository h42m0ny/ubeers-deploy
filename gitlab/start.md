# Avant d'aller plus loin

L'objectif de cette partie est d'automatiser le déploiement de nos applications sur Clever Cloud en utilisant les pipelines de Gitlab. 

Mais avant de rentrer dans le vif du sujet, prenons le temps de faire quelques étapes.

## Création du repository 

Dans un premier temps, il nous faut créer un repository sur Gitlab pour chaque application, relier notre repository local et pousser notre code. 

Je vous recommande de nommer le remote `gitlab`, pour plus de clareté pour la suite.

`git add remote gitlab uri_repo`.

Il faut suffit alors de pousser votre code.

Dans le même esprit, je vous recommande de créer une branche `production` sur chaque application qui nous servira par la suite de branche pour le déploiement de nos applications. 

## Découverte des pipelines de Gitlab

À l'image de Github, Gitlab propose aussi un outil de CI/CD directement intégrée à la plateforme. Pour information, l'intégration du CI/CD nativement par Gitlab est très ancienne puisque Gitlab contrairement à Github. 

La logique d'une pipeline Gitlab est plus ou moins similaire à celle de Github. Il y a des différences assez importantes, mais globalement c'est la même logique. 

L'avantage, c'est que nous allons pouvoir "adapter" nos fichiers de déploiement spécifiques à Github pour Gitlab. On remarquera plus facilement les différences.

En attendant de voir par la pratique une pipeline, n'hésitez pas à consulter la [documentation](https://docs.gitlab.com/ee/ci/pipelines/).

## Et après ? 

Une fois que vous regarder la documentation, vous pouvez passer au premier déploiement depuis Gitlab, à savoir [Ghost](/gitlab/ghost.md)

