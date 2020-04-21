# Déploiement de Ghost à partir de Github

L'objectif de cette partie est de découvrir les Actions de Github pour qu'à chaque commit sur une branche spécifique, un déploiement soit déclenché sur Clever Cloud. 

## Variables d'environnement

L'objectif ici est de déployer via les Clever Tools de la même manière que via votre ligne de commande. Une question vous taraude peux être, quid du login ? Nous n'allons pas ouvrir le navigateur à chaque fois pour déployer ;)  

Nous avons généré un peu plus tôt, un `CLEVER_TOKEN` et un `CLEVER_SECRET`. Pour pouvoir se connecter à Clever à travers ces informations, il suffit d'utiliser cette commande : 

`clever login --token TOKEN --secret SECRET`

Mais comme nous ne voulons pas que ces informations soient en clair dans notre environnement de CI/CD, nous allons donc utiliser des variables d'environnement. 

Pour les créer sur Github, il suffit de vous rendre sur le repository et dans la partie `Secrets`. Vous pouvez ainsi ajouter les variables qui seront par la suite reconnu dans notre workflow de déploiement. 

![Image](/images/secrets-github.png)

Je vous encourage à regarder également la [documentation](https://help.github.com/en/actions/configuring-and-managing-workflows/using-variables-and-secrets-in-a-workflow?algolia-query=secrets) sur les secrets pour bien les utiliser dans votre workflow.


## Création de notre Workflow

Pour créer un workflow, il suffit de créer un dossier `.github` dans lequel nous allons créer un dossier `workflows` qui contiendra un fichier `main.yml`. C'est dans celui-là, qu'on va détailler notre workflow. 

Avant de commencer à l'écrire, répondez à quelques questions : 

- *Quelle machine pour l'environnement ?*
- *Quelle événement qui déclenche le script ?*
- *Quelle branche est concernée ?* 
- *Quels sont les jobs à faire ? Les étapes à l'intérieur ? Et les commandes à effectuer ?*

Cela permet de commencer l'écriture du script en se posant les bonnes questions ;) 

Pour tester votre déploiement, il vous suffit de faire un commit sur la branche en question. Dans la partie Actions sur Github, vous verrez ainsi votre workflow s'éxecuter. 

![Image](/images/deploy-github.png)

Pour vous aider, utilisez la [documentation](https://help.github.com/en/actions/). 

## Et après ? 

Si votre déploiement a bien été fait sur Clever Cloud, félicitations ! Sinon n'hésitez pas à vous aider de la solution plus bas. 

Rendez-vous dans le déploiement via Github de [Strapi](strapi.md).

## Solution

Si vous bloquez sur le premier script, voici un exemple de solution. 

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
  deploy:
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
        run: git fetch --prune --unshallow

      # Installation de clever-tools and deploy app
      - name: Instal and Deploy to Clever
        run: |
          npm install -g clever-tools
          clever login --token ${{ secrets.CLEVER_TOKEN }} --secret ${{ secrets.CLEVER_SECRET }}
          clever link ${{ secrets.CLEVER_APP_ID }} --alias ubeers-strapi
          clever deploy --branch production

  ````
</details>

