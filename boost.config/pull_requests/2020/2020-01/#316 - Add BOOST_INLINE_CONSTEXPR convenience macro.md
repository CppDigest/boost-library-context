# #316 Add BOOST_INLINE_CONSTEXPR convenience macro [Merged]

> Username: glenfe  
> Created at: 2020-01-01 18:11:11 UTC  
> Updated at: 2020-01-24 19:17:26 UTC  
> Merged at: 2020-01-24 19:17:26 UTC  
> Closed at: 2020-01-24 19:17:26 UTC  
> Url: https://github.com/boostorg/config/pull/316  



---

## Comment 1

> Username: glenfe  
> Created_at: 2020-01-24 13:15:53 UTC  
> Url: https://github.com/boostorg/config/pull/316#issuecomment-578125442  

@jzmaddock , I'm guessing you're busy. Any objections to this being merged?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-01-24 18:05:05 UTC  
> Url: https://github.com/boostorg/config/pull/316#issuecomment-578237645  

None but...  
  
* How useful is this if inline variables are not supported - do we not end up with multiple definitions?  
* constexpr implies inline, so isn't declaring a variable simply as BOOST_CONSTEXPR_OR_CONST the same thing anyway?

---

## Comment 3

> Username: glenfe  
> Created_at: 2020-01-24 18:43:28 UTC  
> Url: https://github.com/boostorg/config/pull/316#issuecomment-578251583  

At file scope constexpr does not imply inline linkage, it is internal linkage.   
  
So, for example: https://github.com/boostorg/core/blob/develop/include/boost/core/empty_value.hpp#L142-L148   
  
This would have internal linkage and have a definition in each translation unit. Which is fine because it's the best we can do pre-C++17.  
  
This is actually the use case which prompted Andrey to add BOOST_INLINE_VARIABLE in the first place.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-01-24 19:17:21 UTC  
> Url: https://github.com/boostorg/config/pull/316#issuecomment-578263420  

OK, best merge this then....

---
