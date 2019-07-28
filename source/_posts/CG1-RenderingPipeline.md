---
title: Computer Graphics#1 - Rendering Pipeline
date: 2019-07-27 21:15:59
tags: 
- Computer Graphics
categories:
- Computer Graphics
img: https://ww1.yunjiexi.club/2019/07/27/nrWej.jpg
summary: Computer graphics is a sub-field of computer science which focuses on visual contents. Here we mainly refer to the 3D aspect, and discard 2D aspect like image processing, etc.
mathjax: true
---

## Overview

Rendering pipeline converts 3D scene into a 2D image phase by phase with given virtual camera, objects, light sources, textures, etc. We can generally divide it into 3 parts by order:

- Application stage
- Geometry stage
- Rasterization stage

![](https://ww1.yunjiexi.club/2019/07/27/nr10M.png)

---

## Application Stage

This stage is led by application and done by CPU. Tasks accomplished at this stage are below:

- Load the data from the hard drive into the CPU and do corresponding computations on the data, like collision detection.
- Culling: discard all the objects that aren't within the frustum and don't intersect with the frustum.
- Set up render states.
- Draw call.

>Notice:
>1. Render state defines that how a mesh should be rendered —— what shader to use, what material to apply, etc.
>2. CPU and GPU work independently. How can they communicate? There is something called `command buffer`. CPU can push some orders into the buffer and GPU retrieve orders from the buffer. Draw call is one of the orders which makes the GPU start rendering a particular frame.

Finally, CPU assigns the primitive type and deliver the data of vertices to the GPU.

---

## Geometry Stage

Geometry stage is a pipeline itself:

1. Vertex shader.
2. Tesselation shader.
3. Geometry shader.
4. Clipping.
5. Viewport mapping.

### Vertex Shader

Vertex shader is scriptable. It is used for transformations and vertex shading.

![](https://ww1.yunjiexi.club/2019/07/27/nrbwe.png "Coordinate System From LearnOpenGL")

>Vertex shading is also called `Gourand Shading`. Get more information about it [here]!

[here]: https://learnopengl.com/Lighting/Basic-Lighting

As you see, this process is actually a serie of matrix computations. Vertex shader sequentially carrys on these computatioins on the vertices one by one, converting them from `object space` into `clip space`. As to the following part, in fact, they are not parts of vertex shader, OpenGL will help you with that.

### Clipping

You may have noticed that the virtual camera can probably not cover a complete object through the whole transformation process. When this happens, the part outside the frustum will be discarded and then new vertices will be created. This takes place in the projection transformation process.

![](https://ww1.yunjiexi.club/2019/07/28/nrprp.png "Clipping")

In more detail, when we convert the coordinates from view space into clip space, we have to define a `projection matrix`. This matrix determines a range of coordinates, for example, $(-1000, 1000)$. Then through the projection transfromation, the coordinates within the range will be mapped onto the `NDC (Normalized Device Coordinates)` in which the coodinates are within $(-1.0, 1.0)$. A vertex like $(1200, 400, 600)$ will not appear, because its $x$ component is out of range.

>Notice:
>Clipping is kind of different from the culling we've mentioned. Culling is like a preprocess, and it discards the objects that are completely outside the frustum, while clipping reconstruct the objects that intersect with the frustum.

### Viewport Mapping

Once in the NDC, the `perspective division` is carried on. The $x$, $y$, $z$ components are divided by the homogeneous component $w$, and the 4D clip space coordinates are transform into 3D NDC coordinates. And then they are mapped onto screen space as fragments.

---

## Rasterization Stage

This stage goes through 3 sub-stages:

- Triangle Setup
- Triangle Traversal
- Fragment Shader

### Triangle Setup

The output from the preceding stage are daata of vertices, for a triangle, they are data of the 3 endpoints.

### Triangle Traversal

Triangle traversal is also called `scan conversion`. It checks if a pixel is covered by a triangle. If it is, then create a `fragment`. A fragment is something like a prototype of a pixel, which contains a bunch of data like depth and data from geometry stage. If a fragment is at an endpoint of the triangle, it contains the endpoint data, or its data come from interpolations.

### Fragment Shader

Use interpolated data as input, carry on corresponding shading computations pixel by pixel, like lighting, texture mapping, etc.

---

## Conclusion

This is just an overview of rendering pipeline which mainly goes through some fundamental concepts without any code. Rendering pipeline is a core of computer graphics. It is what you have to know, if you want to be engaged in related fields, and I highly recommend you to study on [LearnOpenGL] which is a really good online OpenGL tutorial with both concepts and program guide. See you guys next time!

[LearnOpenGL]: https://learnopengl.com

---

## Reference

[1] [LearnOpenGL]

[LearnOpenGL]: https://learnopengl.com