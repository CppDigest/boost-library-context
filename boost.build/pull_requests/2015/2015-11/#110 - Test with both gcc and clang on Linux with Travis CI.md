# #110 Test with both gcc and clang on Linux with Travis CI [Merged]

> Username: tee3  
> Created at: 2015-11-20 17:53:37 UTC  
> Updated at: 2021-10-02 22:20:09 UTC  
> Merged at: 2015-12-14 07:03:43 UTC  
> Closed at: 2015-12-14 07:03:43 UTC  
> Url: https://github.com/boostorg/build/pull/110  

This does several very minor things and has no effect on the released version of Boost.Build at all.  
- use the container infrastructure for speed (sudo: false)  
- builds with both gcc and clang under Linux only  
  
This is in preparation for several other ideas enumerated below, which depend on this change.  These are not part of this change since they each have an issue that will need resolving before merging.  
- build on OS X (see https://github.com/tee3/build/tree/develop-travis-ci-osx)  
  - this will fail due to an issue in Travis CI OS X Python support  
- run the Python test suite (see https://github.com/tee3/build/tree/develop-travis-ci-python-tests)  
  - this fails **2** tests when Boost.Jam is built with `gcc`  
  - this fails **1** tests when Boost.Jam is build with `clang`

---

## Comment 1

> Username: vprus  
> Created_at: 2015-12-14 07:04:19 UTC  
> Url: https://github.com/boostorg/build/pull/110#issuecomment-164363854  

Thanks for the patches, merged to develop!

---

## Comment 2

> Username: tee3  
> Created_at: 2015-12-14 09:33:48 UTC  
> Url: https://github.com/boostorg/build/pull/110#issuecomment-164389110  

Great!

---
