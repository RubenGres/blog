---
title: "What academics owe to horny nerds online"
date: 2024-05-15
thumb: "waifu.jpg"
tags:
    - AI
    - research
---

For the past few months the LLM community are excited with the advent of [mergekit](https://github.com/arcee-ai/mergekit) that allow the merging of LLM. [The paper that introduced this method for LLM](https://arxiv.org/abs/2306.01708) failed to mention that there is another – albeit non academic – community that did not wait for this paper to merge models: StableDiffusion finetuning hobbyists, specifically on the website [CivitAI](https://civitai.com/).

Most researchers never took interest in theses obscure communities even though they play a major role in pushing the research and getting people interesed in machine learning. I think the academic world could greatly benefit from keeping an eye on what thoses people are up to. This article is a tribute to theses ethically questionnable communities.

# CivitAI

CivitAI is a model sharing website that allow users to share their finetuned model of StableDiffusion. This way you can find models trained on specific artist styles or optimized to have very realistic results. The website ALLOWS for Not Safe For Work (NSFW) content, so if you decide to take a look for yourself be prepared to encounter many naked, big brested anime girls. Amongst this turmoil, some users decided to create the ultimate hentai model by fusing multiple existing models. Sounds familiar?

Their task turned out to be so successful that at the time of writing this article, the stable diffusion model that yields the best results for anime style is a merge of different models, including NSFW. Now we face a very strange problem where all the models that we ave are heavily biased towards nudity, which you may consider a bug or a feature depending on what you are working on. [PRNGAppreciation looked into this issue](https://www.reddit.com/r/StableDiffusion/comments/12huyk4/evaluation_of_the_latent_horniness_of_the_most/) and compared *latent hornyness* of popular anime style models:

![](/blog/assets/img/evaluation-of-the-latent-horniness-of-the-most-popular.webp)

<center><b>
There is a CLEAR bias towards female character, nudity and NSFW content in all thoses models
</b></center>

# Reddit user "DeepFake"

Nowadays the term deepfake is very common and understood by all, but do you know where that originated from? In 2015 videos of celebrities in very explicit scene started appearing on reddit, posted by an user called "DeepFakes" with no previous post history. People quickly realized that the videos were fake, and the reddit user gave details about the method they used. If you are curious you can find the original post on archive.org **(NSFW, obsviously)**

The first papers reproducing this method came soon after and directly cited the reddit user as a source. From there a long list of papers reproducing and improving the method came one after the other and soon arrived are tools that allowed you to create deepfakes really easily with no technical skills. We will not dive into ethical considerations for letting this kind of tool be widespread on the internet, but there is definitely a discussion to be had.

This technique is now so widespread that Hollywood took interest into it, in the last Star Wars movies, the studio used deepfakes to let the late Peter Cushing and Carrie Fisher be on screen despite not being in the *alive club*. Now actors are [selling their image](https://www.theinformation.com/articles/dont-put-your-head-in-the-sand-stars-are-quietly-inking-deals-to-license-their-ai-doubles) to train AI to create a virtual clone of themselves instead of actually making the movie.

In the end, this user with questionable ethics started a whole new field of research from their technique and coined the term deepfake that is broadly used today.


# Anime research

## Waifu2x

In 2015 the best upscaling algorithm was not created by Google, Microsoft or any other GAFAM. It called Waifu2x and the logo was a cute anime girl with blue hair. If you're not familiar with internet lingo, a waifu is a girl from a manga or anime that is idealized by fans who are attracted to her. Some people develop relationships with their waifu.

On the github page of the project, you can learn that waifu2x is inspired by SRCNN with a reference to a research paper by a japanese university ```(#TODO check the nationality)```

https://fedoramagazine.org/better-image-upscaling-waifu2x/

I remember at the time waifu2x was the reference for upscaling images. For this article I did some investagating and searched who was being this project.

github: nagadomi

```#TODO complete this```

## reddit communities

<!---
#TODO complete this talking about:
https://www.thiswaifudoesnotexist.net
DeepCreamPy: censorship removal
https://www.reddit.com/r/AnimeResearch

they've been following diffusion models since the beginning and moved from stylegan quickly
--->

Anime research is a subreddit that focuses on machine learning applied to anime.
There is currently almost four thousand people following the page, so it is a relatively small community, their main goal is to create decensoring model to unblur genitals in hentais.
There is also a subreddit dedicated 

While technically not NSFW, I would not recommend clicking that link if you are currently in any kind of open office. You've been warned.


# Final thoughts

In conclusion, while these ethically questionable communities may not always have the best intentions, their contributions to the field of machine learning and AI are undeniable. The academic world could greatly benefit from keeping an eye on what these communities are up to, as they often push the boundaries of research and innovation in unexpected ways.
