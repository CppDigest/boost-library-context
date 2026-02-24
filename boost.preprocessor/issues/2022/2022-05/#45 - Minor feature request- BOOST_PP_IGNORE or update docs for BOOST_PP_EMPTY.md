# #45 - Minor feature request: BOOST_PP_IGNORE or update docs for BOOST_PP_EMPTY [Open]

> Username: hadrielk  
> Created at: 2022-05-25 15:29:52 UTC  
> Updated at: 2022-05-26 03:39:16 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/45  

The current `BOOST_PP_EMPTY()` is not variadic, so its usefulness in `BOOST_PP_IF()` and the like is limited to when there are no arguments/params in the resultant call.  
  
For example, this doesn't work:  
```  
#define X(a, b) result(a, b)  
#define MACRO(a, b, c) BOOST_PP_IF(c, X, BOOST_PP_EMPTY)(a, b)  
```  
  
As explained in #34, changing the signature of `BOOST_PP_EMPTY()` is impractical, and another solution exists: `BOOST_VMD_EMPTY(...)` in `boost/vmd/empty.hpp`.  
  
So this is a very minor issue, but it would be nice if either:  
1. There were a `BOOST_PP_IGNORE(...)` to do this, or...  
2. The reference docs for the `BOOST_PP_EMPTY` could also point to the VMD macro to use instead.

---

## Comment 1

> Username: andry81  
> Created at: 2022-05-25 22:44:31 UTC  
> Updated at: 2022-05-25 22:46:05 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/45#issuecomment-1137919651  

Why not just use this: https://www.boost.org/doc/libs/1_77_0/libs/preprocessor/doc/ref/identity_n.html  
  
```cpp  
#define MACRO(a, b, c) BOOST_PP_IF(c, X, BOOST_PP_IDENTITY_N(BOOST_PP_EMPTY(),2))(a, b)  
```

---

## Comment 2

> Username: hadrielk  
> Created at: 2022-05-26 03:39:15 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/45#issuecomment-1138117461  

Oh, it's not that I can't solve it myself - I did - it's just a minor request to either document the VMD one or add one to PP.  
  
Not a big deal; just would be nice.
