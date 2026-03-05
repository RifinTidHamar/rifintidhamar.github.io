---
title: 'Per Texel Light (in-progress)'
date: 2026-02-16
permalink: /posts/2026/02/Per-Texel-Light/
tags:
  - conceptual
  - technical
---
Do you want to emulate older 3D games but with a new touch? This might interest you.

expected completion: mid-late feb

The following article is made for desktop. It will be conceptually and technically breaking down the process of the lighting in this video:
{% include youtube.html id="Uief5d7zBRc" %}

Conceptual
====
-----
Lighting is pretty cool. Cavemen were pretty interested by it, and my great great great great great great great great great great great great great great great great great great great great great great great great great great great great great great grandpa was a caveman, so it makes sense that I would be interested by it too. 

In this *Conceptual* section, we are gonna be breaking down some ideas that will be useful in the technical section. 

Luckily, understanding lighting isn't too hard. The math can get a little wonky, but I think we'll be fine. Although if at any point you are feeling cranky or like crying, that is ok. 

UV Space to World Space
=====
My lighting is a per-texel lighting; that is, lighting is calculated for every texel on an object's texture. The problem is that texels are only recorded in UV space, yet we need to know their position in world space. That way they can interact with the lights in world space. We will utilize the knowledge that the triangles which make up an object, which the texels are wrapped around, are in both world space and UV space. So, in order to get texels from UV space to World space, here's what we do:

* for every texel, [***Te***] (this is a multi-threaded process)
    * for every triangle, [***Tr***]
        1.  using a triangle-relative-position (more on this in a second) see if ***Te*** lies within ***Tr*** in UV space
        1. if it does, then multiply that triangle-relative-position on ***Tr*** in world space to find where ***Te*** lies in world space. 
            * break the loop since we have found the correct ***Tr*** for ***Te***

Ok so now let's get into the good stuff: The concept behind the triangle-relative-position. It's known as *Barrycentric coordinates*. I wonder if the creator was named Barry. What if their name was something like Mobius. That would be something. 

Anyway, first up take a look at the two pictures below. The first picture is a triangle with a texel in World Space, and the second picture is the same triangle and texel but in UV space. 
<div style="text-align:center;">
    <img src="\images\lightScene\barryCentric\WTri.png">
</div>
<div style="text-align:center;">
    <img src="\images\lightScene\barryCentric\UVTri.png">
</div>

Again, we need to know the texel's position in world space. By nature of a texel, we know its position in UV space, but we do not know its position in world space (so *TexW* is highlighted in red in the world space picture above). However we do know the position of the triangle in world space, ***and*** UV space. So if we can find the position of the texel relative ***only*** to the triangle in UV space, then we can map the texel into world space using that relative position. 

So basically here's one way to look at it. Just like you'd graph a point onto a graph, we are going to graph the texel onto the triangle. But instead of using the normal X and Y axis, we are going to use two sides of the triangle as the Axis'. And, I've chosen to specify that from every point on the triangle to another is 1 unit. That is, every side of the triangle is length 1 on it's relative graph. A picture of the triangle "graphed" might make it more clear. I've chose to use P1 to P2 (blue), and P1 to P3 (green) as the Axis'. 
<div style="text-align:center;">
    <img src="\images\lightScene\barryCentric\graphTri.png">
</div>

Remeber, that picture applies whether the triangle is in World Space, or UV space since it is relative only to the triangle. So what we're gonna do is use the UV space triangle, and the UV space texel (since those are the values that we know), and map the texel onto the triangle. 
<div style="text-align:center;">
    <img src="\images\lightScene\barryCentric\UVGraphTri.png">
</div>

And now that we have the relative position (as identified by the blue and green lines), we can multiply that same relative positon onto the world space texel! And now we know our texel position in world space! 
<div style="text-align:center;">
    <img src="\images\lightScene\barryCentric\WGraphTri.png">
</div>

So to summarize, this method allows us to know a texel's position relative to a triangle. Then that relative position can be used to find the texel's position in whatever space we desire, so long as the same triangle is present. There are some more cool math things about this, which we'll discuss later. 

Lit
====

Now let's move away from triangles, and--for the sake of simplicity--lets instead lets consider a 2D pixelated scene. Everything black is geometry, and the red is a light. 
<div style="text-align:center;">
    <img src="\images\lightScene\Scene.png"> 
</div>

Now let's make things lit. To be clear, *not* shadowed, just *lit*. That will make sense in a second. 
<div style="text-align:center;">
    <img src="\images\lightScene\Lit.png"> 
</div>

Notice how neither the crescent object or circle object block light from reaching the wavy ground, nor does the wavy ground block the deep parts of the crevices where light shouldn't reach. Again, the scene is not shadowed, just lit. So how is a pixel lit or unlit? A pixel being lit is based on the angle, [***A***] from ***the surface which the pixel is on*** to the ***direction of the light towards that surface***. 

Here is one example of ***A*** at 90 degrees, where the surface and the direction of the light to the surface are perpendicular, and where the pixel would be most well lit. 
<div style="text-align:center;">
    <img src="\images\lightScene\Perp.png"> 
</div>

Here is one example of ***A*** near 180 degrees, where the surface and the direction of the light to the surface are nearing parallel, and where the pixel would become dimly lit. 
<div style="text-align:center;">
    <img src="\images\lightScene\Near180.png"> 
</div>

Here is one example of ***A*** past 180 degrees, where the surface and the direction of the light to the surface are past parallel and opposite, and where the surface would become unlit. To reiterate, the pixel is unlit because of its angle to the light, not because anything is blocking the light from reaching it. 
<div style="text-align:center;">
    <img src="\images\lightScene\Past180.png"> 
</div>

So that is a conceptual way of how my lighting makes a scene lit, but just in 2D. It's actually pretty common to do lighting this way (just usually for fragments instead of pixels/texels of geometry). Pay attention next time you play a game. You'll see all sorts of lit things where they ought to be covered in shadow! 

Shadowed
====
Next up shadows. Basically, a texel is shaded if there is a surface (besides the surface the texel is on) between it and the light. 

Here's how we do that:

* for every texel, [***Te***] (this is a multi-threaded process)
    * for every light, [***L***]
        * for every triangle, [***Tr***], besides the triangle of ***Te***
            1. shoot a ray from ***L*** to ***Te***, [***LR***]
            1. see if ***LR*** is parallel or past parallel to the surface of ***Tr*** 
            1. if it is *not* (meaning the surface of ***TR*** at least slightly faces ***L***) then check to see if ***LR*** intersectes with ***TR*** using a 3D-triangle-relative-position (This is a slighlty more robust version of the triangle-relative-position from above since relative-position is being caluclated in 3D-world-space vs 2D-UV-space, but we will discuss that later in the technical section)
            1. if it does, then we know that ***TR*** crosses the path of ***LR***, and we must shade ***Te***
                * if the texel *is not* set to be dynamically blurred, break the loop ***Tr*** loop and check the next light. ***Te*** is shaded so we no longer care if ***LR*** intersects any other triangle
                * if the shadow *is* set be dynamically blurred, well then you had daggone better dynamically blur that thing!

Dynamic Blurred Shadows
====
Im going to explain how exactly I understand dynamic blurred shadows. I won't lie, it's probably not even close to physically correct. But, we don't need to be physically correct so long as it looks ok on the screen, and it's able to run well. 

With that being said, set up a light which casts onto a wall. Now place your hand somwhere between the light and the wall. A shadow of your hand is cast onto the wall. 

I bet your brain literally exploded right now. 

Ok, but seriously, now move your hand back and forth between the light and the wall. Notice that as your hand moves closer to the wall, the lines of the shadow becomes more solid. As your hand moves closer to the light, the lines of the shadow blur. Now hold your hand still and move the light around. As the light gets closer to your hand the shadow of your hand blurs, and as the light moves away from your hand the shadow of your hand solidifies. So now we have some useful values:

Visualized as the green line, the total distance of the light ray to the shaded texel, [***LR***]: 
<div style="text-align:center;">
    <img src="\images\lightScene\blur\lightRay.png"> 
</div>
Visualized as the green line, the nearest distance along the light ray from the shaded texel to the shading object, [***NLR***]: \
<div style="text-align:center;">
    <img src="\images\lightScene\blur\lightRayTex.png"> 
</div>

While there are some adjustment values availble to the user, the base of the blurring in my program is just ***Blur*** = ***NLR*** / ***LR***

Technical
=====
-----

Alright so now you hopefully have a good idea of some critical concepts used in my lighting. In this section, we are gonna be mostly going over the compute shader code, which will allow you to fully piece together the concepts above. We will also dive a little deeper into some math--so gird your loins. To download the unity project and see the c# side, check my [github](https://github.com/RifinTidHamar/Shadow-and-Light), although I will give a short summary of the c# below. 

c# summary
====
* on the intial frame
    * initialize all variables, including important structs and textures
    * populate those structs with initial values (for example the triangle info of the mesh)
    * fill buffers to be used in the compute shader
    * Dispatch the **UvToWorld** kernel, which takes all texels of the mesh and finds their position in world space
    * Dispatch the **DynamicLight** kernel for baked lighting
* for every update frame
    * update arrays and buffers based off for real time lights
    * dispatch the **DynamicLight** kernel for real time lighting
    * dispatch the **Apply** kernel, which combines tshe baked and real time lighting into one texture 

Compute Shader Variables and structs
=====
Like the name implies, these are my variables. There isn't much to say, but it might be useful to revisit the structs as you are going through the other parts of the code.
```cuda
struct MeshTriangle
{
    float3 p1WPos;
    float2 p1Uv;
    float3 p2WPos;
    float2 p2Uv;
    float3 p3WPos;
    float2 p3Uv;
    float3 normal;
    float3 tangent;
    float3 binormal;
};

struct CSLight
{
    int shadowType;//0=lit,1=hardShadow,2=dynamicShadow
    float3 loc;
    float4 color;
    float range;
    float intensity;
    float blurMultiplier;
    float blurPower;
};

struct usedUV
{
    float3 worldLoc;
    float3 normal;
    float3 geoNormal;//normal only with respect to geometry
    int used;//is this UV actually on a triangle
    int lit;//is this UV reached by some light
};

StructuredBuffer<MeshTriangle> triangles;
StructuredBuffer<CSLight> lights;
RWStructuredBuffer<usedUV> usedUVs;
StructuredBuffer<int> numLights;

//finalResult of a dispatch (can be the actual final result, or real time lighting, 
//or baked lighting depending on usage) 
RWTexture2D<float4> totalResult; 

//real time light texture used for combining in final apply
RWTexture2D<float4> RlTLight; 
//baked light texture used for combining in final apply
RWTexture2D<float4> BLight; 
//normal map
RWTexture2D<float4> nm; 

int numTriangles;
int texRes;
static float eps = 0.001;//0.046;
```

Checking intersection using Barrycentric coordinates
=====
Rather than a kernel, this next section is a method used in a kernel. The method returns true or false depending on if a given point, *inters* is inside of a given triangle, *tri*. While for most of the technical section I'll be going over code chronologically, this method is actually used later in the program. We're gonna discuss it now because I wrote it and I can do it if I want. Also, it so happens that it's useful to help explain an idea used in other parts of the code, but mostly I just wanna do what I want. So take a look at the code, and then we're gonna go over math, and then we'll very quickly match that math to the code. 
```cuda
bool checkIntersectionInTri(float3 inters, MeshTriangle tri)
{
    //real time collision textbook section 3.4
                            
    float3 p1 = tri.p1WPos;
    float3 p2 = tri.p2WPos;
    float3 p3 = tri.p3WPos;

    float3 v0 = p2 - p1;
    float3 v1 = p3 - p1;
    float3 v2 = inters - p1;

    float d00 = dot(v0, v0);
    float d01 = dot(v0, v1);
    float d11 = dot(v1, v1);
    float d20 = dot(v2, v0);
    float d21 = dot(v2, v1);

    float denom = d00 * d11 - d01 * d01;

    float v = (d11 * d20 - d01 * d21) / denom;
    float w = (d00 * d21 - d01 * d20) / denom;
    
    return 0 - eps <= v && 0 - eps <= w && v + w <= 1 + eps;
}
```
The ideas behind the following explanations are found from the "Real Time Collision Detection" textbook by Christer Ericson (who helped make "God of War" among other things). The wording and explanation is my own, and it is arranged differently than Ericson, but the ideas are from the book. 

Mmmk, the purpose of barrycentric coordinates is to find the position of some point relative to a triangle. Often, the method is useful to see if a point lies within some triangle. 

Look at this function:

$$
P = P_1 + v(P_2 - P_1) + w(P_3 - P_1)
$$

That function there represents the conceptual idea from the "UV space to World space" section above. Basically, a point, \\(P\\), can be represented entirely by a triangle, with \\(P_1, P_2, P_3\\) as the vertices. We decide that the origin is \\(P_1\\), and then we find how far up \\(P\\) is on the axis of "P2 to P1" AKA \\(v(P_2 - P_1)\\) and how far up \\(P\\) is on the axis of "P3 to P1" AKA \\(w(P_3 - P_1)\\). Keep in mind that we choose that those axis' each have a length of 1.

Now that we have some functional basis, our goal is to solve for \\(v\\) and \\(w\\), which are the coordinate names of this triangle-relative-position (like \\(x\\) and \\(y\\) on a standard graph). Here is an overview of how we will do that.

1. turn the function above into a system of equations  
2. use cramer's rule to solve the system

To turn the function into a system of equations, we need to somehow get all scalar values, not position/vector values. At least one way to do that is with the dot product. But first, let's make our equation look a little nicer. \\(P_2 - P_1\\) and \\(P_3 - P_1\\) are really just vectors, so we'll instead call them \\(V_0\\) and \\(V_1\\) respectively. Which gives us the new-looking function:

$$
P = P_1 + v(V_0) + w(V_1)
$$

Let's go one step further and subtract \\(P_1\\) on both sides:

$$
P - P_1 = v(V_0) + w(V_1)
$$

Which allows us to make another vector out of \\(P - P_1\\) which we'll call \\(V_2\\):

$$
V_2 = v(V_0) + w(V_1)
$$

Alright, now let's turn this into a system of equations with the help of the dot product (since the dot product of two vectors is a scalar, which is required for a system of equations):

$$
V_2 \cdot V_0 = V_0 \cdot (v(V_0) + w(V_1))
$$

$$
V_2 \cdot V_1 = V_1 \cdot (v(V_0) + w(V_1))
$$

Then distribute the dot product, so that the equations are more common looking. That will allows us to easier use cramer's rule on the next step:

$$
V_2 \cdot V_0 = v(V_0 \cdot V_0) + w(V_1 \cdot V_0)
$$

$$
V_2 \cdot V_1 = v(V_0 \cdot V_1) + w(V_1 \cdot V_1)
$$

Ok so if you don't know, Cramer's rule just gives us a nice way to solve systems of equations with Matrices. The first step of Cramer's rule is to derive three different matrices from the system of equations. 1. the solution matrix, \\(X\\): made of the variables (\\(x\\) and \\(y\\) in a normal system of equations, \\(v\\) and \\(w\\) in ours) 2. the coefficient matrix, \\(A\\): made of the coefficients to the variables 3. the constant matrix, \\(B\\): made of the constants... duh! 

$$
X = \begin{bmatrix}
v \\
w
\end{bmatrix}
\quad
A = \begin{bmatrix}
V_0 \cdot V_0 & V_1 \cdot V_0 \\
V_0 \cdot V_1 & V_1 \cdot V_1
\end{bmatrix}
\quad
B = \begin{bmatrix}
V_2 \cdot V_0 \\
V_2 \cdot V_1
\end{bmatrix}
$$

Now before we go on, I want to make something perfectly clear. This is my first time making a matrix on a computer screen like that. That's pretty cool.

The 2nd and last step in Cramer's rule is to divide some determinants. Let me show the step first, then explain it a little more after.

$$
v = \frac{\begin{vmatrix}
V_2 \cdot V_0 & V_1 \cdot V_0 \\
V_2 \cdot V_1 & V_1 \cdot V_1
\end{vmatrix}}
{\begin{vmatrix}
V_0 \cdot V_0 & V_1 \cdot V_0 \\
V_0 \cdot V_1 & V_1 \cdot V_1
\end{vmatrix}}
\quad
w = \frac{\begin{vmatrix}
V_0 \cdot V_0 & V_2 \cdot V_0 \\
V_0 \cdot V_1 & V_2 \cdot V_1
\end{vmatrix}}
{\begin{vmatrix}
V_0 \cdot V_0 & V_1 \cdot V_0 \\
V_0 \cdot V_1 & V_1 \cdot V_1
\end{vmatrix}}
$$ 

For \\(v\\), the numerator is \\(A\\), but with the first column replaced by \\(B\\). For \\(w\\), the numerator is \\(A\\), but with the second column replaced by \\(B\\). The denominator in both cases is just \\(A\\). I'm not gonna go over why specifically these matrices-we-are-taking-determinants-of are formed the way they are, but I will just say if you were to solve the system of equations in a more "highschool" way, you would see that Cramer's rule is the same process. It just looks more concise. 

Im gonna explain how to get a determinant value from a 2X2 matrix. Say you have have a Matrix \\(A\\). Now say...

$$ A = 
{\begin{bmatrix}
x & y \\
z & w
\end{bmatrix}}
$$

So then the determinant of \\(A\\) is...

$$
{\begin{vmatrix}A\end{vmatrix}} = 
{\begin{vmatrix}
x & y \\
z & w
\end{vmatrix}} = x * w - y * z
$$

I won't lay out all the determinants, but just for example, I will layout the denominator determinant for \\(v\\) and \\(w\\).

$$
{\begin{vmatrix}
V_0 \cdot V_0 & V_1 \cdot V_0 \\
V_0 \cdot V_1 & V_1 \cdot V_1
\end{vmatrix}}
=
V_0 \cdot V_0 * V_1 \cdot V_1 - V_1 \cdot V_0 * V_0 \cdot V_1
$$

Notice how this equals the `denom` variable from the code above (keeping in mind that dot products are commutative). Then, once you've done a similar process for the other determinants, you'll notice that the numerators of `v` and `w` are equal to the numerators of \\(v\\) and \\(w\\) respectively.

From there it becomes easy to backtrack how the other variables are formed. The last thing to discuss here is the return boolean:

```cuda 
return 0 - eps <= v && 0 - eps <= w && v + w <= 1 + eps;
```
first off, `eps` is just short for epsilon. It is simply a very small value used to give leniency to calculations. 

`v` and `w` tell us a point's position relative to a triangle, but what we really want to know is if the point is inside or outside the triangle. Take a look at this picture again. 
<div style="text-align:center;">
    <img src="\images\lightScene\barryCentric\graphTri.png">
</div>

Right off the bat, we can deduce that any point within the triangle, must at least have positive coordinates, seeing as how the blue and green coordinates are only positive inside the triangle. Therefore the first two terms of the return boolean consider if `v` and `w` are greater than or equal to zero. 

Now as for the last term, `v + w <= 1`, take a look at the picture below. It is another triangle, similar to the last but with a different shape. Click and drag your mouse over the black dotted line (which is representetive of our white line in the above picture), and see if you notice anything about the displayed points.  
<div style="text-align:center;">
    <iframe 
        src="https://www.desmos.com/calculator/zfjjabmhdn?embed"
        width="440"
        height="500"
        style="border:1px solid #ccc;"
        frameborder="0">
    </iframe>
</div>

You will notice that always \\(v + w = 1\\) along the white border line. And since we have set our blue and green axis' to always have length 1 (even if they don't appear so) we know this will be true for any triangle. So we can see that, on top of `v` and `w` being positive this also must be true: `v + w <= 1` (well we can see a lot of things actually. For example if we look down, we can see our legs.)

While there are other ways to show this, for example treating each point as a metaphorical weight, this explanation makes most sense to me. 

UvToWorld Kernel
====

The following code is a kernel. It considers in parallel all texels of a texture, and finds its position in 3D space--if it has one. First look through the code, then Ill tell you some math stuff. Take note that this is the manifestation of the **UV Space to World Space** conceptual section above. It also might be helpful to take another look at the c# summary. 

```cuda
[numthreads(8,8,1)]
void UvToWorld (uint3 id : SV_DispatchThreadID)
{
    float v;
    float w;
    float c;
    float2 curUV = float2((float)id.x / (float)texRes, (float)id.y / (float)texRes);

    for (int i = 0; i < numTriangles; i++)
    {
        float2 p1 = triangles[i].p1Uv;
        float2 p2 = triangles[i].p2Uv;
        float2 p3 = triangles[i].p3Uv;
                
        float denom = (p2.x - p1.x) * (p3.y - p1.y) - (p3.x - p1.x) * (p2.y - p1.y);
        
        v = ((curUV.x - p1.x) * (p3.y - p1.y) - (p3.x - p1.x) * (curUV.y - p1.y)) / denom;
        w = ((p2.x - p1.x) * (curUV.y - p1.y) - (curUV.x - p1.x) * (p2.y - p1.y)) / denom;
        
        if (0 - eps <= v && 0 - eps <= w && v + w <= 1 + eps) //inside triangle
        {
            usedUVs[texRes * id.y + id.x].used = 1;

            float3 wp1 = triangles[i].p1WPos;
            float3 wp2 = triangles[i].p2WPos;
            float3 wp3 = triangles[i].p3WPos;
            usedUVs[texRes * id.y + id.x].worldLoc = wp1 + v * (wp2 - wp1) + w * (wp3 - wp1);
            
            //I don't fully undestand why the g needs to be flipped, 
            //but the normal map isn't right unless it is
            nm[id.xy].g = 1 - nm[id.xy].g;
            //set values between 0->1, incase they are -0.5->0.5
            nm[id.xy] = nm[id.xy] *  2 - 1;
            //apply normal map
            usedUVs[texRes * id.y + id.x].normal = 
                triangles[i].normal * nm[id.xy].b + 
                triangles[i].binormal * nm[id.xy].g + 
                triangles[i].tangent * nm[id.xy].r;
            usedUVs[texRes * id.y + id.x].normal = normalize(usedUVs[texRes * id.y + id.x].normal);
            //get the normal of the triangles without the normal map
            usedUVs[texRes * id.y + id.x].geoNormal = triangles[i].normal;

            //since the the UV is used (ie in some triangle) 
            //it should not be in another triangle, so we can stop searching. 
            break;
        }
    }
}
```
The first thing to discuss is kernel size. This explanation goes for all the kernels: An 8x8 threaded process fits well into a base 2 texture, and it's not too large for any mobile phone worth its salt. (Salty phone, yummy)

Next, you might've noticed that in the first half of the kernel, the code bears barrycentric similarities to the intersection method discussed earlier. It is exactly the same method in fact, only it considers a 2D triangle rather than a 3D triangle. We could still use all those dot products, but it was more efficient to find another way.

recall this piece of math from the intersection method above:

$$
V_2 = v(V_0) + w(V_1)
$$

Same math applies here, but now we are in UV space. So \\(V_2 = curUV - P_1\\), \\(V_0 = P_2 - P_1\\), and \\(V_1 = P_3 - P_1\\). Remeber that we want a system of equations to solve \\(v\\) and \\(w\\). Since it is a 2D triangle with 2D points we don't need the dot product to do that. Instead we can do this:

$$
V_2.x = v(V_0.x) + w(V_1.x)
$$

$$
V_2.y = v(V_0.y) + w(V_1.y)
$$

and the rest of the process is the exact same, for the barrycentric coordinate part. We'll discuss what's inside the `if statement` next

if the UV is inside the triangle...

first, we set a "used" flag on the UV: ```usedUVs[texRes * id.y + id.x].used = 1;```

then this:
```cuda
float3 wp1 = triangles[i].p1WPos;
float3 wp2 = triangles[i].p2WPos;
float3 wp3 = triangles[i].p3WPos;
usedUVs[texRes * id.y + id.x].worldLoc = wp1 + v * (wp2 - wp1) + w * (wp3 - wp1);
```
First off, it's imperative that you listen to [this song from 3 idiots](https://www.youtube.com/watch?v=7PzwOiW8-n0) in order to move forward. 

Now, this code is just this equation:

$$
P = P_1 + v(P_2 - P_1) + w(P_3 - P_1)
$$

Take a look back above in the previous section if you don't remeber what this equation is doing. 

As for the rest of the `if statement`, I will let the comments already there do most of the talking. Basically we are just multiplying the normal map texture onto the normal geometry, and applying that to the UV. Then we break the loop, since the UV should only be inside of one triangle.

Dynamic Light Kernel
==========
```cuda
[numthreads(8,8,1)]
void DynamicLight(uint3 id : SV_DispatchThreadID)
{
    int uvInd = texRes * id.y + id.x;
    if (usedUVs[uvInd].used == 1)
    {
        for (int i = 0; i < numLights[0]; i++)
        {
            float4 threadUnlit = 0;
            float4 threadShad = 0;
            float3 locMinusLight = usedUVs[uvInd].worldLoc - lights[i].loc;
            float3 uvWPos = usedUVs[texRes * id.y + id.x].worldLoc;
            float distUVtoLight = length(locMinusLight);
            float3 lightVec = normalize(locMinusLight);
            float angleBetweenLightAndLocWNormalMap = dot(lightVec, usedUVs[uvInd].normal);
            //outside of range, and facing the light
            if (distUVtoLight < lights[i].range && angleBetweenLightAndLocWNormalMap < 0)
            {
                float distFromLighttoPlane = 0;
                if (lights[i].shadowType > 0)//hard shadows
                {
                    float minDistUVtoInter = 0;
                    for (int j = 0; j < numTriangles; j++)
                    {
                        //see if plane and lightVec are opposite
                        float parallelDot = dot(lightVec, triangles[j].normal);

                        // if they are not, and not parallel, then they intersect
                        if (abs(parallelDot) >= 0) 
                        {
                            //https://www.youtube.com/watch?v=x_SEyKtCBPU
                            distFromLighttoPlane = 
                                dot(triangles[j].p3WPos - lights[i].loc, triangles[j].normal) / parallelDot; 
                        
                            if (distFromLighttoPlane >= distUVtoLight - eps)
                                continue;

                            float3 intersection = lights[i].loc + (distFromLighttoPlane * lightVec);
                            //if the intersection is in the triangle, 
                            //then the triangle casts a shadow on the texel
                            if (checkIntersectionInTri(intersection, triangles[j]))
                            {
                                threadShad = 1;
                                if (lights[i].shadowType == 2)//dynamic blur shadows
                                {
                                    float distFromInterToUV = length(intersection - uvWPos);

                                    if (minDistUVtoInter != 0)
                                    {
                                        minDistUVtoInter = min(distFromInterToUV, minDistUVtoInter);
                                    }
                                    else
                                    {
                                        minDistUVtoInter = distFromInterToUV;
                                    }
                                        
                                    float blurAmount = (minDistUVtoInter / distUVtoLight);
                                    blurAmount = pow(blurAmount, lights[i].blurPower);
                                    blurAmount *= lights[i].blurMultiplier;
                                
                                    threadShad -= blurAmount;
                                    threadShad = clamp(threadShad, 0, 1);
                                }
                                else
                                    // we need to go through every triangle for the dynamic blur since
                                    //it is based off the nearest triangle depth, and the first 
                                    //triangle in the loop may not be the nearest. However for solid shadow,
                                    //that's not necessary, so we can break the loop as soon as
                                    //we find a triangle that shades the texel
                                    break;
                            }
                        }
                    }
                }
                if (angleBetweenLightAndLocWNormalMap < 0)
                {
                    float4 val = 
                        (1 - distUVtoLight / lights[i].range) * abs(angleBetweenLightAndLocWNormalMap);
                    threadUnlit = (1 - threadShad) * val * val * lights[i].color * lights[i].intensity;
                }
            }
            totalResult[id.xy] += threadUnlit;
        }
    }
}
```

Apply Kernel
=====
```cuda
[numthreads(8, 8, 1)]
void Apply(uint3 id : SV_DispatchThreadID)
{
    float4 totCol = RlTLight[id.xy] + BLight[id.xy];
    totCol = saturate(totCol);
    totalResult[id.xy] = totCol;
    RlTLight[id.xy] = 0;
}
```

Improvements and Optimizations
====
----
  1. Allow for multiple meshes
  1. Allow for moving meshes
  1. BSP
  1. LOD textures and mesh 
  1. allow for lighting to work in editor
  1. changing lighting while in play mode should effect lighting in editor
  1. ambient light

  ```sadly, I know that barrycentric is actually spelled barycentric. But I really want it to be barry.```