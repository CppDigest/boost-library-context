# #43 irange: introduce one-parameter variant of irange [Merged]

> Username: timblechmann  
> Created at: 2016-01-17 10:45:09 UTC  
> Updated at: 2017-11-15 19:48:19 UTC  
> Merged at: 2017-11-15 19:48:19 UTC  
> Closed at: 2017-11-15 19:48:19 UTC  
> Url: https://github.com/boostorg/range/pull/43  

this simplifies the use of `boost::irange`, as `boost::irange( 0, foo )`;  
may compile or not depending on the platform-specific integer type of  
`foo`, requiring an explicit cast of `0` to `decltype(foo)`.

---

## Comment 1

> Username: nooploop  
> Created_at: 2017-11-15 18:50:24 UTC  
> Url: https://github.com/boostorg/range/pull/43#issuecomment-344690585  

Please merge this and close #45

---
