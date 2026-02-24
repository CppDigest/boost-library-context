# #555 [Travis CI] Add POCL builds  [Merged]

> Username: jszuppe  
> Created at: 2016-02-11 19:33:36 UTC  
> Updated at: 2016-02-28 18:20:17 UTC  
> Merged at: 2016-02-28 18:20:17 UTC  
> Closed at: 2016-02-28 18:20:17 UTC  
> Url: https://github.com/boostorg/compute/pull/555  

This PR adds POCL builds and updates Khronos ICD builds (new ICD, builds moved to Travis CI container-based infrastructure).  
- I wasn't able to build POCL on Precise (container-based infrastructure) due to `lrt` (real time clock library) related error, so POCL is on Trusty.  
- I used direct linkage and because POCL lacks some OpenCL 1.2 runtime functions OpenCL 1.0 and 1.1 headers are used in POCL builds .    
- clang 3.7 is used in POCL builds , but not in Boost.Compute build due to this https://svn.boost.org/trac/boost/ticket/11664 error.  
- OpenCL 1.2 and 2.0 headers are used in Khronos ICD builds.  
  
After this PR there'll be 8 Linux builds and 1 OSX build:  
- 14.04 (Trusty), POCL, clang 3.5/gcc 4.8, OpenCL 1.0/1.1, build + tests  
- 12.04 (Precise, container-based infrastructure), Khronos ICD, clang 3.4/gcc 4.8, OpenCL 1.2/2.0, only build  
- OSX, clang, build + tests, allowed to fail

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-02-26 19:34:27 UTC  
> Updated_at: 2016-02-26 19:52:48 UTC  
> Url: https://github.com/boostorg/compute/pull/555#issuecomment-189446358  

I can try Intel OpenCL drivers later (Trusty builds seem to run on Intel Xeon) and maybe I'll even try build Boost.Compute on AppVeyor.  
  
Wow and somehow Coveralls worked! Only with gcc, but I think it's possible to make it work with clang too (http://logan.tw/posts/2015/04/28/check-code-coverage-with-clang-and-lcov/).

---

## Comment 2

> Username: ddemidov  
> Created_at: 2016-02-26 20:15:22 UTC  
> Url: https://github.com/boostorg/compute/pull/555#issuecomment-189463578  

I borrowed [JuliaGPU](https://github.com/JuliaGPU/OpenCL.jl)'s Travis setup, and now I have working tests with AMD APP SDK: https://github.com/ddemidov/vexcl/commit/d2af1ba3d902aeb6013c39d496c7ee7c0b4d2696.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-02-26 20:59:25 UTC  
> Url: https://github.com/boostorg/compute/pull/555#issuecomment-189481381  

@ddemidov Thanks! That's great! It works on container-based infrastructure too. I'll add AMD APP SDK with OpenCL 1.2 (if I recall correctly 2.0 is not supported for CPU in AMD APP SDK). I see that you also have working tests with Intel OpenCL SDK works on AppVeyor :+1:

---

## Comment 4

> Username: jszuppe  
> Created_at: 2016-02-27 16:34:48 UTC  
> Url: https://github.com/boostorg/compute/pull/555#issuecomment-189679458  

I've added AMD APP SDK builds. Now list of builds looks like this:  
- 14.04 (Trusty), POCL, clang 3.5/gcc 4.8, OpenCL 1.0/1.1, build + tests  
- 12.04 (Precise, container-based infrastructure), AMD APP SDK, clang 3.4/gcc 4.8, OpenCL 1.2/2.0, build + tests  
- OSX, clang, build + tests, allowed to fail  
- Khronos ICD builds are commented out

---

## Comment 5

> Username: kylelutz  
> Created_at: 2016-02-28 18:20:05 UTC  
> Url: https://github.com/boostorg/compute/pull/555#issuecomment-189917113  

Awesome! Thanks for all your hard work on this! It's great to see coveralls working again.

---
