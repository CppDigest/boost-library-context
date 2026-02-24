# #1 Update rational.hpp [Merged]

> Username: jzmaddock  
> Created at: 2014-07-28 08:51:05 UTC  
> Updated at: 2015-04-02 02:23:54 UTC  
> Merged at: 2015-04-02 02:23:54 UTC  
> Closed at: 2015-04-02 02:23:54 UTC  
> Url: https://github.com/boostorg/rational/pull/1  

Access to the numerator and denominator is unnecessarily inefficient when using rational with multiprecision types (ie where copying is relatively expensive), this change returns const-references to numerator and denominator.  In all other respects existing code should be uneffected by this change.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-03-30 15:44:26 UTC  
> Url: https://github.com/boostorg/rational/pull/1#issuecomment-87728001  

This looks fine to me; but I don't have write access.

---

## Comment 2

> Username: ahmedcharles  
> Created_at: 2015-03-31 02:26:43 UTC  
> Url: https://github.com/boostorg/rational/pull/1#issuecomment-87903828  

Looks good to me too.

---
