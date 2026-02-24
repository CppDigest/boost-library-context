# #607 Allow Bernoulli numbers to support different precisions in different threads [Closed]

> Username: jzmaddock  
> Created at: 2021-04-11 12:40:47 UTC  
> Updated at: 2024-01-24 21:34:33 UTC  
> Closed at: 2021-04-17 15:50:02 UTC  
> Url: https://github.com/boostorg/math/pull/607  

Fixes issue found while working on thread safe variable precision multiprecision types.  
  
Without this we have a race condition where a thread may spot that the cache is of the wrong precision and invalidate it while another thread is still using it.

---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2021-04-11 12:59:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/607#pullrequestreview-633010136  

📁 include/boost/math/special_functions/detail/bernoulli_details.hpp

```diff
 516 |+    // at differing precisions:
 517 |+    //
 518 |+    static BOOST_MATH_THREAD_LOCAL bernoulli_numbers_cache<T, Policy> data;
```

> Username: mborland  
> Created_at: 2021-04-11 12:59:10 UTC  
> Updated_at: 2021-04-17 09:46:58 UTC  
> Url: https://github.com/boostorg/math/pull/607#discussion_r611185233  

It makes no difference, but `BOOST_MATH_THREAD_LOCAL` always expands to `thread_local`. I should have axed that macro like the other pre-C++11 workarounds.

> Username: jzmaddock  
> Created_at: 2021-04-11 18:52:52 UTC  
> Updated_at: 2021-04-17 09:46:58 UTC  
> Url: https://github.com/boostorg/math/pull/607#discussion_r611230718  

Nod.  I did wonder about that, I guess I was worrying about platforms which might not have any thread support at all, but I guess those can just `#define thread_local` and be done with it.


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-04-15 17:39:10 UTC  
> Url: https://github.com/boostorg/math/pull/607#issuecomment-820610993  

OK, we have a C++11 issue to think about here: it turns out that `thread_local` with gcc on windows is quite badly broken: in particular when dealing with non-trivial types.  It seems to be deleting the variable unconditionally and not checking to see if it was actually constructed :(    See for example https://github.com/boostorg/math/pull/607/checks?check_run_id=2318588556.  
  
I also worry we may see other platforms with similar issues down the road.  
  
I'll see if I can find a workaround, but anyone have any thoughts?

---

## Comment 3

> Username: mborland  
> Created_at: 2021-04-15 18:02:42 UTC  
> Updated_at: 2021-04-15 18:09:28 UTC  
> Url: https://github.com/boostorg/math/pull/607#issuecomment-820625385  

`boost/math/tools/config.hpp` has a macro defined with the same linked issue:  
  
```  
//  
// Some mingw flavours have issues with thread_local and types with non-trivial destructors  
// See https://sourceforge.net/p/mingw-w64/bugs/527/  
//  
#if (defined(__MINGW32__) || defined(__MINGW64__)) && !defined(_REENTRANT) && !defined(__clang__)  
#  define BOOST_MATH_NO_THREAD_LOCAL_WITH_NON_TRIVIAL_TYPES  
#endif  
```  
  
Maybe just disable non-trival type usage on mingw ([like in zeta](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/zeta.hpp#L891))? A similar issue was only [recently solved](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=83562).

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-04-15 18:40:36 UTC  
> Url: https://github.com/boostorg/math/pull/607#issuecomment-820650469  

> Maybe just disable non-trival type usage on mingw (like in zeta)?   
  
Ha!  I'd forgotten all about that: thanks Matt!  
  
> A similar issue was only recently solved.  
  
Nod.  Strangely I'm unable to reproduce locally, so at least some mingw versions are OK, they all report as Mingw-8.1.0 though :(

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-04-17 15:50:02 UTC  
> Url: https://github.com/boostorg/math/pull/607#issuecomment-821843748  

Closed in favour of https://github.com/boostorg/math/pull/609.

---
