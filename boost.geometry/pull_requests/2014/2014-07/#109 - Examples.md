# #109 Examples [Merged]

> Username: mkaravel  
> Created at: 2014-07-25 08:40:04 UTC  
> Updated at: 2014-07-25 23:19:06 UTC  
> Merged at: 2014-07-25 15:57:58 UTC  
> Closed at: 2014-07-25 15:57:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/109  

Remove inclusion of `boost/geometry/io/wkt/wkt.hpp`; it is included by `boost/geometry.hpp`

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-07-25 14:19:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/109#issuecomment-50155284  

I guess it's ok.  
  
But I'm wondering why WKT was included in examples in the first place. I see that IOs was added to `geometry.hpp` in 2011. Are the examples older and someone just forgot to update them? Or was the addition of IOs to `geometry.hpp` some quick decision? I'm curious what was the rationale because I'm not sure if it's a good idea to include IOs by default.  
  
Btw, IOs could get some cleanup. `io/io.hpp` only includes WKT and should probably include also other formats which are currently included by `geometry.hpp`. It also defines this `read<format_xxx>()` function which isn't defined if only `wkt.hpp` is included.

---
