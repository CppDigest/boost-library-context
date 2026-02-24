# #59 - inherit histogram iterator from storage iterator [Closed]

> Username: HDembinski  
> Created at: 2018-07-07 20:49:24 UTC  
> Updated at: 2018-07-15 22:36:30 UTC  
> Closed at: 2018-07-15 22:36:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/59  

This should greatly simplify the iterator implementation and we can get rid of the iterator facade.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-07-15 22:36:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/59#issuecomment-405123612  

Ok, this is not simpler, it is more complicated. Both array_storage and adaptive_storage are not real containers and they don't have iterators. Inheriting from storage iterator would mean that both need an implementation of such an iterator. The current solution requires less code and fewer constrains on the storage class.
