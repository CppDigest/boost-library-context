# #33 Describe CMAKE_BUILD_TYPE [Closed]

> Username: Flamefire  
> Created at: 2023-01-07 16:08:42 UTC  
> Updated at: 2023-01-08 14:50:22 UTC  
> Closed at: 2023-01-08 13:04:53 UTC  
> Url: https://github.com/boostorg/cmake/pull/33  

Further describe how to build Debug or Release variants.     
Note that not specifying any build type on single-config generators is not recommended as it will build unoptimized binaries without debug information unless environment variables are set up otherwise.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-01-07 16:11:23 UTC  
> Url: https://github.com/boostorg/cmake/pull/33#issuecomment-1374528506  

CMAKE_BUILD_TYPE is set to Release by default.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2023-01-07 16:14:38 UTC  
> Url: https://github.com/boostorg/cmake/pull/33#issuecomment-1374529382  

> CMAKE_BUILD_TYPE is set to Release by default.  
  
Wouldn't it make more sense to set it to Debug by default so the default is the same on all generators?  
  
Anyway the changes are still relevant independent of the default.

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-01-07 16:18:08 UTC  
> Url: https://github.com/boostorg/cmake/pull/33#issuecomment-1374530294  

No, it wouldn't. Nobody wants a debug Boost under POSIX by default.  
  
The instructions are correct as-is; `cmake ..` configures what one would typically want.  
  
It might make sense to add `CMAKE_BUILD_TYPE` to the Configuration variables section, though.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2023-01-07 16:31:12 UTC  
> Url: https://github.com/boostorg/cmake/pull/33#issuecomment-1374533970  

> No, it wouldn't. Nobody wants a debug Boost under POSIX by default.  
  
I agree. Just wanted to avoid confusion and don't know of a way to make "Release" the default for multi-config generators.  
  
> The instructions are correct as-is; `cmake ..` configures what one would typically want.  
>   
> It might make sense to add `CMAKE_BUILD_TYPE` to the Configuration variables section, though.  
  
Yes. I just wanted to clarify that the part about `--config` does only apply to e.g. Visual Studio, not when using e.g. MinGW or Cygwin with Makefiles and GCC/Clang on Windows.     
I thought about adding it in that section but I think it would require a rather lengthy or possibly ambiguous explanation where to add the flag. Also I think the advice to simply use `CMAKE_BUILD_TYPE` **and** `--config` may save quite some trouble and questions why things are not as expected.  
  
I think it makes sense to show how to configure a debug build (with a hint where to change that) as the shown `cmake --build` command for VS also does a debug build (by default) so we have at least some consistency there. But I'm open for alternatives.

---

## Comment 5

> Username: pdimov  
> Created_at: 2023-01-07 16:43:34 UTC  
> Url: https://github.com/boostorg/cmake/pull/33#issuecomment-1374537129  

I don't want to go into too much detail there, as this can easily overwhelm the reader.  
  
Under e.g. Linux in 80%+ of the cases Boost is built in a single configuration, which is Release, and our existing workflow (the way b2 works) is already optimized for that because it uses the so-called "system layout" (the libraries are called `libboost_foo.a`/`.so`).  
  
Note that there's no encoding of the build type in the name, so if you build Boost twice, once Debug, once Release, and install into the same directory, the result of the second build will overwrite the first one.  
  
If you want both Debug and Release, you can use the tagged layout, in which case you can - I think - build both and install into the same dir, and it might even work when consumed by CMake depending on CMAKE_BUILD_TYPE. (I haven't tried it.)  
  
But that's a complicated and niche scenario that (in my current opinion) doesn't really belong in a README. (Unless, of course, we receive multiple user questions about it. Then it will.)

---

## Comment 6

> Username: Flamefire  
> Created_at: 2023-01-07 17:07:19 UTC  
> Url: https://github.com/boostorg/cmake/pull/33#issuecomment-1374541478  

I simplified the change and added the variable description to the others

---

## Comment 7

> Username: Flamefire  
> Created_at: 2023-01-08 13:04:53 UTC  
> Url: https://github.com/boostorg/cmake/pull/33#issuecomment-1374831843  

Ok looks like you added an own description. Looks good to me so closing this.

---

## Comment 8

> Username: pdimov  
> Created_at: 2023-01-08 14:50:22 UTC  
> Url: https://github.com/boostorg/cmake/pull/33#issuecomment-1374853966  

Any opinion on https://github.com/boostorg/cmake/issues/35?

---
