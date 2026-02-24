# #31 - The current implementation of Iterable::any_of does not allow infinite Iterables [Closed]

> Username: ldionne  
> Created at: 2015-03-26 04:50:03 UTC  
> Updated at: 2015-06-03 22:29:24 UTC  
> Closed at: 2015-06-03 22:29:24 UTC  
> Url: https://github.com/boostorg/hana/issues/31  

This implementation is more compile-time efficient than the previous one, but it does not allow for infinite Iterables. We could provide this implementation only for Foldables, since Foldables must be finite.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-03 18:16:40 UTC  
> Url: https://github.com/boostorg/hana/issues/31#issuecomment-108554594  

This was introduced by 46b4def732ed13bd9b70ffb1f523f706c1e19dbc.
