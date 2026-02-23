---
title: 'Conceptual and technical Per Texel Light (in-progress)'
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

UV Space to World Space
=====
My lighting is a per-texel lighting; that is, lighting is calculated for every texel on an object's texture. The problem is that texels are only recorded in UV space, yet we need to know their position in world space. That way they can interact with the lights in world space. We will utilize the knowledge that the triangles which make up an object, which the texels are wrapped around, are in both world space and UV space. So, in order to get texels from UV space to World space, here's what we do:

* for every texel, [***Te***] (this is a multi-threaded process)
    * for every triangle, [***Tr***]
        1.  using a triangle-relative-position (more on this in a second) see if ***Te*** lies within ***Tr*** in UV space
        1. if it does, then multiply that triangle-relative-position on ***Tr*** in world space to find where ***Te*** lies in world space. 
        1. break the loop since we have found the correct ***Tr*** for ***Te***

Ok so now let's get into the good stuff: The concept behind the triangle-relative-position. It's known as *Barrycentric coordinates*. I wonder if the creator was named Barry. What if their name was something like Mobius. That would be something. 

Anyway, first up take a look at the two pictures below. The first picture is a triangle with a texel in World Space, and the second picture is the same triangle and texel but in UV space. \
<img src="\images\lightScene\barryCentric\WTri.png">
<img src="\images\lightScene\barryCentric\UVTri.png">

Again, we need to know the texel's position in world space. By nature of a texel, we know its position in UV space, but we do not know its position in world space (so it is highlighted in red in the world space picture above). However we do know the position of the triangle in world space, ***and*** UV space. So if we can find the position of the texel relative ***only*** to the triangle in UV space, then we can map the texel into world space using that relative position. 

So basically here's one way to look at it. Just like you'd graph a point onto a graph, we are going to graph the texel onto the triangle. But instead of using the normal X and Y axis, we are going to use two sides of the triangle as the Axis'. And, I've chosen to specify that from every point on the triangle to another is 1 unit. That is, every side of the triangle is length 1 on it's relative graph. A picture of the triangle "graphed" might make it more clear. I've chose to use P1 to P2 (blue), and P1 to P3 (green) as the Axis'. \
<img src="\images\lightScene\barryCentric\graphTri.png">

Remeber, that picture applies whether the triangle is in World Space, or UV space since it is relative only to the triangle. So what we're gonna do is use the UV space triangle, and the UV space texel (since those are the values that we know), and map the texel onto the triangle. \
<img src="\images\lightScene\barryCentric\UVGraphTri.png">

And now that we have the relative position (as identified by the blue and green lines), we can multiply that same relative positon onto the world space texel! And now we know our texel position in world space! \
<img src="\images\lightScene\barryCentric\WGraphTri.png">

So to summarize, this method allows us to know a texel's position relative to a triangle. Then that relative position can be used to find the texel's position in whatever space we desire, so long as the same triangle is present. There are some more cool math things about this, which we'll discuss later. 

Lit
====

Now let's move away from 3D geometry and triangles, and instead lets consider a 2D pixelated scene for the sake of simplicity. Everything black is geometry, and the red is a light. \
<img src="\images\lightScene\Scene.png"> 

Now let's make things lit. To be clear, *not* shadowed, just *lit*. That will make sense in a second. \
<img src="\images\lightScene\Lit.png"> 

Notice how neither the crescent object or circle object block light from reaching the wavy ground, nor does the wavy ground block the deep parts of the crevices where light shouldn't reach. Again, the scene is not shadowed, just lit. So how is a pixel lit or unlit? A pixel being lit is based on the angle, [***A***] from ***the surface which the pixel is on*** to the ***direction of the light towards that surface***. 

Here is one example of ***A*** at 90 degrees, where the surface and the direction of the light to the surface are perpendicular, and where the pixel would be most well lit. \
<img src="\images\lightScene\Perp.png"> 

Here is one example of ***A*** near 180 degrees, where the surface and the direction of the light to the surface are nearing parallel, and where the pixel would become dimly lit. \
<img src="\images\lightScene\Near180.png"> 

Here is one example of ***A*** past 180 degrees, where the surface and the direction of the light to the surface are past parallel and opposite, and where the surface would become unlit. To reiterate, the pixel is unlit because of its angle to the light, not because anything is blocking the light from reaching it. \
<img src="\images\lightScene\Past180.png"> 

So that is a conceptual way of how my lighting makes a scene lit, but just in 2D. It's actually pretty common to do lighting this way (just usually for fragments instead of pixels or texels). Pay attention next time you play a game. You'll see all sorts of lit things where they ought to be covered in shadow! 

Shadowed
====
Next up shadows. Basically, a texel is shaded if there is a surface (besides the surface the texel is on) between it and the light. 

Here's how we do that:

* for every texel, [***Te***] (this is a multi-threaded process)
    * for every light, [***L***]
        * for every triangle, [***Tr***], besides the triangle of ***Te***
            1. shoot a ray from ***L*** to ***Te***, [***LR***]
            1. see if ***LR*** is parallel or past parallel to the surface of ***Tr*** 
            1. if it is *not* (meaning the surface of the triangle at least slightly faces ***L***) then check to see if ***LR*** intersectes with ***TR*** using a 3D-triangle-relative-position (This is a slighlty more robust version of the triangle-relative-position from above since this triangle is being used in 3D space vs UV space, but we will discuss that later in the technical section)
            1. if it does, then we know that ***TR*** crosses the path of ***LR***, and we must shade ***Te***
            1. break the loop. ***Te*** is shaded so we no longer care if ***LR*** intersects any other triangle

Dynamic Shadow Fall Off
====
Im going to explain how exactly I understand dynamic blurred shadows. I won't lie, it's probably not even close to physically correct. But, we don't need to be physically correct so long as it looks ok on the screen. 

With that being said, set up a light which casts onto a wall. Now place your hand somwhere between the light and the wall. A shadow of your hand is cast onto the wall. 

I bet your brain literally exploded right now. 

Ok, but seriously, now move your hand back and forth between the light and the wall. Notice that as your hand moves closer to the wall, the lines of the shadow becomes more solid. As your hand gets closer to the light, the lines of the shadow blur. Now hold your hand still and move the light around. As the light get's closer to your hand the shadow of your hand blurs, and as the light moves away from your hand the shadow of your hand solidifies. So now we have two useful values 

Technical
=====
-----