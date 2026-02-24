# #76 - Move `value_initialized` from Utility [Open]

> Username: Kojoley  
> Created at: 2020-05-25 00:09:36 UTC  
> Updated at: 2020-05-25 02:13:41 UTC  
> Url: https://github.com/boostorg/core/issues/76  

The only user of that header is MPL, but it will allow to drop MPL dependence on Utility.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-05-25 00:19:33 UTC  
> Url: https://github.com/boostorg/core/issues/76#issuecomment-633322080  

`value_init.hpp` uses TypeTraits and Core is not allowed to depend on TypeTraits.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-05-25 00:28:53 UTC  
> Url: https://github.com/boostorg/core/issues/76#issuecomment-633323606  

I now remember that last time at looked at that, I came up with a way to insert the zero-initialization without using aligned storage, through a base class: https://godbolt.org/z/WMNRPc

---

## Comment 3

> Username: Kojoley  
> Created at: 2020-05-25 01:04:08 UTC  
> Url: https://github.com/boostorg/core/issues/76#issuecomment-633329461  

Nice one! BCC55 is fine with that way.

---

## Comment 4

> Username: pdimov  
> Created at: 2020-05-25 02:13:40 UTC  
> Url: https://github.com/boostorg/core/issues/76#issuecomment-633342750  

See https://github.com/boostorg/utility/pull/66.
