# #857 - Save memory in basic_fields [Closed]

> Username: vinniefalco  
> Created at: 2017-10-29 22:26:06 UTC  
> Updated at: 2017-11-21 01:14:01 UTC  
> Closed at: 2017-11-21 01:14:01 UTC  
> Url: https://github.com/boostorg/beast/issues/857  

Instead of using a multiple of `sizeof(value_type)` for allocation, use a POD type that has the smallest size necessary for `alignof(value_type)` alignment.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-29 23:43:32 UTC  
> Url: https://github.com/boostorg/beast/issues/857#issuecomment-340312914  

Use `boost::type_with_alignment` (Boost.TypeTraits)
