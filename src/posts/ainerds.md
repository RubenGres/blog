---
title: "The Waifu Enthusiasts Who Quietly Push AI Forward"
date: 2024-05-15
thumb: "waifu.jpg"
tags:
    - AI
    - research
---

For the past few weeks, the LLM community has been buzzing about [mergekit](https://github.com/arcee-ai/mergekit), a tool that enables the merging of large language models. However, [the academic paper that introduced this method](https://arxiv.org/abs/2306.01708) overlooked a crucial detail: there's another community that didn't wait for academic validation to start merging machine learning models. These pioneers were the Stable Diffusion hobbyists, particularly those congregating on [CivitAI](https://civitai.com/).

## CivitAI: NSFW model dealer and creativity catalyst

CivitAI serves as a model-sharing platform where users distribute their fine-tuned Stable Diffusion models. The site hosts models trained on specific artistic styles, photorealistic techniques, and various aesthetic preferences. What sets CivitAI apart is its permissive stance on Not Safe For Work (NSFW) content—a policy that has led to some unexpected technical breakthroughs.

Within this environment, users began creating sophisticated merged models, combining multiple existing models to achieve superior results. This grassroots experimentation proved remarkably successful. Today, the highest-performing Stable Diffusion models are typically merges of various models, including *NSFW-trained* ones.

This comes with an inherent challenge: the most capable anime models exhibit strong biases toward adult content. [Reddit user PRNGAppreciation looked into the issue](https://www.reddit.com/r/StableDiffusion/comments/12huyk4/evaluation_of_the_latent_horniness_of_the_most/), examining the "latent horniness" of popular anime-style models:

![](/blog/assets/img/evaluation-of-the-latent-horniness-of-the-most-popular.webp)

<center><b>
There is a CLEAR bias towards female character, nudity and NSFW content in all thoses models
</b></center>
<br>

I used to use AnythingV5 quite a while ago and clearly noticed the biases. I since moved to Dreamshaper4 for my go-to model when [Bernat](https://cunicode.com) introduced me to it on [https://wtfood.eu/about/](wtfood.eu), and the difference is really noticeable.


## PonyDiffusion: My Little Pony and AI Innovation

One of CivitAI's most successful models emerged from an unexpected source: the My Little Pony (MLP) fan community. PonyDiffusion, initially trained on MLP fan art, evolved into one of the platform's most popular and technically sophisticated models.

What makes PonyDiffusion particularly noteworthy is its creator's commitment to rigorous methodology and transparent documentation. The developer spent weeks manually scoring over 30,000 images to implement human preferencesimilarly to RLHF (Reinforcement Learning from Human Feedback) for language models.

The PonyDiffusion creator has been vocal about the challenges of working outside traditional research frameworks. In a notable blog post, they expressed frustration with dismissive attitudes from established companies:

> "At the heart of the issue, they appear to dismiss Pony as merely a (perhaps low-effort) niche-focused fine-tune, and they seem uninterested in my technical efforts."

This dynamic highlights a recurring theme: unconventional innovative communities often faces skepticism from established institutions, despite demonstrable technical merit.

## The DeepFake Origin Story

The term "deepfake" is now ubiquitous, but its origins trace back to a single Reddit user. In 2017, a user named "deepfakes" began posting manipulated videos of celebrities, accompanied by detailed technical explanations of their methodology. This represented one of the first accessible implementations of face-swapping technology for general users.

The academic community responded quickly, with researchers citing the Reddit posts in subsequent papers. This led to rapid iteration and improvement of the technique, eventually resulting in user-friendly tools that democratized deepfake creation.

The technology's trajectory from Reddit proof-of-concept to Hollywood production tool illustrates how grassroots innovation can reshape entire industries. Major studios now use similar techniques for digital resurrection of deceased actors and de-aging effects. The broader implications—including the commodification of actors' likenesses—continue to unfold.

## "Anime Research" Pioneer Work

### Super-Resolution Anime Waifus 

In 2015, the most effective image upscaling algorithm didn't emerge from Google, Microsoft, or other tech giants. Instead, [Waifu2x](https://www.waifu2x.net/) was developed by a relatively unknown Japanese Kaggle competitor, motivated by the desire to enhance anime artwork quality.

The name itself, "waifu" is internet slang for anime characters, giing us come clues on the developer motivation. Despite its unconventional origins, Waifu2x represented a significant advance in super-resolution techniques and influenced subsequent research in the field.

### r/AnimeResearch ...Specialized Applications

The [r/AnimeResearch]((https://www.reddit.com/r/AnimeResearch/) subreddit, with approximately 4,000 members, focuses on machine learning for anime. While relatively small, this community has produced notable technical contributions.

Their most significant project, [DeepCreamPy](https://github.com/Deepshift/DeepCreamPy) and [HentAI](https://github.com/natethegreate/hent-AI), uses inpainting for anime... uncensoring.
The community has also developed specialized tools like ["This Anime Does Not Exist"](https://thisanimedoesnotexist.ai/), a parody of [thispersondoesnotexist](https://thispersondoesnotexist.com/) focused on anime girls. After years of focus on GANs, they recently moved to diffusion models and soon enough many lewds models appeared in the wild.

## Implications for Academic Research
These communities demonstrate important patterns for academic researchers to consider. Their ability to **iterate rapidly** allows them to find promising directions before formal research catches up. **Deep domain expertise** in specific content types enables them to spot subtle quality improvements that generalist researchers often miss. Direct user engagement drives **practical innovations** that address real usage patterns rather than theoretical benchmarks. Operating with limited resources, they develop **efficient techniques** that scale well to broader applications, showing how constraints can foster unexpected innovation.

## Final thoughts

While these communities may have unconventional motivations, their technical contributions are huge. The academic world would benefit from engaging more seriously with these alternative research groups.

Once again, they prove to us that the most powerful driver of innovation isn't always noble but can come from skilled individuals wanting to bring their waifus to life.