# #84 - Compile-time error in perf_sort_by_key [Closed]

> Username: roshanrags  
> Created at: 2014-04-17 22:25:58 UTC  
> Updated at: 2014-04-20 16:22:44 UTC  
> Closed at: 2014-04-18 16:28:29 UTC  
> Url: https://github.com/boostorg/compute/issues/84  

The compiler says that `type_name` is not defined for `long`. I got it to work by adding a `type_name_trait` specialisation for `long` (fc98fcdeb9a1a32c399ce1426cb118953b8cc161). But Travis rejected it saying that `long` was being redefined. It took the specialisation for `long_` to be the same as `long`. I think this problem is coming because `cl_long` is platform independent but `long` is platform dependent, so it may or may not be the same as `cl_long`. How can this be addressed?  
  
Travis link : https://travis-ci.org/roshanr95/compute/jobs/23226800

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-04-18 01:33:09 UTC  
> Url: https://github.com/boostorg/compute/issues/84#issuecomment-40779965  

Ya, `long`, and `cl_long` may be different types (and are very possibly different sizes). You should use the Boost.Compute provided typedefs (like `long_` and `int4_`) which are portable to all OpenCL implementations and platforms.

---

## Comment 2

> Username: roshanrags  
> Created at: 2014-04-18 16:28:29 UTC  
> Url: https://github.com/boostorg/compute/issues/84#issuecomment-40822068  

Fixed in #90
