# #37 fix narrowing warnings [Merged]

> Username: DanielaE  
> Created at: 2017-04-26 12:20:34 UTC  
> Updated at: 2017-12-29 06:42:19 UTC  
> Merged at: 2017-04-27 23:15:38 UTC  
> Closed at: 2017-04-27 23:15:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/37  

Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-04-26 12:22:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/37#issuecomment-297388279  

This makes (at least) msvc free of warnings about narrowing conversions.

---

## Review 2 [Commented]

> Username: eldiener  
> Created_at: 2017-04-27 23:08:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/37#pullrequestreview-35245042  

📁 include/boost/date_time/posix_time/conversion.hpp

```diff
  28 |   {
  29 |-     return ptime(gregorian::date(1970,1,1)) + seconds(t);
  29 |+     return ptime(gregorian::date(1970,1,1)) + seconds(static_cast<long>(t));
```

> Username: eldiener  
> Created_at: 2017-04-27 23:08:15 UTC  
> Url: https://github.com/boostorg/date_time/pull/37#discussion_r113822245  

Why is this cast to 'long' needed ?

> Username: eldiener  
> Created_at: 2017-04-27 23:15:20 UTC  
> Url: https://github.com/boostorg/date_time/pull/37#discussion_r113823124  

OK, I see that 'seconds' is constructed from a 'long'.


---
