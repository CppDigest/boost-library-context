# #570 Remove boost.assert and boost.static_assert dependencies [Merged]

> Username: mborland  
> Created at: 2021-03-14 09:56:32 UTC  
> Updated at: 2021-03-20 12:27:09 UTC  
> Merged at: 2021-03-18 19:26:03 UTC  
> Closed at: 2021-03-18 19:26:03 UTC  
> Url: https://github.com/boostorg/math/pull/570  

Convert all uses of `BOOST_ASSERT` and `BOOST_STATIC_ASSERT` to `BOOST_MATH_ASSERT` and `BOOST_MATH_STATIC_ASSERT` respectively. Macros expand depending on if `BOOST_MATH_STANDALONE` is defined.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-14 13:12:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/570#pullrequestreview-611694674  

📁 config/has_128bit_floatmax_t.cpp

```diff
  12 | 
  13 |- BOOST_STATIC_ASSERT(sizeof(boost::floatmax_t) * CHAR_BIT == 128);
  13 |+ BOOST_MATH_STATIC_ASSERT(sizeof(boost::floatmax_t) * CHAR_BIT == 128);
```

> Username: jzmaddock  
> Created_at: 2021-03-14 13:12:57 UTC  
> Updated_at: 2021-03-16 19:42:16 UTC  
> Url: https://github.com/boostorg/math/pull/570#discussion_r593898589  

Is there any reason not to just use `static_assert` here?

> Username: mborland  
> Created_at: 2021-03-14 13:16:32 UTC  
> Updated_at: 2021-03-16 19:42:16 UTC  
> Url: https://github.com/boostorg/math/pull/570#discussion_r593899085  

The message in `static_assert` did not become optional until C++17.

> Username: jzmaddock  
> Created_at: 2021-03-14 14:08:18 UTC  
> Updated_at: 2021-03-16 19:42:16 UTC  
> Url: https://github.com/boostorg/math/pull/570#discussion_r593906501  

Yes I know, we would have to invent some descriptive error messages.

> Username: mborland  
> Created_at: 2021-03-14 14:54:45 UTC  
> Updated_at: 2021-03-16 19:42:16 UTC  
> Url: https://github.com/boostorg/math/pull/570#discussion_r593912760  

That's fair.


---

## Comment 2

> Username: mborland  
> Created_at: 2021-03-16 17:33:39 UTC  
> Url: https://github.com/boostorg/math/pull/570#issuecomment-800466022  

This should be good to go now. There are fixes for the new failures in the misc test set; I am not sure why those started popping up.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-03-16 18:41:18 UTC  
> Url: https://github.com/boostorg/math/pull/570#issuecomment-800513977  

I'm still seeing instances of BOOST_MATH_STATIC_ASSERT_MSG that were missed?

---

## Comment 4

> Username: mborland  
> Created_at: 2021-03-16 18:54:51 UTC  
> Url: https://github.com/boostorg/math/pull/570#issuecomment-800522456  

> I'm still seeing instances of BOOST_MATH_STATIC_ASSERT_MSG that were missed?  
  
The only header they are still in is `TR1.hpp` in the below as an example:  
  
```  
template<class T> int fpclassify NO_MACRO_EXPAND(T x)  
{ BOOST_MATH_STATIC_ASSERT(sizeof(T) == 0); return false; } // must not be instantiated  
```  
  
Should the message be file a compiler bug? There should be nothing that yields `sizeof(T) == 0`

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-03-16 19:12:29 UTC  
> Url: https://github.com/boostorg/math/pull/570#issuecomment-800534267  

I'm seeing them in for example  example/root_elliptic_finding.cpp, plus include/boost/math/cstdfloat/cstdfloat_types.hpp and many others?  
  
This code:  
  
```  
template<class T> int fpclassify NO_MACRO_EXPAND(T x)  
{ BOOST_MATH_STATIC_ASSERT(sizeof(T) == 0); return false; } // must not be instantiated  
```  
  
Is used to force a hard error when a template which should never be instantiated is done so.  In this case, only the float/double/long double specializations are supported.  Looking at TR1, I *think* this probably comes under "undefined behaviour", so an error message along those lines should be fine.

---

## Comment 6

> Username: mborland  
> Created_at: 2021-03-16 19:43:58 UTC  
> Url: https://github.com/boostorg/math/pull/570#issuecomment-800553571  

> I'm seeing them in for example example/root_elliptic_finding.cpp, plus include/boost/math/cstdfloat/cstdfloat_types.hpp and many others?  
  
They are gone now. Easy enough because `BOOST_MATH_STATIC_ASSERT_MSG` expands to `static_assert` cleanly.  
  
> This code:  
>   
> ```  
> template<class T> int fpclassify NO_MACRO_EXPAND(T x)  
> { BOOST_MATH_STATIC_ASSERT(sizeof(T) == 0); return false; } // must not be instantiated  
> ```  
>   
> Is used to force a hard error when a template which should never be instantiated is done so. In this case, only the float/double/long double specializations are supported. Looking at TR1, I _think_ this probably comes under "undefined behaviour", so an error message along those lines should be fine.  
  
I added a message about it being UB.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-03-18 17:32:50 UTC  
> Url: https://github.com/boostorg/math/pull/570#issuecomment-802149222  

All green - good to go?

---

## Comment 8

> Username: mborland  
> Created_at: 2021-03-18 19:15:02 UTC  
> Url: https://github.com/boostorg/math/pull/570#issuecomment-802219007  

> All green - good to go?  
  
Yes; it is good to go.

---
