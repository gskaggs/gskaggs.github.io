---
layout: post
title: The Great Sand Dunes
description: Physical simulation of sand particles to render photo-realistic images of desert dunes and ripples.
date: 2020-06-01 00:00:00
image: '/images/sand_dunes/procedural.png'
tags: [Projects]
---

![](/images/sand_dunes/title.png)

This was a course research project for CS 378H Computer Graphics Honors at UT Austin, which I completed in collaboration with my partner Rahul Krishnan. If you like this article, be sure to check out [the project's public GitHub repository.](https://github.com/gskaggs/Pupilometer-Analysis)

## How It Works
To create desert geometries, we simulated the physics of wind-blown sand particles. Additionally, to improve the realism of the images produced, we also implemented Catmull-Clark subdivision and procedurally generated noise.

### *Physical Simulation*
**Height Map:**
We represent the desert floor as 2D grid of height values which we call the *height map*.
At each time step of the simulation, the value at row *i* and column *j* of the map is updated with the following equation:
![](/images/sand_dunes/hmap.png)
where ![equation](https://latex.codecogs.com/gif.latex?%5Cinline%20%5Cdpi%7B200%7D%20%5Cfn_cm%20%5Ctiny%20n_d) is the number of particles deposited at that point, ![equation](https://latex.codecogs.com/gif.latex?%5Cinline%20%5Cdpi%7B200%7D%20%5Cfn_cm%20%5Ctiny%20n_b) is the number of particles leaving that point, and ![equation](https://latex.codecogs.com/gif.latex?%5Cinline%20%5Cdpi%7B200%7D%20%5Cfn_cm%20%5Ctiny%20%5Cdelta) is the diameter of any given particle. 
![equation](https://latex.codecogs.com/gif.latex?%5Cinline%20%5Cdpi%7B200%7D%20%5Cfn_cm%20%5Ctiny%20n_d) is determined by particle collisions with the floor, while ![equation](https://latex.codecogs.com/gif.latex?%5Cinline%20%5Cdpi%7B200%7D%20%5Cfn_cm%20%5Ctiny%20n_b) is either 1 or 0 and determined with a pseudo random number generator.

**Particles:**
The particles are stored in a linked list. At each time step their individual velocities and positions are updated with the following equations:

![](/images/sand_dunes/particle.png)

where the ![equation](https://latex.codecogs.com/gif.latex?%5Cinline%20%5Cdpi%7B200%7D%20%5Cfn_cm%20%5Ctiny%20F_g) is the force of gravity and ![equation](https://latex.codecogs.com/gif.latex?%5Cinline%20%5Cdpi%7B200%7D%20%5Cfn_cm%20%5Ctiny%20F_d) is the force of drag from the wind.
A particle's initial velocity is determined by the wind velocity and direction, while a particle's initial position is the physical location of the height map point where it was born.

**Collision:**
When a particle collides with the floor, its velocity is attenuated and then redirected upwards.
Then it can either stay alive and bounce if the magnitude of its resulting velocity is greater than some epsilon, or it can be deposited at that point in the floor otherwise.

For more details regarding any aspect of the physical simulation we did please see Wang and Hu's paper referenced below.

### *Catmull-Clark Subdivision*
The physical simulation above resulted in a mesh which was very polygonal. You could see individual triangles like how models were rendered on old game consoles such as the N64. To fix this, we implemented the classic Catmull-Clark algorithm whereby a surface is recursively subdivided and made incrementally smoother. For example, this image taken from [Wikipedia](https://en.wikipedia.org/wiki/Catmull%E2%80%93Clark_subdivision_surface) depicts a cube which is made to increasingly approximate a sphere:
![](/images/sand_dunes/cat-clark.png)

For a complete description of how the algorithm works, see the [Wikipedia article](https://en.wikipedia.org/wiki/Catmull%E2%80%93Clark_subdivision_surface) mentioned above.

### *Procedurally Generated Noise*
To add organic bumps and dune-like geometries to our desert, we implemented a form of noise generation whereby coarse noise is combined with upsampling increasingly finer grained noise. Graphically, the basics of this algorithm can be depicted simply:
![](/images/sand_dunes/noise.png)

### *Rendering*
We converted the polygonal mesh of the floor which results from the above algorithms into a triangular mesh which we rendered on the GPU using OpenGL.

## Example Images
#### Sand Simulation With Catmull-Clark:
![](/images/sand_dunes/ripples.png)

#### Procedural Noise With Sand Simulation and Catmull-Clark:
![](/images/sand_dunes/procedural.png)

#### Sand Simulation Without Catmull-Clark Subdivisions:
![](/images/sand_dunes/edgy.png)

#### Procedural Noise Without Sand Simulation:
![](/images/sand_dunes/bumpy.png)

## Referenced Works
The physical simulation math and physics came from:
*Real-Time Simulation of Aeolian Sand Movement and Sand Ripple Evolution* by Ning Wang and Bao-Gang Hu.
