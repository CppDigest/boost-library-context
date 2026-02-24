# #222 - url_base [Closed]

> Username: alandefreitas  
> Created at: 2022-07-06 17:33:31 UTC  
> Updated at: 2022-08-05 01:15:06 UTC  
> Closed at: 2022-08-05 01:15:06 UTC  
> Url: https://github.com/boostorg/url/issues/222  

The design pattern that we want here is to encapsulate the core functionality of the type into a base class `B`. Users don't instantiate B directly. Instead, the library offers types derived from `B`, lets call them `D1`, `D2`, ... `Dn`. The `D` types inherit all of their domain-specific interface from `B`. For Boost.URL that would be the `url_view` interface, plus the URL mutation operations (non-const member functions). The `D` types provide all of the public special members: construction and assignment. The base class can't have public special members or else you have the problem described in #221 where move assignment throws exceptions (which is a real issue).  
  
[Boost.HTTP.Proto](https://github.com/CPPAlliance/http_proto/tree/develop/include/boost/http_proto) uses this pattern. There are two base types `fields_view_base`, and [`fields_base`](https://github.com/CPPAlliance/http_proto/blob/develop/include/boost/http_proto/fields_base.hpp#L41), which implement the domain-specific APIs for a range of read-only HTTP fields, and a range of modifiable HTTP fields respectively. The derived types are `fields_view`, `request_view`, `response_view` for the read-only containers, and `fields`, `request`, and `response` for the modifiable containers.  
  
In Boost.URL we have two notional derived types. One is the modifiable URL container that uses the default allocator. The other is the modifiable URL container that uses static storage (an in-class data member whose size is specified by the user at compile time). Those types are declared thusly `url::url u;` and `url::static_url< 4096 > u`. Thus, we need to make a new type `url_base` (derived from `url_view`) and move all the mutation operations from `url` into it. And in `url` leave behind the correct special members (construction, assignment) and `swap`:  
  
```cpp  
class url_view {...}  
class url_base : public url_view {...}  
class url : public url_base {...}  
class static_url_base : public url_view {...}  
class static_url : public static_url_base {...}  
```  
  
`url_base` has no public special members, so it can't be sliced and reassigned. This idiom puts the domain-specific logic into the base class, and requires that the derived class add the flavor of special members.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-31 03:23:56 UTC  
> Url: https://github.com/boostorg/url/issues/222#issuecomment-1200339884  

Do we still need to do this?
