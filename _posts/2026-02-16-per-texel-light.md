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

We will be conceptually and technically breaking down the process of the lighting in this video:
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

Anyway, first up take a look at the two pictures below. The first picture is a triangle with a texel in World Space, and the second picture is the same triangle and texel but in UV space. \
<img src="\images\lightScene\barryCentric\WTri.png">
<img src="\images\lightScene\barryCentric\UVTri.png">

Again, we need to know the texel's position in world space. By nature of a texel, we know its position in UV space, but we do not know its position in world space (so *TexW* is highlighted in red in the world space picture above). However we do know the position of the triangle in world space, ***and*** UV space. So if we can find the position of the texel relative ***only*** to the triangle in UV space, then we can map the texel into world space using that relative position. 

So basically here's one way to look at it. Just like you'd graph a point onto a graph, we are going to graph the texel onto the triangle. But instead of using the normal X and Y axis, we are going to use two sides of the triangle as the Axis'. And, I've chosen to specify that from every point on the triangle to another is 1 unit. That is, every side of the triangle is length 1 on it's relative graph. A picture of the triangle "graphed" might make it more clear. I've chose to use P1 to P2 (blue), and P1 to P3 (green) as the Axis'. \
<img src="\images\lightScene\barryCentric\graphTri.png">

Remeber, that picture applies whether the triangle is in World Space, or UV space since it is relative only to the triangle. So what we're gonna do is use the UV space triangle, and the UV space texel (since those are the values that we know), and map the texel onto the triangle. \
<img src="\images\lightScene\barryCentric\UVGraphTri.png">

And now that we have the relative position (as identified by the blue and green lines), we can multiply that same relative positon onto the world space texel! And now we know our texel position in world space! \
<img src="\images\lightScene\barryCentric\WGraphTri.png">

So to summarize, this method allows us to know a texel's position relative to a triangle. Then that relative position can be used to find the texel's position in whatever space we desire, so long as the same triangle is present. There are some more cool math things about this, which we'll discuss later. 

Lit
====

Now let's move away from triangles, and--for the sake of simplicity--lets instead lets consider a 2D pixelated scene. Everything black is geometry, and the red is a light. \
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

Visualized as the green line, the total distance of the light ray to the shaded texel, [***LR***]: \
<img src="\images\lightScene\blur\lightRay.png"> 

Visualized as the green line, the nearest distance along the light ray from the shaded texel to the shading object, [***NLR***]: \
<img src="\images\lightScene\blur\lightRayTex.png"> 

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
    int shadowType;
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
    float3 geoNormal;
    int used;
    float lit;
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

UvToWorld
====
```cuda
[numthreads(8,8,1)]
void UvToWorld (uint3 id : SV_DispatchThreadID)
{
  float a;
  float b;
  float c;
  float2 curUV = float2((float)id.x / (float)texRes, (float)id.y / (float)texRes);

  for (int i = 0; i < numTriangles; i++)
  {
    float2 p1 = triangles[i].p1Uv;
    float2 p2 = triangles[i].p2Uv;
    float2 p3 = triangles[i].p3Uv;

    float denom = (p2.y - p3.y) * (p1.x - p3.x) - (p2.x - p3.x) * (p1.y - p3.y);
    
    a = ((p2.y - p3.y) * (curUV.x - p3.x) - (p2.x - p3.x) * (curUV.y - p3.y)) / denom;
    b = ((p3.y - p1.y) * (curUV.x - p3.x) - (p3.x - p1.x) * (curUV.y - p3.y)) / denom;
    c = 1 - a - b;

    //inside triangle  
    if (0 - eps <= a && a <= 1 + eps && 0 - eps <= b && b <= 1 + eps && 0 - eps <= c && c <= 1 + eps) 
    {
      usedUVs[texRes * id.y + id.x].used = 1;

      float3 wp1 = triangles[i].p1WPos;
      float3 wp2 = triangles[i].p2WPos;
      float3 wp3 = triangles[i].p3WPos;

      usedUVs[texRes * id.y + id.x].worldLoc = a * wp1 + b * wp2 + c * wp3;
      nm[id.xy].g = 1 - nm[id.xy].g;
      nm[id.xy] = nm[id.xy] *  2 - 1;
      usedUVs[texRes * id.y + id.x].normal = 
          triangles[i].normal * nm[id.xy].b
          + triangles[i].binormal * nm[id.xy].g
          + triangles[i].tangent * nm[id.xy].r;
      usedUVs[texRes * id.y + id.x].normal = normalize(usedUVs[texRes * id.y + id.x].normal);
      usedUVs[texRes * id.y + id.x].geoNormal = triangles[i].normal;
      break;
    }
  }
}
```

Dynamic Light
==========
```cuda
[numthreads(8,8,1)]
void DynamicLight(uint3 id : SV_DispatchThreadID)
{
    float4 amb = 0; //float4(0.3/2, 0.15/2, 0.15/2, 1);
    //totalResult[id.xy] = amb; //ambient light

    int uvInd = texRes * id.y + id.x;
    usedUVs[uvInd].lit = 1;
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
            float angleBetweenLightAndLocGeo = dot(lightVec, usedUVs[uvInd].geoNormal);
            if (distUVtoLight < lights[i].range && angleBetweenLightAndLocWNormalMap < 0)
            {
                bool shadowHit = false;
                float distFromLighttoPlane = 0;
                if (lights[i].shadowType > 0)
                {
                    float minDistUVtoInter = 0;
                    float maxDistUVtoInter = 0;
                    for (int j = 0; j < numTriangles; j++)
                    {
                        //see if plane and lightVec are opposite
                        float parallelDot = dot(lightVec, triangles[j].normal); 

                        // if they are not, and not parallel, then they intersect
                        if (abs(parallelDot) >= 0) 
                        {
                            //https://www.youtube.com/watch?v=x_SEyKtCBPU
                            distFromLighttoPlane = 
                              dot(triangles[j].p3WPos - lights[i].loc, triangles[j].normal) 
                              / parallelDot; 
                        
                            if (distFromLighttoPlane >= distUVtoLight - eps)
                                continue;

                            float3 intersection = lights[i].loc + (distFromLighttoPlane * lightVec);

                            if (checkIntersectionInTri(intersection, triangles[j]))
                            {
                                shadowHit = true;
                                threadShad = 1;
                                if(lights[i].shadowType == 2)
                                {
                                    float distFromInterToUV = length(intersection - uvWPos);
      
                                    //this should be changed to only have blurring process
                                    //take place if the mindDist is changed.
                                    //That way it's not done every loop.
                                    if (minDistUVtoInter != 0)
                                    {
                                        minDistUVtoInter = min(distFromInterToUV, minDistUVtoInter);
                                        maxDistUVtoInter = max(distFromInterToUV, maxDistUVtoInter);
                                    }
                                    else
                                    {
                                        minDistUVtoInter = distFromInterToUV;
                                        // investigate how this could be used
                                        maxDistUVtoInter = distFromInterToUV;
                                    }
                                        
                                    float blurAmount = (minDistUVtoInter / distUVtoLight);
                                    blurAmount = pow(blurAmount, lights[i].blurPower);
                                    blurAmount *= lights[i].blurMultiplier;
                                
                                    threadShad -= blurAmount;
                                    threadShad = clamp(threadShad, 0, 1);
                                }
                                else
                                    // we need to go through every triangle for the dynamic 
                                    //blur since it is based off the nearest triangle depth,
                                    //and the first triangle in the loop may not be the nearest. 
                                    //However for solid shadow, that's not necessary, 
                                    //so we can break the loop as soon as we find a triangle
                                    //that shades the texel
                                    break;
                            }
                        }
                    }
                }
                if (angleBetweenLightAndLocWNormalMap < 0)
                {
                    float4 val = 
                      (1 - distUVtoLight / lights[i].range) * -1 * angleBetweenLightAndLocWNormalMap;
                    threadUnlit = 
                      (1 - threadShad) * val * val 
                      * usedUVs[uvInd].lit * lights[i].color * lights[i].intensity;
                }
            }
            totalResult[id.xy] += threadUnlit;
            //totalResult[id.xy] = clamp(totalResult[id.xy], 0, 1);
        }
    }
}
```
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
    float d01 = dot(v0, v1); //dot is shorthand for v0.x * v1.x + v0.y * v1.y + v0.z * v1.z
    float d11 = dot(v1, v1);
    float d20 = dot(v2, v0);
    float d21 = dot(v2, v1);

    float denom = d00 * d11 - d01 * d01;

    float a = (d11 * d20 - d01 * d21) / denom;
    float b = (d00 * d21 - d01 * d20) / denom;
    float c = 1.0f - a - b;
    
    return 
      0 - eps <= a && a <= 1 + eps && 0 - eps <= b && b <= 1 + eps && 0 - eps <= c && c <= 1 + eps;
}
```

Apply
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
  1. BSP
  1. LOD textures and mesh 
  1. allow for lighting to work in editor
  1. changing lighting while in play mode should effect lighting in editor