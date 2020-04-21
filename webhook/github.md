# Les Webhooks avec Github

A travers le CI/CD de Github, il est possible d'exécuter à distance une action de Workflow. En l'occurence ici, nous avons besoin de déclencher les jobs `Build` et `Deploy` qu'on a créé dans les étapes précédentes.

Avant d'aller plus loin, je vous conseille de lire la [documentation](https://help.github.com/en/actions/reference/events-that-trigger-workflows#external-events-repository_dispatch) de Github sur les événements qui déclenche les jobs.

À noter que vous pouvez également voir que l'on peut faire aussi plannifier des workflows avec le format [CRON](https://crontab.guru/). Par exemple, si vous souhaitez que l'applicaiton soit builder tous les jours en début de journée de afin de garantir une livraison continue de votre application, l'évènement `schedule` est celui qui correspond à ce besoin.

Pour autant, celui qui nous intéresse n'est pas celui ci mais le `repository_dispatch` qui nous permet de lancer sur l'api de Github une requête `POST` avec un payload déclenchant cet événement.

Sur la documentation, il est aussi marqué qu'on ne peut pas exécuter cet événement sur une branche autre que la `master`. Lors de la création du fichier correspondant, pensez à faire un checkout sur la production ;)

## Création du Github Token

La création d'un Github Token est indispensable pour pouvoir utilsier l'API de Github afin de faire des actions sur votre compte et vos repos.

Vous pouvez créer ce token dans la partie `Settings` de votre compte et non du repository car il est associé au compte. Lorsque vous créer un token, sélectionnez seulement le scope `repo` conformément à la [documentation](https://developer.github.com/v3/repos/#create-a-repository-dispatch-event) de l'API.

![Image](/images/github-token.png)

## Et après ?

Maintenant que nous avons notre GITHUB_TOKEN, il est temps de faire notre fichier de configuration sur l'événement `repository_dispatch`. Rendez-vous sur [Gatsby](gatsby.md).
