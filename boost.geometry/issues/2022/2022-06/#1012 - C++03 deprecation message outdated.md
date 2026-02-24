# #1012 - C++03 deprecation message outdated [Closed]

> Username: Flamefire  
> Created at: 2022-06-05 09:20:00 UTC  
> Updated at: 2022-08-03 08:17:08 UTC  
> Closed at: 2022-07-13 12:39:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/1012  

There is a deprecation warning for C++03 targetting versions prior to Boost 1.75:  
  
 https://github.com/boostorg/geometry/blob/d31cd27488a236ad4f4c4bd4023dc99fbe279d66/include/boost/geometry/geometry.hpp#L27  
  
That should/can now be enforced as we will be at 1.80.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-07-13 12:39:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/1012#issuecomment-1183172596  

Thanks for the report! It's fixed and merged.

---

## Comment 2

> Username: Flamefire  
> Created at: 2022-07-13 13:46:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1012#issuecomment-1183246736  

You could add that requirement to the Jamfile(s) so that it isn't even tried to build on C++< 14  
Like https://github.com/boostorg/nowide/blob/fed43a678bf29e1e1e3a342e724961d198a3ac37/build/Jamfile.v2#L25  
  
As Geometry is header-only this may go into the test jamfiles. But not fully sure what the correct way is then. At least this will prevent the C++03/C++11 regression testers from even trying to compile the tests wasting resources on expected failures.

---

## Comment 3

> Username: mloskot  
> Created at: 2022-07-20 09:13:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/1012#issuecomment-1190029863  

Yes, it's only feasible for tests and examples.  
We have similar situation in GIL and the root `Jamfile` only declares projects for tests and examples  
https://github.com/boostorg/gil/blob/develop/Jamfile  
  
but the actual requirements like `cxx14_constexpr` go to `test/Jamfile`, e.g.  
https://github.com/boostorg/gil/blob/develop/test/Jamfile#L26-L37
