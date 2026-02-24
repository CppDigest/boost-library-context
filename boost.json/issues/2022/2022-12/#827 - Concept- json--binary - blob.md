# #827 - Concept: json::binary / blob [Open]

> Username: klemens-morgenstern  
> Created at: 2022-12-27 08:44:13 UTC  
> Updated at: 2022-12-28 00:45:01 UTC  
> Url: https://github.com/boostorg/json/issues/827  

Json could have a binary/blob type that can story raw memory and must/should have a conversion operator to a data type that can be represented as json. If optional that would be a string.  
  
# Motivation:  
  
## 1. json::value as freeform data  
  
In a statically typed language it is often useful to still have free form data for certain things, e.g. for dumping diagnostic information or data in addition to what's typed. `json::value` does an excellent job at this, and I have used it in exactly this way.  
  
Adding in a binary type will make this more efficient and "more correct" in that we don't need to stringify data that is by nature binary. As an example, consider the following enum:  
  
```cpp  
enum this_idea  
{  
   great = 1,  
   greater,  
   the_greatest  
};  
```  
  
This enum could be stored as a single byte, yet obviously serialized into a string for json.   
  
## 2. other serialization format  
  
Some issues, such as #822 and #510 have address the possibility of adding other formats, such as MsgPack & CBOR respectively. Both of those formats know of a binary type. Since they are made to lower in data, the above enum would most likely be serialized as it's binary value in cbor and msgpack.  
  
# Rough design idea  
  
The rough design idea is to provide a `json::binary` type that is either similar to `json::string` without the `.c_str()` or holds the raw memory as a `void*, size_t` pair.  
  
At construction it should get assigned a tag and an optional conversion function. The tag indicates bulit-in conversions, such as are provided by [cbor](https://www.rfc-editor.org/rfc/rfc8949.html#name-tagging-of-items).  
The conversion function shall be without context and thus a raw function pointer.   
  
  
With other serializers boost::json can become the basis of advanced RPC libraries to compete with the big ones, such as gRPC.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-12-27 12:47:41 UTC  
> Updated at: 2022-12-27 12:52:12 UTC  
> Url: https://github.com/boostorg/json/issues/827#issuecomment-1365877479  

No. Note that Boost.JSON strings already support "binary." They can hold anything, and get escaped properly on serialization.

---

## Comment 2

> Username: pfeatherstone  
> Created at: 2022-12-27 15:29:43 UTC  
> Url: https://github.com/boostorg/json/issues/827#issuecomment-1365983890  

Why is it not a good idea to support msgpack? When sending data to the browser and back it is way more efficient and faster to do so using this binary format. I basically just see json as a dictionary type.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-12-27 17:40:11 UTC  
> Updated at: 2022-12-27 17:46:38 UTC  
> Url: https://github.com/boostorg/json/issues/827#issuecomment-1366069244  

MSGPACK does not roundtrip to `json::value`. We can offer it as an example program, with limited support. But offering it as a library component is out of scope. The over-arching design element of Boost.JSON is the `json::value` variant type, and ensuring that it can be parsed and serialized correctly. It is not the intent of the library to become a "swiss army knife of JSON-like formats." There's already plenty of those.  
  
You could also simply apply gzip or br Transfer-Encoding to HTTP bodies containing application/json.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-12-28 00:03:32 UTC  
> Url: https://github.com/boostorg/json/issues/827#issuecomment-1366269341  

with a binary it could round trip, which is the point of this issue.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-12-28 00:42:45 UTC  
> Updated at: 2022-12-28 00:45:01 UTC  
> Url: https://github.com/boostorg/json/issues/827#issuecomment-1366286709  

> with a binary it could round trip  
  
we're not adding a "binary" property to `json::string`, that's out of scope. there are infinite things that this library "could" do, but they are all out of scope.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-12-28 00:44:43 UTC  
> Url: https://github.com/boostorg/json/issues/827#issuecomment-1366287558  

> With other serializers boost::json can become the basis of advanced RPC libraries  
  
It is not a goal of the library to have official support for "other serializers." The goal is to offer a DOM solution which is the best in class in terms of offering first-class value types, allocator control, and performant parsing and serialization.
