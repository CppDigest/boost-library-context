# #2 - traits<T> allows T to be a [smart] pointer to a function object [Closed]

> Username: badair  
> Created at: 2016-03-26 06:53:21 UTC  
> Updated at: 2016-03-28 09:58:03 UTC  
> Closed at: 2016-03-28 09:58:03 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/2  

It would be a cool feature were it only consistent with `std::invoke`, which is more strict. This is accomplished in `general.hpp` and `normalize_reference.hpp`. Should this functionality be removed, or simply separated? Either way, it should probably not remain in its current state, since CallableTraits will probably be expected to be consistent with `std::invoke`.
