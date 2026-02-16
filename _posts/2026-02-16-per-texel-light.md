---
title: 'Conceptual and technical Per Texel Light (Incomplete)'
date: 2026-02-16
permalink: /posts/2026/02/Per-Texel-Light/
tags:
  - conceptual
  - technical
---
Do you want to emulate older 3D games but with a new touch? This might interest you.

expected completion: mid-late feb

We will be conceptually and technically breaking down the process of the lighting in this video.
{% include youtube.html id="Uief5d7zBRc" %}

Conceptual
====
-----
Lighting is pretty cool. Cavemen were pretty interested by it, and my great great great great great great great great great great great great great great great great great great great great great great great great great great great great great great grandpa was a caveman, so it makes sense that I would be interested by it too. 

Luckily, conceptually understanding lighting isn't too hard. The math can get a little wonky, but I think we'll be fine. Although if at any point you are feeling cranky or like crying, that is ok. 

The next few bits are going to be about how my lighting works. 

Lit
====

So here's our scene. For the sake of simplicity, it will be a pixelated 2D scene. Everything black is geometry, and the red is a light. 

<img src="\images\lightScene\Scene.png">

Now let's make things lit. To be clear, *not* shadowed, just *lit*. That will make sense in a second.

<img src="\images\lightScene\Lit.png">

Notice how neither the crescent object or circle object block light from reaching the wavy ground, nor does the wavy ground block the deep parts of the crevices where light shouldn't reach. Again, the scene is not shadowed, just lit. So how is a surface lit or unlit? A surface being lit is based on the angle, [***A***] from ***the surface*** to the ***direction of the light towards the surface***. 

Here is one example of ***A*** at 90 degrees, where the surface and the direction of the light to the surface are perpendicular, and where the surface would be most well lit. 

<img src="\images\lightScene\Perp.png">

Here is one example of ***A*** near 180 degrees, where the surface and the direction of the light to the surface are nearing parallel, and where the surface would become dimly lit. 

<img src="\images\lightScene\Near180.png">

Here is one example of ***A*** past 180 degrees, where the surface and the direction of the light to the surface are past parallel and opposite, and where the surface would become unlit. To reiterate, the surface is unlit because of its angle to the light, not because anything is blocking the light from reaching it. 

<img src="\images\lightScene\Past180.png">

So that is a conceptual way of how my lighting makes a scene lit, but just in 2D. It's actually pretty common to do lighting this way. Pay attention next time you play a game. You'll see all sorts of lit things where they ought not to be lit! 

Shadowed
====

Dynamic Blurred
====


Technical
=====
-----