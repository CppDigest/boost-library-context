# #246 - Improving Nbody Example [Closed]

> Username: f-koehler  
> Created at: 2014-08-20 08:26:45 UTC  
> Updated at: 2017-04-24 21:36:57 UTC  
> Closed at: 2017-04-24 21:36:57 UTC  
> Url: https://github.com/boostorg/compute/issues/246  

I have to admit that I am not aware of most features of Boost.Compute's features. I am trying to follow the suggestions that were made [here](https://github.com/kylelutz/compute/pull/241). The current state is [here](https://github.com/f-koehler/compute/blob/50278e9d5493de5e85d49be0a521280183221180/example/nbody.cpp). I need help working further on it. (please do not wonder to much about that only the x component is unequal to zero, this was part of an experiment).  
  
I created a `boost::compute::vector<float4_>` to be able to use the `fill()`-algorithm. Is my assumption that the representing buffer obtained `boost::compute::vector<float4_>::get_buffer()` is a  segment of memory containing the `float4` linearly?  
  
I have no idea how to generate the initial positions randomly on the device. The problem is that OpenCL-OpenGL interaction is possible in one direction only. Can I create a `vector` or anything else that offers iterators from my VBO or is there another way to init it with random values?  
  
Also I seem to have messed up the drawing routine, no vertices are appearing. There is probably something wrong with my `glVertexPointer()` and/or my `glDrawArrays()` call (already tried to use. I am definitely lacking the experience with OpenGL to see what's wrong.  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-20 14:56:14 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-52789916  

No problem, just leaving suggestions. It's looking great!  
  
And yes, the buffer stored by `boost::compute::vector<T>` is linear and contiguous (just like with `std::vector<T>`). So you can just do `fill(m_velocity.begin(), m_velocity.end(), float4_(0, 0, 0, 0), queue);`. Also note that you don't have to allocate the actual vector object with `new`, you can just directly declare it like `compute::vector<float4_> m_velocity;` and then use it without the extra pointer indirection (and also you don't have to worry about `delete`ing it.  
  
As for using the random number generators, something like this should work (taken from the `random_walk.cpp` example):  
  
```  
compute::default_random_engine random_engine(queue);  
compute::uniform_real_distribution<float> random_distribution(-0.5f, 0.5f);  
  
random_distribution.generate(  
    random_values.begin(), random_values.end(), random_engine, queue  
);  
```  
  
You would then just have to copy the values to the OpenGL buffer after you create it (note you can create a OpenGL VBO with a NULL data field and it will just allocate space that can be written to from OpenCL).  
  
Hope this helps.

---

## Comment 2

> Username: f-koehler  
> Created at: 2014-08-20 17:00:18 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-52808243  

The reason I dynamically allocated the vector was, because it should be created in the same OpenCL context. By default it would youse a non-OpenCL-OpenGL-interop-context and I did not want to put my context creation and all the other stuff in a long initializer list (or this something you would do?).

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-08-21 01:36:58 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-52868640  

Ahh yeah, that makes sense now. Another option would be to use `boost::optional<boost::compute::vector<T>>` and initialize it once the OpenGL context is set up but this works fine.  
  
Any luck on figuring out the rendering problem? From a brief look, everything seems right to me.

---

## Comment 4

> Username: f-koehler  
> Created at: 2014-08-27 20:27:27 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-53635324  

I have no clue why the rendering is not working. Also tried to tell OpenGL when rendering that the VBO consists of 3 floats and set the stride to 4Byte. At the moment I have not to much spare time working on this problem.

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-08-29 05:04:23 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-53837717  

Ahh, I see the issue. In OpenGL, vertices are stored in [homogenous coordinates](http://en.wikipedia.org/wiki/Homogeneous_coordinates) so, in order to render correctly, the last component of the position should be `1.0` instead of `0.0`. The velocities are good to stay with `0.0` as the last component as they shouldn't update `position.w`. See the [`opengl_sphere`](https://github.com/kylelutz/compute/blob/master/example/opengl_sphere.cpp) example where we calculate the sphere vertex positions and set the `v.w` component to `1.0`  
  
Also, You can use the swizzling operators in OpenCL to simplify the kernels for updating only the `.x`, `.y`, and `.z` components like this (from the `updatePosition` kernel):  
  
```  
position[gid].xyz += dt*velocity[gid].xyz;  
```

---

## Comment 6

> Username: f-koehler  
> Created at: 2014-09-01 17:08:04 UTC  
> Updated at: 2014-09-01 17:08:41 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-54079432  

You were right with homogenous coordinates it works ([here](https://github.com/f-koehler/compute/blob/nbody/example/nbody.cpp) is the current status), but a new problem emerged. Even though I clear the buffer before every draw the first draw remains on the screen everything else works just fine.  
  
Your're suggestions with those swizzle operations do not seem to work. This is the error message when compiling  
  
```  
Boost.Compute: kernel compilation failed (-11)  
--- source ---  
__kernel void updateVelocity(__global const float4* position, __global float4* velocity, float dt, uint N) { uint gid = get_global_id(0); float4 r = { 0.0f, 0.0f, 0.0f, 0.0f }; float f = 0.0f; for(uint i = 0; i != gid; i++) { if(i != gid) { r = position[i]-position[gid]; f = length(r)+0.001f; f *= f*f; f = dt/f; velocity[gid] += f*r; } } } __kernel void updatePosition(__global float* position, __global const float* velocity, float dt) { uint gid = get_global_id(0); position[gid].xyz += dt*velocity[gid].xyz; }  
--- build log ---  
"/tmp/OCL23528T5.cl", line 1: error: expression must have struct or union type  
  __kernel void updateVelocity(__global const float4* position, __global float4* velocity, float dt, uint N) { uint gid = get_global_id(0); float4 r = { 0.0f, 0.0f, 0.0f, 0.0f }; float f = 0.0f; for(uint i = 0; i != gid; i++) { if(i != gid) { r = position[i]-position[gid]; f = length(r)+0.001f; f *= f*f; f = dt/f; velocity[gid] += f*r; } } } __kernel void updatePosition(__global float* position, __global const float* velocity, float dt) { uint gid = get_global_id(0); position[gid].xyz += dt*velocity[gid].xyz; }  
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        ^  
  
"/tmp/OCL23528T5.cl", line 1: error: expression must have struct or union type  
  __kernel void updateVelocity(__global const float4* position, __global float4* velocity, float dt, uint N) { uint gid = get_global_id(0); float4 r = { 0.0f, 0.0f, 0.0f, 0.0f }; float f = 0.0f; for(uint i = 0; i != gid; i++) { if(i != gid) { r = position[i]-position[gid]; f = length(r)+0.001f; f *= f*f; f = dt/f; velocity[gid] += f*r; } } } __kernel void updatePosition(__global float* position, __global const float* velocity, float dt) { uint gid = get_global_id(0); position[gid].xyz += dt*velocity[gid].xyz; }  
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                ^  
```  
  
Do they even improve performance?

---

## Comment 7

> Username: kylelutz  
> Created at: 2014-09-02 03:01:16 UTC  
> Updated at: 2014-09-02 03:01:26 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-54104978  

The swizzling operations should work, you just have to update the `updatePosition` kernel to take the `position` and `velocity` arguments as `float4*` instead of `float*`.  
  
As for efficiency, it should be about the same as manually writing out the operation for each component. Swizzling is just more compact in code.

---

## Comment 8

> Username: f-koehler  
> Created at: 2014-09-02 06:58:52 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-54115676  

Totally overlooked that this function was still usign `float*` instead of `float4*`. Also my drawing issue disappeared (fro some magical reason I do not understand). See PR [#254].(https://github.com/kylelutz/compute/pull/254)  
  
Maybe one should also improve the viewing (viewport and perspective), maybe to zoom out or let the user do that. I have not done this yet, because I do not what the best way to do this would be.

---

## Comment 9

> Username: f-koehler  
> Created at: 2014-09-04 13:52:09 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-54479704  

I also have a issue contributing via git, maybe I do not understand the workflow. Let's assume I worked on my branch `nbody` which is some commits ahead (as I worked on it) and some behind `upstream/master` branch as you worked on boost compute as well. Now I want to create a PR.  
1. I sync to your remote with `git fetch upstream` (I also do `push origin master` to update `origin/master`)  
2. I rebase my local `nbody` branch via `git rebase master` (to apply the patches that from `upstream/master`)  
3. I update `origin/nbody` via `git push origin nbody`  
4. I create a PR in the usual way.  
  
As you stated in PR [#254](https://github.com/kylelutz/compute/pull/254) something went wrong and I just do not get it.

---

## Comment 10

> Username: kylelutz  
> Created at: 2014-09-04 14:53:24 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-54488938  

That workflow is correct.  
  
If you look at the list of commits from PR #254, the `"Merge branch 'nbody' of github.com:f-koehler/compute into nbody"` (0731aba) commit looks to be the issue. It seems at some point after making the first two commits, you rebased on `upstream/master`, pushed that to `origin/nbody`, made another commit, then did `git merge origin nbody` or `git pull origin nbody` which generated the extra merge commit and duplicated the first two commits.  
  
Hope this helps.

---

## Comment 11

> Username: f-koehler  
> Created at: 2014-09-05 10:20:03 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-54608025  

We will see if that helped when I create my next PR ;)  
  
Sorry for my stupidity

---

## Comment 12

> Username: kylelutz  
> Created at: 2014-09-05 14:54:24 UTC  
> Url: https://github.com/boostorg/compute/issues/246#issuecomment-54635275  

No problem, while git can be a bit tricky/subtle sometimes, it's also always possible to cleanup and rearrange commits/branches after they've been made.
