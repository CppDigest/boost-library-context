# #495 - hana::sort seems like it should work with hana::experimental::types but does't [Open]

> Username: catskul  
> Created at: 2021-10-23 02:08:43 UTC  
> Updated at: 2021-10-26 20:08:42 UTC  
> Url: https://github.com/boostorg/hana/issues/495  

While it might seem odd considering types don't have a lexicographical order where integral values would, with a predicate, the types might be sorted by some arbitrary manner.  
  
This is currently not possible because `types` is missing a `Sequence` specialization, but even if it did have it, it would choke in `sort.hpp` though I'm not informed enough to understand why.  
  
Not sure if this is helpful, but here's an example error when attempting to use a hacked up version where `Sequence` has been set to true for `types`  
  
```  
boost/hana/sort.hpp:65:36: error: no matching function for call to object of type ... >::(lambda at ...)'    
            using apply = decltype(std::declval<Pred>()(    
                                   ^~~~~~~~~~~~~~~~~~~~    
boost/hana/sort.hpp:118:51: note: in instantiation of template type alias 'apply' requested here    
                    Pred, T, (bool)Pred::template apply<T, Result1>::value,    
                                                  ^  
boost/hana/sort.hpp:128:35: note: in instantiation of template class 'boost::hana::detail::insertion_sort_impl<...  
```

---

## Comment 1

> Username: ricejasonf  
> Created at: 2021-10-23 03:32:49 UTC  
> Updated at: 2021-10-23 03:33:08 UTC  
> Url: https://github.com/boostorg/hana/issues/495#issuecomment-950049890  

I think the reason `types` can't be a `Sequence` is that any implementation of `make` would lose the run-time information for all of the elements.  
  
For example this would fail:  
```cpp  
static_assert(hana::front(hana::make<types_tag>(1, '2', 3.3)) == 1, "");  
```

---

## Comment 2

> Username: catskul  
> Created at: 2021-10-25 16:12:18 UTC  
> Url: https://github.com/boostorg/hana/issues/495#issuecomment-951083010  

Given the concept of the types container, the expectation is that I cannot hold runtime info, right? It would seem, from my naive perspective, that it should be possible to cleanly disable certain actions without disabling the use of the types container completely.  
  
But perhaps I'm missing a critical insight.  
  
At least conceptually, it's both possible and desirable to be able to sort a type container.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2021-10-26 18:00:08 UTC  
> Url: https://github.com/boostorg/hana/issues/495#issuecomment-952178953  

It's also possible and desirable to sort a `std::vector`:stuck_out_tongue:, but for that you would not use `hana::sort` which works  with objects that satisfy `hana::Sequence`.   
  
I don't know that it is explicitly forbidden. You still have to implement the functions required to make it a `Sequence`. Here is an example I posted on how to do that: https://stackoverflow.com/a/61879631/800347  
  
I do remember barking up this tree myself and getting shot down. In any case, Boost.Hana is optimized for heterogeneously typed containers that contain run-time information so it is slow for type-only computations compared to libraries such as Boost.Mp11. (It has a sort function.)
