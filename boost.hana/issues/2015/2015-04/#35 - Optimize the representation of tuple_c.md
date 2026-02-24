# #35 - Optimize the representation of tuple_c [Closed]

> Username: ldionne  
> Created at: 2015-04-01 14:52:05 UTC  
> Updated at: 2016-01-30 15:27:04 UTC  
> Closed at: 2016-01-30 15:27:04 UTC  
> Url: https://github.com/boostorg/hana/issues/35  



---

## Comment 1

> Username: ldionne  
> Created at: 2015-04-01 14:55:30 UTC  
> Url: https://github.com/boostorg/hana/issues/35#issuecomment-88513364  

For example, we could experiment with specializing `_tuple<_integral_constant<...>...>`, and then `tuple_c<T, v...>` would just be `_tuple<_integral_constant<T, v>...>{}`. It might be possible to do the same for `Type`s. The fact that `decltype(type<T>)` is not exactly `_type<T>` but just derives from it is a bummer though. We might be able to detect a `make_tuple(type<T>...)` by doing something like  
  
``` cpp  
template <typename ...T>  
struct _tuple<T..., when_valid<T::is_type_hana_foobar_19u3128831uubs...>> {  
    ...  
};  
```  
  
which would work 99.9% of the time.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-01-30 15:21:34 UTC  
> Url: https://github.com/boostorg/hana/issues/35#issuecomment-177207672  

It just stroke me that `tuple_c` is not much more than `std::integer_sequence`. It seems like those that want to store compile-time integers should use `std::integer_sequence`  instead of a tuple, and if the full power of a `Sequence` is required, then `hana::to_tuple` can be used.

---

## Comment 3

> Username: ldionne  
> Created at: 2016-01-30 15:27:04 UTC  
> Url: https://github.com/boostorg/hana/issues/35#issuecomment-177210115  

Furthermore, regarding issues with efficiently storing `hana::type`s in sequences, #248 should bring an answer. I'll be closing this in favour of #248 and using `std::integer_sequence` when it makes sense.
