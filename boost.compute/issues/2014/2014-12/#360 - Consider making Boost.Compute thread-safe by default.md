# #360 - Consider making Boost.Compute thread-safe by default [Open]

> Username: kylelutz  
> Created at: 2014-12-29 18:02:46 UTC  
> Updated at: 2017-03-21 04:14:52 UTC  
> Url: https://github.com/boostorg/compute/issues/360  

Consider making the `BOOST_COMPUTE_THREAD_SAFE` macro enabled by default. For C++03 compilers or C++11 compilers without `thread_local` support, this would make the library non-header-only (would now require linking with Boost.Thread). There may also be other solutions to reach this goal.  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-29 22:08:33 UTC  
> Url: https://github.com/boostorg/compute/issues/360#issuecomment-68308736  

Also related to issue #359.
