# #9 Allow instrumentation in RTTI-off env and improve error reporting [Closed]

> Username: apolukhin  
> Created at: 2014-09-02 09:14:10 UTC  
> Updated at: 2024-01-24 20:52:30 UTC  
> Closed at: 2024-01-24 20:52:29 UTC  
> Url: https://github.com/boostorg/math/pull/9  

However, this pull request adds dependency to the Boost.TypeIndex library.  
  
With this patch GCC error reports:  
  
```  
boost::math::test_function<N5boost4math8concepts12real_conceptE>(N5boost4math8concepts12real_conceptE, N5boost4math8concepts12real_conceptE, N5boost4math8concepts12real_conceptE)   
```  
  
look like:  
  
```  
boost::math::test_function<boost::math::concepts::real_concept>(boost::math::concepts::real_concept, boost::math::concepts::real_concept, boost::math::concepts::real_concept)  
```  
  
with and without RTTI.  
  
In other words: almost the same look on all platforms with any compilation flags.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-09-02 17:38:06 UTC  
> Url: https://github.com/boostorg/math/pull/9#issuecomment-54188732  

I'm a bit unsure about this: looking at the code for pretty_name there appear to be two implementations: one for MSVC and everything else assumes that __cxa_demangle is available which is a GCC-specific API.  I happen to know that Boost.Math is used on a wide variety of other compilers including IBM xlc, the Cray compiler etc, which I presume type_Index won't compile on?  
  
So... for the diagnostic trace functions envoked by BOOST_MATH_INSTRUMENT_CODE, I don't see this adds much - they're pretty hardcore anyway ;-)  For the error handling code this can add genuine value, particularly for user defined types, but I think I'd like to see the "nameof" functions retained and use type_index for msvc and gcc (and emulations thereof) only.  
  
Thoughts?

---

## Comment 2

> Username: apolukhin  
> Created_at: 2014-09-03 07:53:58 UTC  
> Url: https://github.com/boostorg/math/pull/9#issuecomment-54263151  

Unfortunately I see no Cray or IBM xlc regression testers so it's hard to tell for sure. I had a strong feeling that all the Linux compilers support __cxa_demangle for compatibility reasons, but now I'm unsure.  
  
I'd love to see TypeIndex working on most platforms and all the `#ifdef`s residing in its sources, removing the macro/compatibility burden from users.  
  
Let's leave this pull request unmerged until I find some testers and ensure that TypeIndex works well on all the platforms including exotic ones.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2024-01-24 20:52:29 UTC  
> Url: https://github.com/boostorg/math/pull/9#issuecomment-1908896309  

Since this PR has been open for nearly a decade without resolution, I think we can safely close it.

---
