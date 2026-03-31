---
title: "My Learning"
permalink: /my-learning/
author_profile: true
---
<i class="fa fa-calendar"></i> 3/30/2026 \
Im going on vacation this week! 

<i class="fa fa-calendar"></i> 3/24/2026 \
Today I took a break from learning the SRP, as I was applying for a cool job and wanted to have a very fine tuned application. I did still learn though. First I learned a little more about what it means to be a tech artist through this [udemy course](https://www.udemy.com/share/10ao4y3@a1vE37t1TrmJrvfRp5Mh6EUgkxDj3VTUDvbLqDg3RYZ8z9o42YtTMsLLQKvFLm0kaw==/). Specifically I learned about a good process for identifying performance issues. In summary, check to make sure there is a performance issues, disable suspect issues to see if they are actually issues, debug a frame of the program and check to see where spikes take place. I was already familiar with this process, but it was nice to have it confirmed. 

I also started reading this [article](https://www.adriancourreges.com/blog/2016/09/09/doom-2016-graphics-study/) on the what exactly is being rendered per frame in doom 2016. One cool thing I learned is that Id tech 6 has a massive texture (16k by 8k) which is just a batch of a bunch of 128 x 128 textures. Although I don't understand where that would be useful. Perhaps for LOD? And I guess the answer is yes, but I am suprised it is quicker to continuously modify a huge texture versus just make a bunch of new small textures. 

Another thing that's cool about Id tech 6 is that they don't render every light every frame. Only if a light needs to be re-rendered do they actually calculate it again. That's something I should implement in my lighting. In fact I will right now add it as an optimization for the future. 

Lastly, I learned some things that I need to... well learn about: 
    * Forward vs Deferred rendering
    * Graphics study AAA (I'm choosing Doom 2016, Eternal, and the dark ages)
    * PBR

I may know concepts from these topics already, but I should learn terminology used by people in the industry. 

<i class="fa fa-calendar"></i> 3/23/2026 \
Today I learned about getting into tech art. This is the series I wish I had found years ago. It's a [udemy course](https://www.udemy.com/share/10ao4y3@a1vE37t1TrmJrvfRp5Mh6EUgkxDj3VTUDvbLqDg3RYZ8z9o42YtTMsLLQKvFLm0kaw==/). I specifically learned about the different types of tech artists, of which these two sounded most interesting: lighting artist, and procedural artist. The lighting artist really doesn't need to know a lot outside of lighting and pbr. While the procedural artist should know a lot about linear algebra. The procedural artist will also deal with things like placing objects in the world, which I didn't expect, but I suppose makes sense. 

Here's one thing that did suprise me. Apparently Tech artists need to know about all aspects of an engine, even things like audio and AI. AI I would get a little more, as perhaps an NPC could wander into your tech art mud, for example. But audio? Im excited to learn more about it. 

I also learned a little more about the SRP, specifcally legacy shader tags. Basically what we did in the tutorial is consider all of the legacy shader tags so that we could color them purple, to show the user that these kinds of shaders are not allowed in our SRP. So I guess I actually learned less about legacy shader tags and more about error handling--which is cool. 

<i class="fa fa-calendar"></i> 3/20/2026 \
Today I learned about how to start setting up a custom Scriptable Render Pipeline. I'm using this [tutorial](https://catlikecoding.com/unity/tutorials/custom-srp/custom-render-pipeline/). It's a bit outdated, but still works with modern Unity versions. I'll just have to also learn the more modern way to do things once I'm done. Which will be a nice excercise in intself. 

Specifcally I learned how to show a skybox, be able to actually traverse the skybox once pulling camera projections, display unlit objects and transparent unlit objects. The conflict introduced was that opaque and transparent objects were drawn together, but then the skybox would draw over the transparent objects since they aren't part of the depth buffer--which was actually a [neat effect](https://imgur.com/a/BAhoiub) that I'll have to remeber. The solution was to first draw the opaque objects, then the skybox, and then the transparent objects. One thing I was confused about was how a transparent object can be drawn over another transparent object if they have no depth buffer. After looking at the frame debugger, now it makes sense. Objects are still drawn by distance to the camera. So further away objects are drawn first. That way closer objects are on top of of further away objects. That's why when you have two transparent objects overlapping, they get wonky. I've actually had to deal with that issue in the past. The article suggested that a potential solution is to cut up the transparent overlapping objects into multiple objects. But why not just draw transparent objects to a depth buffer where transparency is considered? 

