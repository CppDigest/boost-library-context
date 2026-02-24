# #1140 - try_make_tuple_like can't create an std::array [Closed]

> Username: beojan  
> Created at: 2026-01-30 22:35:30 UTC  
> Updated at: 2026-02-18 16:47:48 UTC  
> Closed at: 2026-02-18 16:47:48 UTC  
> Url: https://github.com/boostorg/json/issues/1140  

I'm trying to decode into an `std::array` of a non-default constructible class. I have a `tag_invoke` specialization for the class itself, and I've added the `is_sequence_like<std::array<TYPE>> : std::false_type {}` specialization.  
  
Unfortunately, this still doesn't quite work because line 318 of `boost/json/detail/value_to.hpp` uses:  
  
```cpp  
    return {  
        boost::system::in_place_value, T(std::move(*std::get<Is>(items))...});  
```  
  
instead of   
```cpp  
    return {  
        boost::system::in_place_value, T{std::move(*std::get<Is>(items))...}};  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2026-02-01 08:39:07 UTC  
> Url: https://github.com/boostorg/json/issues/1140#issuecomment-3830625002  

Do you create `TYPE` via a `tag_invoke` overload? I personally wouldn't recomment using the tuple overload for `std::array`, as it will instantiate quite a lot of templates for larger arrays. Maybe it makes sense for you to create a `tag_invoke` overload for `array<TYPE, N>` directly?  
  
Anyways, it probably makes sense to make the change you are suggesting. I don't expect there to be clashes between constructors of tuple  types that change behaviour because of a switch from `Tuple(a, b)` to `Tuple{a, b}`.

---

## Comment 2

> Username: beojan  
> Created at: 2026-02-02 17:00:29 UTC  
> Url: https://github.com/boostorg/json/issues/1140#issuecomment-3836498441  

Yes, `TYPE` has a `tag_invoke` overload, and I have currently put in a `tag_invoke` overload for `std::array<TYPE, N>` too.
