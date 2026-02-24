# #1215 - emscripten builds need BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS [Closed]

> Username: NickelWenzel  
> Created at: 2024-10-22 16:00:00 UTC  
> Updated at: 2024-10-24 13:01:20 UTC  
> Closed at: 2024-10-24 13:01:20 UTC  
> Url: https://github.com/boostorg/math/issues/1215  

Using boost::math when compiling to wasm32 via emscripten is not possible out of the box right now because `BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS` is not defined.  
  
It seems like this is a regression that was introduced by the following PR #753 which seems to be related to #728 and #729.  
  
@jzmaddock to me it looks like uncommenting the `define` in `config.hpp`  
  
```  
#if (defined(__NetBSD__) || defined(__EMSCRIPTEN__)\  
   || (defined(__hppa) && !defined(__OpenBSD__)) || (defined(__NO_LONG_DOUBLE_MATH) && (DBL_MANT_DIG != LDBL_MANT_DIG))) \  
   && !defined(BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS)  
//#  define BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS  
#endif`  
```  
  
would solve the problem and i'd happily prepare a PR if you see no negative consequences for `NetBSD` and `hppa` since they would also get affected by this change.

---

## Comment 1

> Username: mborland  
> Created at: 2024-10-22 16:36:32 UTC  
> Url: https://github.com/boostorg/math/issues/1215#issuecomment-2429755012  

I would recommend making a PR that only affects your platform. We do not have NetBSD in the CI nor do we have hppa since it requires specific hardware.

---

## Comment 2

> Username: NickelWenzel  
> Created at: 2024-10-23 07:26:00 UTC  
> Url: https://github.com/boostorg/math/issues/1215#issuecomment-2431135540  

Thanks for getting back so swiftly :) I did that feel free to have a look at #1216.
