# #787 - Extend boost::json::serialize [Closed]

> Username: pdimov  
> Created at: 2022-10-25 11:41:29 UTC  
> Updated at: 2024-10-09 14:27:04 UTC  
> Closed at: 2024-10-09 14:27:04 UTC  
> Url: https://github.com/boostorg/json/issues/787  

Currently, `boost::json::serialize(x)` works when `x` is one of `json::value`, `json::array`, `json::object`, `json::string`, and `string_view`.  
  
It would be natural to extend it to work for other types. Supporting `nullptr_t` and `bool` for `x` is trivial. Integral and floating point types are also obvious. For the more complex cases, we can use the same traits-based approach as with `value_from`, and serialize string-like types as strings, sequence-like and tuple-like types as arrays, and map-like types and described classes as objects. This is consistent with the currently allowed arguments to `serialize` and will, in effect, do the equivalent of `serialize(value_from(x))` without creating a temporary `value` (but will of course work only if `value_from(x)` doesn't invoke user-defined `tag_invoke` conversions).  
  
This is possible to implement today, but the current `serializer` engine is not well suited to it, because it wants to take `value*`. A different, push-based, serializer infrastructure will be a better fit:  
```  
template<class T, class Out> void serialize_impl( T const& x, Out out );  
```  
where `Out` is a function object that is called with a `string_view`. So that `serialize` becomes  
```  
template<class T> std::string serialize( T const& x )  
{  
    std::string r;  
    serialize_impl( x, [&](auto sv){ r.append( sv.data(), sv.size() ); } );  
    return r;  
}  
```  
Or perhaps we can make `Out` take a `char const*` and a `size_t` instead of `string_view`, to avoid the need to commit to a specific string view type.  
```  
template<class T> std::string serialize( T const& x )  
{  
    std::string r;  
    serialize_impl( x, [&](char const* p, size_t n){ r.append( p, n ); } );  
    return r;  
}  
```  
This would allow users more freedom in creating their own serializers. E.g. for the use case in #687, one might use `std::map<std::string, std::string_view>` (or a described struct with `string_view` members) as the data structure to be serialized, which allows the string values to be "unowned".

---

## Comment 1

> Username: pdimov  
> Created at: 2022-10-25 13:33:33 UTC  
> Url: https://github.com/boostorg/json/issues/787#issuecomment-1290565255  

Proof of concept: https://gist.github.com/pdimov/d5646e1887be00d7991b199892adb9c1

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-10-25 15:13:16 UTC  
> Url: https://github.com/boostorg/json/issues/787#issuecomment-1290728303  

This can be done cleanly by extending `serializer` to support the serialization of user-defined types. First some observations:  
  
- If you look at a typical stack for a nested object being serialized under this scheme, first you have user defined types then you have JSON types  
- In other words, once you start serializing a JSON type it is not possible to then encounter a nested user type  
- `serializer` doesn't do anything with the stack until it runs out of space in the output  
- We can add a new state to serializer which means "run the type-erased handler in `fn0_`".  
  
The user-facing function to select a UDT would look like  
  
```  
template< class T>  
void  
reset( T const* t ) // can no longer be noexcept  
{  
    fn0_ = &serializer::write_udt< T >( stream* );  
    pt_ = t; // `void const*` added to union     
    st_ = state::udt;  
}  
```  
  
Unlike the implementation for native JSON types, the udt algorithms don't get to add constants to the `state` enum (since the set of values is unbounded). They would need to use their own constants and they would not have the benefit of leveraging the jump table already in `serializer`, but this is OK. The udt algorithm would look like:  
  
```  
template< class T > bool serializer::write_udt( stream* );  
```  
  
UDT algorithms would need to:  
  
- emit the proper delimiters for their corresponding JSON type (e.g. '[' and ']')  
- emit correct JSON for elements by calling other serializer members (including any instantiation of `write_udt` ) or doing it themselves  
- handle the case where a suspend is indicating, either by running out of buffer space or when a callee signals suspension  
  
To suspend the algorithm need simply save the value of `fn0_`, replace `fn0_` with its own function pointer, and push any additional state needed to get back to the proper context on resume, including its own internal equivalent of `state` if it needs it. Might also need to save the value of `pt_` or whatever is in there.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-10-09 14:27:04 UTC  
> Url: https://github.com/boostorg/json/issues/787#issuecomment-2402503281  

Implemented in #956.
