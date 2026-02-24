# #139 - CMake build system [Open]

> Username: nemothenoone  
> Created at: 2019-07-14 12:15:47 UTC  
> Updated at: 2020-06-21 14:08:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/139  

Well, first of all, I'd like to ask, are there some chance for the CMake-based build system to be merged?  
  
I mean, I'm using it for the development, so I think that would be useful for others to have an optional support for it too.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-07-14 13:53:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/139#issuecomment-511204789  

I'm not a CMake user, and don't speak the language as it were, so while I'm not totally against it, such a submission would have to come with at least some minimal tests (hooked up to the CI system) to verify that it does actually work and pull in all the dependencies it needs.

---

## Comment 2

> Username: nemothenoone  
> Created at: 2019-07-14 13:54:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/139#issuecomment-511204850  

That is great. I have just the stuff. Pull request is coming.

---

## Comment 3

> Username: nemothenoone  
> Created at: 2020-06-20 05:46:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/139#issuecomment-646946331  

I believe it would be great to link this issue to PR #143, so it will close just as that PR will be merged.

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-06-20 12:39:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/139#issuecomment-646989446  

@nemothenoone : Is there a chance that the `CMake` system can produce a C++-20 module for Boost.Multiprecision, without jeopardizing backwards compatibility for users who consume the header-only version?

---

## Comment 5

> Username: nemothenoone  
> Created at: 2020-06-21 08:02:20 UTC  
> Updated at: 2020-06-21 08:03:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/139#issuecomment-647094345  

@NAThompson Definitely. Kitware guys already has modules support implementation going, there is an example available: https://github.com/mathstuf/cxx-modules-sandbox. So, I'ill simply introduce same mechanism usage in here.  
  
Or are you talking about generating the module definition itself?

---

## Comment 6

> Username: NAThompson  
> Created at: 2020-06-21 14:08:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/139#issuecomment-647133190  

> Or are you talking about generating the module definition itself?  
  
Sadly I haven't yet educated myself enough on how to do this to even understand this comment! What I had envisioned was that CMake would somehow make putting a module up on conan.io easier . . .
