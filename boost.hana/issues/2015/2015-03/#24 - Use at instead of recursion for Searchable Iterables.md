# #24 - Use at instead of recursion for Searchable Iterables [Closed]

> Username: ldionne  
> Created at: 2015-03-22 16:33:31 UTC  
> Updated at: 2015-06-03 18:46:35 UTC  
> Closed at: 2015-06-03 18:46:35 UTC  
> Url: https://github.com/boostorg/hana/issues/24  

For `Iterables`, `Searchable` could use `at` instead of recursive iteration whenever that's more efficient, which is probably always the case anyway.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-03 18:46:35 UTC  
> Url: https://github.com/boostorg/hana/issues/24#issuecomment-108571287  

Turns out that we can't do that, because that would require knowing the length of the sequence. Since `Iterable`s are allowed to be infinite (i.e. they don't have to be `Foldable`), we can't peek at their `length`. What _could_ be done, however, is to have overloads for `Iterable`s that are also `Foldable`, in which case we could use index-based iteration. However, this is probably not _that_ useful, since `Tuple` already optimizes the `Searchable` minimal complete definition, and everything finite is essentially based on `Tuple`. Closing this.
