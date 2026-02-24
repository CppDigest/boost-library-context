# #458 Add AppVeyor CI support [Closed]

> Username: kylelutz  
> Created at: 2015-05-19 03:55:47 UTC  
> Updated at: 2017-04-29 13:08:10 UTC  
> Closed at: 2017-04-29 13:08:10 UTC  
> Url: https://github.com/boostorg/compute/pull/458  



---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-05-28 02:55:28 UTC  
> Url: https://github.com/boostorg/compute/pull/458#issuecomment-106154850  

Ahh, thanks for posting that. Hopefully we can figure out how to get their SDK to run on AppVeyor.

---

## Comment 2

> Username: ddemidov  
> Created_at: 2015-05-28 10:31:20 UTC  
> Url: https://github.com/boostorg/compute/pull/458#issuecomment-106265212  

http://help.appveyor.com/discussions/suggestions/682-install-intel-opencl-code-builder

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-02-26 19:29:35 UTC  
> Url: https://github.com/boostorg/compute/pull/458#issuecomment-189444140  

It's possible to build and use Khronos ICD (see https://github.com/clMathLibraries/clSPARSE) on AppVeyor CI and maybe even POCL (see https://github.com/pocl/pocl/blob/master/README.Windows). Are you sure Intel drivers can be used on AppVeyor?

---

## Comment 4

> Username: jszuppe  
> Created_at: 2016-02-28 20:06:32 UTC  
> Url: https://github.com/boostorg/compute/pull/458#issuecomment-189935695  

I'm making progress with AppVeyor - see https://ci.appveyor.com/project/haahh/compute, but 1h build time limit does not seem to be enough for Boost.Compute + random tests fail (see https://ci.appveyor.com/project/haahh/compute/build/1.0.17 and https://ci.appveyor.com/project/haahh/compute/build/1.0.18).

---
