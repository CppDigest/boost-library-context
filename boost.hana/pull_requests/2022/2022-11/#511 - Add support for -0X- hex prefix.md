# #511 Add support for "0X" hex prefix. [Merged]

> Username: tzlaine  
> Created at: 2022-11-14 23:35:54 UTC  
> Updated at: 2022-11-15 14:16:20 UTC  
> Merged at: 2022-11-15 14:16:20 UTC  
> Closed at: 2022-11-15 14:16:20 UTC  
> Url: https://github.com/boostorg/hana/pull/511  

For integral types, you can use a capital `'X'` as part of a hex-prefix, like `0Xdeadbeef`.  This adds support for that to `ic_detail::parse()`.

---
