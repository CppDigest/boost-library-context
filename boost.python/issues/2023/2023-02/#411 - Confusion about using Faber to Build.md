# #411 - Confusion about using Faber to Build [Open]

> Username: fishbotics  
> Created at: 2023-02-03 18:34:16 UTC  
> Updated at: 2023-02-03 19:27:08 UTC  
> Url: https://github.com/boostorg/python/issues/411  

Hi,  
  
I'm trying to build the library (Ubuntu 20.04, Python 3.7 installed from Deadsnakes, Boost installed from Aptitude). When I clone the repo and run Faber, the build seems to succeed.   
  
When I run `faber test.report`, most of the tests pass. I get the following   
  
`test summary: 85 passes, 4 expected failures, 3 skipped`  
  
But, when I try to link against this from my other project--a project that uses the typical CMake search function--it can't find the library. Is there some install command I need to run in order to put the binary in the right place? Something similar to `make install`? I see the binary inside the repo at `src/g++-9/x86_64/shared/libboost_python37.so`  
  
Thanks!

---

## Comment 1

> Username: fishbotics  
> Created at: 2023-02-03 19:00:02 UTC  
> Url: https://github.com/boostorg/python/issues/411#issuecomment-1416275478  

FWIW, I managed to make it work by manually copying the shared objects (Python and Numpy) into the system directory using:  
  
```  
cp ~/boost-python/src/g++-9/x86_64/shared/libboost_* /usr/lib/x86_64-linux-gnu/  
```

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2023-02-03 19:27:08 UTC  
> Url: https://github.com/boostorg/python/issues/411#issuecomment-1416309715  

OK, thanks for that ! Perhaps it would be good to add an `install` target.
