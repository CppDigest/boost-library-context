# #521 - Add std::hash specializations [Closed]

> Username: doganulus  
> Created at: 2021-03-05 17:35:08 UTC  
> Updated at: 2022-10-17 21:10:12 UTC  
> Closed at: 2022-10-17 21:10:12 UTC  
> Url: https://github.com/boostorg/json/issues/521  

It might be nice to add std::hash support for `boost::json` types to enable their use in associative containers as keys. Currently the library only provides std::hash support for `boost::json::string`. For the full support, std::hash specializations for other constructs should be added.  
  
```cpp  
namespace std {  
  
template <>  
struct hash<boost::json::array> {  
  size_t operator()(const boost::json::array& jv) const;  
};  
  
template <>  
struct hash<boost::json::object> {  
  size_t operator()(const boost::json::object& jv) const;  
};  
  
template <>  
struct hash<boost::json::value> {  
  size_t operator()(const boost::json::value& jv) const;  
};  
  
}  // namespace std  
```  
  
Then, in my implementation, I noticed that `boost::json::object` preserves the insertion order when iterating over `key_value_pair`s as my test case below failed initially.  
  
```cpp  
TEST_CASE("Boost Json Object Hash") {  
  auto r1 = std::hash<boost::json::value>{}(  
      boost::json::value({{"a", 11}, {"b", 12}}));  
  auto r2 = std::hash<boost::json::value>{}(  
      boost::json::value({{"b", 12}, {"a", 11}}));  
  CHECK_EQ(r1, r2);  
}  
```  
Currently, when calculating hashes for `boost::json::object`s, I additionally sort pairs with respect to keys beforehand but wonder if this feature can be implemented better and added to the library.   
  
Thank you!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-03-05 17:49:13 UTC  
> Url: https://github.com/boostorg/json/issues/521#issuecomment-791579588  

hmm... `std::hash` is not defined for `vector`,  `map`, or `unordered_map`. However it is defined for `variant`.  
  
As you noted, `json::object` is particularly interesting because it preserves order. An implementation of `std::hash<json::object>` would need to sort the keys every time it calculates the hash. We can't put this in the object itself because it would add overhead to everyone. There's no good choices here.  
  
You can still use `json` containers in associative containers by defining your own hash function, as you noted.

---

## Comment 2

> Username: doganulus  
> Created at: 2021-03-05 18:48:13 UTC  
> Url: https://github.com/boostorg/json/issues/521#issuecomment-791611374  

Now I think of that a commutative hash function (e.g. simple xor'ing) would work for `json::object`.   
  
I am not sure about the quality of hashing as I do not have much experience with hash functions.

---

## Comment 3

> Username: doganulus  
> Created at: 2021-03-05 19:01:25 UTC  
> Updated at: 2021-03-05 19:02:42 UTC  
> Url: https://github.com/boostorg/json/issues/521#issuecomment-791618376  

Here is my implementation. Open to suggestions.  
  
```cpp  
inline std::size_t combine(std::size_t seed, std::size_t h) {  
  seed ^= h + 0x9e3779b9 + (seed << 6U) + (seed >> 2U);  
  return seed;  
}  
  
inline std::size_t combine_comm(std::size_t x, std::size_t y) { return x ^ y; }  
  
namespace std {  
  
size_t hash<boost::json::array>::operator()(  
    const boost::json::array& jv) const {  
  auto seed =  
      combine(static_cast<std::size_t>(boost::json::kind::array), jv.size());  
  for (const auto& value : jv) {  
    seed = combine(seed, std::hash<boost::json::value>{}(value));  
  }  
  return seed;  
}  
  
size_t hash<boost::json::object>::operator()(  
    const boost::json::object& jv) const {  
  auto seed =  
      combine(static_cast<std::size_t>(boost::json::kind::object), jv.size());  
  for (const auto& [key, value] : jv) {  
    const auto k = std::hash<boost::json::string>{}(key);  
    const auto kv = combine(k, std::hash<boost::json::value>{}(value));  
    seed = combine_comm(seed, kv);  
  }  
  return seed;  
}  
  
size_t hash<boost::json::value>::operator()(  
    const boost::json::value& jv) const {  
  switch (jv.kind()) {  
    default:  
    case boost::json::kind::null:  
    case boost::json::kind::bool_:  
      return std::hash<bool>{}(jv.get_bool());  
    case boost::json::kind::int64:  
      return std::hash<std::int64_t>{}(jv.get_int64());  
    case boost::json::kind::uint64:  
      return std::hash<std::uint64_t>{}(jv.get_uint64());  
    case boost::json::kind::double_:  
      return std::hash<double>{}(jv.get_double());  
    case boost::json::kind::string:  
      return std::hash<boost::json::string>{}(jv.get_string());  
    case boost::json::kind::array:  
      return std::hash<boost::json::array>{}(jv.get_array());  
    case boost::json::kind::object:  
      return std::hash<boost::json::object>{}(jv.get_object());  
  }  
}  
  
}  // namespace std  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-03-05 19:06:07 UTC  
> Url: https://github.com/boostorg/json/issues/521#issuecomment-791620916  

hmm yeah that's a good idea, it solves the problem of ordering. But I'm not yet convinced that the library should have an opinion on how `array` and `object` are hashed. @pdimov ?

---

## Comment 5

> Username: pdimov  
> Created at: 2021-03-05 19:27:33 UTC  
> Url: https://github.com/boostorg/json/issues/521#issuecomment-791631639  

Arrays are always hashed in the same way, pretty much as implemented in the above comment; the only difference is whether the size is hashed first (as most people do) or last (as in [N3980](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n3980.html).) I used to follow N3980 in Hash2, but will be switching to size first for performance reasons.  
  
I prefer + instead of ^ for the commutative combine, but I suppose it doesn't really matter here.  
  
I see no problem with the library having an opinion on how array and object are hashed, as there's no one else who can have such an opinion.  
  
I think I would `hash_combine` `jv.kind()` first in `hash<value>`, instead of only combining it in the array and object cases. However this needs to match `operator==`, which currently requires an exact kind match for everything _except_ `int64` and `uint64`, so the easiest way to match it would be to combine `int64` in the `jv.kind() == uint64` case.  
  
Also, the `null` case falls through to the `bool` case, which doesn't seem correct.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-03-05 19:38:27 UTC  
> Url: https://github.com/boostorg/json/issues/521#issuecomment-791637002  

@doganulus I'm open to a pull request if you would like to submit it

---

## Comment 7

> Username: grisumbras  
> Created at: 2022-10-17 14:36:07 UTC  
> Url: https://github.com/boostorg/json/issues/521#issuecomment-1280964154  

#522 implemented this, right?

---

## Comment 8

> Username: doganulus  
> Created at: 2022-10-17 19:12:08 UTC  
> Url: https://github.com/boostorg/json/issues/521#issuecomment-1281348593  

Yes, this is done.
