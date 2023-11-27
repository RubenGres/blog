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

From initial testing, the bot is surprisingly accurate in its answers. The main issue is that it doesn’t always gather the most useful documents and the information its looking for may exist in another article. I talked about this project to friends and the reception was overwhelming positive. I already have people that have started using it in their daily life.

Whenever I have a question related to the law, I'm now doing a quick gouvx search as a starting point.

When compared to ChatGPT, for the same question gouvx will be able to answer your question almost every time, be more precise and most importantly redirect you to the official page on [service-public.fr](http://service-public.fr)

# Advantages and limitations of this approach

The main advantage of this method is that I am sure that I won’t hallucinate information, in the worst case scenario I fail to collect the relevant document and gouvx doesn’t answer the question.

Sometimes though, it doesn't grab the right article and answers with false data...

Another limitation is that I’m not keeping up to date with the latest modifications on the website since I collect the data on a given day. I could have used a search query and scrape the first results but it would have taken more time to reply to the user (and admittedly I really wanted to try out weaviate)

From this method I can use any LLM without having to finetune it. This allows me to switch models easily to keep up the current rate of new models.

## The part where I get technical

**Everything is serverless and scalable**, here are some technical details:

- **Hosting:**
I’m using GitHub pages to host the website, it's free as long as the page is static.

- **API:**
A GCP Cloud function that doesn't run when no one needs it.

- **Embedding:**
I use HuggingFace’s inference endpoints for the embeddings. It's really simple to set up but the model can take a bit to load at first. In the future I might set up a small CPU embedding service
 
- **Database:** Hosted on Weaviate Cloud, I'm using their free tier sandbox one since I can't afford the 25€/month... but hey it works!

- **LLM:** ChatGPT! There is other models out there that could do the trick but ChatGPT is cheap is simple to use.

This cloud first design scale very easily (within limits of my credit card) and if no one uses the service I’m not paying a penny!

# Some thoughts on democratic uses of LLM

I am certain that we are going through a revolution in the way we interact with machines. ChatGPT has been out for less than a year and a lot of people have already integrated in their daily workflow. This technology empowers everyone that can get their hands on it and re-shuffle the cards by letting everyone have an expert by their side at all times.

Now the biggest challenge we face is trying to keep greedy corporate interest away and build a more open, fairer future where this technology can help lower the bar for digital literacy.

With this project I not only wanted to help people navigate the french law but I also wanted to prove there is a place for democratic uses for this technology to make more informed decisions.

# How to contribute to the project

![xxlarge](/blog/assets/img/end.png)

The whole codebase is opensource and you can find it [here on GitHub](https://github.com/gouvx). If you want to participate feel free to make a pull request or create an issue. Right now we need to scrape, embed and include more government websites.

If this project sounds interesting and you want to contact me, you can send me a mail at *ruben.gres@proton.me* and I will gladly reply !

À un futur avec des IA plus utiles 🤖
