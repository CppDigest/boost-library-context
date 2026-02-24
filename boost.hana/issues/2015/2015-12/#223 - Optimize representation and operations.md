# #223 - [map] Optimize representation and operations [Closed]

> Username: ldionne  
> Created at: 2015-12-17 21:28:30 UTC  
> Updated at: 2016-02-17 05:54:56 UTC  
> Closed at: 2016-02-17 05:54:56 UTC  
> Url: https://github.com/boostorg/hana/issues/223  

`hana::map` is currently fairly inefficient. It is implemented on top of `hana::tuple`, which is an index-based container. This causes `find_if`, `any_of`, `at_key`, `erase_key`, `insert` and friends to be implemented in terms of a linear search in the underlying tuple of pairs. This is unacceptably slow at compile-time.  
  
By changing the representation of `hana::map`, we could most likely achieve interesting speedups. Consider for example the following representation (which is extremely to the underlying implementation of `hana::tuple`):  
  
``` c++  
template <int index, typename Value>  
struct holder { Value value; };  
  
template <typename Indices, typename ...Pairs>  
struct map_impl;  
  
template <int ...index, typename ...Key, typename ...Value>  
struct map_impl<std::integer_sequence<int, index...>, hana::pair<Key, Value>...>  
    : holder<index, Value>...  
{ };  
  
template <typename ...Pairs>  
struct map  
    : map_impl<std::make_integer_sequence<int, sizeof...(Pairs)>, Pairs...>  
{ };  
```  
  
Of course, we would need to generalize this to work for arbitrary `hana::Product`s, but it's a detail. Let's focus on `hana::pair`s for now. With this representation, we could implement `hana::find_if` as  
  
``` c++  
template <typename Predicate, typename ...T>  
constexpr int find_index_eager() {  
    bool results[] = {  
        decltype(std::declval<Predicate const&>()(std::declval<T const&>()))::value...  
    };  
  
    // Find first satisfied index  
    for (int index = 0; index < static_cast<int>(sizeof...(T)); ++index) {  
        if (results[index])  
            return index;  
    }  
  
    return -1;  
}  
  
template <int index, typename Value>  
constexpr auto get_helper(hana::int_<index>, holder<index, Value> const& element) {  
    return hana::just(element.value);  
}  
  
constexpr auto get_helper(hana::int_<-1>, ...) {  
    return hana::nothing;  
}  
  
template <typename ...Key, typename ...Value, typename Predicate>  
constexpr auto find_if(map<hana::pair<Key, Value>...> const& m, Predicate const&) {  
    constexpr int index = find_index_eager<Predicate, Key...>();  
    return get_helper(hana::int_<index>{}, m);  
}  
```  
  
This takes advantage of the fact that the order of elements in a `map` is not defined. Hence, we can eagerly compute the `Predicate` on all the keys of the map, which is most likely more efficient than doing a recursive linear scan (for small to average map sizes, which should be the most common). A similar technique could then be used to optimize various operations on a map.  
  
Also, by making the key part of the `holder<...>`, we could optimize any lookup operation on an object whose comparison operator `hana::equal` is equivalent to `std::is_same` on the type of that object (like `hana::type`). Indeed, consider:  
  
``` c++  
// generic implementation for normal keys  
template <typename Map, typename T>  
constexpr auto at_key(Map const& m, T const& key) {   
    // ...  
}  
  
// Optimized implementation for `hana::type`s  
template <int index, typename Value, typename T, /* only when T is a hana::type */>  
constexpr auto at_key(holder<T, index, Value> const& element, T const&) {  
    return element.value;  
}  
```  
  
This takes advantage of template argument deduction to perform lookup based on an exact type more efficiently than with the above trick. This way, we could have optimally efficient lookup on `hana::type`s, and decently efficient lookup otherwise. Note: We need to handle any object with `hana::type_tag`, which includes `hana::basic_type` too.  
  
Of course, all of the above is only hand-waving; we would need to make sure that it works properly (I only did superficial tests so far), and also that the compile-time performance is as expected (**by benchmarking**). Then, we could implement the above ideas  non-naively, similarly to how `hana::tuple` is implemented.  
  
Worth seeing are also:  
- issue #118, which suggests using a type-level hash function.  
- issue #25, which suggests adding support for arbitrary key comparators in maps. This could make our job more difficult by adding a customization point that we must workaround to keep things efficient.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2015-12-20 19:06:25 UTC  
> Url: https://github.com/boostorg/hana/issues/223#issuecomment-166147759  

Should this go in `hana::experimental::map` or just modify the existing `hana::map`?

---

## Comment 2

> Username: ldionne  
> Created at: 2015-12-20 19:09:30 UTC  
> Url: https://github.com/boostorg/hana/issues/223#issuecomment-166147899  

I would just modify the current `hana::map`. That way, you'll make sure that you're not changing the functionality provided by `hana::map` or introducing bugs (because of the existing tests), but merely optimizing.  
  
Note that if we _have_ to modify some of the interface of `hana::map` to provide dramatic speedups, though, I'm totally open to it.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2016-01-16 06:35:19 UTC  
> Url: https://github.com/boostorg/hana/issues/223#issuecomment-172164592  

Regarding the use of a "type-level hash", do you mean an `integral_constant`?

---

## Comment 4

> Username: ldionne  
> Created at: 2016-01-16 20:00:09 UTC  
> Url: https://github.com/boostorg/hana/issues/223#issuecomment-172250648  

Well, that would be ideal. Unfortunately, I think it would be very hard, if not impossible, to come up with a non-trivial hash for some kinds of objects, such as `hana::type`s. Instead, I think expressing the hash in the form of an arbitrary C++ type would be easier. For many objects, it could just be the identity (and the bucket in which they would be placed would be identified by their type). For other objects, like `IntegralConstant`s, we would have to normalize them to some type (like `hana::int_<...>`), to make sure that if two `IntegralConstant`s are equal, then their hash is equal too.  
  
So basically, I'm thinking about arbitrary C++ types acting as the hash-value, because doing otherwise seems unimplementable.

---

## Comment 5

> Username: ricejasonf  
> Created at: 2016-01-20 00:59:18 UTC  
> Url: https://github.com/boostorg/hana/issues/223#issuecomment-173038924  

It sounds like the indices would go away. Would `find_if_impl` simply perform linear search on its keys?  
  
I would like to take another stab at this, unless you already have another commit in the works.

---

## Comment 6

> Username: ldionne  
> Created at: 2016-01-20 14:47:08 UTC  
> Url: https://github.com/boostorg/hana/issues/223#issuecomment-173225412  

`find_if` would have to perform a linear search, but `find` would be much more efficient because it could use the hash. If you want to give it a try, go ahead. I would suggest that we switch our discussion about hash-based containers to #118.
