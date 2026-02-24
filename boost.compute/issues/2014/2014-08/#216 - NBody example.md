# #216 - NBody example [Closed]

> Username: f-koehler  
> Created at: 2014-08-07 11:23:44 UTC  
> Updated at: 2014-08-15 03:28:59 UTC  
> Closed at: 2014-08-15 03:28:59 UTC  
> Url: https://github.com/boostorg/compute/issues/216  

I think it would be nice to have an example that shows how to share VBOs between OpenGL and OpenCL. One possible example would be to simulate bodys (gravitational force or as an Lennard-Jones-Fluid).  
  
If I find the time today I will work on it.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-08 02:09:15 UTC  
> Url: https://github.com/boostorg/compute/issues/216#issuecomment-51555798  

This would be great to have! There was a request for something similar made a couple weeks ago (see issue #198). Let me know if you need any help.

---

## Comment 2

> Username: f-koehler  
> Created at: 2014-08-08 08:21:11 UTC  
> Url: https://github.com/boostorg/compute/issues/216#issuecomment-51575219  

Thanks I already wrote an example, which is not yet finished. It creates VBO to store particle positions and a buffer to store velocities. I already can draw the particles (still need to change the viewing) and integrate there equations of motion using Euler's method (knowing that a symplectic integrator like Verlet would suit this scenario better, but we only want to demonstrate how this can generally be done). But somehow my particles are not moving yet for some reason.

---

## Comment 3

> Username: f-koehler  
> Created at: 2014-08-11 18:01:37 UTC  
> Updated at: 2014-08-11 18:22:42 UTC  
> Url: https://github.com/boostorg/compute/issues/216#issuecomment-51816689  

The first version can be found [here](https://github.com/f-koehler/compute/tree/nbody). For the simplicity of an example I used Euler's method even though I would prefer a symplectic integrator (bye bye energy conservation and Liouville's theorem). It can simulate ~100,000 (50,000 is in the code at the moment) particles in 3d space and with gravitational interaction in realtime (this is roughly where it begins to lag on my computer).  
  
The graphics are not fancy at all as this is actually my first OpenGL program (before I only used OpenCL to simulate and compute rendering was done externally). Maybe someone has ideas on how to improve the visual impression, the code in general or the performance.  
  
More documentation will be added later.

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-08-12 03:43:42 UTC  
> Url: https://github.com/boostorg/compute/issues/216#issuecomment-51870279  

Awesome! Ran it on my desktop and things look great! I'll take a look through the code and get back to you with more ideas.

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-08-15 03:28:59 UTC  
> Url: https://github.com/boostorg/compute/issues/216#issuecomment-52271157  

Implemented in PR #236
