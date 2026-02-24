# #571 Windows related changes [Merged]

> Username: jszuppe  
> Created at: 2016-03-09 18:05:55 UTC  
> Updated at: 2016-03-10 23:17:47 UTC  
> Merged at: 2016-03-10 18:14:03 UTC  
> Closed at: 2016-03-10 18:14:04 UTC  
> Url: https://github.com/boostorg/compute/pull/571  

Fixes for bugs resulting in build errors on Windows that I found I was trying to run AppVeyor.  
- For Windows `Boost_USE_STATIC_LIBS` should be set `ON`.  
- `FindOpenCL.cmake` does not work for Intel OpenCL SDK, default FindOpenCL module introduced in CMake 3.1 works.  
- On Windows linking to Boost.Chrono and Boost.System (required by Thread) are required in some places (see [1.0.7](https://ci.appveyor.com/project/haahh/compute/build/1.0.7) and [1.0.19](https://ci.appveyor.com/project/haahh/compute/build/1.0.19) for proof - search for `LINK : fatal error LNK1104`)  
- Misc. warnings fixes (saving unsigned int as int without casting etc.).

---

## Comment 1

> Username: kylelutz  
> Created_at: 2016-03-10 18:14:16 UTC  
> Url: https://github.com/boostorg/compute/pull/571#issuecomment-194983451  

Awesome! Thanks for fixing all these!

---
