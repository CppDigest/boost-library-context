# #599 Turn off coveralls for POCL builds [Closed]

> Username: jszuppe  
> Created at: 2016-04-25 11:58:09 UTC  
> Updated at: 2016-04-25 21:12:51 UTC  
> Closed at: 2016-04-25 21:12:51 UTC  
> Url: https://github.com/boostorg/compute/pull/599  

It takes a long time to run POCL builds and having coveralls in them only makes them fail due to exceeding the maximum time limit (see https://travis-ci.org/boostorg/compute/jobs/125418210). We will still have coveralls stats from AMD APP builds with gcc (OpenCL 1.2 and 2.0).

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-04-25 13:24:54 UTC  
> Url: https://github.com/boostorg/compute/pull/599#issuecomment-214324904  

Ah.. It still sometimes takes too much time...   
  
We can consider not running tests in POCL+gcc builds (clang builds are OK) until POCL becomes faster or just ignore it and wait for the next release of POCL which should be faster (POCL master branch is now faster than current release [0.13] or at least was because we did not have those problems before switching from master to 0.13).

---
