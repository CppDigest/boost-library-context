# #591 - test_cxx11 in build.sh doesn't use CXXFLAGS [Open]

> Username: jwakely  
> Created at: 2020-05-05 16:53:26 UTC  
> Updated at: 2021-05-29 17:22:44 UTC  
> Url: https://github.com/boostorg/build/issues/591  

The `error_exit` function in `src/engine/build.sh` says:  
  
> For any toolset you can override the path to the compiler with the CXX  
> environment variable. You can also use additional flags for the compiler  
> with the CXXFLAGS environment variable.  
  
But `CXXFLAGS` is not used by the `test_cxx11` function. If the compiler you're trying to use to build B2 needs additional flags you have to pass them as part of the `CXX` env var instead, e.g. `CXX="/path/to/g++ -pthread" ./build.sh gcc`  
  
Also, redirecting all output and errors to `/dev/null` in `test_cxx11` makes it awkward to see *why* the test fails. You need to modify the `build.sh` script to find out why it's failing and what you might need to do to solve the problem.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:15 UTC  
> Url: https://github.com/boostorg/build/issues/591#issuecomment-850868300  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
