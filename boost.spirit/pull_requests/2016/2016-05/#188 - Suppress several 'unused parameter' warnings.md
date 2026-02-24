# #188 Suppress several 'unused parameter' warnings [Merged]

> Username: bebuch  
> Created at: 2016-05-12 19:08:20 UTC  
> Updated at: 2016-05-12 21:47:53 UTC  
> Merged at: 2016-05-12 21:47:53 UTC  
> Closed at: 2016-05-12 21:47:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/188  



---

## Comment 1

> Username: Kojoley  
> Created_at: 2016-05-12 19:13:42 UTC  
> Updated_at: 2016-05-12 19:36:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/188#discussion_r63079719  

Maybe it's better to use [`boost::ignore_unused(rule_name)`](http://www.boost.org/doc/libs/1_60_0/libs/core/doc/html/core/ignore_unused.html) ?

---

## Comment 2

> Username: bebuch  
> Created_at: 2016-05-12 19:39:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/188#issuecomment-218863445  

I added the ignore_unused()-function, it is more comprehensible.

---
