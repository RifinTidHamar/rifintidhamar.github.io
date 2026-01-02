---
permalink: /
title: "About me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

I am a Tech Artist with a love for Stylized Graphics. 

My desire to make games goes back to my first time playing Halo 3. I adored the game, and I wanted to make something like it. When I was 10, I even told Bungie through a physical letter how much I loved Halo. In fact they responded and gave me a sticker, which I still have today on my original Xbox 360 casing. 

Blender
======

My first step into game creation was when I was 12. It was honest, but somewhat misguided. I started learning Blender. Here are some of [my very earliest renders](https://imgur.com/a/v21uabQ). You see, I had zero idea what it meant to make a game, and for some reason I thought people made games completely inside of tools like Blender. Like I actually thought a game was exactly like a movie, and they just pre-rendered every possible thing a user might do for every possibility. Can you imagine the infinite amount of storage that would take? Eventually I figured out that's not how games work, but I still always loved Blender. 

While I didn't explicitly learn to make games with Blender, I did learn valuable concepts that you see popping up in tech art all the time: 3D models, vertices, UV coordinates, texture wrapping, normals maps, an introduction into shaders with their node system, and even how to properly handle problem solving: bashing my skull against the nearest wall. It helped me visualize what I would later code, except head bashing. That's pretty much the same. 

Hello World
======

At 13, my first time learning anything about programming was during the winter of 2014, with this channel called [Chili Tomato Noodle Soup](https://www.youtube.com/watch?v=PwuIEMUFUnQ&list=PLqCJpWy5FohcehaXlCIt8sVBHBFFRVWsx). The objective of the series was to make a small game, where every pixel was hand placed using DirectX. The environment around my first time coding still sticks with me as a core memory. I had just moved to Washington, I was in my Aunt's warm basement watching the snow fall in the woods outside, my older brother was playing Destiny next to me, I was listening to Puscifer's [Humbling River](https://www.youtube.com/watch?v=siWusSBld7k&list=RDsiWusSBld7k&start_radio=1), and I was just about to print on my screen for the first time ***"Hello World!"***  

Over the years
======

Now speedrunning the next few years.
- For a 10th grade elective, I made [1V1 simulator](https://imgur.com/a/3wLoBUT) with a friend, in Zero Engine. 
- During that summer I started learning unity. I made a dog jump, and some cubes move around. Unfortunately all I have from that time is a [sprite of my dog](https://imgur.com/a/CPYk7wi)
- I planned a cinematic game, which to my teenage brain was very deep and moving. 
- I studied the basics of C#/OOP.
- During 11th grade, using Unity I started a game of mini-games inspired by WarioWare. Again, all I have left are unfortunately [some sprites](https://imgur.com/a/mini-game-fantasy-lBA0zw2). But, a lot of the vibe of those early steps actually stick with me in my current in-progress game, Night Walk. 
- In 12th grade I took AP Computer Science.
- I began my CS degree at CWU after graduating highschool. 
- I made [ITERUM](https://rifintidhamar.itch.io/iterum) for a game-jam using Unity. It didn't do too well, but people thought it looked nice. 

Shaders
======

I always cared about how my games looked, both those that I played and those that I made. I would do my best with pixel art or modeling, but there was always something missing: The FX. Sure I could use built in materials or particles, but it just never quite hit exactly how I wanted. 

I had heard whispers from the dark seldom-trodden limits of the internet of this strange... *thing* called a "Shader." But it was all rumor or perhaps something only the truly mad would dare engage. At best, maybe something I would learn in the distant future I thought. Then I stumbled across [Sebastian Lague](https://www.youtube.com/@SebastianLague). This guy made things in Unity I couldn't even imagine how to start: volumetric atmospheres, lovecraftian generated 3D fractals, and procedural planets. At the core of all these were "Shaders." It was so cool, and frankly I was jealous *he* could do that *I* couldn't. So I started learning.

Much of my early shader learning was through [The Art of Code](https://www.youtube.com/@TheArtofCodeIsCool). That channel taught me about concepts like fragment shaders, ray marching, and procedural shapes. Here is my first shader generated thing, ["Josh."](https://www.shadertoy.com/view/tl2XWc) 

Dr. Boris Kovalerchuk and Data Visualization 
======

I took an OpenGL elective. Afterwards I asked my professor, Boris Kovalerchuk, if he could oversee me in an independent-study class about shaders. I showed him the textbook I was going to study, and he OKed it. I learned about a ton of built in GLSL operations, the different 3D spaces, the graphics pipeline, and other basic shader ideas. I made a tree shader with both [adjustable snow, and generated limbs](https://docs.google.com/document/d/1Yx2_UjM1IJNNf9DwON1ezYPg9U3vbM46NbN4Rc7akSQ/edit?tab=t.0), which gave my professor the idea that I could start working on a program, at the time, called Shifted-Pair-Coordinates-3D. 

I suggested using unity as the tool, and by the end of the quarter, my professor and I got the program to a place which we thought was finished. Except the program was slow. So I suggested another independent-study class, graphics optimization, which Dr.K also OKed. I then learned about Nvidia Nsight, and optimized the program after identifying the source of slowness (lines needed to be drawn in batch). 

During this time the depth of the program's capability was increased, and its name was changed to General-Line-Coordinates-Linear-3D. Here is a brief summary of [all the iterations of GLCL-3D](https://docs.google.com/document/d/1hHytZ8aGnkuSFSVkQ49HqIrbxxc2BJqqTiZjnFHkA5A/edit?tab=t.0). In effect, GLCL-3D was just a ***big tool*** intended to be used by data researchers. I also briefly learned about Compute shaders at this point, in an attempt to speedup calculations.

After the optimization class, Dr.K hired me on as an official Research Assistant, where I continued working on GLCL-3D, including [publishing a research paper](https://arxiv.org/ftp/arxiv/papers/2403/2403.13014.pdf), and giving a [presentation at IV2023](https://www.youtube.com/watch?v=0DLcBeQzfCg). One of my other professors even cited me in their own research paper, isn't that crazy?

I also worked on another program called VisCanvas, where I would document code, create a sequential rule generation algorithm for speeding up rule Generation, drastically reformat rule text-output, and play with existing OpenGL code. 

After College
======

This is the part of my journey that I probably look on most fondly, in terms of game creation. I now had a ton of freetime and potential only limited by my dedication and creativity (and those detestable dolla' dolla' bills). Again inspired by Sebastian Lague, I wanted to learn about compute shaders. So I bought a [Udemy course on compute shaders](https://www.udemy.com/course/compute-shaders/learn/lecture/22732787?start=0#overview) by Nicholas Lever. Out of all the tools I've learned so far, compute shaders are hands down my favorite. 

I started seriously making [Night Walk](https://www.youtube.com/watch?v=SUMMFn2hcaA&t), a mobile game about graphical experiences, and mini games connected by dialogue adventure. A game I still frequently work on. 

[Another paper](https://drive.google.com/file/d/1xEpuJN9kI83a6U_qD2yfoxV5oSYkVQix/view?usp=sharing) was published by Dr.K, using my program for the visuals. 

Los Angeles
======

Then I moved to Los Angeles for a year, where admittedly my time in game making went way down. I had a job in IT for 6 months as an intern, and I did a few unrelated graphical things, but mostly I was working on social skills. Figured it was about time. Still working on them. 

Now
======

Anyway, LA wasn't my scene, so I moved back to Washington and I'm back into making Night Walk. Hopefully good things are in my future. Like a job. A job would be swell. Real swell. 

If you find any spelling or grammatical errors... they are an artistic choice. 

TEH END
======
