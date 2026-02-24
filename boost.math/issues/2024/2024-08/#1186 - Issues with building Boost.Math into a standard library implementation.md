# #1186 - Issues with building Boost.Math into a standard library implementation [Open]

> Username: frederick-vs-ja  
> Created at: 2024-08-24 10:59:25 UTC  
> Updated at: 2024-08-24 11:19:07 UTC  
> Url: https://github.com/boostorg/math/issues/1186  

Currently MSVC STL builds Boost.Math into its static library, which cause the static library contains non-_Uglified linking symbols and causes non-conforming linking behavior. See microsoft/STL#362.  
  
I'm trying to resolve this in Boost.Math by adding the `BOOST_MATH_NAMESPACE` macro  
```C++  
#ifndef BOOST_MATH_NAMESPACE  
#define BOOST_MATH_NAMESPACE math  
#endif  
```  
and then replace every occurrence of `math` which is a namespace with `BOOST_MATH_NAMESPACE`.  
  
With the changes above, MSVC STL's [`special_math.cpp`](https://github.com/microsoft/STL/blob/62205ab155d093e71dd9588a78f02c5396c3c14b/stl/src/special_math.cpp) can define `BOOST_MATH_NAMESPACE` as `_Math` (or some other _Uglified identifier, e.g. `__math__`) before including `<boost/math/*>` and then use `::boost::BOOST_MATH_NAMESPACE::*` to get rid of non-_Uglified symbols.  
  
But the changes need to touch 500+ files in Boost.Math and make maintenance a bit harder (every namespace `math` should be spelt as `BOOST_MATH_NAMESPACE` later). Not sure whether this approach is OK.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2024-08-24 11:18:12 UTC  
> Updated at: 2024-08-24 11:19:07 UTC  
> Url: https://github.com/boostorg/math/issues/1186#issuecomment-2308358953  

Hi @frederick-vs-ja this issue is also being tracked in #769. None of us has actually gotten around to progressing on it so far.  
  
> need to touch 500+  
  
I believe we are generally aware of this and still consider the request to be reasonable.  
  
I think we just need to discuss:  
  
- Is this actually is the best approach.  
- Who will actually do it (I wanted to give it a try, but find myself hopelessly busy with other projects)  
- Should the outer namespace be only for standalone Math only or for all of Boost.Math's configs?  
- When to do it?  
- Does anything need to be done for Multiprecision? I suspect there might be some moderately harmless cross-talk to Multiprecision.  
  
Cc: @jzmaddock and @mborland and @NAThompson
