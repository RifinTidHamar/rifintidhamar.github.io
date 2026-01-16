---
title: 'Conceptual Generated Forest'
date: 2026-01-17
permalink: /posts/2026/01/generated-forest/
tags:
  - conceptual
---
Are you curious how to turn simple shapes into complex scenes? This might answer that for you!

We will be conceptually breaking down the process of the forest in this video.
{% include youtube.html id="y8nq1nw3OyY" %}

Tree
------
Look at this amazing thing. Two triangles. Beautiful. \
<img src="\images\generated-tree\twoTriangles.png">

but what's this? Now there is a rhombus. Indeed quite beautiful... but where did the two triangles go? \
<img src="\images\generated-tree\rhombus.png">

It's too bad really... wait... unless. Maybe I have it all wrong.

Yes, now that I think about it. The rhombus is the two triangles, but come together as one! Simply amazing. Nature's wonder is non-ceasing. 
\
\
ah... Exquisite...
\
\
\
\
But now wait. What's this. Another rhombus? \
<img src="\images\generated-tree\twoRhombuses.png">

Indeed I thought one rhombus was good. Two rhombuses is just... It's beautiful. That's all I really mean to say. Beautiful. 
\
\
My eyes surely decieve me! For it appears the rhombuses have joined just as the triangles previous! It is a shape that indeed I cannot name, and yet... I don't need to. Its beauty excells a name! \
<img src="\images\generated-tree\joinedRhombuses.png">

And what is this? Indeed another beautful shape stacked upon the other. A line! Yes a line! Not too bent nor too straight. Indeed not too bent nor too straight! \
<img src="\images\generated-tree\line.png">

Of all things, I coudln't have imagined this. No I couldn't have imagined anything near its standard! A perfectly noise-varied brown line! \
<img src="\images\generated-tree\brownLineVaried.png">

A good day say I! a good day! Ah, and though it is over, I feel no sadness. For it was magnificent. 
\
\
\
\
But have I spoken too soon? Is more even possible? For now I see a green line. \
<img src="\images\generated-tree\treeWOneLeaf.png">
 

And yet again. Alas, I have gone mad. It must be. \
But no! Two more green lines! \
<img src="\images\generated-tree\treeWTwoLeaf.png">

I fear this may be too much for me. Can my eyes handle this, neigh, my soul? For the lines ever increase! \
<img src="\images\generated-tree\treeOThird.png">
<img src="\images\generated-tree\treeTThird.png">

Though I was in darkness, now I see. It was mere shapes to me, and that would have been enough. But now, as though I have been struck, it is a tree before me. Yes, I was blind. \
<img src="\images\generated-tree\tree.png">

Grass
-----
So uh... that was a little weird. Just trying to make it interesting you know? Anyway, here's what those steps look like generated in-engine for a single tree. \
<img src="\images\generated-tree\treeWithGrass.png">

You'll notice that picture has grass. We are gonna go through that next. For ease of understanding first I will write out the steps, and then show pictures.

Written Steps
======

1. Make a noise texture [***N***]. This will be cut apart, and used as our grass. 
    * Generate large noise [***LN***].
    * Generate very small noise [***SN***] which is slightly more compressed horizontally than vertically.
    * ***N*** = ***LN*** \* ***SN***. This way our final grass will have larger hills ***LN*** with finer blades ***SN***.
1. Make the grass bounding box, which will be the grass-noise [***GN***]. 
    * Create four very steep gradients.
    * Two gradients will be vertical, and two horizontal.
    * multiply each gradient by ***N***.
    * add the gradients together, and you will have ***GN***.
1. Using ***GN*** as a mask, take out the pixels from the tree bark. 
1. Again using ***GN*** as a mask, fill in the area just removed with the color of the grass. 

Visual steps
=====

Make a noise texture, ***N*** of small and big noise. For clear visuals, the picture is brightend. \
<img src="\images\generated-tree\noise.png">

Create four gradients which form a bounding-box. For clear visuals, the pictures are brightend, and no noise is multiplied. Normally noise would be multiplied onto these gradients as they are being made. The order of the bounds below are left bound, right bound, upper bound, and lower bound. \
<img src="\images\generated-tree\leftLimit.png">
<img src="\images\generated-tree\rightLimit.png"> 
<img src="\images\generated-tree\upperLimit.png">
<img src="\images\generated-tree\lowerLimit.png">


Add the bounds together. The images from here on out are no longer brightened. \
<img src="\images\generated-tree\combinedLimit.png">

This is what it will look like after each gradient is multiplied by ***N***. \
<img src="\images\generated-tree\combinedLimitWNoise.png">

Now round the the pixels so we have only black and white (for the sake of the clean-pixel art look). This is ***GN***. \
<img src="\images\generated-tree\combinedLimitWRoundedNoise.png">

Mask out the pixels of the tree where grass should go using ***GN***, and everything below ***GN***. The first picture is the tree with no grass, The second picture is the tree masked out. Zoomed in for clarity. \
<img src="\images\generated-tree\treeWithNoGrass.png">
<img src="\images\generated-tree\treeWithNoGrassMaskedOut.png">

Add in the grass color, again using ***GN***. Zoomed in for clarity. \
<img src="\images\generated-tree\treeWithGrassZoom.png">

And now you have your grass! 

Many Treess
------

One tree is fine and all, but it's not much of a forest. That would be like walking around with one brain cell and calling it a brain. Although, that happens to exactly describe my little sister. 

Anyway, we had better add some more trees. Like before I will describe the steps, and then visualize them. But before that here's the gist. We are going to fake perspective and lighting. We are going to do this by moving a bunch of trees towards an orthographic camera, while scaling the trees up and making them brighter as they get closer to that camera. We are using an orthographic camera since transparent sprites don't play very nice in perspective. 

Written Steps
======

1. Make a bounding Rectangle [***BR***].
1. Place an orthographic camera [***OC***] with the forward vector perpendicular to one of ***BR***'s sides. 
1. Randomly place a generated tree sprite [***T***] within ***BR***, with rotation opposite that of ***OC***'s forward vector (so that it is facing ***OC***). 
    * Do not spawn ***T*** within a narrow line following ***OC***'s forward vector all the way through the middle of ***BR***, so that it will not hit ***OC*** as it moves forward in a later step.
1. Create an exponential function which takes ***T***'s Z-position, considers where that position is within ***BR***, and outputs a value, Tree-Z [***TZ***], which exponentially grows between 0 and 1 depending on how close the position is to the side of ***BR*** nearest ***OC***. 
    * Linearly interpolate ***T***'s scale between a minimum scale and a maximum scale based on ***TZ*** (***T*** get's exponetially bigger as it get's nearer).
    * Linearly interpolate ***T***'s color between a dark color and a bright color based on ***TZ*** (***T*** get's exponentially brighter as it get's nearer).
1. Move ***T*** opposite ***OC***'s forward vector by a small amount (moving toward's ***OC***)
1. Create a linear function which takes ***T***'s X-position, considers where that position is within ***BR***, and outputs a value, Tree-X [***TX***], which linearly grows between 0 and 1 as the tree approaches the side of the square parallel ***T***'s movement. 
    * Linearly intSerpolate ***T***'s horizontal movement between a left-speed and a right-speed based on ***TX***. Multiply the horizontal movent by ***TZ*** (***T*** moves epxonentially faster out of frame as it get's nearer).
    * notice having a left and right speed makes it so the if ***T*** is left of ***OC***, it moves left, and if it is right of ***OC***, it moves right. 
1. If ***T*** reaches a certain scale, respawn ***T***S (with a new generated texture) on a random position anywhere on the edge of ***BR*** farthest ***OC***. (this keeps the forest infinitely scrolling)  
1. Do step 3 for as many trees as you would like in your scene, and repeate steps 4-7 for every tree for every frame. 

Visual Steps
======

Make ***BR*** (gray plane) and ***OC*** (white camera Icon). Notice ***OC***'s forward vector is perpendicular one of ***BR***'s sides. \
<img src="\images\generated-tree\movingTrees\TopDownCam.png">

Randomly place ***T*** on ***BR***, excluding a small width following ***OC***'s forward vector (Red). For the sake of display, the tree is not randomly placed in my picture. Instead it is placed at the far-back center of ***BR*** to display an extreme case throughout the following visuals. The left picture is the scene view from above (the green square being the tree location and size), and the right picture is the view from ***OC***. \
<img src="\images\generated-tree\movingTrees\initWithRed.png">
<img src="\images\generated-tree\movingTrees\init.png">

Next ***T*** must be moved Towards the camera. The effects of such will be displayed in 9 snapshots each captured a few seconds apart. Notice the Scale, Color, and Horizontal movement of ***T*** as it get's closer to the camera. Per row, the first picture is a top down view showing where ***T*** is on the plane and its relative scale (represented as a green square), the second picture is a 45 degree view of ***T*** to show scale relative to distance, and the third picture is the view from ***OC***. \
<img src="\images\generated-tree\movingTrees\1T.png">
<img src="\images\generated-tree\movingTrees\145.png">
<img src="\images\generated-tree\movingTrees\1.png">

<img src="\images\generated-tree\movingTrees\2T.png">
<img src="\images\generated-tree\movingTrees\245.png">
<img src="\images\generated-tree\movingTrees\2.png">

<img src="\images\generated-tree\movingTrees\3T.png">
<img src="\images\generated-tree\movingTrees\345.png">
<img src="\images\generated-tree\movingTrees\3.png">

<img src="\images\generated-tree\movingTrees\4T.png">
<img src="\images\generated-tree\movingTrees\445.png">
<img src="\images\generated-tree\movingTrees\4.png">

<img src="\images\generated-tree\movingTrees\5T.png">
<img src="\images\generated-tree\movingTrees\545.png">
<img src="\images\generated-tree\movingTrees\5.png">

<img src="\images\generated-tree\movingTrees\6T.png">
<img src="\images\generated-tree\movingTrees\645.png">
<img src="\images\generated-tree\movingTrees\6.png">

<img src="\images\generated-tree\movingTrees\7T.png">
<img src="\images\generated-tree\movingTrees\745.png">
<img src="\images\generated-tree\movingTrees\7.png">

***T*** out of frame.\
<img src="\images\generated-tree\movingTrees\8T.png">
<img src="\images\generated-tree\movingTrees\845.png">
<img src="\images\generated-tree\movingTrees\8.png">

***T*** respawned randomly on the back edge after given-scale is exceeded. \
<img src="\images\generated-tree\movingTrees\9T.png">
<img src="\images\generated-tree\movingTrees\945.png">
<img src="\images\generated-tree\movingTrees\9.png">

Final Touch
------

This is still frame of our forest with all the steps from above applied to 200 hundred trees. \
<img src="\images\generated-tree\movingTrees\noBG.png">

Our last step is filling in all the grey spots in the background [***BG***]. We need 3 things: a night sky, trees in the horizon, and under-grass. Perhaps we could generate something, but since the gray space is little it's easier and more time effective to make a flat image which goes behind the generated trees. \
<img src="\images\generated-tree\movingTrees\EastNightSky.png">

Notes
------
Make sure to keep all the pixel resolutions the same for a pixel perfect final product. You also will need to render the image to a resolution which equals ***BG***. This way the trees stay pixel perfect while they are scaled up. 

There are also shooting stars, which I did not include in this breakdown as they aren't part of the forest. But, they are basically just a pixel that shoots across a texture. That texture is not refreshed every frame, but instead every pixel alpha is subtracted a small amount which leaves a tail behind the moving pixel. 

Thanks for Reading
======