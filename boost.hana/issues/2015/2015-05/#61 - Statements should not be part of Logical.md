# #61 - Statements should not be part of Logical [Closed]

> Username: ldionne  
> Created at: 2015-05-04 16:36:52 UTC  
> Updated at: 2016-02-21 15:42:49 UTC  
> Closed at: 2016-02-21 15:42:49 UTC  
> Url: https://github.com/boostorg/hana/issues/61  

We could introduce statements like `while_` and `for_` that are not methods of a concept. We could also handle conditional statements that way. This would give us a clean(er) `Logical` concept that we might even want to rename to `Boolean` or something like that.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-02-21 15:42:49 UTC  
> Url: https://github.com/boostorg/hana/issues/61#issuecomment-186846243  

The strength of the current design is that it allows for both compile-time and runtime logicals. While that does not provide the simplest design, it's more flexible. I think this change might be more suited for a Hana-lite library that would implement the strict minimum for heterogeneous algorithms and containers, but not for this library.
