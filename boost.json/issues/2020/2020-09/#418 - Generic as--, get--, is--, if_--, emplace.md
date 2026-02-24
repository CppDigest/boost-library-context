# #418 - Generic as<>, get<>, is<>, if_<>, emplace<> [Closed]

> Username: anarthal  
> Created at: 2020-09-26 10:26:43 UTC  
> Updated at: 2022-05-29 06:48:13 UTC  
> Closed at: 2022-05-29 06:48:13 UTC  
> Url: https://github.com/boostorg/json/issues/418  

To enable generic code interoperate easier with json::value, add template accessors to `json::value`.  
  
I see two options in terms of making them member/non-member:  
- Implement them as member functions. Easier for the user to discover (they are part of the class interface). Requires the word `template` in generic code though. They become coupled to the class, so no chance of moving them to a separate file.  
- Implement them as standalone functions. They become more in the line of `value_from`, `value_to` and `number_cast`. No need for `template` keyword. May be more extensible, too.  
  
I would say to implement the following set (I will write them in member function notation):   
- `T& as()`  
- `const T& as() const`  
- `T& emplace()`  
- `T& get()`  
- `const T& get() const`  
- `T* if_()`  
- `const T* if_() const`  
- `bool is() const`  
  
In terms of types, I would say keep it simple and accept just the basic JSON types:  
- `bool`  
- `std::uint64_t`  
- `std::int64_t`  
- `double`  
- `json::string`  
- `json::object`  
- `json::array`  
- `std::nullptr_t`  
  
I guess it is arguable whether `std::nullptr_t` should be accepted for the getters (anything except for `emplace` and `is`).

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-05-23 18:29:45 UTC  
> Url: https://github.com/boostorg/json/issues/418#issuecomment-1135004286  

Can you show me some examples of use of such functions? I am currently skeptical of their utility.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-24 00:44:46 UTC  
> Updated at: 2022-05-24 00:46:16 UTC  
> Url: https://github.com/boostorg/json/issues/418#issuecomment-1135277716  

I'm not fond of this as member functions. I could _maybe_ be convinced they should be added as free functions. What if you write `emplace<int32>( jv )`? Is that the same as `jv.emplace_uint64()`?

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-05-24 07:38:29 UTC  
> Url: https://github.com/boostorg/json/issues/418#issuecomment-1135519467  

As far as I can see, it would be something like `value_from`, but only for a subset of types (arithmetic types, bool, etc.). But I would like to know if there are actual use cases.

---

## Comment 4

> Username: anarthal  
> Created at: 2022-05-28 20:01:52 UTC  
> Url: https://github.com/boostorg/json/issues/418#issuecomment-1140322343  

This was just an appreciation I had during the review, based on past experiences with custom containers. I don't have a real use case to share. If you think `value_from` and `value_to` are enough, then feel free to close this issue.
