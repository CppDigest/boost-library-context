# #1016 - Findings from CI in Multiprecision on MSVC 14_0 [Closed]

> Username: ckormanyos  
> Created at: 2023-08-22 05:41:52 UTC  
> Updated at: 2023-08-22 14:50:56 UTC  
> Closed at: 2023-08-22 14:50:55 UTC  
> Url: https://github.com/boostorg/math/issues/1016  

In Multiprecision Issues like [this](https://github.com/boostorg/multiprecision/issues/564) we find stuff maybe relevant for Math.  
  
- MSVC 14.0 does not comprehend `/std:c++17` and the CI run(s) should use `[14, latest]` in the _standard_ matrix.  
- MSVC 14.0 seems to not comprehend the preprocessor _jargon_ for testing if it _has_ the header `<stdfloat>`. We need to do some preprocessor exclusions around [here](https://github.com/boostorg/math/blob/dc10fc04b29a356c3cf12c2052c45b0c3b3da5f2/include/boost/math/tools/promotion.hpp#L30). This one is probably running wild in the field so maybe we get to it for 1.84.  
  
On the second topic, something like below?  
  
```cpp  
#if !(defined(_MSC_VER) && (_MSC_VER <= 1900))  
#if __has_include(<stdfloat>)  
#  include <stdfloat>  
#endif  
```  
  
But I don't know 1f 14.1 comprehends `__has_include` so we need to check that too.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2023-08-22 07:11:42 UTC  
> Url: https://github.com/boostorg/math/issues/1016#issuecomment-1687603562  

I can take a crack at these items.  
  
Cc: @jzmaddock and @mborland

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-08-22 08:38:56 UTC  
> Url: https://github.com/boostorg/math/issues/1016#issuecomment-1687736547  

You should use #ifdef __has_Include to verify it's existence.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2023-08-22 08:49:23 UTC  
> Url: https://github.com/boostorg/math/issues/1016#issuecomment-1687754287  

> use #ifdef __has_Include to verify it's existence  
  
```cpp  
#if (defined(__has_include) && __has_include(<stdfloat>))  
#  include <stdfloat>  
#endif  
```  
  
Like that. Thx John.  
I will try. But it was a problem with lexical parsing so I'm not sure it works. I will report after trying it out...

---

## Comment 4

> Username: ckormanyos  
> Created at: 2023-08-22 09:27:16 UTC  
> Url: https://github.com/boostorg/math/issues/1016#issuecomment-1687816413  

See also #1017

---

## Comment 5

> Username: mborland  
> Created at: 2023-08-22 12:21:05 UTC  
> Url: https://github.com/boostorg/math/issues/1016#issuecomment-1688079189  

Since MSVC 14.0 is not really C++14 compliant this is another vote from me to drop support for it entirely. I don't think its worth the effort to add workarounds for something that we're going to continue to break as the code is modernized/features added.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2023-08-22 12:39:06 UTC  
> Updated at: 2023-08-22 12:39:51 UTC  
> Url: https://github.com/boostorg/math/issues/1016#issuecomment-1688106816  

> Since MSVC 14.0 is not really C++14 compliant this is another vote from me to drop support for it entirely. I don't think its worth the effort to add workarounds for something that we're going to continue to break as the code is modernized/features added.  
  
Yea. I'd be ready to drop it any time. It's going to be a nuisance with `constexpr` anyway, ...  
  
Yet, this workaround at the moment is just about finished. I'd just go ahead and finish it. Then we could continue the _drop_ _vc140_ discussion at another place/time?
