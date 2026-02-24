# #110 is_assignable_test: Fixed copy-paste error [Merged]

> Username: Kojoley  
> Created at: 2019-02-19 16:11:16 UTC  
> Updated at: 2019-02-26 12:12:00 UTC  
> Merged at: 2019-02-26 09:20:54 UTC  
> Closed at: 2019-02-26 09:20:54 UTC  
> Url: https://github.com/boostorg/type_traits/pull/110  

The test `is_assignable<noexcept_assignable&>` checks self-assignability, while the  
operator was defined for other type, so it was testing the non-noexcept implicit  
generated copy assignment operator instead.

---
