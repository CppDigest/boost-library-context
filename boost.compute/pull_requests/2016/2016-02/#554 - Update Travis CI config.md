# #554 Update Travis CI config [Merged]

> Username: jszuppe  
> Created at: 2016-02-08 16:07:54 UTC  
> Updated at: 2016-02-09 19:15:37 UTC  
> Merged at: 2016-02-09 18:18:26 UTC  
> Closed at: 2016-02-09 18:18:26 UTC  
> Url: https://github.com/boostorg/compute/pull/554  

- disable running tests in linux build as fglrx does not work,  
- use OpenCL-ICD to in linux build (now we can at least build tests),   
- enable OSX build   
  - currently it's allowed to fail as there are many `Invalid Work Group Size` errors, some are legit, some are very weird  
  - only clang as building with gcc gives `Undefined symbols for architecture x86_64:` errors I don't know how to fix (see https://travis-ci.org/boostorg/compute/jobs/107840912#L3677)  
  
I'll later try POCL drivers + linux. I don't know if we can use Intel OpenCL driver as it works only on Intel CPU (Core, Xeon etc.) and I'm not sure if builds always run on machine with Xeon CPU.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-02-09 09:27:25 UTC  
> Url: https://github.com/boostorg/compute/pull/554#issuecomment-181776790  

I added `-Wno-deprecated-declarations` to all builds, because OpenCL 1.2 `cl.h` file is used and Boost.Compute contains calls to deprecated OpenCL API functions in order to maintain backward compatibility. It was explained here a few times. I don't know why after suppressing deprecated declaration warnings are still printed. There is also another solution to this problem:  
  
``` cpp  
#pragma GCC diagnostic push   
#pragma GCC diagnostic ignored "-Wdeprecated-declarations"  
//use deprecated stuff  
#pragma GCC diagnostic pop  
```  
  
`-Wno-c99-extensions` was added to OSX build, because otherwise this error occurs https://github.com/roboptim/roboptim-core/issues/74 `error: empty macro arguments are a C99 feature`. Maybe someone has better solution?  
  
In OSX build the newest available Boost is used, in Trusty build 1.55 version and in precise 1.48 version. I think we should add linux build with up-to-date Boost, unfortunately we would need to build it..

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-02-09 18:18:40 UTC  
> Url: https://github.com/boostorg/compute/pull/554#issuecomment-181989881  

Awesome! Thanks for getting this to work again!

---

## Comment 3

> Username: kylelutz  
> Created_at: 2016-02-09 18:21:59 UTC  
> Url: https://github.com/boostorg/compute/pull/554#issuecomment-181990959  

Yeah, building Boost from source may be to much work. Are there still time limits for Travis builds on the new infrastructure?  
  
Instead we may just want to wait until Ubuntu 16.04 comes out in a couple months. As this is the new LTS, hopefully Travis will update and we'll get the newer Boost with that.

---

## Comment 4

> Username: jszuppe  
> Created_at: 2016-02-09 19:15:37 UTC  
> Url: https://github.com/boostorg/compute/pull/554#issuecomment-182013853  

Well, we just need to build System, Filesystem, Test and Thread. I think it's possible to build it and cache it using ccache (https://docs.travis-ci.com/user/caching/#ccache-cache). But first I'll try to use POCL.  
  
As for time limits:   
  
> Because it is very common for test suites or build scripts to hang, Travis CI has specific time limits for each job. If a script or test suite takes longer than 50 minutes (or 120 minutes on travis-ci.com), or if there is not log output for 10 minutes, it is terminated, and a message is written to the build log.  
  
I'll also look into OSX-related bugs.

---
