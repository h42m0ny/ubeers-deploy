# Création du fichier de déploiement dans Gatsby

Dans `ubeers-gatsby` sur la branche `master`, si vous n'avez pas de dossier `.github`/`workflow` créez les. Enfin ajouter un fichier `main.yml`.

Dans celui-ci, il faudra reprendre ce qu'on a fait dans le déploiement de Gatsby depuis `Github`. Mais l'adapter à l'événement `repository_dispatch`.

Pour tester votre fichier, je vous conseille d'utiliser Postman afin de créer une requête POST sur l'api de Github. À noter qu'en retour vous aurez le statut `204` mais pas de payload ;) Vous pouvez alors voir sur Github dans la partie Actions le déroulé de votre configuration.

Aidez-vous de la [documentation](https://developer.github.com/v3/repos/#create-a-repository-dispatch-event) de l'API pour avoir l'URI et configurer les headers de votre requête (Authorization, Accept)

## Et après ?

Si votre déploiement avec le build a bien été fait sur Github vers Clever Cloud, félicitations ! Sinon n'hésitez pas à vous aider de la solution plus bas.

Maintenant que nous avons fait la partie la plus difficile, nous allons modifier notre application `ubeers-deploy` pour accepter les webhooks. Rendez-vous sur cette [page](webhooks.md).

## Une solution

<details>
  <summary>Voir une solution</summary>
  <p>Dans le dossier `images`, sur le fichier `postman.png`, vous avez une capture d'écran de la configuration de la requête sur Github. Je ne pouvais pas l'insérer ici. </p>
  
  ````yml 
name: Restart

# Sur un événement "push" sur la branche production

on: repository_dispatch

# Création de notre job deploy

jobs:
build: # On sélectionne un ubuntu
runs-on: ubuntu-latest
env:
GHOST_URI: ${{ secrets.GHOST_URI }}
      GHOST_TOKEN: ${{ secrets.GHOST_TOKEN }}
STRAPI_URI: \${{ secrets.STRAPI_URI }}

    steps:
      # On utilise une action de Github pour récupérer le repo local
      - uses: actions/checkout@v2

      # Utilisation de notre version de node
      - name: Use Node.js 12.16.1
        uses: actions/setup-node@v1
        with:
          node-version: 12.16.1

      - name: Build Gatsby
        run: |
          git fetch --prune --unshallow
          git checkout production
          npm install
          npm run build

      # Création d'un artifacts
      - name: Archive build artifacts
        uses: actions/upload-artifact@v1
        with:
          name: public
          path: ./public/

deploy:
needs: [build] # On sélectionne un ubuntu
runs-on: ubuntu-latest

    steps:
      # On utilise une action de Github pour récupérer le repo local
      - uses: actions/checkout@v2

      # Utilisation de notre version de node
      - name: Use Node.js 12.16.1
        uses: actions/setup-node@v1
        with:
          node-version: 12.16.1

      # Récupération du build
      - uses: actions/download-artifact@master
        with:
          name: public
          path: ./public/

      # Récupération de l'ensemble des commits du repository. Par défaut, le clone ne récupére pas un historique profond. Or pour Clever Cloud, c'est indispensable
      - name: Run git commands
        run: |
          git fetch --prune --unshallow
          git checkout production
          git config --global user.email cois.tony@gmail.com
          git config --global user.name TonyCois
          git add --force ./public/
          git commit -m 'gatsby build prepare to deploy'

      # Installation de clever-tools and deploy app
      - name: Instal and Deploy to Clever
        run: |
          npm install -g clever-tools
          clever login --token ${{ secrets.CLEVER_TOKEN }} --secret ${{ secrets.CLEVER_SECRET }}
          clever link ${{ secrets.CLEVER_APP_ID }} --alias ubeers-strapi
          clever deploy --branch production --force

```
</details>
```
