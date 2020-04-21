# Avant d'aller plus loin

À la différence d'un CMS comme Wordpress où la modification/ajout d'un article se fait de manière immédiate, il va falloir implémenter un redéploiement de notre application Gatsby si une mise à jour est faite sur l'un des providers de contenu : Ghost ou Strapi.

Pour être au courrant de ces mises à jour, nous allons faire communiquer les Webhooks de Ghost et Strapi avec ceux de Github/Gitlab afin de faire un build et un déploiement.

## Architecture

Pour pouvoir faire communiquer les Webhooks entre eux, nous allons devoir mettre en place une application Node qui recevra en amont les Webhooks entrant de Ghost/Strapi et qui se chargera de déclencher via l'api de Github/Gitlab le build et le déploiement de Gatsby.

Voici sur un schéma du fonctionnement :

![Image](/images/archi_reload_content.png)

## Un petit mot sur Netlify

Si vous recherchez des articles sur la JAM Stack, vous tomberez nécessairement sur Netlify qui simplifie toute cette procédure pour faciliter le développement de site sous la JAM Stack.

Nous en avions parlé dans la partie cours sur le [retour d'expérience](https://www.netlify.com/blog/2017/03/16/smashing-magazine-just-got-10x-faster/) de Smashing Magazine avec Netlify. Une [vidéo](https://vimeo.com/163522126) de l'équipe était d'ailleurs intéressante à regarder pour comprendre la philosophie derrière la JAM.

A juste titre, il y a beaucoup d'articles sur Netlify lorsqu'on recherche des informations sur la JAM, et en particulier autour de Gatsby.

Pour autant, nous avons choisi de baser entièrement notre stack sur Clever Cloud pour différentes raisons : en France, support hyper réactif, expérience de déploiements, les Clever Tools, ...

Et comme nous allons le voir par la suite, cela ne va posera aucun souci, bien au contraire ;)

## Et après ?

On va créer notre application Deploy sur Clever Cloud et jouer avec les Webhooks. Rendez-vous sur la partie [Deploy](deploy.md).
