# #22 Guard diagnostic_information against null what() [Merged]

> Username: Oberon00  
> Created at: 2019-03-11 16:58:18 UTC  
> Updated at: 2019-03-17 05:39:31 UTC  
> Merged at: 2019-03-17 05:39:27 UTC  
> Closed at: 2019-03-17 05:39:27 UTC  
> Url: https://github.com/boostorg/exception/pull/22  

Printing a `char*` nullptr to an `std::ostream` is UB. Since diagnostic_information is used for debugging, I guess it would be good for it to be as robust as possible.

---

## Comment 1

> Username: zajo  
> Created_at: 2019-03-17 05:39:31 UTC  
> Url: https://github.com/boostorg/exception/pull/22#issuecomment-473618486  

Yes, very good. Thank you!

---
