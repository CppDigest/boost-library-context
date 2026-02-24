# #41 - Provide a model of Foldable for Map [Closed]

> Username: ldionne  
> Created at: 2015-04-10 21:22:40 UTC  
> Updated at: 2015-04-11 20:55:37 UTC  
> Closed at: 2015-04-11 20:55:37 UTC  
> Url: https://github.com/boostorg/hana/issues/41  

I initially thought this would violate the consistency of the model for `Searchable`, but it does not because that consistency is only required for finite `Iterable`s, and `Map` is obviously not `Iterable`.
