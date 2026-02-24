# #74 - mixmax.hpp violate min/max guidelines [Closed]

> Username: jzmaddock  
> Created at: 2020-12-23 11:58:53 UTC  
> Updated at: 2023-05-13 18:04:23 UTC  
> Closed at: 2023-05-13 18:04:23 UTC  
> Url: https://github.com/boostorg/random/issues/74  

Here: https://github.com/boostorg/random/blob/b8d5df31168a07e04a175ace751f0defa7be5e28/include/boost/random/mixmax.hpp#L65  
  
I'm seeing:  
  
```  
D:\a\math\boost-root\boost/random/mixmax.hpp(65): warning C4003: not enough arguments for function-like macro invocation 'min'  
```  
  
See: https://github.com/boostorg/math/issues/468

---

## Comment 1

> Username: kotika  
> Created at: 2020-12-23 15:43:37 UTC  
> Url: https://github.com/boostorg/random/issues/74#issuecomment-750356279  

On Wed, 23 Dec 2020 at 1:59 PM, jzmaddock <notifications@github.com> wrote:  
  
> Here:  
> https://github.com/boostorg/random/blob/b8d5df31168a07e04a175ace751f0defa7be5e28/include/boost/random/mixmax.hpp#L65  
>  
> I'm seeing:  
>  
> D:\a\math\boost-root\boost/random/mixmax.hpp(65): warning C4003: not enough arguments for function-like macro invocation 'min'  
>  
> See: boostorg/math#468 <https://github.com/boostorg/math/issues/468>  
>  
A quick google search for the warning locates the culprit.  
“ This seems to be due to (long existent) conflict between  
std::numeric_limits::max() and the max(a, b) macro defined in minwindef.h  
included as part of Windows.h.”  
  
   I will investigate what can be done to avoid the problem but here point  
out that min and max are mandated members of an RNG per ISO C++11.  
  
Best Regards,  
Kostas Savvidis  
  
  
<https://github.com/boostorg/math/issues/468>  
>

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-12-23 17:49:22 UTC  
> Url: https://github.com/boostorg/random/issues/74#issuecomment-750407036  

I should have been clearer:  
  
* We have a macro for this: BOOST_PREVENT_MACRO_SUBSTITUTION, so 👍   
  
```  
BOOST_STATIC_CONSTEXPR result_type min BOOST_PREVENT_MACRO_SUBSTITUTION() {return mixmax_min;}  
    BOOST_STATIC_CONSTEXPR result_type max BOOST_PREVENT_MACRO_SUBSTITUTION() {return mixmax_max;}  
```  
  
Should fix things.  
  
* There are tests to catch these errors in the inspect program, http://boost.cowic.de/rc/docs-inspect-develop.html#random shows several similar issues plus a few others.  
  
HTH, John.

---

## Comment 3

> Username: kotika  
> Created at: 2020-12-24 14:16:40 UTC  
> Url: https://github.com/boostorg/random/issues/74#issuecomment-750894947  

Thanks!   
mixmax.hpp fixed and PR created at https://github.com/boostorg/random/pull/76

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-12-26 18:32:28 UTC  
> Url: https://github.com/boostorg/random/issues/74#issuecomment-751383689  

Thanks!  
  
But the issue also appears to be present in other files:  
  
```  
boost/random/detail/qrng_base.hpp: *M* violation of Boost min/max guidelines on line 239  
boost/random/niederreiter_base2.hpp: *M* violation of Boost min/max guidelines on line 122  
boost/random/sobol.hpp: *M* violation of Boost min/max guidelines on line 61  
```
