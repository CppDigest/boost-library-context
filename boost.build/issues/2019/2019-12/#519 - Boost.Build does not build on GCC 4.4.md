# #519 - Boost.Build does not build on GCC 4.4 [Closed]

> Username: ned14  
> Created at: 2019-12-19 16:04:05 UTC  
> Updated at: 2019-12-19 16:11:20 UTC  
> Closed at: 2019-12-19 16:11:20 UTC  
> Url: https://github.com/boostorg/build/issues/519  

The release notes for https://www.boost.org/users/history/version_1_72_0.html claim that Boost has been tested on GCC 4.4. This cannot be true, as Boost.Build does not build on GCC 4.4.  
  
Please fix either the release notes (we actually only test earliest GCC 4.6) or Boost.Build.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2019-12-19 16:11:20 UTC  
> Url: https://github.com/boostorg/build/issues/519#issuecomment-567554798  

B2 needs a C++11 capable compiler to build the engine. But it is entirely usable with non-11 compilers outside of that. I.e. you can build Boost, or other projects, that are pre-11 *after* building b2 with an 11 compiler. As such only gcc 4.7 onward is supported for building b2 ATM. This requirement is mentioned in the B2 release notes: https://github.com/boostorg/build/releases/tag/4.0.0
