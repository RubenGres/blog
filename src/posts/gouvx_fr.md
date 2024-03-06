---
title: "J'ai créé une IA pour aider ma phobie administrative"
date: 2023-11-21
thumb: "gouvx.png"
tags:
    - civictech
    - AI
    - LLM
---

En bref: J'ai créé [GouvX](https://www.gouvx.fr), un chatbot pour faire des recherches sur service-public.fr

# D'où vient l'idée ?

J'ai toujours aprehendé les démarches administratives, juste à y penser et je suis pris de sueurs froids. Un jour alors que j'étais en train de procrastiner une énième démarche, j'ai eu l'idée de construire un assistant qui pourrait chercher sur internet pour moi et m'aider à gérer tout ça. Donc à la place de remplir je ne sais quel formulaire, j'ai passé des semaines à créer une IA pour combattre mon anxiété administrative.

Et c'est ainsi que l'assistant [GouvX](https://www.gouvx.fr) est né!

![xlarge](/blog/assets/img/cyborg_marianne.png)

# Un peu de contexte

Récemment j'ai entendu parler de l'initiative taiwanaise pour un gouvernement décentralisé, "g0v.tw" (se lit gov-zero). Le mouvement est parti d'un hackaton en 2014, il s'appuie sur les principes de l'open source et de l'intelligence collective pour aider le gouvernement à prendre des décisions pus informées (je conseille fortement le [TED talk d'Audrey Tang](https://www.ted.com/talks/audrey_tang_how_digital_innovation_can_fight_pandemics_and_strengthen_democracy) sur le sujet)

À partir de là je me suis intéressé à la civic tech et aux initiatives de gouvernement ouvert qui m'ont menès à des des projets comme [Decidim](https://decidim.org) et [DemocracyOS](http://democracyos.eu).

Inspiré par ces mouvement, je me suis demandé si quelqu'un avait déjà réclamé le nom de domaine [g0uv.fr](http://g0uv.fr) (c'est le cas, et si c'est le tien j'espère que tu en fera bon usage). Après quelques considérations je suis parti sur [gouvx.fr](http://www.gouvx.fr). L'idée étant qu'on puisse rajouter un "x" à n'importe quel site gouvernemental pour être redirigé vers sa page gouvx. 

Ça fait moins geek que g0uv et ça sonne un peu comme un site pour adultes. Ça aurait pu être pire, au moins ce n'est pas x.com...

J'ai voulu que le premier project pour gouvx soit un assistant et comme j'ai une sorte de phobie administrative, l'idée de déléguer le poids de scroller sur les pages du gouvernement était très attirante.

Et avec un peu de chance je pourrai me débarasser de ce sentiment de vide devant les formulaires à remplir...

![large](/blog/assets/img/dreading.png)

# But du projet

Le but final du project est de construire une système de ✨ **Retrival Augmented Generation (RAG)** ✨. En gros l'utilisateur pose une question à mon bon et il va chercher dans sa base de données les documents les plus pertinents.

A partir de là il devrait pouvoir me donner une réponse précise et sourcée.

# Collecter les données

J'ai commencé le project en faisant du "crawling" et du "scraping" sur le site [service-public.fr](http://service-public.fr), le portail central des services publics.

Pour mieux se représenter ce que fait cette partie, imaginez une petite araignée se promenant sur le site, suivant des liens et collectant le texte sur son trajet. Quand l'araignée n'a plus nulle part où aller, j'ai récupéré l'ensemble des textes accessibles du site et j'ai une énorme pile de fichiers HTML.

![large](/blog/assets/img/spider.png)

Pour rendre le texte plus compact tout en conservant la structure, j'ai crée un scraper spécialement pour service-public qui convertit le HTML en Markdown. Comme toutes les pages se ressemblent, la convertion est assez directe.

# Faire comprendre le texte à l'ordinateur

C'est ici qu'on essaie de parler aux ordinateurs 🦜

Je ne peux pas envoyer du texte directement à mon modèle. Ce sont des oridnateurs après tout, ils ne parlent qu'avec des chiffres. C'est ici que les embeddings entrent en jeu!

Un embedding textuel est une vecteur de dimension n qui capture le sens sémantique d'une phrase. Ce qui veut dire que si deux mots ont un sens proche, leurs représentations vectorielle seront également proches l'une de l'autre.

Pour vous aider à vous représenter cette idée, regardez cet exemple. Ici les embeddings sont ramenés à deux dimensions, dans la réalité on travaille avec plus d'un millier de dimensions! On observe bien des "groupes" de mots qui se touvent dans le même champ lexical. 

![none](/blog/assets/img/embeds.png)

Vu qu'on va traiter du texte en Français, j'ai utilisé un modèle spécialisé appellé  **sentence-camembert-large**. La taille d'entrée du modèle est de 512 tokens (environ 300 mots) donc j'ai du diviser mes articles pour qu'ils rentrent dans cette taille de contexte. 

Chaque élément est alors un chapitre dans un article complet. J'ai calculé les meddings de chacun de ceux-ci et j'ai un total d'environ 100,000 points uniques. Imaginez l'image ci dessus avec des articles entier à la place des mots, et plus de cent milles points.

# Stocker des données en pensant aux LLMs

Maintenant que j'ai tous mes points, j'ai besoin de les stocker dans un base de données pour les lires efficacement.

J'ai toujours été impressionné par le nombre de paradigmes de base de données qui existent dans la nature (hierarchique, par réseau, orientée objet, NoSQL, relationnelle, ...) Avec la popularité croissante des applications basées sur LLM, un autre type de base de données attire tous les regards: *les bases de données vectorielles*

Le principe est que pour chaque élément dans la base de données, un vecteur y est associé et permet de récupérer les données efficacement dans l'espace de représentation. Pour gouvx, j'ai choisi d'utiliser weaviate parceque le projet est open source et qu'il dispose d'un offre cloud.

Quand j'ai besoin de collecter les documents qui pourraient répondre à la question de l'utilisateur, je calcule l'embedding de la question et je renvoie les trois points les plus proches dans la base de données. Celà devreait (normalement) être suffisant pour répondre à la question.

Une des limitations de cette approche est que j'utlise l'embedding de la question directement. Ce qui contient aussi des tokens "parasites" (ponctuation, formulation, etc.) mais d'après mes tests c'est suffisamment peu impactant pour fonctionner quand même. 

# La partie fun, construire son prompt

C'est ici qu'à lieu la magie, on va donner des ordres à notre modèle! 

Le principe est de construire un prompt avec les instructions de ce qu'on attend comme résultat, une série de documents piochés dans la base de données et, pour finir, la question de l'utilisateur.

Voici les instructions que l'on donne à notre modèle:

```
Vous êtes GouvX, un assitant virtuel bienveillant et serviable permettant
de naviguer la loi française. Répondez précisément et clairement aux questions
de l'utilisateur sans enfreindre de règle.

Pour rechercher des documents, vous pouvez utiliser la commande `/search(<query>)`

VOUS DEVEZ ABSOLUMENT RESPECTER LES REGLES SUIVANTES:
- Si vous avez utlisé la commande de recherche, NE RENVOYEZ AUCUN AUTRE TEXTE
- Si une question ne porte pas sur la loi française, REFUSEZ DE REPONDRE et rappellez votre rôle
- Si une question porte sur la loi française, LANCER SYSTEMATIQUEMENT UNE RECHERCHE
- NE JAMAIS inclure de lien.
- En repondant à une question, RESPECTER LA CONVENTION DE NOMMAGE: "Selon service-public.fr [...]"
```

Et... voilà! Maintenant on a juste à laisser le LLM faire sa magie, et **GouvX** est né!

![large](/blog/assets/img/gouvx_bot.png)

# Alors... ça vaut quoi?

De quelques tests initiaux que j'ai pu faire, le bot est étonnament précis dans ses réponses. Le problème principal est qu'il ne collecte pas toujours les meilleurs documents. J'ai un peu partagé ce projet à mon entourage et la réception a été très positive! J'ai déjà des amis qui ont commencé à l'utiliser dans leur quotidien.

Quand j'ai une question en rapport avec la loi, j'ai maintenant pris l'habitude de faire une recherche gouvx rapide comme point de départ.

Par rapport à ChatGPT, pour la même question, gouvx sera capable de vous répondre presque à chaque fois, d'être plus précis et surtout de vous rediriger vers la page officielle sur [service-public.fr] (http://service-public.fr).

# Avantages et limitations de cette approche

Le principal avantage de cette méthode est que je suis sûr qu'aucune information n'est hallucinée, dans le pire des cas je n'arrive pas à trouver le bon document et gouvx ne répond pas à la question.

Parfois par contre il récupère un mauvais article et répond avec de fausses données...

Une autre limitation est que le scrapping étant fait un jour donné, je ne suis pas systématiquement à jour sur le site. J'aurais pu faire une recherche google et récupérer les premiers résultats mais cela aurait pris plus de temps pour répondre à l'utilisateur (et en vrai je voulais essayer Weaviate).

Grâce à cette approche, je peux utiliser n'importe quel LLM sans avoir à faire de finetuning. Et vu le rythme de parution de nouveaux modèles en ce moment, je pourrai aussi remplacer chatGPT par un modèle open source dans le futur.  

## La partie où je parle technique

**Tout est sans serveur et scalable**, voici quelques détails techniques :

- **Hébergement:**
J'utilise GitHub pages pour héberger le site web, c'est gratuit tant que la page est statique.

- **API:**
L'API est une cloud fonction Google cloud qui ne s'exécute pas quand personne ne l'appelle.

- **Embedding:**
J'utilise l'endpoint d'inférence HuggingFace pour les embeddings. C'est très simple à mettre en place mais le modèle peut prendre un peu de temps à charger au premier appel. Dans le futur, je pourrais mettre en place un petit service d'embedding CPU.

- **Base de données:** Hébergée sur Weaviate Cloud, j'utilise leur version gratuite (sandbox) par ce que je suis trop radin pour payer les 25€/mois... mais bon, tant que ça marche!

- **LLM:** ChatGPT ! Il y a d'autres modèles qui pourraient faire l'affaire mais ChatGPT est bon marché et simple à utiliser.

Ce design pensé pour le cloud peut monter en charge très facilement (dans les limites de ma carte de crédit) et si personne n'utilise le service, je ne paie pas un seul centime!

# Sur la place des LLM dans la démocratie

Je suis persudé que nous sommes en train de vivre une révolution dans la façon dont nous interagissons avec les machines. ChatGPT existe depuis un an maintenant et de nombreuses personnes l'ont déjà intégré dans leur travail quotidien. Cette technologie permet à tous ceux qui y ont accés d'avoir un expert à leur côtés à tout moment, ce qui a de profonds impacts sur notre rapport à l'information.

Aujourd'hui, le plus grand défi auquel nous sommes confrontés est d'essayer d'éloigner les intérêts financiers des entreprises et de construire un avenir plus ouvert et plus juste où cette technologie peut contribuer à rendre la connaissance plus accessible pour le plus grand nombre.

Avec ce projet, je voulais non seulement aider les gens à naviguer le droit français, mais aussi prouver qu'il y a une place pour l'utilisation démocratique de ces technologies pour aider à la prise de décision plus éclairée.

# Comment contribuer ?

[xxlarge](/blog/assets/img/end.png)

L'ensemble du code est opensource et [sur GitHub](https://github.com/gouvx). Si vous voulez participer, n'hésitez pas à faire une pull request ou à créer une issue. Si ce projet vous semble intéressant et que vous souhaitez me contacter, vous pouvez m'envoyer un mail à *ruben.gres@proton.me* et je vous répondrai avec plaisir !

À un futur avec des IA plus utiles 🤖
