# #1098 - Conflicting checks for BOOST_CXX14_CONSTEXPR [Closed]

> Username: bartoli  
> Created at: 2024-02-19 10:07:47 UTC  
> Updated at: 2024-02-22 16:04:32 UTC  
> Closed at: 2024-02-22 16:04:32 UTC  
> Url: https://github.com/boostorg/math/issues/1098  

I built boost 1.84 using VS2015 U3 compiler.  
Then i am trying to use it from VS2022 IDE (using VS2015 compiler), on code that works with 1.63.  
  
There are compile errors because of conflicting BOOST_CXX14_CONSTEXPR definitions.  
  
First, a redefinition warning says that it is first defined in boost/config/details/suffix.hpp.  
Here, BOOST_NO_CXX14_CONSTEXPR is defined for this toolset (because _MSC_VER=1900 for vs2015 i presume), so BOOST_CXX14_CONSTEXPR is defined to nothing.  
But then later, boost has included boost/math/tools/config.hpp, which defines BOOST_CXX14_CONSTEXPR depending on the condition   
#if (__cplusplus > 201400L || _MSVC_LANG > 201400L)  
Which succceeds, then redefines BOOST_CXX14_CONSTEXPR to constexpr  
  
Then the code does not build because of those constexpr  
  
Shouldn't the check for the macro definition be the same in both places (the first one would seem the valid one?), or even , shouldn't the macro be defined in a single place?

---

## Comment 1

> Username: glenfe  
> Created at: 2024-02-19 19:35:06 UTC  
> Url: https://github.com/boostorg/math/issues/1098#issuecomment-1953064366  

Transferred to boostorg/math. I really don't like the idea of Math defining macros defined in Config. They should only define BOOST_MATH_CXX14_CONSTEXPR and only use that.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-02-20 15:56:58 UTC  
> Url: https://github.com/boostorg/math/issues/1098#issuecomment-1954524310  

You appear to be mixing including other Boost headers, with defining BOOST_MATH_STANDALONE - that's really not a good idea, if the rest of Boost is present, then don't define BOOST_MATH_STANDALONE and let Boost.Math use Boost.Config for configuration.  We might need to look at VS2015's configuration in standalone mode, but to be honest, I'm not sure we really support that old compiler in that usage mode anyway...  @mborland ?

---

## Comment 3

> Username: mborland  
> Created at: 2024-02-20 16:03:30 UTC  
> Url: https://github.com/boostorg/math/issues/1098#issuecomment-1954537782  

> You appear to be mixing including other Boost headers, with defining BOOST_MATH_STANDALONE - that's really not a good idea, if the rest of Boost is present, then don't define BOOST_MATH_STANDALONE and let Boost.Math use Boost.Config for configuration. We might need to look at VS2015's configuration in standalone mode, but to be honest, I'm not sure we really support that old compiler in that usage mode anyway... @mborland ?  
  
I doubt it works, but we don't test that configuration. Standalone assumes proper C++14 which VS2015 certainly does not have. I have wanted to drop support for it entirely for some time.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2024-02-20 16:06:20 UTC  
> Url: https://github.com/boostorg/math/issues/1098#issuecomment-1954543656  

I didn’t check but do the docs provide guidance on both these points? Don’t mix standalone with the rest of boost, and which compilers actually support knowingly standalone

---

## Comment 5

> Username: mborland  
> Created at: 2024-02-20 16:13:00 UTC  
> Url: https://github.com/boostorg/math/issues/1098#issuecomment-1954557391  

> I didn’t check but do the docs provide guidance on both these points? Don’t mix standalone with the rest of boost, and which compilers actually support knowingly standalone  
  
I guess I never wrote anywhere that explicitly states don't mix standalone with the rest of boost. Closest thing is that standalone Multiprecision is compatible with standalone math. Refactoring all the config macro names should allow interoperability but that's not really the use case. On the second point there is "Currently a compiler that is fully compliant with C++14" but then we list VS2015 as supported which is conflicting information.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2024-02-20 16:18:48 UTC  
> Url: https://github.com/boostorg/math/issues/1098#issuecomment-1954569970  

Something like, “it is best to use either purely standalone mode or math within the context of a complete Boost. But it is not intended to mix standalone mode with the rest of the Boost headers.”  
  
“Support for non-fully compliant compilers might be not fully tested or guaranteed. For instance standalone mode with VS2015 does not work, see issue X”  
  
or some stuff like that?

---

## Comment 7

> Username: mborland  
> Created at: 2024-02-20 16:29:18 UTC  
> Url: https://github.com/boostorg/math/issues/1098#issuecomment-1954591160  

> Something like, “it is best to use either purely standalone mode or math within the context of a complete Boost. But it is not intended to mix standalone mode with the rest of the Boost headers.”  
>   
  
I can add something like that.  
  
> “Support for non-fully compliant compilers might be not fully tested or guaranteed. For instance standalone mode with VS2015 does not work, see issue X”  
>   
  
I am nearly certain VS2015 is the only compiler we still test on that has broken C++14 support. I think we finally drop it which would make these caveats go away.

---

## Comment 8

> Username: ckormanyos  
> Created at: 2024-02-20 16:32:41 UTC  
> Updated at: 2024-02-20 16:33:10 UTC  
> Url: https://github.com/boostorg/math/issues/1098#issuecomment-1954598555  

It’s not entirely my call. But I think if we did ultimately decide to drop that particular compiler, we would  need an announce banner for 1.85 and maybe 1.86. It’s close to freezing 1.85.  
  
so if an announce banner is the way to go, we could get the banner into 1.85

---

## Comment 9

> Username: glenfe  
> Created at: 2024-02-20 20:14:08 UTC  
> Url: https://github.com/boostorg/math/issues/1098#issuecomment-1954982019  

Dropping non-standalone and having only standalone (even if it means dropping support for a particular compiler) would be better, in my opinion. I would rather avoid each library author inventing their own different ways of redefining macros from Config for their own brand or flavor of dual mode consumption support.
