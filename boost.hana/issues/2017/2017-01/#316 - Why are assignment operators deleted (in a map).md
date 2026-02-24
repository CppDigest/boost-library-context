# #316 - Why are assignment operators deleted (in a map)? [Closed]

> Username: michalk-1  
> Created at: 2017-01-06 08:56:53 UTC  
> Updated at: 2017-02-22 09:05:25 UTC  
> Closed at: 2017-02-22 07:34:35 UTC  
> Url: https://github.com/boostorg/hana/issues/316  

From [hana/map.hpp#L104](https://github.com/boostorg/hana/blob/master/include/boost/hana/map.hpp#L104):  
```cpp  
constexpr map_impl() = default;  
constexpr map_impl(map_impl const& other) = default;  
constexpr map_impl(map_impl&& other) = default;  
```  
Explicit declarations make the assignment operators like `constexpr map_impl& operator=(map_impl&&)` deleted. I assume it's intentional, but what's the reason behind this?  
  
I'm asking because it prevents from reusing a variable. Example:  
```cpp  
auto t0 = hana::make_map(hana::make_pair(hana::int_c<2>, std::string{"abcdef"}));  
auto moved = std::move(t0);  
t0 = std::move(moved);  
BOOST_HANA_RUNTIME_CHECK(  
    t0 == hana::make_map(hana::make_pair(hana::int_c<2>, std::string{"abcdef"}))  
);  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2017-01-07 21:40:37 UTC  
> Url: https://github.com/boostorg/hana/issues/316#issuecomment-271112089  

The thing is that it opens a huge can of worms as to what the map should be assignable from. Should it be assignable from any map with the same keys and values that can be assigned? That's very hard to implement in an efficient manner (in terms of compilation time). Should it only be assignable from a map with exactly the same type? That seems kind of incomplete.  
  
Instead of answering these questions, it was easier to simply disallow this, especially since there was no clear use case. If this is something that you'd like to be supported (and I agree it is a worthwhile improvement), could you suggest semantics that the assignment should have?

---

## Comment 2

> Username: michalk-1  
> Created at: 2017-01-10 08:41:51 UTC  
> Url: https://github.com/boostorg/hana/issues/316#issuecomment-271517289  

Thanks **ldionne**. I've come up with something. Please have a look.  
  
---  
Strongly typed assignment.   
In order to assign one variable to another, the types of both variables need to have undergone the same sequence of type transformations and need to have had the same initial type. Example:  
  
```cpp  
auto t0 = hana::make_map(  
    hana::make_pair(hana::type_c<bool>, 1),  
    hana::make_pair(hana::type_c<char>, 2));  
auto t1 = hana::erase_key(t0, hana::type_c<char>);  
auto t2 = hana::insert(t1, hana::make_pair(hana::type_c<char>, 3));  
  
auto u0 = hana::make_map(  
    hana::make_pair(hana::type_c<bool>, 4),  
    hana::make_pair(hana::type_c<char>, 5));  
auto u1 = hana::erase_key(u0, hana::type_c<char>);  
auto u2 = hana::insert(u1, hana::make_pair(hana::type_c<char>, 6));  
  
// t0 = t2; // illegal, will raise a compiler error only in MOST cases  
u2 = t2; // OK  
BOOST_HANA_RUNTIME_CHECK(u2 == t2);  
BOOST_HANA_RUNTIME_CHECK(hana::at_key(t2, hana::type_c<char>) == 3);  
BOOST_HANA_RUNTIME_CHECK(hana::at_key(u2, hana::type_c<char>) == 3);  
```

---

## Comment 3

> Username: ldionne  
> Created at: 2017-02-22 07:34:35 UTC  
> Url: https://github.com/boostorg/hana/issues/316#issuecomment-281591815  

Merged in `develop`, will be in the next release of Boost (1.64.0).

---

## Comment 4

> Username: michalk-1  
> Created at: 2017-02-22 09:05:25 UTC  
> Url: https://github.com/boostorg/hana/issues/316#issuecomment-281610133  

Great news, thanks! :+1:
