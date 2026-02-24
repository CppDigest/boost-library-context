# #275 - serialize array, object, string, underlying types [Closed]

> Username: vinniefalco  
> Created at: 2020-09-05 13:26:51 UTC  
> Updated at: 2020-09-07 18:43:14 UTC  
> Closed at: 2020-09-07 18:43:14 UTC  
> Url: https://github.com/boostorg/json/issues/275  

See if the `serializer` can be refactored to support serializing the container types and possibly the underlying types (like `double`). Provide overloads of `to_string` and `operator<<` for these types.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-09-05 13:35:24 UTC  
> Url: https://github.com/boostorg/json/issues/275#issuecomment-687612360  

Doesn't this work automatically due to these types being convertible to `json::value`?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-05 13:39:13 UTC  
> Url: https://github.com/boostorg/json/issues/275#issuecomment-687612829  

No, because the serializer stores a pointer to the value being serialized:  
https://github.com/CPPAlliance/json/blob/adacd6670cd92ef5b8e2890550a7a6171e86d854/include/boost/json/serializer.hpp#L66  
  
To make this work, serializer needs to type-erase the top-level thing being serialized, probably by changing `jv_` to be `void*`, and storing a pointer to an overloaded member function. The existing write_* members should probably take a const ref to the thing they are serializing, to avoid depending on `jv_` being a `value const*`.

---

## Comment 3

> Username: pdimov  
> Created at: 2020-09-05 13:52:08 UTC  
> Url: https://github.com/boostorg/json/issues/275#issuecomment-687614487  

I was talking about `to_string` and `operator<<`.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-09-05 14:17:02 UTC  
> Url: https://github.com/boostorg/json/issues/275#issuecomment-687617359  

> I was talking about to_string and operator<<.  
  
Yikes... yes. Those will make a copy :(
