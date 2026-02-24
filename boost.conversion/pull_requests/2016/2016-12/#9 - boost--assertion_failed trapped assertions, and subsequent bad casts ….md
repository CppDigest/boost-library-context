# #9 boost::assertion_failed trapped assertions, and subsequent bad casts … [Merged]

> Username: very-cool-name  
> Created at: 2016-12-16 07:51:33 UTC  
> Updated at: 2016-12-17 08:45:13 UTC  
> Merged at: 2016-12-17 08:45:03 UTC  
> Closed at: 2016-12-17 08:45:03 UTC  
> Url: https://github.com/boostorg/conversion/pull/9  

boost::assertion_failed trapped asserts, so bad casts were executed anyway. Now it throws exceptions for assert-like behavior.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2016-12-17 08:45:13 UTC  
> Url: https://github.com/boostorg/conversion/pull/9#issuecomment-267751085  

Thanks for the fix!

---
