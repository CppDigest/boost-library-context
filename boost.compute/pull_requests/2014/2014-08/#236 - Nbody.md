# #236 Nbody [Merged]

> Username: f-koehler  
> Created at: 2014-08-14 19:43:04 UTC  
> Updated at: 2014-08-15 03:28:59 UTC  
> Merged at: 2014-08-15 01:45:08 UTC  
> Closed at: 2014-08-15 01:45:08 UTC  
> Url: https://github.com/boostorg/compute/pull/236  

Added a OpenCl/OpenGL interop example using VBOs to simulate particles.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-08-14 19:57:36 UTC  
> Url: https://github.com/boostorg/compute/pull/236#issuecomment-52235152  

[![Coverage Status](https://coveralls.io/builds/1085413/badge)](https://coveralls.io/builds/1085413)  
  
Coverage remained the same when pulling **8e37337eeb813dac9a5d1e035b498f0e25d0d86b on f-koehler:nbody** into **bc360c4dd267441844e72aa5d4c9b4be463c6794 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-08-15 01:45:38 UTC  
> Url: https://github.com/boostorg/compute/pull/236#issuecomment-52266437  

Very cool! Merged!

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-08-15 01:52:57 UTC  
> Url: https://github.com/boostorg/compute/pull/236#issuecomment-52266824  

One nice thing to have would be allowing the user to specify the number of particles on the command line (e.g. `nbody --particles=1000`). The current count looks good on my desktop but my older laptop struggles to keep up. The `matrix_transpose` example has some code you could copy for using Boost.ProgramOptions to parse the command line.  
  
Another thing to try out would be using `float4` for the positions/velocities. This would mean a few extra bytes per particle but using aligned data types allows for more efficient computation on the GPU (and also you can use the builtin geometry functions like `norm()` and `length()`).  
  
Thanks for working on this!

---
