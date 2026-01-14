---
title: "CV"
permalink: /cv/
author_profile: true
redirect_from:
  - /resume
---

{% include base_path %}

Experience
======
* Data visualization research assistant at CWU — remote job (Nov 2022 – Jul 2023)
  * ***GLC-3D — Unity / C# / HLSL***
  * Developed GLC-3D: a 3D visualization tool for data sets (ie mushroom data). Featuring camera angle snapping, arrow camera movement, attribute shifting, rule testing, UI toggles, and Excel data importing.
  * Iterated dozens of prototypes and layouts based off of weekly meetings with my professor. This required that I gave both accurate timelines, and potential challenges for every step. [protos](https://docs.google.com/document/d/1hHytZ8aGnkuSFSVkQ49HqIrbxxc2BJqqTiZjnFHkA5A/edit?usp=sharing)
  * Optimized frame time from 33 ms to 5 ms on data sets exceeding 8000 points after troubleshooting the vector rendering using Nvidia Nsight. Batch vector rendering was required.
  * Published two papers. One about how GLC-3D benefits human analysis of data, and presented GLC-3D at IV2023. Published another paper which uses GLC-3D to explain Machine learning models. 

  * ***VisCanvas Rule Enhancement — OpenGL / C++ / Windows forms***
  * Sequential Rules: a rule-generation algorithm for data sets (ie mushroom data) for faster, less-detailed rules. The algorithm helped a previous more-expensive algorithm allocate resources and time towards generating more-detailed rules. Possibly reducing compute time by hours on larger data sets. [code](https://github.com/CWU-VKD-LAB/Standalone_Rule_Generation/blob/main/StandaloneRuleGeneration_V1.2/Source.cpp)
  * Improved user experience by developing a loading screen. Uses only two lines of code where programmers expected lengthy calculations, and it allows for changing text as the program loads. Previously the program would just freeze until finished.
  * Documented uncommented code from previous programmers by creating UML diagrams in the user manual, leaving comments in the code, writing extensive pseudocode, and creating an FAQ page. This was to help future programmers get onboard quicker.

* IT intern at Archer school for girls (Jun 2024 – Dec 2024)
  * Scripted in bash. Deployed as automatic scripts to student mac laptops. One script had students update their laptop, another had students restart their laptop if it had been over two weeks.
  * Kindly helped students, parents, and teachers with any technical issues–usually apps not showing up.
  * Listened to the instructions of my supervisor for whatever task they might have had. This included reorganizing and documenting the hardware inventory over the course of 4 weeks.

* Computer Science TA (Jan 2023 – Jun 2023)
  * Valuable feedback which included kindly explaining what was wrong, and then tips on how to course-correct.
  * Explanation would involve analogies to relate the material to something easily understandable.
  * Graded papers weekly for professors. It was important to me to leave more than a score, including encouraging words when a student didn’t do well.

Publications  
======
  <ul>{% for post in site.publications reversed %}
    {% include archive-single-cv.html %}
  {% endfor %}</ul>

Potentially Relevant Points
======
* Dialogue Adventure Mini Game: an entirely hand made graphically filled game connected by dialogue. Includes Ray marching, real time lighting, particles, and texture/geometry generation. Uses unity-android debug overlay to ensure I maintain a smooth framerate even on older devices. Received hundreds of up votes on various Reddit communities. Active development. 
* Card Memorization Game: developed in 3 months in a group using scrum for a class project. By bouncing ideas and having mutual pride in our game, we made something we were proud of. Python and Pygame project.
* Ray-Triangle Intersection: uses the Möller-Trumbore method. Used to find the coordinates and normal vector data (triangle normal and texture normal) of a texel in 3D space for per-texel lighting. Also used to find collisions of GPU-liquid physics-particles with 3D models. Android project. 
* Editor Tools for both optimization and visuals for my per-texel-lighting. Optimization tools: baked shadows/light, optional shadows per light, and resolution scale. Visual tools: intensity, range, color, contrast, and falloff.
* Procedural 2D/3D textures: a forest with trees, grass, and shooting stars; and interactable smoke. Also generated 3D models: caves and line-primitive branches. Android project. 
* Cloud valley: Integrated phone gyroscope interaction with opensource-liquid-physics compute-shader particles. I wanted the particles to flow through hills, but I had placed them in a 3D texture so that I could ray march it. After creating good hill-geometry that I only rendered to a depth texture, I then drew a matching 2D picture to keep a consistent art style, I took the geometry depth, and then hid the particles which crossed the hill depth texture, so that it all appeared to be in the same scene 
* GPU Instanced up to 5000 models with compute-shader-boids. They have separate textures by using a texture atlas. Placing planes with a crowd script allows the user to decide where and how many people are placed. Placing any geometry with a destination script allows for multiple destinations for the crowds to flock to. 
* Infinite texture-forest that a player “walks through”. Uses a compute shader for tree and grass generation and a movement script to move the trees and grass towards the camera. Trees are composed of 2D polygons with quantized noise for trunk detail. Uses an orthographic camera to prevent z-fighting, and appropriate scaling to make the trees and grass appear 3D. 
* Optimized smoke particle ray marching to maintain 60fps on standard Android phones from 2018 onwards. Reduced the resolution of the 3D texture and steps in the ray marcher, which required increasing contrast and limiting colors to remove blockiness and jitter. 

Skills
======
* C++
* C#
* Stylized 2D/3D VFX
* Compute shaders
* Vertex Shaders
* Fragment shaders
* Linear algebra
* Nvidia Nsight
* Vector Math
* Unity engine
* Unreal engine
* Godot engine
* Blender
* Photoshop
* Trello
* Jira
* Halo 3

Education
======
* September 2019 – July 2023  
  * Central Washington University  
  * Computer science BS, Math Minor  
  * Relevant Courses:
    * Guided study in shaders
    * Guided study in graphics profiling
    * OpenGL
    * UI design
    * Unreal engine
    * Linear algebra
    * Udemy course: unity compute shaders by Nicholas Lever