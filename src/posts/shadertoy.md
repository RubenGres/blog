---
title: "How to Paint Using High School Maths and a GPU"
date: 2024-01-21
thumb: "shadertoy.jpg"
tags:
    - art
    - math
---

In middle school I used to play Minecraft on an old pc with the lowest possible settings. I could barely reach 15 FPS and dreamt of installing those "shaders" I kept seeing everywhere. They promised to turn the pixelated game into a shiny and glittering experience. Then one day I installed one of them and it turned my game into a literal slideshow...  but it was so pretty!

Later when I was learning Blender I stumbled on shaders again, for the first time outside of Minecraft. I played around in the shader graph - Blender's no code shader system - but learning to write shaders by hand seemed like a daunting task. It turns out "shaders" are not only present in Minecraft but are in every video game I ever played. There are different types of shaders for different purposes, but in this article I will only focus on pixel shader, also known as *fragment shader*. In a fragment shader the code describe every pixel color from its coordinates and runs for every pixel on every frame. Fragment shaders are the last step in the video game processing pipeline. 

![xlarge](/blog/assets/img/minecraft_shader.jpg)

When I stumbled on [this video by Kishimisu](https://www.youtube.com/watch?v=f4s1h2YETNY) on fragment shader coding and I was floored with how much you could do with what is essentially just a big formula for every pixel. In this way shader coding (in this case GLSL - OpenGL Shader Language) is the intersection of math, art and programming I never knew I wanted. The possibilities are pretty much endless and it is a fantastic way to learn and apply new math concepts.

In the end what I thought to be an almost impossible task is pretty doable once you understand the basics. What differs most from traditional programming is the GPU pixel space paradigm. Those calculations are done for every pixel of the screen for every frame. The complexity does not come from the language itself but from the mathematical implications of this approach.

But beyond the technical aspects, it's the community that really stuck with me. The tutorial made use of the website [shadertoy](https://www.shadertoy.com/) and exploring it blew my mind to pieces. The demos people were coming up with were unbelievable: from [animations](https://www.shadertoy.com/view/lcXSDM), [fractals](https://www.shadertoy.com/view/4ds3zn), [2D](https://www.shadertoy.com/view/ssjyWc), [3D](https://www.shadertoy.com/view/XcXXzS) and even [4D](https://www.shadertoy.com/view/WtB3Wt). Some people push the technique to it's limit by creating complex scenes with [only a few lines of code](https://www.shadertoy.com/view/DlySDD) or [integrate full on machine learning models](https://www.shadertoy.com/view/msVXWD).

![xlarge](/blog/assets/img/fractal.png)

The code for every shader is open, and you can really simply start changing variables or fork it to make your own version. Most often than not, the creator leaves behind a few comments so you can try to understand how it's made. Every art piece is also an opportunity to learn something new.

What I find most interesting about this small online niche is that there is not that many people active on the site. After a while, you start recognizing names and you can even guess who wrote what shader just by looking at it. It's a community of passionate people that are contributing to the website only for the sake of technical achievement and bragging rights. There is no recommendation algorithm, no ads and no way to make money. The feeling of community around this obscure art form is refreshing; I don't know anything about these people but in a way I feel like I get to know them from how they express themselves through this limited language.

At the moment I didn't create a lot of shaders on there but I wanted to take the time to write about my new found obsession. If you want to take a look at my small creations you can find my [profile here](https://www.shadertoy.com/user/retronyme). If you never picked up shader coding I'm convinced shadertoy is a great way to start toying around and be part of a supportive community!

I'll leave you with a picture of a cat passed through my RGB game of life shader for good measure.
![large](/blog/assets/img/cat.png)
