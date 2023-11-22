---
title: "I hate paperwork so I built an AI to help"
date: 2023-11-21
thumb: "gouvx.png"
tags:
    - civictech
    - AI
    - LLM
---

TL;DR: I built [GouvX](https://gouvx.fr), a chatbot to search service-public.fr

# Where the idea came from

I’ve always dreaded working with the administration, just thinking about it makes me sweaty and nauseous. One day while I was procrastinating another administrative task, I got the idea to build an assistant that could look up the internet for me and help me go through this mess. So instead of filling paperwork, I spent weeks building an AI to help with my administrative anxiety.  

And just like that the [GouvX assistant](https://gouvx.fr), was born!

![xlarge](/assets/img/cyborg_marianne.png)

# Some context

Recently I’ve read about "g0v.tw" (read gov zero), the Taiwan initiative for a more decentralized and open government. The g0v movement was started in a hackaton in 2014, it leverages open source contribution and collective intelligence to help the governement make more informed decisions (do yourself a favor and look up [Audrey Tang](https://en.wikipedia.org/wiki/Audrey_Tang)).

From there I fell into a rabbit hole of civic tech and open governments initiatives that led me to projects like [Decidim](https://decidim.org) and [DemocracyOS](http://democracyos.eu).

Inspired by thoses movement, I figured we could use some of this energy here in France and tried to claim the [g0uv.fr](http://g0uv.fr) domain. But it was already taken... (If it's yours, I hope you'll make good use of it). After some consideration I settled for [gouvx.fr](http://gouvx.fr). The idea is that you can add an “x” to any existing government website to be redirected to the corresponding page.

It's not as techy as g0uv and it sounds a bit like an erotic website. (Could have been worse, at least it's not called x.com) 

I wanted the first project for gouvx to be an assistant and, since I have some kind of administrative phobia, the idea was to delegate to it the weight of scrolling through government websites.

And hopefully get rid of administrative paralysis along the way…

![large](/assets/img/dreading.png)

# Gathering the data

I started the project by crawling and scraping [service-public.fr](http://service-public.fr), the central portal for public services.  

To better picture what this part does, imagine a small spider going around the website, following links and hoarding text content as they go. When the spider visited all the pages and has no where else to go, I am left with a huge pile of HTML pages.

![large](/assets/img/spider.png)

To make the text a bit more compact while keeping the structure, I made my own scraper specially for service-public that would convert the HTML to markdown. The pages mostly followed the same layout so this part was pretty straight forward

# Make the computer understand the text

This is the part where we try to speak to computers 🦜

I can't pass text directly to my AI models, they are computer programs after all, they only speak in numbers. This is where text embedding comes in!

A text embedding is a n-dimensional vector that capture the semantic sense of a sentence. This means that when two words are close together in meaning, their vectors are close in embedding space.

To help clear things out, take a look at this example. Here the embeddings are represented in two dimensions but in reality we are working with more than a thousand. Notice how you can make out “clusters” of words of the same category.

![none](/assets/img/embeds.png)

Since we are processing text in french, I used a specialized model called **sentence-camembert-large** that was created to process french texts. The input size of the model was 512 tokens (or around 300 words) so I had to split my articles to fit within this context window.

Each element is therefore a chapter in a bigger article. I computed the embedding of every one of theses and got with around 100k unique data points. Picture the above figure with entire articles instead of words, and a hundred thousand of them.

# Storing data with LLM in mind

Now that I have my gazillion points, I need to throw them in a database for efficient querying.

I’ve always been in terrified of the sheer amount of database paradigms there is out there (hierarchical, network, object oriented, NoSQL, relational, ...) With the rise of LLM based applications, another type of databases is bringing attention to itself: *vector-based databases*.

The main principle here is that every entry in your database has an associated vector and you can efficiently query data based on its spatial representation. For this project I went with weaviate because it’s open source and has a built in cloud service.

Whenever I want to gather documents that may answer the user question, I compute the question's embedding and return the three closest points. This should (hopefully) be all the context we need to answer the question.

A limitation of this approach is that by embedding the question directly I'm also including "parasites" tokens (ponctuation, greetings, etc.). But from my tests it was good enough!

# The fun part: building the prompt

Now that’s where the magic happens, we are going to give orders to our model.

The principle here is to build a prompt from instructions of what we expect as a result, a series of document gathered from our database and finally the user question.

The instructions that we’re giving to our model look something like this (pardon my French):

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

And… that’s it! Now we just have to let the LLM do its magic, and **GouvX** is born !

![large](/assets/img/gouvx_bot.png)

# So… how good is it ?

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

- **LLM:** ChatGPT! There is other models out there that could do the trickk but ChatGPT is cheap is simple to use.

This cloud first design scale very easily (within limits of my credit card) and if no one uses the service I’m not paying a penny!

# Some thoughts on democratic uses of LLM

I am certain that we are going through a revolution in the way we interact with machines. ChatGPT has been out for less than a year and a lot of people have already integrated in their daily workflow. This technology empowers everyone that can get their hands on it and re-shuffle the cards by letting everyone have an expert by their side at all times.

Now the biggest challenge we face is trying to keep greedy corporate interest away and build a more open, fairer future where this technology can help lower the bar for digital literacy.

With this project I not only wanted to help people navigate the french law but I also wanted to prove there is a place for democratic uses for this technology to make more informed decisions.

# How to contribute to the project

![xxlarge](/assets/img/end.png)

The whole codebase is opensource and you can find it [here on GitHub](https://github.com/gouvx). If you want to participate feel free to make a pull request or create an issue. Right now we need to scrape, embed and include more government websites.

If this project sounds interesting and you want to contact me, you can send me a mail at *ruben.gres@proton.me* and I will gladly reply !

Here's to a future with more helpful AI assistants 🤖
