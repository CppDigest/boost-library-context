# #5 Fix compilation for Oracle Studio 12.5 [Merged]

> Username: Lastique  
> Created at: 2017-01-23 22:33:13 UTC  
> Updated at: 2017-01-30 12:10:37 UTC  
> Merged at: 2017-01-24 04:00:26 UTC  
> Closed at: 2017-01-24 04:00:26 UTC  
> Url: https://github.com/boostorg/array/pull/5  

The compiler expects a qualified dependent name after `typename`, so move `const` after the name.  
  
This is supposed to fix compiler errors like this:  
  
`"../boost/array.hpp", line 376: Error: expected a qualified name after "typename".`  
  
http://www.boost.org/development/tests/develop/developer/output/oracle-intel-S2-12-5_next-cpp11-boost-bin-v2-libs-atomic-test-atomicity-test-sun-12-5_next_cpp11-release-threading-multi.html

---

## Comment 1

> Username: mclow  
> Created_at: 2017-01-24 04:00:22 UTC  
> Url: https://github.com/boostorg/array/pull/5#issuecomment-274703813  

Please ping me to merge to master in a week or so after all the bots have cycled

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-01-30 12:10:37 UTC  
> Url: https://github.com/boostorg/array/pull/5#issuecomment-276047639  

On 01/24/17 07:00, Marshall Clow wrote:  
> Please ping me to merge to master in a week or so after all the bots  
> have cycled  
  
A friendly ping. Oracle compiler testers that have cycled no longer show   
this error.

---
