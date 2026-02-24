# #553 X3: Hide `unused_type` streaming operators [Merged]

> Username: Kojoley  
> Created at: 2019-11-19 18:49:58 UTC  
> Updated at: 2019-11-20 11:53:48 UTC  
> Merged at: 2019-11-19 21:37:20 UTC  
> Closed at: 2019-11-19 21:37:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/553  

The `unused_type` has an implicit constructor from any type, because of that  
ADL can find and match its streaming operator for any type. Removing they from namespace scope fixes #552.

---
