# #44 Fix compilation errors on msvc caused by min/max macros. [Merged]

> Username: awulkiew  
> Created at: 2015-03-29 03:49:24 UTC  
> Updated at: 2015-03-29 18:51:13 UTC  
> Merged at: 2015-03-29 08:50:49 UTC  
> Closed at: 2015-03-29 08:50:49 UTC  
> Url: https://github.com/boostorg/test/pull/44  



---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2015-03-29 08:51:20 UTC  
> Url: https://github.com/boostorg/test/pull/44#issuecomment-87373016  

Thanks!  
Would you please point me to the failing tests?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-03-29 12:02:09 UTC  
> Url: https://github.com/boostorg/test/pull/44#issuecomment-87403609  

See e.g. distance_x_x Geometry tests for msvc in the develop regression matrix (http://www.boost.org/development/tests/develop/developer/geometry.html).  
  
E.g. http://www.boost.org/development/tests/develop/developer/output/teeks99-08e-win2012R2-64on64-boost-bin-v2-libs-geometry-test-algorithms-distance-distance_areal_areal-test-msvc-12-0-dbg-adrs-mdl-64-archt-x86-async-excpt-on-thrd-mlt.html

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2015-03-29 18:51:13 UTC  
> Url: https://github.com/boostorg/test/pull/44#issuecomment-87455016  

Thanks, I will add a test to catch this.

---
