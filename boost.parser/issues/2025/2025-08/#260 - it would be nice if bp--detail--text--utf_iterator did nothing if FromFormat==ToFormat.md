# #260 - it would be nice if bp::detail::text::utf_iterator did nothing if FromFormat==ToFormat [Closed]

> Username: andreasbuhr  
> Created at: 2025-08-05 14:43:49 UTC  
> Updated at: 2025-10-13 01:16:09 UTC  
> Closed at: 2025-10-13 01:16:09 UTC  
> Url: https://github.com/boostorg/parser/issues/260  

Because I know that boost.parser uses utf32 internally, I specify all string literals as utf32 strings, like `bp::lit(U"keyword")`.  
  
Now I see in the profiler that quite some time is spent in utf_iterator<4,4,...>::read().  
If FromFormat and ToFormat are identical, no transcoding is necessary. It would be nice if utf_iterator skipped all internal recoding if FromFormat and ToFormat are identical. Everything can be forwarded to curr().

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-10-13 01:16:09 UTC  
> Url: https://github.com/boostorg/parser/issues/260#issuecomment-3395583045  

This is a reasonable complaint, but I'm not going to address it now.  I'm waiting for P2728 to get farther along, and use that design -- which will eventually obviate this issue.
