# #87 - Add a matrix_transpose example [Closed]

> Username: kylelutz  
> Created at: 2014-04-18 03:20:05 UTC  
> Updated at: 2014-06-25 04:19:13 UTC  
> Closed at: 2014-06-25 04:19:13 UTC  
> Url: https://github.com/boostorg/compute/issues/87  

Add a new example named `matrix_transpose` which demonstrates an OpenCL implementation of a matrix-transpose operation for a matrix of `float`'s.  
  
Perhaps also implement a few different versions and use the kernel/event-profiling functions to print out how long each takes and to demonstrate the different optimizations (see http://devblogs.nvidia.com/parallelforall/efficient-matrix-transpose-cuda-cc/).

---

## Comment 1

> Username: banche  
> Created at: 2014-05-09 02:25:57 UTC  
> Url: https://github.com/boostorg/compute/issues/87#issuecomment-42627932  

If nobody is doing it, I can do it this weekend

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-05-09 03:21:16 UTC  
> Url: https://github.com/boostorg/compute/issues/87#issuecomment-42630028  

No one that I know of. Thanks for working on this!

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-06-25 04:19:13 UTC  
> Url: https://github.com/boostorg/compute/issues/87#issuecomment-47059125  

Implemented in 8587c1b9.
