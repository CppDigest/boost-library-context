# #53 Try to reproduce issue with has_trivial_constructor and pair<deleted_… [Closed]

> Username: pdimov  
> Created at: 2017-10-30 13:05:05 UTC  
> Updated at: 2019-11-02 11:41:32 UTC  
> Closed at: 2018-01-31 18:56:23 UTC  
> Url: https://github.com/boostorg/type_traits/pull/53  

…construct,int>

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-10-30 14:51:31 UTC  
> Url: https://github.com/boostorg/type_traits/pull/53#issuecomment-340468118  

Reproduces, but only with `std::pair`, not with `holder`. No idea why. Perhaps specialize `is_default_constructible` for `pair` as Vinnie suggests and be done with it?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-01-31 18:56:23 UTC  
> Url: https://github.com/boostorg/type_traits/pull/53#issuecomment-362033757  

Closing as now fixed.

---
