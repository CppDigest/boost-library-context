# #518 - General map-like conversions [Open]

> Username: vinniefalco  
> Created at: 2021-03-04 02:26:22 UTC  
> Updated at: 2024-04-09 19:26:53 UTC  
> Url: https://github.com/boostorg/json/issues/518  

> To repeat what I said on Slack, now that tuple-likes are supported it looks like general maps (where the key is not string-like) would also start working (being treated as sequences of pairs.) If they do work, we need to have a test for it. If they don't, we might want to look into why.

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-03-04 06:18:45 UTC  
> Url: https://github.com/boostorg/json/issues/518#issuecomment-790328147  

I tested and it does indeed work. For example:  
```cxx  
enum class K { A, B, C };  
  
// tag_invoke for K removed for brevity  
  
check(std::map<K, int> {  
    { K::A, 1 }, { K::B, 2}, { K::C, 3},  
});  
```  
  
I have to comment that serializing maps into arrays is suboptimal. If we could check during compilation that `tag_invoke` for key produces a `json::string` then we would be able to serialize them into objects. But that would require a different `tag_invoke` signature. Something like `json::value tag_invoke(value_from_tag, K const&)` ( and users could write `json::string tag_invoke(value_from_tag, K const&)`).

---

## Comment 2

> Username: grisumbras  
> Created at: 2021-05-23 06:55:13 UTC  
> Url: https://github.com/boostorg/json/issues/518#issuecomment-846514494  

So, since #515 we do support all associative containers.  
Currently  
* If the container has unique keys (checked via the result type of `emplace`) and the type of key is StringLike, we convert to/from object  
* Otherwise we convert to/from array of 2-element arrays.  
  
Should we pursue the former to expand to  maps with keys that convert to/from `json::string`? E.g.  
  
```cxx  
enum class E { e1, e2, e3 };  
void tag_invoke(json::value_from_tag, json::value& jv, E e);  
  
std::map<E, X> my_map;  
auto jv = value_from(my_map);  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-05-26 15:47:18 UTC  
> Url: https://github.com/boostorg/json/issues/518#issuecomment-848887489  

I don't know

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-04-08 16:40:08 UTC  
> Url: https://github.com/boostorg/json/issues/518#issuecomment-2043205878  

If #989 is done, this could be supported. E.g.:  
  
```c++  
enum class E { e1, e2, e3 };  
struct E_String { // is_string_like  
  E e;  
  E_String(E);  
  explicit E_String(string_view);  
  operator string_view() const;  
};  
   
namespace boost::json {  
template<>  
struct serialize_as<E> : mp11::identity<E_String> {};  
}  
  
std::map<E, X> my_map;  
auto jv = value_from(my_map);  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2024-04-09 19:26:52 UTC  
> Url: https://github.com/boostorg/json/issues/518#issuecomment-2045914424  

let someone ask for it :)
