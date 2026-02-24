# #152  Fix regressions & update .yml scripts [Merged]

> Username: CromwellEnage  
> Created at: 2018-12-20 04:34:18 UTC  
> Updated at: 2019-01-21 18:57:32 UTC  
> Merged at: 2019-01-16 18:13:55 UTC  
> Closed at: 2019-01-16 18:13:55 UTC  
> Url: https://github.com/boostorg/graph/pull/152  

<boost/graph/named_function_params.hpp>  
* Original fix worked only when one boost::parameter::keyword was bound and passed in.  This fix now works when binding and passing in one or more boost::parameter::keyword objects.  
  
<boost/graph/isomorphism.hpp>  
* Make isomorphism_impl a metafunction that returns the function object result_type for the fix to work.  
  
"test/isomorphism.cpp"  
* Test the fix with two boost::parameter::keyword objects.  
  
".travis.yml"  
"appveyor.yml"  
* Update as per Peter Dimov's message announcing the merging of CMake into boostorg/develop.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-01-16 18:13:48 UTC  
> Url: https://github.com/boostorg/graph/pull/152#issuecomment-454882843  

Apologies, I missed this one, looks good, so merging.

---
