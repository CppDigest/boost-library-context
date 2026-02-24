# #418 Math standalone [Merged]

> Username: mborland  
> Created at: 2022-01-23 12:15:05 UTC  
> Updated at: 2022-03-01 08:24:38 UTC  
> Merged at: 2022-02-28 17:48:30 UTC  
> Closed at: 2022-02-28 17:48:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418  

Offer better context detection of Boost.Math standalone in conjunction with Boost.MP standalone.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-01-25 19:38:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1021541643  

@jzmaddock Almost all of my failures are in the concepts test where `fpclassify`/`isnan`/`isinf` are replaced with a macro that is designed to generate a compilation failure ([example](https://github.com/boostorg/multiprecision/runs/4925863564?check_suite_focus=true#step:17:18982)). Is there a workaround for this or something I am missing? I am just calling the `boost::math` implementation for almost all of these (see new `<boost/multiprecision/detail/fpclassify.hpp>`)

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-01-26 09:27:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1022016988  

Although they're declining in number, there are some platforms that define isnan/isinf etc as macros (rather like the min/max issue on Windows), so all those functions need to be macro expansion proof.  
  
For declarations we can do that with:  
  
```  
bool isnan BOOST_PREVENT_MACRO_SUBSTITUTION(T args); // or  
bool isnan /*Suppress macro expansion*/(T args);  
```  
  
For calls it's either:  
  
```  
(boost::math::isnan)(x) // or  
boost::math::isnan  BOOST_PREVENT_MACRO_SUBSTITUTION(x); // or  
boost::math::isnan /*Suppress macro expansion*/(x);  
```  
  
HTH, John.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-02-14 19:09:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1039450993  

Hi Matt, thanks for persevering with this, I think we can simply things here quite a bit, but I need to check through the changes line by line to be sure, so be prepared for a small blizzard of line-comments!

---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:09:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882074786  

📁 include/boost/multiprecision/concepts/mp_number_archetypes.hpp

```diff
 211 | {
 211 |-    return (boost::math::fpclassify)(arg.m_value);
 212 |+    return (boost::multiprecision::detail::fpclassify)(arg.m_value);
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:09:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806157711  

This can just be std::fpclassify here.


---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:11:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882075976  

📁 include/boost/multiprecision/cpp_bin_float.hpp

```diff
 374 | 
 368 |-       switch ((boost::math::fpclassify)(f))
 375 |+       switch ((boost::multiprecision::detail::fpclassify)(f))
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:11:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806158619  

Argument is required to be a floating point type, so this can be std::fpclassify.


---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:11:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882076576  

📁 include/boost/multiprecision/cpp_bin_float.hpp

```diff
 381 |          m_sign     = ((boost::math::signbit)(f) > 0);
 382 |+          #else
 383 |+          m_sign     = (f < 0);
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:11:47 UTC  
> Updated_at: 2022-02-14 19:11:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806159036  

We can use std::signbit here.


---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:13:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882078598  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
2431 |       return *this = one();
2432 | 
2427 |-    if ((boost::math::isinf)(a))
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:13:51 UTC  
> Updated_at: 2022-02-14 19:13:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806160558  

The function is enable_if'ed on is_floating_point so we can use std::isinf.


---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:14:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882078972  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
2432 | 
2427 |-    if ((boost::math::isinf)(a))
2433 |+    if ((boost::multiprecision::detail::isinf)(a))
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:14:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806160827  

And std::isnan, and so on down this function.


---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:16:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882080828  

📁 include/boost/multiprecision/cpp_int.hpp

```diff
1631 |       }
1632 | 
1631 |-       if ((boost::math::isinf)(a) || (boost::math::isnan)(a))
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:16:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806162159  

Again, only used for builtin floats, so we can use std::isinf etc here.


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:18:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882083524  

📁 include/boost/multiprecision/cpp_int/misc.hpp

```diff
 235 |             *result = boost::math::float_next(*result);
 236 |+             #else
 237 |+             if (std::is_floating_point<R>::value)
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:18:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806164060  

The function is already enable_if constrained by is_floating_point, so there's no need for this check here.


---

## Review 11 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:20:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882085301  

📁 include/boost/multiprecision/detail/fpclassify.hpp

```diff
  45 |+ inline constexpr bool isnan BOOST_PREVENT_MACRO_SUBSTITUTION (const T val) noexcept
  46 |+ {
  47 |+     return val != val;
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:20:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806165273  

I'm hoping we can ditch this whole header, but the val != val trick is known not to work for some optimisation levels.  Actually std::isnan breaks when some optimisations are enabled for gcc as well, but we have no control over that :(


---

## Review 12 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:22:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882087730  

📁 include/boost/multiprecision/detail/functions/trunc.hpp

```diff
  24 |+ inline T floor BOOST_PREVENT_MACRO_SUBSTITUTION (const T arg)
  25 |+ {
  26 |+     T result = 1;
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:22:50 UTC  
> Updated_at: 2022-02-14 19:22:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806166963  

what's wrong with std::floor/ceil?  Do we really use these with non-builtin types?


---

## Review 13 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:27:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882092171  

📁 include/boost/multiprecision/detail/functions/trunc.hpp

```diff
  63 |+ inline T trunc BOOST_PREVENT_MACRO_SUBSTITUTION (const T arg)
  64 |+ {
  65 |+     return (arg > 0) ? boost::multiprecision::detail::impl::floor(arg) : boost::multiprecision::detail::impl::ceil(arg);
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:27:14 UTC  
> Updated_at: 2022-02-14 19:27:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806170256  

I would tend to go with:  
  
```  
using std::floor;  using std::ceil;  
return (arg > 0) ? floor(arg) : ceil(arg);  
```


---

## Review 14 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:28:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882093208  

📁 include/boost/multiprecision/detail/generic_interconvert.hpp

```diff
 422 |       e -= shift;
 421 |-       long long ll = boost::math::lltrunc(val);
 423 |+       long long ll = boost::multiprecision::detail::lltrunc(val);
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:28:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806170995  

I notice while we're at it, that this function (and maybe subsequent ones) should have some using declarations for ldexp/frexp.  My bad on that!


---

## Review 15 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:31:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882095831  

📁 include/boost/multiprecision/detail/number_compare.hpp

```diff
 155 |    {
 155 |-       return (boost::math::isnan)(a);
 156 |+       return (boost::multiprecision::detail::isnan)(a);
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:31:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806172871  

I think we could use:  
  
```  
using std::isnan;  
return isnan /*prevent macro expansion*/(a);  
```


---

## Review 16 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:34:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882098930  

📁 include/boost/multiprecision/gmp.hpp

```diff
 305 |-       BOOST_MP_ASSERT(!(boost::math::isnan)(a));
 306 |-       #endif
 303 |+       BOOST_MP_ASSERT(!(boost::multiprecision::detail::isinf)(a));
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:34:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806175135  

This code has to work with float, double, long double and __float128, I think we can use `isinf` etc as unqualified calls (with macro expansion suppression), and update the using declarations at the start, plus BOOST_MP_FLOAT128_USING to bring the functions into scope.


---

## Review 17 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:34:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882099498  

📁 include/boost/multiprecision/gmp.hpp

```diff
1526 |-       BOOST_MP_ASSERT(!(boost::math::isnan)(a));
1527 |-       #endif
1522 |+       BOOST_MP_ASSERT(!(boost::multiprecision::detail::isinf)(a));
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:34:55 UTC  
> Updated_at: 2022-02-14 19:34:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806175522  

as above.


---

## Review 18 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:35:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882099652  

📁 include/boost/multiprecision/gmp.hpp

```diff
2514 |-       BOOST_MP_ASSERT(!(boost::math::isnan)(a));
2515 |-       #endif
2508 |+       BOOST_MP_ASSERT(!(boost::multiprecision::detail::isinf)(a));
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:35:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806175652  

as above.


---

## Review 19 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:36:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882100547  

📁 include/boost/multiprecision/mpc.hpp

```diff
1374 | {
1373 |-    if ((boost::math::isnan)(a))
1375 |+    if ((boost::multiprecision::detail::isnan)(a))
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:36:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806176329  

We can use std::isnan etc throughout here, as it's all double or long double.


---

## Review 20 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:37:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882102372  

📁 include/boost/multiprecision/mpfi.hpp

```diff
 287 |-       BOOST_MP_ASSERT(!(boost::math::isinf)(a));
 288 |-       BOOST_MP_ASSERT(!(boost::math::isnan)(a));
 287 |+       BOOST_MP_ASSERT(!(boost::multiprecision::detail::isinf)(a));
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:37:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806177670  

Argument is __float128, as before update BOOST_MP_FLOAT128_USING and make an unqualified call.


---

## Review 21 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:39:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882103644  

📁 include/boost/multiprecision/mpfr.hpp

```diff
 320 |-       BOOST_MP_ASSERT(!(boost::math::isinf)(a));
 321 |-       BOOST_MP_ASSERT(!(boost::math::isnan)(a));
 326 |+       BOOST_MP_ASSERT(!(boost::multiprecision::detail::isinf)(a));
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:39:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806178632  

Another where an update to BOOST_MP_FLOAT128_USING and unqualified call fixes things.


---

## Review 22 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:39:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882104029  

📁 include/boost/multiprecision/mpfr.hpp

```diff
 731 |-       BOOST_MP_ASSERT(!(boost::math::isinf)(a));
 732 |-       BOOST_MP_ASSERT(!(boost::math::isnan)(a));
 737 |+       BOOST_MP_ASSERT(!(boost::multiprecision::detail::isinf)(a));
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:39:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806178915  

as above.


---

## Review 23 [Commented]

> Username: jzmaddock  
> Created_at: 2022-02-14 19:40:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/418#pullrequestreview-882105086  

📁 include/boost/multiprecision/tommath.hpp

```diff
 250 |-       BOOST_MP_ASSERT(!(boost::math::isnan)(a));
 251 |-       #endif
 246 |+       BOOST_MP_ASSERT(!(boost::multiprecision::detail::isinf)(a));
```

> Username: jzmaddock  
> Created_at: 2022-02-14 19:40:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#discussion_r806179702  

Another where an unqualified call and update to BOOST_MP_FLOAT128_USING would fix things.


---

## Comment 24

> Username: jzmaddock  
> Created_at: 2022-02-14 19:44:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1039483648  

OK, apologies for the comment blizzard, but I think we can simplify things a lot with this.  
  
Note that in general, I'd rather not replace calls to boost::math::isnan to std::isnan as the former keeps working even when certain "fast-and-loose" optimisations are in place.  One option to reduce the #if logic, would be a macro that expands to either `using std::isnan` or `using boost::math::isnan` depending on which is available.

---

## Comment 25

> Username: mborland  
> Created_at: 2022-02-15 12:24:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1040210958  

I think this latest commit simplifies what you were looking for. It defines macros such as `BOOST_MP_ISINF` that either expands to `boost::math::isinf` if it is available, or SFINAEd definitions of `isinf` depending on if the type is supported by existing definitions (e.g. STL and libquadmath)

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2022-02-15 18:21:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1040621393  

Thanks Matt, can we just get rid of the floor/ceil code in place of std::floor/ceil, and then hopefully this should be good to go?

---

## Comment 27

> Username: mborland  
> Created_at: 2022-02-15 18:34:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1040634014  

> Thanks Matt, can we just get rid of the floor/ceil code in place of std::floor/ceil, and then hopefully this should be good to go?  
  
Locally I don't see any issues with std::floor/ceil so the superfluous code is gone now. Sorry I missed that one with the fixes earlier.

---

## Comment 28

> Username: mborland  
> Created_at: 2022-02-16 15:10:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1041594270  

@jzmaddock This should be good now. The 2 failures are timeouts on the CI's side.

---

## Comment 29

> Username: mborland  
> Created_at: 2022-02-17 13:38:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1042958521  

Upon a deeper look this is in fact not good to go. I spun up a VM that has nothing installed to see where we are at. There are more lingering instances that need to be removed/replaced.

---

## Comment 30

> Username: mborland  
> Created_at: 2022-02-23 10:35:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1048646024  

@jzmaddock This is now good to go. I pulled this branch into a VM with none of boost installed and it all compiles now.

---

## Comment 31

> Username: mariospr  
> Created_at: 2022-02-24 03:59:05 UTC  
> Updated_at: 2022-02-24 05:57:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1049470506  

FWIW, and just in case it helps: I was initially not being able to build this multiprecision library in standalone mode off the `develop` branch because of several references to `<boost/math/...>` not guarded by the `BOOST_MP_STANDALONE` define, when I tried to do it yesterday for the first time before finding this PR.  
  
But then I found this PR and I can confirm I could build in standalone mode now with the only addition of Boost.Config, which I had to download as well as it's still referenced from standalone_config.hpp (despite of the [comment saying that this package _"already includes a copy of Boost.Config so no other donwloads are required_"](https://github.com/boostorg/multiprecision/blob/develop/README.md?plain=1#L31), which I must say confused me a bit).  
  
Anyway, this is not a review (I'm not qualified at all to do so), just a quick comment based on my recent experience in case it helps in some way. Thanks a lot for this PR in any case!.

---

## Comment 32

> Username: mborland  
> Created_at: 2022-02-24 08:31:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1049609984  

>   
  
@mariospr I am glad to hear that this PR works for you. The intent is to have a packaged standalone release on the home page much like we do with [Boost.Math](https://github.com/boostorg/math/releases) that will include Boost.Config. The updates to the docs may have been pre-mature so I apologize for the confusion.

---

## Comment 33

> Username: mariospr  
> Created_at: 2022-02-24 10:48:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1049728201  

> @mariospr I am glad to hear that this PR works for you. The intent is to have a packaged standalone release on the home page much like we do with [Boost.Math](https://github.com/boostorg/math/releases) that will include Boost.Config.   
  
Ah, I see, that makes a lot of sense, thanks for clarifying.  
  
> The updates to the docs may have been pre-mature so I apologize for the confusion.  
  
No worries. I'm not too familiar with Boost so I was unsure after reading the README whether I was missing something out, but this explains things, thanks again.

---

## Comment 34

> Username: mborland  
> Created_at: 2022-02-28 11:52:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/418#issuecomment-1054179096  

Ping @jzmaddock. We are coming up on the beta/release window, and I think it's important this makes it.

---
