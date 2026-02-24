# #117 - [String] Allow concatenating strings in one way or the other [Closed]

> Username: ldionne  
> Created at: 2015-06-14 18:32:31 UTC  
> Updated at: 2016-11-18 02:44:57 UTC  
> Closed at: 2016-11-18 02:44:57 UTC  
> Url: https://github.com/boostorg/hana/issues/117  

It could be via `Monoid` or via `MonadPlus`. I don't like concatenating `String`s with `plus`, but at the same time it is perfectly sound mathematically speaking. If we're able to make it a `MonadPlus` and use `concat`, that would be better (but it has to satisfy the laws).

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-03-15 15:52:22 UTC  
> Url: https://github.com/boostorg/hana/issues/117#issuecomment-196891729  

We could make a new concept `IntegralSequence<T>`, add impls for all functions for concepts that `Sequence` refines and add it in the concept checks for each of those functions. (a `TypeSequence` would be cool too)
