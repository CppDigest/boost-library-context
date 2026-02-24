# #44 - Define boost::hash (and maybe std::hash) for string_view [Closed]

> Username: dimztimz  
> Created at: 2018-04-19 18:54:55 UTC  
> Updated at: 2018-05-01 14:51:37 UTC  
> Closed at: 2018-05-01 14:51:37 UTC  
> Url: https://github.com/boostorg/utility/issues/44  

This is a feature request.  
  
The requirement that it should return the same hash for equal `std::string` should be met.  
  
Additionally, if possible, provide `std::hash<string_view>` with the above requirement, although this is probably not possible to do efficiently because there is no thing like `std::hash(char* ptr, size_t len)`.

---

## Comment 1

> Username: mclow  
> Created at: 2018-04-20 00:47:31 UTC  
> Url: https://github.com/boostorg/utility/issues/44#issuecomment-382927010  

We can do something like this for `boost::hash` (make `string/string_view` return the same value), but making that work for `std::hash` is not possible (since the standard library provides the hash for `string`) w/o creating a temporary string, and I don't think we want to do that.

---

## Comment 2

> Username: Lastique  
> Created at: 2018-04-20 01:23:34 UTC  
> Url: https://github.com/boostorg/utility/issues/44#issuecomment-382933672  

Maybe we shouldn't provide `std::hash` specializations until C++17 then? In C++17 we can leverage `std::hash<std::string_view>` specialization.  
  
Also, may I ask to provide these integration bits in separate headers to reduce the dependencies brought in by including `string_view.hpp`?

---

## Comment 3

> Username: dimztimz  
> Created at: 2018-04-20 10:02:56 UTC  
> Url: https://github.com/boostorg/utility/issues/44#issuecomment-383049381  

`boost::hash<string_view>` and `hash_value(string_view)` should be provided in the same header as string_view, `string_view.hpp`. This is how `std hash` is defined for string, it's in the same header as string, you don't include `<string_hash>`.  
  
Otherwise, it will just confuse the users.

---

## Comment 4

> Username: Lastique  
> Created at: 2018-04-20 10:13:44 UTC  
> Url: https://github.com/boostorg/utility/issues/44#issuecomment-383051872  

There's nothing confusing about having integration stuff in separate headers. This is common practice in Boost. Although, I have to admit, supporting Boost.Hash is cheap - it doesn't require including its headers, you just have to define `hash_value` function overload. So maybe it's not worth the hassle to have it in a separate header.  
  
`std::hash` is different, it requires you to include the whole `<functional>` just to get the forward declaration of the `std::hash` template.

---

## Comment 5

> Username: dimztimz  
> Created at: 2018-04-20 10:21:24 UTC  
> Url: https://github.com/boostorg/utility/issues/44#issuecomment-383053683  

There is this `hash_fwd.hpp` thingy, maybe depend only on that.
